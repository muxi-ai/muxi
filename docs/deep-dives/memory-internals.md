---
title: Memory Internals
description: Technical deep dive into MUXI's three-tier memory system
---
# Memory Internals

## Technical deep dive into MUXI's three-tier memory system


Technical implementation of MUXI's memory system: data structures, algorithms, caching strategies, and performance characteristics.


## Architecture Overview

```plain
┌─────────────────────────────────────────────────────────┐
│                    Memory Manager                       │
├─────────────────────────────────────────────────────────┤
│  ┌───────────────┐  ┌─────────────┐  ┌───────────────┐  │
│  │   Buffer      │  │   Working   │  │   Persistent  │  │
│  │   Memory      │  │   Memory    │  │    Memory     │  │
│  │               │  │             │  │               │  │
│  │  In-memory    │  │  FAISSx     │  │  SQLite/      │  │
│  │  ring buffer  │  │  vectors    │  │  PostgreSQL   │  │
│  └──────┬────────┘  └──────┬──────┘  └────────┬──────┘  │
│         └──────────────────┼──────────────────┘         │
│                ┌───────────┴───────────┐                │
│                │     Query Planner     │                │
│                │  (semantic + recency) │                │
│                └───────────────────────┘                │
└─────────────────────────────────────────────────────────┘
```


## Buffer Memory

### Implementation

```python
class BufferMemory:
    def __init__(self, size=50, multiplier=10):
        self.messages = deque(maxlen=size)
        self.summaries = deque(maxlen=size * multiplier)
        self.embedding_cache = LRUCache(1000)
```

### Behavior

| Operation | Complexity | Notes |
|-----------|------------|-------|
| Add message | O(1) | Append to ring buffer |
| Get recent N | O(N) | Slice from deque |
| Overflow | O(k) | Summarize k oldest messages |

### Summarization

When buffer exceeds `size`:

1. Take oldest N messages (batch)
2. LLM summarizes into single entry
3. Summary moved to extended buffer
4. Original messages dropped

```
Messages: [1, 2, 3, ..., 50, 51] ← overflow!
                    ↓
Summarize [1-10] → "User discussed project setup..."
                    ↓
Messages: [11, 12, ..., 50, 51]
Summaries: ["User discussed project setup..."]
```


## Working Memory

### Vector Store

MUXI uses FAISS for vector similarity search:

```python
class WorkingMemory:
    def __init__(self, dimension=1536):
        self.index = faiss.IndexFlatIP(dimension)  # Inner product
        self.metadata = {}  # id → metadata mapping
```

### Indexing

```
New content (tool output, user info)
         ↓
    Embed with LLM
         ↓
    Add to FAISS index
         ↓
    Store metadata (timestamp, source, user_id)
```

### Search

```python
def search(self, query: str, k: int = 5) -> List[Memory]:
    query_vec = embed(query)
    distances, indices = self.index.search(query_vec, k)
    return [self.metadata[i] for i in indices[0]]
```

Semantic similarity, not keyword matching.

### FIFO Cleanup

When `max_memory_mb` exceeded:

```python
def cleanup(self):
    while self.size_mb > self.max_memory_mb:
        oldest = self.get_oldest()
        self.remove(oldest.id)
```

Configurable interval:
```yaml
memory:
  working:
    max_memory_mb: 10
    fifo_interval_min: 5
```


## Persistent Memory

### Dynamic Embedding Dimensions

Persistent memory uses **dimension-specific tables** rather than a single fixed table. The table name is derived from the embedding model's output dimension:

| Embedding Model | Dimension | Table Name |
|----------------|-----------|------------|
| `openai/text-embedding-3-small` | 1536 | `memories_1536` |
| `openai/text-embedding-3-large` | 3072 | `memories_3072` |
| `local/sentence-transformers/all-MiniLM-L6-v2` | 384 | `memories_384` |
| `local/sentence-transformers/all-mpnet-base-v2` | 768 | `memories_768` |

This is handled by the `get_memory_model(dimension)` factory, which dynamically creates SQLAlchemy ORM models:

```python
def get_memory_model(dimension: int):
    tablename = f"memories_{dimension}"
    # Returns a dynamically-created SQLAlchemy model class
    # with Vector(dimension) column matching the embedding size
```

Multiple formations sharing the same database can each use a different embedding model -- their tables won't conflict.

### Schema (PostgreSQL)

```sql
-- Table name varies by embedding dimension (e.g., memories_1536, memories_384)
CREATE TABLE memories_1536 (
    id UUID PRIMARY KEY,
    user_id VARCHAR(255) NOT NULL,
    session_id VARCHAR(255),
    content TEXT NOT NULL,
    embedding VECTOR(1536),    -- Matches the embedding model's dimension
    memory_type VARCHAR(50),   -- 'fact', 'preference', 'context'
    importance FLOAT DEFAULT 0.5,
    created_at TIMESTAMP DEFAULT NOW(),
    accessed_at TIMESTAMP DEFAULT NOW(),
    access_count INT DEFAULT 0
);

CREATE INDEX idx_user ON memories_1536(user_id);
CREATE INDEX idx_embedding ON memories_1536 USING ivfflat (embedding vector_cosine_ops);
CREATE INDEX idx_importance ON memories_1536(importance DESC);
```

### Local Embedding Models

MUXI supports running embedding models locally with no API key required. Use the `local/` prefix followed by a HuggingFace repo id:

