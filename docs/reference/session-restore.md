---
title: Session Restore
description: Restore conversation history from external storage
---
# Session Restore

## Restore conversation history from external storage

Build ChatGPT-like chat applications where users can return to previous conversations. The Session Restore API lets you hydrate conversation history from your own database.


## Why Session Restore?

**MUXI's buffer memory is ephemeral:**
- Messages are lost on runtime restart
- Old messages roll off (FIFO with buffer size limits)
- No built-in persistence to disk

**For chat applications, you need:**
- Users return to previous conversations days/weeks later
- Full conversation context restored
- Your own database controlling retention policies

**Session Restore solves this:**
```
1. User chats → Store messages in your DB
2. User leaves → Buffer clears
3. User returns → Restore from your DB
4. User continues → Full context available
```

---

## API Reference

### Endpoint

```http
POST /sessions/{session_id}/restore
```

### Headers

```http
X-Muxi-User-ID: user@example.com
Content-Type: application/json
```

### Request Body

```json
{
  "messages": [
    {
      "role": "user",
      "content": "What's the weather like?",
      "timestamp": "2025-10-23T10:00:00Z"
    },
    {
      "role": "assistant", 
      "content": "The weather today is sunny with a high of 72F.",
      "timestamp": "2025-10-23T10:00:15Z",
      "agent_id": "weather-assistant"
    },
    {
      "role": "user",
      "content": "Thanks! What about tomorrow?",
      "timestamp": "2025-10-23T10:01:00Z"
    }
  ]
}
```

**Message fields:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `role` | string | Yes | `user`, `assistant`, or `system` |
| `content` | string | Yes | Message text |
| `timestamp` | ISO 8601 | Yes | Original message time |
| `agent_id` | string | No | Agent that generated response |
| `metadata` | object | No | Additional data to preserve |

### Response

```json
{
  "object": "session",
  "type": "session.restored",
  "success": true,
  "data": {
    "session_id": "sess_abc123",
    "messages_loaded": 3,
    "messages_dropped": 0,
    "message": "Session restored successfully"
  }
}
```

**Response fields:**

| Field | Description |
|-------|-------------|
| `messages_loaded` | Number of messages added to buffer |
| `messages_dropped` | Messages that didn't fit (buffer overflow) |
| `session_id` | The restored session ID |

---

## How It Works

### 1. Store Messages

Store messages as they flow through your app:

```python
# Via webhook
@app.post("/muxi-webhook")
async def handle_muxi_webhook(payload: dict):
    message = payload["message"]
    
    # Store in your database
    await db.messages.insert({
        "session_id": payload["session_id"],
        "user_id": payload["user_id"],
        "role": payload["role"],
        "content": message,
        "timestamp": payload["timestamp"],
        "agent_id": payload.get("agent_id")
    })
```

Or poll for new messages:

```python
# Poll formation API
messages = await formation.get_messages(session_id)
for msg in messages:
    await db.messages.insert(msg)
```

### 2. Restore When User Returns

When user opens an old conversation:

```python
from muxi import FormationClient

# User selects conversation from history
conversation_id = "conv_xyz789"

# Fetch messages from your database
messages = await db.messages.find({
    "user_id": user_id,
    "conversation_id": conversation_id
}).sort("timestamp")

# Initialize client
formation = FormationClient(
    server_url="http://localhost:7890",
    formation_id="my-assistant",
    client_key="...",
)

# Restore session
formation.restore_session(
    session_id=conversation_id,
    user_id=user_id,
    messages=[
        {
            "role": msg["role"],
            "content": msg["content"],
            "timestamp": msg["timestamp"].isoformat()
        }
        for msg in messages
    ]
)

# Continue chatting with full context
for event in formation.chat_stream(
    {"message": "Continue from where we left off", "session_id": conversation_id},
    user_id=user_id
):
    if event.get("type") == "text":
        print(event.get("text"), end="")
```

---

## Buffer Overflow

**Buffer has size limits (default: 50 messages).**

