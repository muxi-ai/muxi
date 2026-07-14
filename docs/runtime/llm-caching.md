---
title: LLM Caching
description: Configure the runtime response cache
---
# LLM Caching

MUXI initializes OneLLM's in-process response cache when a formation starts.
The cache can reuse exact or semantically similar LLM responses, depending on
the runtime environment and configuration.

## Configuration

Configure caching under `llm.settings.caching` in `formation.afs`:

```yaml
llm:
  models:
    - text: openai/gpt-4o-mini
  settings:
    caching:
      enabled: true
      max_entries: 10000
      p: 0.98
      hash_only: false
      ttl: 86400
      stream_chunk_strategy: sentences
      stream_chunk_length: 1
```

| Field | Default | Description |
|-------|---------|-------------|
| `enabled` | `true` | Enables response caching |
| `max_entries` | `10000` | Maximum number of cached responses |
| `p` | `0.98` | Similarity threshold used by the semantic cache |
| `hash_only` | `false` | Uses exact request hashes instead of semantic matching |
| `ttl` | `86400` | Cache entry lifetime in seconds |
| `stream_chunk_strategy` | `sentences` | Chunking strategy for streamed responses |
| `stream_chunk_length` | `1` | Number of strategy units per cached stream chunk |

Disable caching explicitly when responses must always be regenerated:

```yaml
llm:
  models:
    - text: openai/gpt-4o-mini
  settings:
    caching:
      enabled: false
```

## Semantic and Hash-Only Modes

With `hash_only: false`, OneLLM attempts to initialize semantic matching. If
semantic-cache model support is unavailable in the runtime environment, it
falls back to hash-only matching. Set `hash_only: true` when only identical
requests should share a cached response.

A higher `p` requires closer semantic similarity. Use a conservative threshold
for responses that depend heavily on wording or context.

## Scope and Lifecycle

The cache is local to the runtime process. It is initialized once during
formation startup, is not shared across runtime replicas, and is cleared when
the process exits. Restart the runtime after changing cache settings.

Cache effectiveness depends on workload repetition, prompt context, selected
model, tools, and personalization. Measure behavior in your deployment rather
than assuming a fixed hit rate or cost reduction.

## Operational Guidance

- Disable caching for highly personalized, time-sensitive, or nondeterministic
  responses when reuse would be unsafe.
- Prefer hash-only mode when semantic equivalence is too broad for the domain.
- Bound `max_entries` and `ttl` according to available process memory and data
  freshness requirements.
- Keep user and session context in mind: cache keys include the LLM request
  content, so context changes generally produce different entries.
