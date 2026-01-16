---
title: Sessions & Conversations
description: How MUXI organizes conversations and recalls past interactions
---
# Sessions & Conversations

## How MUXI organizes conversations and recalls past interactions

A session is a single conversation thread. Users can have multiple sessions (like chat windows), and MUXI can recall information from past sessions when relevant.

## What Is a Session?

A session is one continuous conversation:

```
Session 1 (Monday):
  User:  "Help me plan a trip to Japan"
  MUXI: "I'd love to help! When are you thinking of going?"
  User:  "March, for 2 weeks"
  MUXI: "Great! Cherry blossom season..."

Session 2 (Wednesday):
  User:  "What's the weather in Tokyo?"
  MUXI: "Currently 15°C and sunny..."

Session 3 (Friday):
  User:  "Following up on our trip planning from Monday..."
  MUXI: "Of course! For your 2-week Japan trip in March..."
  ↑ MUXI recalls info from Session 1
```

Each session has its own ID and message history, but memory spans across sessions.

---

## Cross-Session Memory Recall

> **Key feature:** Working memory can access information from other sessions.

When a user says "remember our chat about X" or "following up on...", MUXI searches across all of that user's sessions:

```
User:  "What did we discuss about the budget last week?"
         ↓
MUXI searches working memory across sessions
         ↓
Finds relevant context from Session 5 (last Tuesday)
         ↓
MUXI: "Last week you mentioned a $5,000 budget for the project..."
```

This happens automatically - users don't need to specify which session.

---

## How Sessions Work

### Session Lifecycle

```
User starts chat → Session created (sess_abc123)
         ↓
Messages exchanged
         ↓
Buffer stores recent messages
         ↓
Important facts → Working memory (searchable)
         ↓
User closes chat → Session ends (buffer cleared)
         ↓
Working memory persists for future recall
```

### What's Stored Where

| Storage | Scope | Survives Session End? |
|---------|-------|----------------------|
| **Buffer** | Current session messages | No - cleared when session ends |
| **Working Memory** | Facts, context, tool outputs | Yes - persists for recall |
| **Persistent Memory** | Long-term user knowledge | Yes - survives restarts |
| **User Synopsis** | Who the user is | Yes - always available |

---

## Session IDs

Sessions are identified by unique IDs:

```
sess_V1StGXR8_Z5jdHi6B
```

SDKs track sessions automatically, or you can manage them explicitly:

```python
# Let SDK manage sessions
response = formation.chat("Hello!", user_id="alice@email.com")
# SDK creates/continues session automatically

# Or manage explicitly
response = formation.chat(
    "Hello!",
    user_id="alice@email.com",
    session_id="sess_my_custom_session"
)
```

---

## Buffer vs Working Memory

### Buffer Memory (Session-Scoped)

The buffer holds recent messages for immediate context:

```yaml
memory:
  buffer:
    size: 50  # Last 50 messages
```

- Fast, in-memory
- Cleared when session ends
- Used for "what did I just say?" context

### Working Memory (Cross-Session)

Working memory stores facts that can be recalled later:

```
Session 1:
  User:  "My budget is $5,000"
  → Stored in working memory: "User's budget is $5,000"

Session 2 (days later):
  User:  "What was my budget again?"
  → Working memory search finds the fact
  MUXI: "Your budget is $5,000"
```

- Vector-indexed (FAISSx) for semantic search
- Persists across sessions
- Enables "remember when we discussed..." queries

---

## Multi-Session Users

Users can have many active sessions:

```
Alice:
  ├── sess_web_chat (Web UI)
  ├── sess_slack_123 (Slack bot)
  └── sess_mobile_app (Mobile app)
```

All sessions share:
- User synopsis (who Alice is)
- Working memory (facts about Alice)
- Persistent memory (long-term knowledge)

Each session has its own:
- Buffer (recent messages in that conversation)
- Context (current topic being discussed)

---

## Session Restore

For persistent chat history (like ChatGPT's sidebar), developers can restore sessions from external storage.

### Why Session Restore?

MUXI's buffer is ephemeral:
- Lost on runtime restart
- Old messages roll off (FIFO with size limits)
- Not automatically persisted

If you need persistent conversation history:
1. Store messages in your database (via webhooks or polling)
2. When user returns, restore the session

### How It Works

```
1. User chats → You persist messages to your DB
2. User leaves, runtime restarts, buffer clears
3. User returns
4. You fetch messages from your DB
5. Call POST /sessions/{id}/restore
6. Buffer hydrated with conversation history
7. User continues chatting with full context
```

### API

```bash
POST /sessions/{session_id}/restore
X-Muxi-User-ID: alice

{
  "messages": [
    {"role": "user", "content": "What's the weather?", "timestamp": "..."},
    {"role": "assistant", "content": "It's sunny...", "timestamp": "..."}
  ]
}
```

---

## Use Cases

### Chat Application (Multiple Windows)

```python
# User opens new chat
session1 = formation.create_session(user_id="alice")
response = formation.chat("Help with project A", session_id=session1)

# User opens another chat window
session2 = formation.create_session(user_id="alice")
response = formation.chat("Help with project B", session_id=session2)

# Both sessions can recall user's general preferences
# But have separate conversation contexts
```

### Cross-Platform Access

```python
# User chats on web
response = formation.chat(
    "My flight is March 15",
    user_id="alice@email.com",
    session_id="sess_web"
)

# Later, user asks on Slack
response = formation.chat(
    "When is my flight?",
    user_id="U12345ABC",  # Slack ID, but linked to same user
    session_id="sess_slack"
)
# MUXI: "Your flight is March 15"
# ↑ Found via cross-session memory, even different session
```

### Persistent Chat History

```python
# On user message, save to your DB
@webhook.on("message.created")
def save_message(event):
    db.save_message(
        session_id=event.session_id,
        role=event.role,
        content=event.content
    )

# When user returns, restore session
def on_user_return(user_id, session_id):
    messages = db.get_messages(session_id)
    formation.restore_session(session_id, messages, user_id=user_id)
```

---

## Configuration

```yaml
memory:
  buffer:
    size: 50                    # Messages per session

  working:
    provider: faissx            # Vector store for cross-session search

  persistent:
    provider: postgres          # Required for multi-user
    user_synopsis:
      enabled: true             # Build user profiles
```

---

## Summary

| Concept | Scope | Purpose |
|---------|-------|---------|
| **Session** | One conversation | Organize messages into threads |
| **Buffer** | Per-session | Recent messages for immediate context |
| **Working Memory** | Cross-session | Facts that can be recalled later |
| **Session Restore** | Developer-managed | Persistent chat history |

The key insight: **Sessions organize conversations, but memory flows across them.** Users can say "remember when we discussed..." and MUXI finds the context regardless of which session it was in.

---

## Learn More

- [Memory System](memory-system.md) - Four memory layers in depth
- [Multi-Identity Users](multi-identity.md) - One user, many identifiers
- [SDK Reference](../sdks/README.md) - Session management in code