If you restore more messages than buffer size:
- Newest N messages are kept
- Oldest messages are dropped
- Response shows `messages_dropped` count

```python
# Restore 100 messages, buffer size is 50
result = await formation.restore_session(
    session_id="sess_123",
    user_id=user_id,
    messages=all_100_messages  # Too many!
)

# Response
{
  "messages_loaded": 50,      # Only 50 fit
  "messages_dropped": 50      # 50 oldest dropped
}
```

**Recommendation:** Only restore recent messages (last 20-50) for performance.

---

## Common Patterns

### Pattern 1: Auto-Restore on First Message

```python
async def chat(session_id: str, message: str, user_id: str):
    # Check if this is the first message in a resumed session
    session_exists = await formation.session_exists(session_id)
    
    if not session_exists:
        # New session - check if we have history for it
        history = await db.messages.find({
            "session_id": session_id,
            "user_id": user_id
        })
        
        if history:
            # Restore before first message
            await formation.restore_session(
                session_id=session_id,
                user_id=user_id,
                messages=history
            )
    
    # Now send the message
    return await formation.chat(message, session_id, user_id)
```

### Pattern 2: Lazy Restore

```python
# Only restore when user explicitly opens conversation
@app.get("/conversations/{conversation_id}/open")
async def open_conversation(conversation_id: str, user_id: str):
    # Fetch history
    messages = await db.messages.find({
        "conversation_id": conversation_id,
        "user_id": user_id
    }).limit(50)  # Only last 50
    
    # Restore
    await formation.restore_session(
        session_id=conversation_id,
        user_id=user_id,
        messages=messages
    )
    
    return {"status": "ready", "message_count": len(messages)}
```

### Pattern 3: Time-Windowed Restore

```python
from datetime import datetime, timedelta

# Only restore recent messages (last 7 days)
cutoff = datetime.utcnow() - timedelta(days=7)

messages = await db.messages.find({
    "session_id": session_id,
    "user_id": user_id,
    "timestamp": {"$gte": cutoff}
})

await formation.restore_session(
    session_id=session_id,
    user_id=user_id,
    messages=messages
)
```

---

## Behavior

### Replaces Existing Buffer

Restoring **clears existing buffer first**, then loads new messages:

```python
# Session has: ["Hello", "Hi there"]
await formation.restore_session(
    session_id="sess_123",
    messages=[
        {"role": "user", "content": "What's up?", "timestamp": "..."}
    ]
)
# Buffer now has: ["What's up?"]
# Previous messages are gone
```

### Idempotent

Restoring same messages multiple times results in same state:

```python
# First restore
await formation.restore_session(session_id, messages)

# Second restore (same messages)
await formation.restore_session(session_id, messages)

# Buffer state is identical
```

### Sorted by Timestamp

Messages are automatically sorted by timestamp:

```python
# Send out of order
messages = [
    {"role": "assistant", "content": "Answer", "timestamp": "2025-01-09T10:02:00Z"},
    {"role": "user", "content": "Question", "timestamp": "2025-01-09T10:01:00Z"}
]

await formation.restore_session(session_id, messages)

# Buffer stores in correct order:
# 1. "Question" (10:01)
# 2. "Answer" (10:02)
```

---

## Error Handling

### 400 Bad Request

```json
{
  "error": {
    "type": "invalid_request",
    "message": "Invalid timestamp format"
  }
}
```

**Common causes:**
- Missing required fields (`role`, `content`, `timestamp`)
- Invalid timestamp format (not ISO 8601)
- Empty `content` field
- Invalid `role` (not `user`/`assistant`/`system`)

### 401 Unauthorized

```json
{
  "error": {
    "type": "authentication_error",
    "message": "Invalid API key"
  }
}
```

Missing or invalid API key.

### 404 Not Found

```json
{
  "error": {
    "type": "resource_not_found",
    "message": "Session not found"
  }
}
```

Session ID doesn't exist. You can restore to a new session ID - it will be created.

