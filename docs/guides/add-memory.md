---
title: Add Memory
description: Make your agents remember conversations
---
# Add Memory

## Give agents memory that persists

Enable memory so agents remember what users told them - within a conversation and across sessions. This guide adds persistent memory to your formation.

## Overview

MUXI memory layers:

1. **Buffer** - Recent messages (in-memory)
2. **Vector Search** - Semantic similarity
3. **Persistent** - Long-term storage (database)

> [!TIP]
> **Persistent memory is enabled by default.** When you create a formation, SQLite-backed persistent memory is automatically active with `memory.db` in the formation directory. This guide covers customization and production setups.

## Step 1: Enable Buffer Memory

Add to `formation.afs`:

```yaml
memory:
  buffer:
    size: 50        # Keep 50 recent messages
```

This remembers the current conversation.

## Step 2: Enable Vector Search

Find related past conversations:

```yaml
memory:
  buffer:
    size: 50
    vector_search: true
```

### Using Local Embeddings

You can run embedding models locally with no API key required:

```yaml
llm:
  models:
    - embedding: "local/nomic-ai/nomic-embed-text-v1.5"   # 768 dims, default
```

The id after `local/` is the full HuggingFace repo id (`<org>/<model>`). This is useful for development, testing, or air-gapped environments. The default model (`local/nomic-ai/nomic-embed-text-v1.5`) is pre-downloaded by `muxi-server init` into a shared cache (`$MUXI_CACHE_DIR` or `<data_dir>/cache`) and bind-mounted into formations at `/opt/hf-cache`, so the first deploy doesn't stall on a multi-hundred-MB fetch.

Common picks:
- `local/nomic-ai/nomic-embed-text-v1.5` (768 dims, 8k context, Apache-2.0) -- default
- `local/nomic-ai/nomic-embed-text-v2-moe` (768 dims, multilingual)
- `local/sentence-transformers/all-mpnet-base-v2` (768 dims)
- `local/sentence-transformers/all-MiniLM-L6-v2` (384 dims)

Any HuggingFace embedding repo works. To pin a specific revision, append `:<git-revision>` to the slug (e.g. `local/nomic-ai/nomic-embed-text-v1.5:e04b7e4...`).

> [!IMPORTANT]
> Short-name aliases like `local/all-MiniLM-L6-v2` were removed -- use the full `local/sentence-transformers/all-MiniLM-L6-v2` form, or migrate to the new default.

## Step 3: Enable Persistent Memory

Persistent memory is enabled by default with SQLite. For production, use PostgreSQL:

### SQLite (Default)

No configuration needed -- works out of the box. To customize:

```yaml
memory:
  persistent:
    connection_string: "sqlite:///data/memory.db"
```

### PostgreSQL (Multi-user)

```yaml
memory:
  persistent:
    connection_string: ${{ secrets.POSTGRES_URI }}
```

Add secret:

```bash
muxi secrets set POSTGRES_URI
# Enter: postgresql://user:pass@host:5432/muxi
```

## Step 4: Test

```bash
muxi dev
```

Test memory:

```
You: My name is Alice
Assistant: Nice to meet you, Alice!

[Restart formation]

You: What's my name?
Assistant: Your name is Alice.
```

## Full Configuration

```yaml
memory:
  buffer:
    size: 50
    multiplier: 10
    vector_search: true

  persistent:
    connection_string: ${{ secrets.POSTGRES_URI }}
```

## Multi-User Memory

Isolate memory per user (requires PostgreSQL):

```yaml
memory:
  persistent:
    connection_string: ${{ secrets.POSTGRES_URI }}
```

Pass user ID in requests:

```bash
curl -H "X-Muxi-User-Id: user_123" ...
```

## Disable Memory

For stateless interactions:

```yaml
memory:
  buffer:
    size: 0
  persistent:
    enabled: false
```

## Migrating Embedding Models

If you switch embedding models (e.g., from an API model to a local one), existing memories need re-embedding since the dimensions differ. Use the migration script:

```bash
python scripts/migrate_embeddings.py \
  --from-dim 1536 \
  --to-dim 768 \
  --to-model "local/nomic-ai/nomic-embed-text-v1.5" \
  --connection-string "postgresql://user:pass@localhost/muxi"
```

The source table is preserved -- the script creates a new table for the target dimension.

## Troubleshooting

### Memory Not Persisting

Check database connection:

```bash
psql $POSTGRES_URI -c "SELECT 1"
```

### High Memory Usage

Reduce buffer size:

```yaml
memory:
  buffer:
    size: 20
```

## Learn More

[+] [Memory Reference](../reference/memory.md) - Configuration options
[+] [Memory Concept](../concepts/memory-system.md) - Architecture overview
[+] [Deep Dive: Memory Systems](../deep-dives/memory-internals.md) - Technical internals
[+] [Multi-User Support](../deep-dives/multi-user.md) - User isolation