```yaml
llm:
  models:
    - embedding: "local/sentence-transformers/all-MiniLM-L6-v2"   # 384 dimensions
    # or: "local/sentence-transformers/all-mpnet-base-v2"          # 768 dimensions
```

The id after `local/` is passed straight to HuggingFace, so any HuggingFace embedding repo works. Models download automatically on first use into the standard HuggingFace cache (`$HF_HOME` or `~/.cache/huggingface/hub/`). This is useful for development, air-gapped environments, or reducing API costs.

> **Note:** SQLite-backed formations automatically fall back to local embeddings when no API-based embedding model is configured.

### Migrating Between Embedding Models

If you change your embedding model (e.g., from `openai/text-embedding-3-small` at 1536 dims to `local/sentence-transformers/all-MiniLM-L6-v2` at 384 dims), existing memories need re-embedding. Use the migration script:

```bash
python scripts/migrate_embeddings.py \
  --from-dim 1536 \
  --to-dim 384 \
  --to-model "local/sentence-transformers/all-MiniLM-L6-v2" \
  --connection-string "postgresql://user:pass@localhost/muxi"
```

The script reads from `memories_{from_dim}`, re-embeds each memory with the target model, and inserts into `memories_{to_dim}`. The source table is preserved (not deleted).

### Importance Scoring

Memories are scored for importance:

```python
def calculate_importance(memory: Memory) -> float:
    recency = time_decay(memory.created_at)
    access_frequency = log(memory.access_count + 1)
    explicit_importance = memory.explicit_importance or 0.5

    return (0.3 * recency +
            0.3 * access_frequency +
            0.4 * explicit_importance)
```

Higher importance = kept longer, surfaced more often.


## User Synopsis Caching

### Two-Tier Cache

```plain
┌─────────────────────────────────────┐
│         Identity Cache              │  TTL: 7 days
│  (name, preferences, traits)        │
└─────────────────────────────────────┘
                ↓
┌─────────────────────────────────────┐
│         Context Cache               │  TTL: 1 hour
│  (recent topics, current goals)     │
└─────────────────────────────────────┘
```

### Synopsis Generation

```python
def generate_synopsis(user_id: str) -> Synopsis:
    # Gather all user memories
    memories = persistent.get_all(user_id)

    # LLM synthesizes into structured synopsis
    synopsis = llm.synthesize(
        prompt="Create a user profile from these memories...",
        memories=memories
    )

    # Cache with appropriate TTL
    cache.set(f"identity:{user_id}", synopsis.identity, ttl=7*24*3600)
    cache.set(f"context:{user_id}", synopsis.context, ttl=3600)

    return synopsis
```

### Token Savings

| Approach | Tokens per Request |
|----------|-------------------|
| Full history | 5,000-50,000 |
| Synopsis only | 500-1,000 |
| **Savings** | **80-95%** |


## Query Planning

When an agent needs context, the Query Planner coordinates all tiers:

```python
def get_context(query: str, user_id: str) -> Context:
    # 1. Always include recent buffer
    recent = buffer.get_recent(10)

    # 2. Semantic search across all tiers
    working_results = working.search(query, k=5)
    persistent_results = persistent.search(query, user_id, k=5)

    # 3. Get user synopsis
    synopsis = cache.get(f"synopsis:{user_id}") or generate_synopsis(user_id)

    # 4. Rank and deduplicate
    all_results = rank_and_dedupe(
        recent + working_results + persistent_results
    )

    # 5. Fit within context window
    return fit_to_window(synopsis, all_results, max_tokens=4000)
```


## Multi-User Isolation

### Implementation

Every memory operation includes `user_id`:

```python
def add_memory(content: str, user_id: str):
    # User ID is part of the primary key namespace
    memory = Memory(
        id=f"{user_id}:{uuid4()}",
        user_id=user_id,
        content=content
    )
    self.store(memory)

def search(query: str, user_id: str):
    # Always filter by user_id
    return self.index.search(
        query,
        filter={"user_id": user_id}
    )
```

No cross-user access possible at the data layer.


## Performance Characteristics

| Operation | Latency | Notes |
|-----------|---------|-------|
| Buffer add | <1ms | In-memory deque |
| Buffer search | 1-5ms | Linear scan, small N |
| Working search | 5-20ms | FAISS similarity |
| Persistent search | 20-100ms | PostgreSQL + pgvector |
| Synopsis generation | 500-2000ms | LLM call (cached) |
| Full context build | 50-200ms | All tiers + ranking |


## Configuration Reference

```yaml
memory:
  buffer:
    size: 50                    # Messages before summarization
    multiplier: 10              # Extended capacity factor
    vector_search: true         # Enable semantic search
    embedding_model: openai/text-embedding-3-small  # Or local/sentence-transformers/all-MiniLM-L6-v2

  working:
    max_memory_mb: 10           # Memory limit
    fifo_interval_min: 5        # Cleanup frequency

  persistent:
    enabled: true
    provider: postgresql
    connection_string: ${{ secrets.POSTGRES_URI }}
    user_isolation: true

  synopsis:
    identity_ttl: 604800        # 7 days
    context_ttl: 3600           # 1 hour
    refresh_on_access: true
```


## Next Steps

- [Memory Concepts](../concepts/memory-system.md) - High-level overview
- [Memory Configuration](../reference/memory.md) - YAML reference
- [Multi-Tenancy](multi-tenancy.md) - User isolation details
