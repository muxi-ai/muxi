# Formation API Reference

The Formation API is exposed by each running formation. Access directly (`http://localhost:8001/v1/`) or through the server proxy (`http://server:7890/api/{formation_id}/v1/`).

## Authentication

Two API keys (auto-generated if not set in `formation.afs`):

| Key | Header | Purpose |
|-----|--------|---------|
| Admin | `X-MUXI-ADMIN-KEY` | Formation management (agents, secrets, config, logs) |
| Client | `X-MUXI-CLIENT-KEY` | User interactions (chat, sessions, events) |

Client endpoints also require `X-Muxi-User-ID` header to identify the user.

## Response Envelope

All responses follow this format:

```json
{
  "object": "chat_response",
  "timestamp": 1706616000000,
  "type": "chat.completed",
  "request": {
    "id": "req_abc123",
    "idempotency_key": null
  },
  "success": true,
  "error": null,
  "data": { ... }
}
```

## Public Endpoints (no auth)

| Method | Path | Description |
|--------|------|-------------|
| GET | `/` | Service info |
| GET | `/v1` | API version |
| GET | `/health` | Health check |

## Admin Endpoints (Admin Key)

| Method | Path | Description |
|--------|------|-------------|
| GET | `/v1/status` | Formation status and stats |
| GET | `/v1/config` | Full formation configuration |
| GET | `/v1/formation` | Basic formation info |
| GET | `/v1/overlord` | Overlord configuration |
| GET | `/v1/overlord/persona` | Overlord persona |
| GET | `/v1/agents` | List all agents |
| GET | `/v1/agents/{id}` | Get specific agent |
| GET | `/v1/secrets` | List secrets (masked values) |
| POST | `/v1/secrets` | Create secret (`{"key": "NAME", "value": "..."}`) |
| PUT | `/v1/secrets/{key}` | Update secret |
| DELETE | `/v1/secrets/{key}` | Delete secret |
| GET | `/v1/llm/settings` | LLM configuration |
| GET | `/v1/memory` | Memory configuration |
| GET | `/v1/async` | Async configuration |
| GET | `/v1/scheduler` | Scheduler configuration |
| GET | `/v1/a2a` | A2A configuration |
| GET | `/v1/logging` | Logging configuration |
| GET | `/v1/mcp/servers` | List MCP servers |
| GET | `/v1/mcp/servers/{id}` | Get MCP server details |
| GET | `/v1/logs` | Stream logs (SSE) |
| GET | `/v1/audit` | Audit log |

## Client Endpoints (Client Key + User ID)

### Chat

```
POST /v1/chat
```

**Request:**
```json
{
  "message": "What's the weather?",
  "group_id": "analyst",
  "session_id": "sess_123",
  "webhook_url": "https://myapp.com/callback",
  "files": [{
    "filename": "doc.pdf",
    "content": "base64...",
    "content_type": "application/pdf",
    "size": 12345
  }]
}
```

**Response (sync):**
```json
{
  "data": {
    "status": "completed",
    "processing_mode": "sync",
    "response": [
      { "type": "text", "text": "The weather is sunny..." }
    ],
    "usage": {
      "total_tokens": 150,
      "prompt_tokens": 50,
      "completion_tokens": 100
    }
  }
}
```

**Response (async, 202):**
```json
{
  "data": {
    "request_id": "job_456",
    "mode": "async",
    "estimated_seconds": 120
  }
}
```

**Streaming:** Set `Accept: text/event-stream` header. SSE events:
```
data: {"token": "Hello"}
data: {"token": " there!"}
event: done
data: {"finished": true}
```

### Audio Chat

```
POST /v1/audiochat
```

Send audio files for transcription + conversational response. Designed for voice notes (WhatsApp, Telegram). Only accepts `audio/*` content types.

### Events (SSE)

| Method | Path | Description |
|--------|------|-------------|
| GET | `/v1/events` | All user events (SSE stream) |
| GET | `/v1/events/{session_id}` | Session events |
| GET | `/v1/events/{session_id}/{request_id}` | Request events |

Event types: `stream_open`, `token`, `stream_completed`, `error`, `chat.started`, `agent.selected`, `chat.completed`.

### Sessions

| Method | Path | Description |
|--------|------|-------------|
| GET | `/v1/sessions` | List sessions |
| GET | `/v1/sessions/{id}` | Get session details |
| GET | `/v1/sessions/{id}/messages` | Get session messages |

### Requests

| Method | Path | Description |
|--------|------|-------------|
| GET | `/v1/requests` | List requests |
| GET | `/v1/requests/{id}` | Get request status |
| DELETE | `/v1/requests/{id}` | Cancel in-progress request |

### SOPs

| Method | Path | Description |
|--------|------|-------------|
| GET | `/v1/sops` | List SOPs |
| GET | `/v1/sops/{name}` | Get SOP details |

### Triggers

| Method | Path | Description |
|--------|------|-------------|
| GET | `/v1/triggers` | List triggers |
| GET | `/v1/triggers/{name}` | Get trigger details |
| POST | `/v1/triggers/{name}` | Execute trigger |

### Memory

| Method | Path | Description |
|--------|------|-------------|
| GET | `/v1/memory` | Memory configuration |
| GET | `/v1/memory/users/{user_id}` | User memories |
| POST | `/v1/memory/users/{user_id}` | Add memory |
| DELETE | `/v1/memory/users/{user_id}/{id}` | Delete memory |
| GET | `/v1/memory/buffer/{user_id}` | User buffer |
| DELETE | `/v1/memory/buffer/{user_id}` | Clear buffer |

### Scheduler

| Method | Path | Description |
|--------|------|-------------|
| GET | `/v1/scheduler/jobs` | List scheduled jobs |
| POST | `/v1/scheduler/jobs` | Create job |
| GET | `/v1/scheduler/jobs/{id}` | Get job details |
| DELETE | `/v1/scheduler/jobs/{id}` | Delete job |
