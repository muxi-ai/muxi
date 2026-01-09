---
title: Async Operations
description: Background task processing for long-running agent operations
---
# Async Operations

## Handle long-running tasks without blocking

Some operations take too long for synchronous HTTP - complex research, multi-step workflows, large file processing. MUXI's async system queues these tasks, processes them in the background, and delivers results via polling or webhooks.

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

**Decision logic & retries (runtime):**
- Auto async when estimated duration/complexity exceeds threshold or when webhook is provided; override with `use_async` flag.
- Background executor retries transient failures with backoff; final status lands in `failed` if retries exhaust.
- Webhook payloads mirror the status API; include `request_id`, `status`, and `result` or `error`.

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
