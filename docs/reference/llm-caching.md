---
title: LLM Caching
description: Configuration and optimization for prompt caching
---
# LLM Caching

## Reduce costs by caching LLM responses

MUXI automatically caches LLM responses using semantic similarity matching, reducing API costs by 70%+ for repeated or similar queries.


## How It Works

### Semantic Cache

Instead of exact string matching, MUXI uses semantic similarity:

```
Query 1: "What are the benefits of cloud computing?"
Query 2: "Tell me about cloud computing advantages"
         ↓
Semantic similarity: 92%
         ↓
Cache hit! Return cached response
```

**Cost savings:** Second query costs $0 (no LLM call).

---

## Configuration

### Enable Caching

```yaml
# formation.afs
llm_cache:
  enabled: true
  max_entries: 10000           # Maximum cached responses
  similarity_threshold: 0.95   # 95% similarity required for cache hit
  ttl: 86400                   # 24 hours (in seconds)
```

### Parameters

**`max_entries`** (default: 10000)
- Maximum number of cached responses
- LRU eviction when limit reached
- Memory: ~1KB per entry

**`similarity_threshold`** (default: 0.95)
- Minimum similarity score for cache hit (0.0-1.0)
- Higher = more strict matching
- Lower = more cache hits but less accurate

**`ttl`** (default: 86400 seconds = 24 hours)
- Time-to-live for cached entries
- Expired entries automatically removed
- Set to 0 for no expiration

**`hash_only`** (default: false)
- Use exact hash matching instead of semantic similarity
- Faster but less flexible
- Only exact duplicates match

---

## Tuning Similarity Threshold

| Threshold | Behavior | Use Case |
|-----------|----------|----------|
| 0.99 | Very strict - almost exact matches only | Precise queries |
| 0.95 | Balanced (default) | General use |
| 0.90 | Lenient - similar queries match | Broad matching |
| 0.85 | Very lenient - loosely related queries | Maximum savings |

**Example with 0.95 threshold:**

```
Cached: "List three cloud computing benefits"

Matches:
✓ "What are three benefits of cloud computing" (similarity: 0.97)
✓ "Tell me three advantages of cloud" (similarity: 0.96)

Doesn't match:
✗ "Cloud computing disadvantages" (similarity: 0.75)
✗ "How much does cloud cost" (similarity: 0.60)
```

---

## Performance Metrics

### Cache Statistics

```python
from muxi.runtime.formation import Formation

formation = Formation()
await formation.load("formation.afs")

# Get cache stats
stats = formation.get_cache_stats()

print(f"Cache hits: {stats['hits']}")
print(f"Cache misses: {stats['misses']}")
print(f"Hit rate: {stats['hit_rate']:.1%}")
print(f"Total entries: {stats['entries']}")
print(f"Memory usage: {stats['memory_mb']:.2f} MB")
```

**Example output:**
```
Cache hits: 3,421
Cache misses: 1,234
Hit rate: 73.5%
Total entries: 8,765
Memory usage: 8.76 MB
```

### Monitor Performance

```yaml
# Enable cache metrics
observability:
  metrics:
    cache:
      enabled: true
      interval: 60  # Report every 60 seconds
```

---

## Cost Savings

### Example Calculation

**Scenario:** Customer support bot with 10,000 queries/day

**Without caching:**
```
10,000 queries × $0.002 per query = $20/day = $600/month
```

**With caching (70% hit rate):**
```
3,000 cache misses × $0.002 = $6/day = $180/month
Savings: $420/month (70%)
```

### When Caching Helps Most

✅ **High cache hit rate:**
- FAQ bots (users ask similar questions)
- Support bots (common issues)
- Data lookup (repeated queries)
- Summarization (same documents)

❌ **Low cache hit rate:**
- Unique creative requests
- Personalized responses
- Ever-changing data
- Random queries

---

## Memory Management

### Size Limits

```yaml
llm_cache:
  max_entries: 5000        # Limit to 5K entries
  max_memory_mb: 50        # Or 50MB (whichever comes first)
```

When limits reached:
- LRU (Least Recently Used) eviction
- Oldest unused entries removed first
- Automatic and transparent

### Memory Footprint

| Entries | Estimated Memory |
|---------|------------------|
| 1,000 | ~1 MB |
| 10,000 | ~10 MB |
| 100,000 | ~100 MB |

Formula: ~1KB per cached response (varies by response length).

---

## Streaming Mode

Cache works with streaming:

```yaml
overlord:
  response:
    streaming: true

llm_cache:
  enabled: true
```

**Behavior:**
- **Cache hit:** Entire cached response streamed instantly
- **Cache miss:** Response streamed normally, then cached

**User experience:**
```
First request: 
[2 second delay] Generating response... [streams]

Identical second request:
[instant] [streams cached response immediately]
```

---

## Advanced Configuration

### Different Strategies

```yaml
llm_cache:
  enabled: true
  
  # Strategy 1: Semantic similarity (default)
  strategy: semantic
  similarity_threshold: 0.95
  
  # Strategy 2: Exact hash matching
  # strategy: hash
  # hash_only: true
  
  # Strategy 3: Hybrid (semantic + hash)
  # strategy: hybrid
  # similarity_threshold: 0.90
```

