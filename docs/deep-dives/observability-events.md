---
title: Observability Event Types
description: Reference for MUXI event streams (system + conversation)
---
# Observability Event Types

## Complete reference for system and conversation events

MUXI emits two tiers of events that you can route to your logging and monitoring infrastructure:
- **System events:** infrastructure (startup/shutdown, service init, API/server errors)
- **Conversation events:** user request lifecycle (request/agent/memory/tool/A2A, etc.)

Configure routing in `logging.system` and `logging.conversation.streams` (see `deep-dives/observability.md`). Events are JSONL.

## System events (examples)
- `system.startup`, `system.shutdown`
- `service.init`, `service.error`
- `api.request`, `api.response`
- `error.validation`, `error.runtime`

## Conversation events (examples)
- Request lifecycle: `request.received`, `request.completed`, `request.failed`
- Agent routing: `agent.selected`, `agent.response`
- Memory: `memory.read`, `memory.write`, `memory.synopsis.refresh`
- Tools/MCP: `tool.call`, `tool.success`, `tool.error`
- A2A: `a2a.request`, `a2a.response`
- Async: `async.queued`, `async.started`, `async.completed`, `async.failed`
- Triggers/Webhooks: `trigger.received`, `webhook.delivered`, `webhook.failed`
- Artifacts/Knowledge: `artifact.created`, `artifact.downloaded`, `knowledge.ingested`

## Sample event envelope

```json
{
  "id": "evt_abc123",
  "timestamp": 1706616000000,
  "level": "info",
  "event": "request.completed",
  "request": {
    "id": "req_xyz789",
    "status": "completed",
    "duration_ms": 842,
    "formation_id": "my-formation",
    "user_id": "usr_123",
    "session_id": "sess_456"
  },
  "data": {
    "agent": "researcher",
    "tokens_prompt": 1200,
    "tokens_completion": 450
  }
}
```

## Filtering & routing
- Use `logging.system.destination` for system events.
- Add multiple `logging.conversation.streams` with `events` filters (e.g., `request.*`, `agent.*`, `memory.*`).
- Common transports: `stdout`, `file`, `stream` (HTTP), `trail`.
