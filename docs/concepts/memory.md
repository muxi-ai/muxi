---
title: Memory System
description: How MUXI remembers context across conversations and sessions
---

# Memory System

MUXI's three-tier memory handles everything from immediate conversation context to long-term user knowledge. Automatic tiering, intelligent caching, and semantic search - built in.

---

## The Three Tiers

```
┌──────────────────────────────────────────┐
│              Buffer Memory               │
│    Recent messages (fast, in-memory)     │
│              ~50 messages                │
└───────────────────┬──────────────────────┘
                    ↓ overflow
┌──────────────────────────────────────────┐
│              Working Memory              │
│     Tool outputs, intermediate state     │
│              Vector-indexed              │
└───────────────────┬──────────────────────┘
                    ↓ important info
┌──────────────────────────────────────────┐
│            Persistent Memory             │
│    Long-term storage (SQLite/Postgres)   │
│            Survives restarts             │
└──────────────────────────────────────────┘
```

Each tier serves a purpose:

| Tier | Speed | Capacity | Survives Restart |
|------|-------|----------|------------------|
| Buffer | Fastest | ~50 msgs | No |
| Working | Fast | Configurable | No |
| Persistent | Slower | Unlimited | Yes |

---

## How It Works

### Automatic Flow

1. **New message arrives** → stored in buffer
2. **Buffer fills up** → older messages summarized, moved to working memory
3. **Important information detected** → promoted to persistent storage
4. **Query arrives** → all three tiers searched for relevant context

You don't manage this manually - MUXI handles tiering automatically.

### Semantic Search

When an agent needs context, MUXI doesn't just look at recent messages:

```
User: "What's my API key?"
      ↓
MUXI searches:
  - Buffer: recent conversation
  - Working: tool outputs, cached results
  - Persistent: "You mentioned API key xyz123 on Jan 5"
      ↓
Agent: "Your API key is xyz123, which you shared on January 5th."
```

Vector embeddings enable semantic similarity, not just keyword matching.

---

## User Synopsis Caching

For each user, MUXI maintains an LLM-synthesized profile:

```
User Synopsis (cached):
- Prefers Python over JavaScript
- Works at Acme Corp
- Timezone: PST
- Communication style: Direct, technical
- Recent projects: ML pipeline, API integration
```

This synopsis:
- **Updates automatically** as new information emerges
- **Two-tier caching**: identity (long-term) + context (refreshed regularly)
- **Reduces tokens by 80%+** vs. including full history every time

---

## Context Management

### Intelligent Prioritization

Not all context is equal. MUXI scores relevance:

```
High priority (always included):
- User preferences and identity
- Current session context
- Explicitly remembered facts

Medium priority (included if space):
- Related past conversations
- Relevant tool outputs

Low priority (summarized or dropped):
- Old, unrelated messages
- Redundant information
```

### FIFO Cleanup

When memory limits are reached:

```yaml
memory:
  working:
    max_memory_mb: 10
    fifo_interval_min: 5
```

Oldest, least relevant items are removed first. Important information is preserved.

---

## Multi-User Isolation

Each user gets completely isolated memory:

```
User A: "My password is secret123"
        ↓ stored under user_a

User B: "What's my password?"
        ↓ searches user_b namespace
        → "I don't have that information"
```

This happens automatically with `user_isolation: true` - no data leaks between users.

---

## Why This Matters

| Without MUXI | With MUXI |
|--------------|-----------|
| Manually manage context windows | Automatic tiering |
| Forget after session ends | Persistent memory |
| Same context for everyone | Per-user isolation |
| Full history = high token costs | Synopsis caching saves 80%+ |
| Keyword search only | Semantic similarity |

The result: **agents that remember**, not stateless chatbots.

---

## Quick Setup

```yaml
memory:
  buffer:
    size: 50
    vector_search: true
  persistent:
    enabled: true
    provider: sqlite
```

That's it. Three-tier memory, semantic search, automatic management.

---

## Learn More

- [Configure memory](../reference/memory.md) - YAML syntax
- [Add Memory Guide](../guides/add-memory.md) - Step-by-step tutorial
- [Memory Internals](../deep-dives/memory.md) - Technical deep dive
