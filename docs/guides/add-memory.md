---
title: Add Memory
description: Make your agents remember conversations
---
# Add Memory

## Give agents memory that persists

Enable memory so agents remember what users told them - within a conversation and across sessions. This guide adds persistent memory to your formation.

## Overview

MUXI memory tiers:

1. **Buffer** - Recent messages (in-memory)
2. **Vector Search** - Semantic similarity
3. **Persistent** - Long-term storage (database)

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

## Step 3: Enable Persistent Memory

Save across sessions:

### SQLite (Simple)

```yaml
memory:
  persistent:
    enabled: true
    provider: sqlite
```

### PostgreSQL (Multi-user)

```yaml
memory:
  persistent:
    enabled: true
    provider: postgresql
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
    enabled: true
    provider: postgresql
    connection_string: ${{ secrets.POSTGRES_URI }}
    user_isolation: true
```

## Multi-User Memory

Isolate memory per user:

```yaml
memory:
  persistent:
    enabled: true
    provider: postgresql
    user_isolation: true
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
[+] [Memory Concept](../concepts/memory.md) - Architecture overview
[+] [Deep Dive: Memory Systems](../deep-dives/memory.md) - Technical internals
[+] [Multi-User Support](../deep-dives/multi-user.md) - User isolation
