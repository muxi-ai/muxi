---
title: Session Restore
description: Restore conversation history from external storage
---
# Session Restore

## Restore conversation history from external storage

Build ChatGPT-like chat applications where users can return to previous conversations. The Session Restore API lets you hydrate conversation history from your own database.


## Why Session Restore?

**MUXI's buffer memory is ephemeral:**
- Messages lost on runtime restart
- Old messages roll off (buffer size limits)
- No built-in persistence to disk

**For chat applications, you need:**
- Users return to conversations days/weeks later
- Full conversation context restored
- Your database controlling retention

**Session Restore solves this:**
```
1. User chats → Store messages in your DB
2. User leaves → Buffer clears
3. User returns → Restore from your DB
4. User continues → Full context available
```

---

## Quick Start

[[tabs]]

[[tab Python]]
```python
from muxi import Muxi

client = Muxi()

# Restore a previous conversation
client.sessions.restore(
    session_id="conv_abc123",
    user_id="user@example.com",
    messages=[
        {
            "role": "user",
            "content": "What's the weather?",
            "timestamp": "2025-01-09T10:00:00Z"
        },
        {
            "role": "assistant",
            "content": "It's sunny and 72°F.",
            "timestamp": "2025-01-09T10:00:15Z"
        }
    ]
)

# Continue the conversation with full context
response = client.chat(
    message="What about tomorrow?",
    session_id="conv_abc123",
    user_id="user@example.com"
)
```
[[/tab]]

[[tab TypeScript]]
```typescript
import { Muxi } from '@muxi/sdk';

const client = new Muxi();

// Restore a previous conversation
await client.sessions.restore({
  sessionId: 'conv_abc123',
  userId: 'user@example.com',
  messages: [
    {
      role: 'user',
      content: "What's the weather?",
      timestamp: '2025-01-09T10:00:00Z'
    },
    {
      role: 'assistant',
      content: "It's sunny and 72°F.",
      timestamp: '2025-01-09T10:00:15Z'
    }
  ]
});

// Continue the conversation with full context
const response = await client.chat({
  message: 'What about tomorrow?',
  sessionId: 'conv_abc123',
  userId: 'user@example.com'
});
```
[[/tab]]

[[tab Go]]
```go
client := muxi.NewClient()

// Restore a previous conversation
client.Sessions.Restore(ctx, &muxi.RestoreRequest{
    SessionID: "conv_abc123",
    UserID:    "user@example.com",
    Messages: []muxi.Message{
        {Role: "user", Content: "What's the weather?", Timestamp: "2025-01-09T10:00:00Z"},
        {Role: "assistant", Content: "It's sunny and 72°F.", Timestamp: "2025-01-09T10:00:15Z"},
    },
})

// Continue the conversation with full context
response, _ := client.Chat(ctx, &muxi.ChatRequest{
    Message:   "What about tomorrow?",
    SessionID: "conv_abc123",
    UserID:    "user@example.com",
})
```
[[/tab]]

[[/tabs]]

---

## Message Format

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `role` | string | Yes | `user`, `assistant`, or `system` |
| `content` | string | Yes | Message text |
| `timestamp` | ISO 8601 | Yes | Original message time |
| `agent_id` | string | No | Agent that generated response |
| `metadata` | object | No | Additional data to preserve |

---

## Common Patterns

### Auto-Restore on First Message

[[tabs]]

[[tab Python]]
```python
from muxi import Muxi

client = Muxi()

async def chat(session_id: str, message: str, user_id: str):
    # Check if we have stored history for this session
    history = await db.messages.find(session_id=session_id, user_id=user_id)
    
    if history:
        # Restore before sending message
        client.sessions.restore(
            session_id=session_id,
            user_id=user_id,
            messages=history
        )
    
    # Send message with full context
    return client.chat(message=message, session_id=session_id, user_id=user_id)
```
[[/tab]]

[[tab TypeScript]]
```typescript
import { Muxi } from '@muxi/sdk';

const client = new Muxi();

async function chat(sessionId: string, message: string, userId: string) {
  // Check if we have stored history for this session
  const history = await db.messages.find({ sessionId, userId });
  
  if (history.length > 0) {
    // Restore before sending message
    await client.sessions.restore({ sessionId, userId, messages: history });
  }
  
  // Send message with full context
  return client.chat({ message, sessionId, userId });
}
```
[[/tab]]

[[tab Go]]
```go
func chat(ctx context.Context, sessionID, message, userID string) (*muxi.Response, error) {
    // Check if we have stored history for this session
    history, _ := db.Messages.Find(ctx, sessionID, userID)
    
    if len(history) > 0 {
        // Restore before sending message
        client.Sessions.Restore(ctx, &muxi.RestoreRequest{
            SessionID: sessionID,
            UserID:    userID,
            Messages:  history,
        })
    }
    
    // Send message with full context
    return client.Chat(ctx, &muxi.ChatRequest{
        Message:   message,
        SessionID: sessionID,
        UserID:    userID,
    })
}
```
[[/tab]]