### Per-Agent Caching

```yaml
agents:
  - id: support
    llm_cache:
      enabled: true
      similarity_threshold: 0.90  # More lenient for support
  
  - id: legal
    llm_cache:
      enabled: true
      similarity_threshold: 0.99  # Very strict for legal
  
  - id: creative
    llm_cache:
      enabled: false  # No caching for creative work
```

### Streaming Strategy

```yaml
llm_cache:
  stream_chunk_strategy: sentence  # Cache after each sentence
  # Options: token, sentence, paragraph
  stream_chunk_length: 0           # 0 = use strategy, >0 = fixed length
```

---

## Cache Invalidation

### Manual Invalidation

```python
# Clear all cache
await formation.clear_cache()

# Clear specific entry
await formation.clear_cache_entry(prompt="What is cloud computing?")

# Clear by pattern
await formation.clear_cache_pattern("cloud computing")
```

### Automatic Invalidation

```yaml
llm_cache:
  ttl: 3600  # 1 hour
  
  # Invalidate on:
  invalidate_on_agent_update: true   # Agent config changes
  invalidate_on_tool_update: true    # Tool definitions change
  invalidate_on_knowledge_update: true  # Knowledge base updates
```

---

## Debugging

### Cache Miss Analysis

```python
# Enable debug logging
import logging
logging.getLogger('muxi.cache').setLevel(logging.DEBUG)

# Shows why cache misses occur
# "Cache miss: similarity 0.87 below threshold 0.95"
```

### Inspect Cache Entries

```python
# List cached entries
entries = await formation.list_cache_entries(limit=10)

for entry in entries:
    print(f"Prompt: {entry.prompt[:50]}...")
    print(f"Similarity: {entry.last_similarity}")
    print(f"Hits: {entry.hit_count}")
    print(f"Age: {entry.age_seconds}s")
    print()
```

---

## Best Practices

### 1. Start with Defaults

```yaml
llm_cache:
  enabled: true
  # Use defaults initially
```

Monitor hit rate, then tune.

### 2. Monitor Hit Rate

Aim for >50% hit rate:
- <30%: Increase threshold or disable caching
- 30-50%: Consider tuning threshold
- 50-70%: Good performance
- >70%: Excellent caching

### 3. Adjust by Use Case

```yaml
# FAQ/Support: Lenient
llm_cache:
  similarity_threshold: 0.90

# Data extraction: Strict  
llm_cache:
  similarity_threshold: 0.98

# Creative: Disabled
llm_cache:
  enabled: false
```

### 4. Set Appropriate TTL

```yaml
# Fast-changing data: Short TTL
llm_cache:
  ttl: 3600  # 1 hour

# Stable data: Long TTL
llm_cache:
  ttl: 604800  # 1 week

# Permanent: No expiration
llm_cache:
  ttl: 0
```

---

## Limitations

### What Gets Cached

✅ **Cached:**
- LLM text responses
- Tool-augmented responses
- Multi-turn conversation results

❌ **Not cached:**
- Tool execution results (tools always run)
- Real-time data queries
- User-specific data (unless explicitly enabled)

### Cache Key

Cache key includes:
- User message
- System prompt
- Model name
- Temperature setting

**Changes to any = cache miss.**

---

## Security

### User Isolation

```yaml
llm_cache:
  enabled: true
  user_isolation: true  # Cache per user (default: false)
```

**`user_isolation: false` (default):**
- All users share cache
- More cache hits
- Less privacy

**`user_isolation: true`:**
- Each user has separate cache
- Better privacy
- Fewer cache hits

### Sensitive Data

**Cache excludes:**
- User credentials
- API keys
- Passwords
- Secrets

**Automatically filtered from cache.**

---

## Monitoring

### Prometheus Metrics

```yaml
observability:
  prometheus:
    enabled: true
```

**Metrics exported:**
- `muxi_cache_hits_total`
- `muxi_cache_misses_total`
- `muxi_cache_hit_rate`
- `muxi_cache_entries`
- `muxi_cache_memory_bytes`

### Logs

```yaml
logging:
  cache:
    enabled: true
    level: info
```

**Log events:**
- Cache hits (with similarity score)
- Cache misses (with reason)
- Evictions (when full)
- Invalidations

---

## Troubleshooting

### Low Hit Rate

**Problem:** Hit rate <30%

**Solutions:**
1. Lower similarity threshold
2. Check query diversity (unique queries don't cache well)
3. Increase max_entries (might be evicting too fast)
4. Consider disabling for this use case

### High Memory Usage

**Problem:** Cache using too much memory

**Solutions:**
1. Reduce max_entries
2. Set max_memory_mb limit
3. Reduce TTL (expire faster)
4. Use hash_only mode (smaller cache entries)

### Cache Not Working

**Problem:** 0% hit rate

**Checks:**
1. Is caching enabled? (`llm_cache.enabled: true`)
2. Are queries actually similar?
3. Is threshold too high?
4. Check debug logs for reasons

---

## Learn More

- [Memory System](../concepts/memory.md) - Other caching systems
- [Deep Dive: Streaming](../deep-dives/streaming.md) - Caching with streaming
- [Observability](../deep-dives/observability.md) - Monitoring cache performance