---

## SDK Examples

### Python

```python
from muxi import FormationClient

formation = FormationClient(
    server_url="http://localhost:7890",
    formation_id="my-assistant",
    client_key="...",
)

# Restore session
result = formation.restore_session(
    session_id="sess_abc123",
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

print(f"Loaded {result.get('messages_loaded')} messages")

# Continue chatting
for event in formation.chat_stream(
    {"message": "Thanks! What about tomorrow?", "session_id": "sess_abc123"},
    user_id="user@example.com"
):
    if event.get("type") == "text":
        print(event.get("text"), end="")
```

### TypeScript

```typescript
import { Formation } from '@muxi/sdk';

const formation = new Formation({ apiKey: 'muxi_...' });

// Restore session
const result = await formation.restoreSession({
  sessionId: 'sess_abc123',
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

console.log(`Loaded ${result.messagesLoaded} messages`);

// Continue chatting
const response = await formation.chat({
  message: 'Thanks! What about tomorrow?',
  sessionId: 'sess_abc123',
  userId: 'user@example.com'
});
```

### cURL

```bash
curl -X POST https://api.muxi.ai/v1/formations/my-formation/sessions/sess_abc123/restore \
  -H "Authorization: Bearer muxi_..." \
  -H "X-Muxi-User-ID: user@example.com" \
  -H "Content-Type: application/json" \
  -d '{
    "messages": [
      {
        "role": "user",
        "content": "What is the weather?",
        "timestamp": "2025-01-09T10:00:00Z"
      },
      {
        "role": "assistant",
        "content": "It is sunny and 72°F.",
        "timestamp": "2025-01-09T10:00:15Z"
      }
    ]
  }'
```

---

## Best Practices

### 1. Store Messages Immediately

Don't wait to batch - store as messages flow:

```python
# ✅ Good: Store immediately
@app.post("/chat")
async def chat(message: str):
    response = await formation.chat(message, session_id, user_id)
    
    # Store immediately
    await db.save_message(user_id, session_id, "user", message)
    await db.save_message(user_id, session_id, "assistant", response.content)
    
    return response

# ❌ Bad: Batch later (risk of data loss)
```

### 2. Limit Restore Size

Only restore what's needed:

```python
# ✅ Good: Last 20-50 messages
messages = await db.messages.find({...}).limit(50)

# ❌ Bad: All 10,000 messages
messages = await db.messages.find({...})  # Slow! Wasteful!
```

### 3. Handle Overflow Gracefully

```python
result = await formation.restore_session(...)

if result.messages_dropped > 0:
    logger.warning(f"Dropped {result.messages_dropped} oldest messages")
    # Maybe notify user: "Showing last 50 messages"
```

### 4. Validate Timestamps

```python
from datetime import datetime

# ✅ Good: Validate format
try:
    dt = datetime.fromisoformat(msg["timestamp"])
except ValueError:
    # Fix or skip invalid message
    pass

# ❌ Bad: Trust all timestamps
```

### 5. Include Metadata

Preserve useful context:

```python
{
    "role": "assistant",
    "content": "Created issue #123",
    "timestamp": "2025-01-09T10:00:00Z",
    "agent_id": "github-agent",
    "metadata": {
        "issue_id": 123,
        "repository": "muxi-ai/muxi",
        "tool_used": "github_create_issue"
    }
}
```

---

## Security

**User Isolation:**
- Messages only restored for specified `X-Muxi-User-ID`
- No cross-user access possible

**API Key Required:**
- All restore requests require valid formation API key

**Data Validation:**
- Timestamps validated (ISO 8601)
- Required fields enforced
- Invalid data rejected with 400 error

---

## Learn More

- [Memory System](../concepts/memory.md) - How buffer memory works
- [Session Management](../guides/sessions.md) - Managing user sessions
- [Memory Configuration](memory.md) - Buffer size and persistence settings
- [Webhooks](../guides/webhooks.md) - Receive messages via webhooks