[[tab cURL]]
```bash
# Step 1: Restore session with history from your database
curl -X POST 'https://api.muxi.ai/v1/sessions/sess_abc123/restore' \
  -H 'Authorization: Bearer YOUR_API_KEY' \
  -H 'X-Muxi-User-Id: user@example.com' \
  -H 'Content-Type: application/json' \
  -d '{"messages": [...]}'

# Step 2: Send new message with full context
curl -X POST 'https://api.muxi.ai/v1/chat' \
  -H 'Authorization: Bearer YOUR_API_KEY' \
  -H 'X-Muxi-User-Id: user@example.com' \
  -H 'Content-Type: application/json' \
  -d '{"message": "Continue our conversation", "session_id": "sess_abc123"}'
```
[[/tab]]

[[/tabs]]

### Time-Windowed Restore

Only restore recent messages (e.g., last 7 days):

[[tabs]]

[[tab Python]]
```python
from datetime import datetime, timedelta

# Only restore messages from last 7 days
cutoff = datetime.utcnow() - timedelta(days=7)

messages = await db.messages.find(
    session_id=session_id,
    timestamp__gte=cutoff
)

client.sessions.restore(
    session_id=session_id,
    user_id=user_id,
    messages=messages[-50:]  # Last 50 only
)
```
[[/tab]]

[[tab TypeScript]]
```typescript
// Only restore messages from last 7 days
const cutoff = new Date(Date.now() - 7 * 24 * 60 * 60 * 1000);

const messages = await db.messages.find({
  sessionId,
  timestamp: { $gte: cutoff }
});

await client.sessions.restore({
  sessionId,
  userId,
  messages: messages.slice(-50)  // Last 50 only
});
```
[[/tab]]

[[tab Go]]
```go
// Only restore messages from last 7 days
cutoff := time.Now().AddDate(0, 0, -7)

messages, _ := db.Messages.Find(ctx, sessionID, userID, cutoff)

// Take last 50 only
if len(messages) > 50 {
    messages = messages[len(messages)-50:]
}

client.Sessions.Restore(ctx, &muxi.RestoreRequest{
    SessionID: sessionID,
    UserID:    userID,
    Messages:  messages,
})
```
[[/tab]]

[[tab cURL]]
```bash
# Restore with time-filtered messages from your database
curl -X POST 'https://api.muxi.ai/v1/sessions/sess_abc123/restore' \
  -H 'Authorization: Bearer YOUR_API_KEY' \
  -H 'X-Muxi-User-Id: user@example.com' \
  -H 'Content-Type: application/json' \
  -d '{
    "messages": [
      {"role": "user", "content": "Recent message", "timestamp": "2025-01-08T10:00:00Z"},
      {"role": "assistant", "content": "Recent reply", "timestamp": "2025-01-08T10:00:15Z"}
    ]
  }'
```
[[/tab]]

[[/tabs]]

---

## Buffer Overflow

Buffer has size limits (default: 50 messages). If you restore more:

- **Newest N messages** are kept
- **Oldest messages** are dropped
- Response shows `messages_dropped` count

```python
result = client.sessions.restore(
    session_id="sess_123",
    user_id=user_id,
    messages=all_100_messages  # Too many!
)

# Result:
# messages_loaded: 50
# messages_dropped: 50  (oldest 50 dropped)
```

**Recommendation:** Only restore last 20-50 messages for performance.

---

## Behavior

| Behavior | Description |
|----------|-------------|
| **Replaces buffer** | Existing buffer cleared, then new messages loaded |
| **Idempotent** | Same restore twice = same state |
| **Auto-sorted** | Messages sorted by timestamp automatically |
| **User-isolated** | Only restores for specified user |

---

## Best Practices

### 1. Store Messages Immediately

```python
# Store as messages flow - don't batch
response = client.chat(message, session_id, user_id)
await db.save_message(session_id, "user", message)
await db.save_message(session_id, "assistant", response.content)
```

### 2. Limit Restore Size

```python
# Good: Last 50 messages
messages = await db.messages.find({...}).limit(50)

# Bad: All 10,000 messages (slow, wasteful)
```

### 3. Handle Overflow

```python
result = client.sessions.restore(...)
if result.messages_dropped > 0:
    # Notify user: "Showing last 50 messages"
```

### 4. Include Metadata

```python
{
    "role": "assistant",
    "content": "Created issue #123",
    "timestamp": "2025-01-09T10:00:00Z",
    "agent_id": "github-agent",
    "metadata": {"issue_id": 123, "repo": "muxi-ai/muxi"}
}
```

---

## Learn More

- [Sessions Concept](../concepts/sessions.md) - How sessions work
- [Memory System](../concepts/memory-system.md) - Buffer and persistent memory
