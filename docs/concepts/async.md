---
title: Async Processing
description: Background task execution for long-running operations
---
# Async Processing

## Let users keep working while agents handle big tasks

Some tasks take time - major research, multi-step workflows, document generation. Users shouldn't have to sit and wait. With async processing, MUXI accepts the request, works in the background, and notifies users when it's done.

## Why Async?

It's a productivity feature, not a technical workaround.

**Synchronous (user waits):**
```
User:  "Research AI trends and create comprehensive report"
         ↓
User waits... (30 seconds)
         ↓
User waits... (60 seconds)
         ↓
User waits... (2 minutes)
         ↓
Finally: "Here's your report"

User: lost 2 minutes staring at a spinner
```

**Asynchronous (user keeps working):**
```
User:  "Research AI trends and create comprehensive report"
         ↓
MUXI: "Got it. I'll notify you when it's ready."
         ↓
User continues with other work
         ↓
[MUXI works in background]
         ↓
Webhook/notification: "Your report is ready!"

User: stayed productive the whole time
```

## When MUXI Uses Async

**MUXI automatically switches to async when:**

```yaml
async:
  threshold_seconds: 30          # Default: 30 seconds
```

If estimated execution time ≥ threshold → async mode automatically.

**Typical async operations:**
- ✅ Complex research (5+ sources)
- ✅ Multi-step workflows (decomposed tasks)
- ✅ Large file processing
- ✅ External API calls (slow responses)
- ✅ Database queries (large datasets)

## How It Works

```
User Request → Overlord
         ↓
Estimate execution time:
  - Complexity score
  - Number of subtasks
  - Tool requirements
  - Historical data
         ↓
Estimated time ≥ threshold?
         ↓
    YES: Async Mode
      - Return request_id immediately
      - Execute in background
      - Notify via webhook (optional)
         ↓
    NO: Sync Mode
      - Execute immediately
      - Return result when done
```

## Execution Time Estimation

**MUXI analyzes:**

| Factor | Time Added |
|--------|------------|
| Research required | +5-15s per source |
| Complex analysis | +10-30s |
| Tool execution | +1-10s per tool |
| File generation | +5-20s per file |
| External API calls | +2-10s per call |
| Agent coordination | +1-5s per agent |

**Example:**
```
Request: "Research 3 competitors, compare features, create report"

Estimated breakdown:
- Research 3 companies: 3 × 10s = 30s
- Feature comparison: 15s
- Report generation: 10s
- Agent coordination: 5s

Total estimate: 60s ≥ 10s threshold
→ Use async mode
```

## Sync vs Async

### Synchronous (Default)
**Best for:**
- Quick questions (<10 seconds)
- Interactive conversations
- Real-time responses needed

**Example:**
```
User:  "What's the weather in SF?"
         ↓
MUXI executes immediately
         ↓
Response: "It's 68°F and sunny" (2 seconds)
```

### Asynchronous
**Best for:**
- Long-running tasks (>10 seconds)
- Background processing
- Batch operations
- Webhook triggers

**Example:**
```
User:  "Analyze entire codebase and create security report"
         ↓
MUXI: "Task started. ID: req_abc123"
         ↓
[Executes in background]
         ↓
Webhook: "Task complete! Security report ready."
```

## Configuration

### Async Modes

There are three ways to control async behavior:

```yaml
# Mode 1: Let Overlord decide (RECOMMENDED)
async:
  mode: auto                     # Default - Overlord estimates time
  threshold_seconds: 60          # Switch to async if estimated > 60s

# Mode 2: Everything synchronous (not recommended)
async:
  mode: sync                     # Always wait for completion

# Mode 3: Everything asynchronous (not recommended)
async:
  mode: async                    # Always return request_id immediately
```

**Mode 1 (auto)** is recommended because the Overlord intelligently estimates how long each request will take. Quick questions get immediate answers; complex tasks go async.

**Per-request override:** You can also pass `async: true` or `async: false` in individual requests to override the formation setting.

### Threshold Configuration

```yaml
async:
  threshold_seconds: 60          # Default: 60 seconds
```

The Overlord compares estimated execution time to this threshold:
- Below threshold → synchronous (user waits)
- Above threshold → async (immediate response with request ID)

**Adjust threshold based on UX needs:**

| Threshold | When to Use |
|-----------|-------------|
| 10-30s | Mobile apps, impatient users |
| 60s | Default, balanced experience |
| 120-300s | Desktop apps, tolerant users |

### Webhook Configuration

Set up automatic notifications when async tasks complete:

```yaml
async:
  webhook_url: "https://your-app.com/muxi-callback"  # Default webhook
  webhook_retries: 3              # Retry failed deliveries (default: 3)
  webhook_timeout: 10             # Timeout per delivery attempt in seconds (default: 10)
```

