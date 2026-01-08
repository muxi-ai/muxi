# Multi-User Support

User isolation and session management.

## Overview

MUXI isolates users through:

1. **User ID** - Identity header
2. **Sessions** - Conversation contexts
3. **Memory Isolation** - Per-user storage

## User Identification

Pass user ID in header:

```bash
curl -X POST http://localhost:8001/v1/chat \
  -H "X-Muxi-User-Id: user_123" \
  -d '{"message": "Hello"}'
```

Default user ID: `"0"` (single-user mode)

## Session Management

### Create Session

```bash
curl -X POST http://localhost:8001/v1/sessions \
  -H "X-Muxi-Client-Key: fmc_..."
```

Response:

```json
{
  "id": "sess_abc123",
  "user_id": "user_123",
  "created_at": "2025-01-08T10:00:00Z"
}
```

### Use Session

```bash
curl -X POST http://localhost:8001/v1/chat \
  -H "X-Muxi-Client-Key: fmc_..." \
  -d '{"message": "Hello", "session_id": "sess_abc123"}'
```

### Get Session History

```bash
curl http://localhost:8001/v1/sessions/sess_abc123 \
  -H "X-Muxi-Client-Key: fmc_..."
```

## Memory Isolation

### Configuration

```yaml
memory:
  persistent:
    enabled: true
    provider: postgresql
    connection_string: ${{ secrets.POSTGRES_URI }}
    user_isolation: true
```

### How It Works

```
User A: "Remember I like Python"
         ↓
    Stored: user_a.preferences.python = true

User B: "What language do I like?"
         ↓
    Search: user_b.preferences (empty)
    Response: "I don't know your preference yet"
```

## Database Schema

PostgreSQL with user isolation:

```sql
CREATE TABLE memories (
    id SERIAL PRIMARY KEY,
    user_id VARCHAR(255) NOT NULL,
    session_id VARCHAR(255),
    content TEXT,
    embedding VECTOR(1536),
    created_at TIMESTAMP DEFAULT NOW()
);

CREATE INDEX idx_user ON memories(user_id);
```

## Session Lifecycle

```
Create Session
      ↓
Multiple Messages (same session)
      ↓
Session Expires (configurable)
      ↓
Memory Persists (if enabled)
```

### Session Expiry

```yaml
memory:
  session_timeout: 3600  # 1 hour
```

## Multi-Tenant Architecture

For SaaS deployments:

```
Tenant A
├── User 1 → Isolated memory
├── User 2 → Isolated memory
└── User 3 → Isolated memory

Tenant B
├── User 1 → Isolated memory
└── User 2 → Isolated memory
```

Use compound user IDs:

```
X-Muxi-User-Id: tenant_a:user_1
```

## Security

- Users cannot access other users' data
- Sessions bound to user ID
- No cross-user memory leakage
- API keys control access level

## SDK Usage

```python
# Create session for user
session = formation.create_session(user_id="user_123")

# Chat with user context
response = formation.chat(
    "Hello",
    session_id=session.id,
    user_id="user_123"
)
```
