# Async Operations

## Technical deep dive


Background task processing for long-running operations.

## Overview

Some operations take too long for synchronous HTTP:

- Complex research tasks
- Multi-step workflows
- Large file processing
- Webhook triggers

MUXI handles these asynchronously.

## Async Chat

Request:

```bash
curl -X POST http://localhost:8001/v1/chat \
  -H "X-Muxi-Client-Key: fmc_..." \
  -d '{"message": "Research AI trends", "async": true}'
```

Response (immediate):

```json
{
  "request_id": "req_abc123",
  "status": "processing"
}
```

## Check Status

```bash
curl http://localhost:8001/v1/requests/req_abc123 \
  -H "X-Muxi-Client-Key: fmc_..."
```

Response:

```json
{
  "request_id": "req_abc123",
  "status": "completed",
  "result": {
    "text": "Based on my research...",
    "agent": "researcher"
  }
}
```

## Request States

| State | Description |
|-------|-------------|
| `pending` | Queued, not started |
| `processing` | Currently executing |
| `completed` | Finished successfully |
| `failed` | Error occurred |
| `cancelled` | Cancelled by user |

## Triggers (Default Async)

Triggers default to async:

```bash
curl -X POST http://localhost:8001/v1/triggers/github-issue \
  -H "X-Muxi-Client-Key: fmc_..." \
  -d '{"data": {...}}'
```

Returns immediately:

```json
{
  "request_id": "req_xyz789",
  "status": "processing"
}
```

### Sync Trigger

Force synchronous:

```bash
curl -X POST http://localhost:8001/v1/triggers/github-issue \
  -d '{"data": {...}, "use_async": false}'
```

Waits for completion.

## Webhooks (Callbacks)

Get notified when complete:

```bash
curl -X POST http://localhost:8001/v1/chat \
  -d '{
    "message": "Complex task",
    "async": true,
    "webhook_url": "https://your-app.com/callback"
  }'
```

MUXI POSTs to callback:

```json
{
  "request_id": "req_abc123",
  "status": "completed",
  "result": {...}
}
```

## Timeout Configuration

```yaml
workflow:
  task_timeout: 300  # 5 minutes per task
```

## Cancel Request

```bash
curl -X DELETE http://localhost:8001/v1/requests/req_abc123 \
  -H "X-Muxi-Client-Key: fmc_..."
```

## SDK Usage

```python
# Async request
request = formation.chat_async("Complex task")
print(request.id)  # req_abc123

# Poll for result
while True:
    status = formation.get_request(request.id)
    if status.is_complete:
        print(status.result.text)
        break
    time.sleep(1)

# Or use callback
formation.chat_async(
    "Complex task",
    webhook_url="https://..."
)
```

## Best Practices

1. **Use async for long tasks** - Don't timeout clients
2. **Implement webhooks** - Better than polling
3. **Handle failures** - Check status for errors
4. **Set reasonable timeouts** - Balance time vs resources