The webhook URL can also be specified per-request in the API call.

## Using Async Mode

### 1. Explicit Async Request

**API:**
```bash
curl -X POST http://localhost:8001/v1/chat \
  -H "X-Muxi-Client-Key: fmc_..." \
  -d '{
    "message": "Research AI trends",
    "async": true
  }'
```

**Response (immediate):**
```json
{
  "request_id": "req_abc123",
  "status": "processing",
  "estimated_time": "45-60 seconds"
}
```

### 2. Check Status

**Poll for completion:**
```bash
curl http://localhost:8001/v1/requests/req_abc123 \
  -H "X-Muxi-Client-Key: fmc_..."
```

**Response:**
```json
{
  "request_id": "req_abc123",
  "status": "processing",
  "progress": 60,
  "message": "Analyzing third data source..."
}
```

**When complete:**
```json
{
  "request_id": "req_abc123",
  "status": "completed",
  "result": {
    "text": "Based on my research...",
    "artifacts": ["ai_trends_report.pdf"]
  },
  "duration_ms": 47320
}
```

### 3. Webhook Notification

**Request with webhook:**
```bash
curl -X POST http://localhost:8001/v1/chat \
  -d '{
    "message": "Research AI trends",
    "async": true,
    "webhook_url": "https://your-app.com/muxi-callback"
  }'
```

**MUXI posts to your webhook when done:**
```bash
POST https://your-app.com/muxi-callback
Content-Type: application/json

{
  "request_id": "req_abc123",
  "status": "completed",
  "result": {
    "text": "Based on my research...",
    "artifacts": ["ai_trends_report.pdf"]
  }
}
```

## Request States

| State | Description |
|-------|-------------|
| `pending` | Queued, not started yet |
| `processing` | Currently executing |
| `completed` | Finished successfully |
| `failed` | Error occurred |
| `cancelled` | Cancelled by user |

## Progress Tracking

**For long-running tasks:**

```json
{
  "request_id": "req_abc123",
  "status": "processing",
  "progress": 45,
  "current_step": "Researching competitor B",
  "total_steps": 5,
  "completed_steps": 2
}
```

## SDK Examples

### Python

**Polling for async request status:**
```python
from muxi import FormationClient
import time

formation = FormationClient(
    server_url="http://localhost:7890",
    formation_id="my-assistant",
    client_key="...",
)

# Streaming chat - long operations return request_id
for event in formation.chat_stream(
    {"message": "Research AI trends"},
    user_id="user_123"
):
    if event.get("request_id"):
        request_id = event.get("request_id")
        print(f"Request ID: {request_id}")

# Poll for completion
while True:
    status = formation.get_request_status(request_id)

    if status.get("status") == "completed":
        print(status.get("result"))
        break

    print(f"Status: {status.get('status')}")
    time.sleep(2)
```

**Webhook configuration:**
Configure webhooks in your `formation.afs`:
```yaml
async:
  webhook_url: "https://your-app.com/callback"
  webhook_retries: 3
```

**Async with callback:**
```python
def on_complete(result):
    print(f"Done! {result.text}")

def on_progress(status):
    print(f"Progress: {status.progress}%")

# Start with callback
request = formation.chat_async(
    "Research AI trends",
    on_complete=on_complete,
    on_progress=on_progress
)
```

### TypeScript

```typescript
import { FormationClient } from "@muxi-ai/muxi-typescript";

const formation = new FormationClient({
  serverUrl: "http://localhost:7890",
  formationId: "my-assistant",
  clientKey: "...",
});

// Get request ID from streaming response
let requestId: string;
for await (const chunk of await formation.chatStream(
  { message: "Research AI trends" },
  "user_123"
)) {
  if (chunk.requestId) {
    requestId = chunk.requestId;
    console.log(`Request ID: ${requestId}`);
  }
}

// Poll for completion
while (true) {
  const status = await formation.getRequestStatus(requestId);

  if (status.status === "completed") {
    console.log(status.result);
    break;
  }

  console.log(`Status: ${status.status}`);
  await new Promise(resolve => setTimeout(resolve, 2000));
}

// Async with webhook
await formation.chatAsync("Research AI trends", {
  webhookUrl: "https://your-app.com/callback"
});
```

## Triggers (Default Async)

**Webhook triggers default to async:**

```bash
# GitHub webhook triggers MUXI
POST http://localhost:8001/v1/triggers/github-issue
{
  "action": "opened",
  "issue": {...}
}

# Returns immediately
{
  "request_id": "req_xyz789",
  "status": "processing"
}

# Webhook caller doesn't wait
# MUXI processes in background
```

**Force sync trigger:**
```bash
POST http://localhost:8001/v1/triggers/github-issue
{
  "data": {...},
  "use_async": false     # Wait for completion
}
```

## Timeout Configuration

Task and workflow timeouts are configured under `overlord.workflow`:

```yaml
overlord:
  workflow:
    timeouts:
      task_timeout: 300          # 5 minutes per task (default: 300)
      workflow_timeout: 1800     # 30 minutes total (default: 3600)
```

**What happens on timeout:**
```
Task running for 5 minutes...
         ↓
Timeout reached
         ↓
Task cancelled
         ↓
Status: "failed"
Error: "Task exceeded timeout (300s)"
```

## Cancelling Requests

**Cancel in-flight request:**

```bash
DELETE /v1/requests/req_abc123
```

**Response:**
```json
{
  "request_id": "req_abc123",
  "status": "cancelled",
  "message": "Request cancelled by user"
}
```

**SDK:**
```python
formation.cancel_request("req_abc123")
```

## Error Handling

### Failed Requests

```json
{
  "request_id": "req_abc123",
  "status": "failed",
  "error": {
    "type": "tool_error",
    "message": "Web search API rate limit exceeded",
    "retryable": true
  }
}
```

### Automatic Retries

Configure retry behavior in overlord workflow settings:

```yaml
overlord:
  workflow:
    retry:
      max_attempts: 3            # Default: 3
      initial_delay: 1.0         # Seconds before first retry (default: 1.0)
      max_delay: 60.0            # Max retry delay (default: 60.0)
      backoff_factor: 2.0        # Exponential backoff (default: 2.0)
```

**On failure:**
```
Task fails (API timeout)
         ↓
Wait 5 seconds
         ↓
Retry (attempt 2/3)
         ↓
Success! Continue processing
```

## Use Cases

### 1. Research & Analysis
```python
# Long research task
request = formation.chat_async(
    "Research top 10 AI companies, analyze products, create comparison report"
)
# Estimated: 2-3 minutes
```

### 2. Batch Processing
```python
# Process 100 documents
for doc in documents:
    formation.chat_async(
        f"Analyze {doc.name}",
        webhook_url=f"https://app.com/callback/{doc.id}"
    )
# All process in parallel
```

### 3. Scheduled Reports
```python
# Daily report generation
@schedule.daily("09:00")
def generate_report():
    request = formation.chat_async(
        "Generate daily analytics report",
        webhook_url="https://app.com/report-ready"
    )
```

### 4. Webhook Integrations
```python
# GitHub webhook → MUXI
@app.post("/github-webhook")
def handle_github(payload):
    request = formation.trigger_async(
        "github-issue-created",
        data=payload
    )
    return {"request_id": request.id}
```

## Best Practices

**DO:**
- ✅ Use async for tasks >10 seconds
- ✅ Implement webhooks (better than polling)
- ✅ Handle failures gracefully
- ✅ Set reasonable timeouts
- ✅ Show progress to users
- ✅ Provide cancel option

**DON'T:**
- ❌ Poll too frequently (use webhooks)
- ❌ Set timeout too low (causes failures)
- ❌ Block UI waiting for async tasks
- ❌ Ignore failure status
- ❌ Use async for quick tasks (<5s)

## Sync vs Async Decision

**Use SYNC when:**
- ✅ Task completes in <10 seconds
- ✅ User needs immediate response
- ✅ Interactive conversation
- ✅ Simple queries

**Use ASYNC when:**
- ✅ Task takes >10 seconds
- ✅ Background processing acceptable
- ✅ Batch operations
- ✅ Webhook triggers
- ✅ Resource-intensive tasks

## Monitoring

**Track async performance:**

```yaml
observability:
  track_async_tasks: true
```

**Metrics:**
- Average async task duration
- Async task success rate
- Webhook delivery success
- Queue depth

## Troubleshooting

### Task Stuck in "Processing"
```yaml
# Check timeout settings
overlord:
  workflow:
    timeouts:
      task_timeout: 600          # Increase if needed (default: 300)
      workflow_timeout: 7200     # Increase overall limit (default: 3600)
```

### Webhook Not Receiving Callbacks
```
1. Check webhook URL is accessible
2. Verify webhook endpoint accepts POST
3. Check firewall/security settings
4. Test with ngrok for local development
```

### Polling Too Slow
```
# Increase poll frequency (but don't spam)
while True:
    status = formation.get_request(request.id)
    if status.is_complete: break
    time.sleep(2)  # Poll every 2 seconds (reasonable)
```

## Learn More

- **[Agent Formation Schema](https://github.com/agent-formation/afs-spec)** - Official formation schema specification
- [Workflows & Task Decomposition](workflows.md) - Complex task execution
- [Triggers & Webhooks](triggers.md) - Webhook integrations
- [Request Cancellation](../runtime/request-cancellation.md) - Cancel in-flight requests
