---
title: Request Cancellation
description: API reference for cancelling in-flight requests
---
# Request Cancellation

## Stop long-running agent requests and free resources

Cancel in-flight requests to stop processing, free up resources, and prevent unnecessary LLM costs.

---

## Quick Start

```bash
# Cancel a request by ID
curl -X DELETE 'http://localhost:8001/v1/requests/{request_id}' \
  -H 'X-Muxi-Client-Key: YOUR_CLIENT_KEY' \
  -H 'X-Muxi-User-Id: user123'
```

**Response:**
```json
{
  "status": "cancelled",
  "request_id": "req_abc123",
  "message": "Request cancellation initiated"
}
```

---

## How It Works

### Graceful Termination

```
1. User sends DELETE request
         ↓
2. Request marked as "cancelled"
         ↓
3. Processing continues until next checkpoint
         ↓
4. Checkpoint detects cancellation
         ↓
5. Raises CancellationException
         ↓
6. Exception caught, processing stops
         ↓
7. Resources freed, empty response returned
```

**Key point:** Cancellation is graceful, not immediate. Processing stops at the next safe checkpoint.

### Checkpoints

Cancellation is checked after every long-running operation:

| Operation | Typical Duration | Checkpoint |
|-----------|------------------|------------|
| LLM calls | 1-30+ seconds | After each call |
| MCP tool invocations | 1-60+ seconds | After call returns |
| A2A agent requests | 5-60+ seconds | After call returns |
| Clarification analysis | 1-5 seconds | After LLM call |
| Request analysis | 1-5 seconds | After LLM call |
| Task decomposition | 2-10 seconds | After LLM call |

**Example flow:**
```
Agent making LLM call (5 seconds)
         ↓
[User cancels during this time]
         ↓
LLM call completes (can't cancel mid-call)
         ↓
Checkpoint: Check if cancelled
         ↓
Cancelled! Stop processing
         ↓
Return empty response
```

---

## API Reference

### Cancel Request

```
DELETE /v1/requests/{request_id}
```

**Path Parameters:**
- `request_id` (required) - The request ID to cancel

**Headers:**
- `X-Muxi-Client-Key` (required) - Your client API key
- `X-Muxi-User-Id` (required) - User identifier

**Response:**
```json
{
  "status": "cancelled",
  "request_id": "req_abc123",
  "message": "Request cancellation initiated",
  "timestamp": "2025-01-09T10:30:00Z"
}
```

**Status Codes:**
- `200` - Cancellation initiated successfully
- `404` - Request not found
- `400` - Invalid request ID format
- `401` - Authentication failed

---

## Use Cases

### Cancel Streaming Request

```python
import httpx
import asyncio

async def chat_with_cancel():
    async with httpx.AsyncClient() as client:
        # Start streaming request
        request_id = "my-request-123"
        
        async with client.stream(
            "POST",
            "http://localhost:8001/v1/chat",
            json={
                "message": "Write a very long story about...",
                "request_id": request_id,
                "stream": True
            },
            headers={
                "X-Muxi-Client-Key": "key",
                "X-Muxi-User-Id": "user1"
            }
        ) as response:
            # Read some chunks
            chunks_read = 0
            async for chunk in response.aiter_lines():
                print(chunk)
                chunks_read += 1
                
                # Cancel after 10 chunks
                if chunks_read >= 10:
                    await client.delete(
                        f"http://localhost:8001/v1/requests/{request_id}",
                        headers={
                            "X-Muxi-Client-Key": "key",
                            "X-Muxi-User-Id": "user1"
                        }
                    )
                    break
                    
            print(f"Cancelled after {chunks_read} chunks")
```

### Cancel from UI (React)

```typescript
import { useState } from 'react';

function ChatInterface() {
  const [requestId, setRequestId] = useState<string | null>(null);
  const [isProcessing, setIsProcessing] = useState(false);

  const sendMessage = async (message: string) => {
    const id = `req_${Date.now()}`;
    setRequestId(id);
    setIsProcessing(true);
    
    try {
      const response = await fetch('/v1/chat', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
          'X-Muxi-Client-Key': apiKey,
          'X-Muxi-User-Id': userId
        },
        body: JSON.stringify({ message, request_id: id })
      });
      
      const data = await response.json();
      // Handle response...
    } finally {
      setIsProcessing(false);
      setRequestId(null);
    }
  };

  const cancelRequest = async () => {
    if (!requestId) return;
    
    try {
      await fetch(`/v1/requests/${requestId}`, {
        method: 'DELETE',
        headers: {
          'X-Muxi-Client-Key': apiKey,
          'X-Muxi-User-Id': userId
        }
      });
      
      setIsProcessing(false);
      setRequestId(null);
    } catch (error) {
      console.error('Failed to cancel:', error);
    }
  };

  return (
    <div>
      {/* Chat UI */}
      {isProcessing && (
        <button onClick={cancelRequest}>
          Cancel Request
        </button>
      )}
    </div>
  );
}
```

### Cancel Long-Running Operations

```python
import asyncio
from muxi.runtime.formation import Formation

async def process_with_timeout():
    formation = Formation()
    await formation.load("formation.afs")
    
    # Start processing
    request_id = "long-task-123"
    task = asyncio.create_task(
        formation.overlord.chat(
            "Analyze this 1000-page document...",
            request_id=request_id
        )
    )
    
    # Wait with timeout
    try:
        result = await asyncio.wait_for(task, timeout=30.0)
    except asyncio.TimeoutError:
        # Timeout - cancel the request
        await formation.cancel_request(request_id)
        print("Request timed out and was cancelled")
        return None
    
    return result
```

---

## Response Codes

### Success Cases

**200 - Cancelled Successfully**
```json
{
  "status": "cancelled",
  "request_id": "req_abc123",
  "message": "Request cancellation initiated"
}
```

**200 - Already Completed**
```json
{
  "status": "completed",
  "request_id": "req_abc123",
  "message": "Request already completed, cannot cancel"
}
```

**200 - Already Cancelled**
```json
{
  "status": "cancelled",
  "request_id": "req_abc123",
  "message": "Request was already cancelled"
}
```

### Error Cases

**404 - Request Not Found**
```json
{
  "error": "Request not found",
  "request_id": "req_invalid",
  "message": "No request found with ID: req_invalid"
}
```

**400 - Invalid Request ID**
```json
{
  "error": "Invalid request ID format",
  "message": "Request ID must start with 'req_'"
}
```

**401 - Authentication Failed**
```json
{
  "error": "Unauthorized",
  "message": "Invalid or missing API key"
}
```

---

## Behavior

### Idempotent

Cancelling the same request multiple times is safe:

```bash
# First cancellation
curl -X DELETE .../requests/req_abc123
# Response: "cancelled"

# Second cancellation (idempotent)
curl -X DELETE .../requests/req_abc123  
# Response: "already cancelled"
```

### No Partial Results

Cancelled requests return empty response:

```python
response = await overlord.chat("Generate a report...", request_id="req_123")

# [User cancels]

# Response:
{
  "content": "",
  "status": "cancelled",
  "request_id": "req_123"
}
```

**Why no partial results?**
- Consistency: All or nothing
- Simplicity: Clear success/failure
- Safety: Incomplete data can be misleading

### Immediate Acknowledgment

DELETE request returns immediately:

```python
# Returns in <10ms
response = await client.delete(f"/v1/requests/{request_id}")

# Processing continues briefly until next checkpoint
# Then stops
```

---

## Performance

### Overhead

**Normal requests (not cancelled):**
- Checkpoint check: ~1 microsecond per checkpoint
- Memory overhead: None
- API latency impact: Negligible

**Cancelled requests:**
- Stop within milliseconds of next checkpoint
- LLM tokens saved if cancelled before next call
- Resources freed immediately

### Cost Savings

**Example:**
```
Long LLM call started: 10,000 tokens expected
User cancels after 2 seconds
         ↓
LLM call completes (can't cancel mid-call): 1,500 tokens used
Next LLM call would have used: 8,500 tokens
         ↓
Cancelled at checkpoint before second call
         ↓
Saved: 8,500 tokens ($0.17 at $0.02/1K tokens)
```

---

## Limitations

### Cannot Cancel Mid-Operation

```
✗ Cannot cancel:
- During LLM call (once started, runs to completion)
- During tool execution (MCP call must complete)
- During database transaction (atomic operation)

✓ Can cancel:
- Between LLM calls
- Between tool invocations
- During agent decision-making
- Between task steps
```

### Streaming Partial Results

Already-sent SSE events cannot be recalled:

```
Streaming response:
  Chunk 1: "Hello"  ✓ Sent
  Chunk 2: "there"  ✓ Sent
  [User cancels]
  Chunk 3: "friend" ✗ Not sent
  
User already received: "Hello there"
(Cannot unsend those chunks)
```

### Async Operations

For async (background) operations:

```bash
# Start async operation
curl -X POST /v1/chat \
  -d '{"message": "...", "use_async": true}'
# Returns: request_id

# Cancel it
curl -X DELETE /v1/requests/{request_id}
# Cancellation works the same way
```

---

## Monitoring

### Track Cancellations

```yaml
# Enable cancellation metrics
observability:
  metrics:
    request_cancellation:
      enabled: true
```

**Metrics:**
- `muxi_requests_cancelled_total` - Total cancellations
- `muxi_requests_cancelled_by_phase` - Where cancelled (LLM, tool, etc.)
- `muxi_cancellation_latency_seconds` - Time from cancel to stop

### Logs

```yaml
logging:
  request_cancellation:
    enabled: true
    level: info
```

**Log events:**
```
INFO: Request req_abc123 cancellation initiated
DEBUG: Checkpoint detected cancellation for req_abc123
INFO: Request req_abc123 processing stopped (phase: tool_execution)
```

---

## Best Practices

### 1. Show Cancel Button

```tsx
// Always show cancel option for long operations
{isProcessing && (
  <button onClick={cancelRequest} className="cancel-btn">
    ⊗ Cancel
  </button>
)}
```

### 2. Use Timeouts

```python
# Don't let requests run forever
try:
    result = await asyncio.wait_for(
        formation.chat(message, request_id=req_id),
        timeout=60.0  # 60 second timeout
    )
except asyncio.TimeoutError:
    await formation.cancel_request(req_id)
    raise RequestTimeoutError()
```

### 3. Track Request IDs

```python
# Keep track of active requests
class RequestManager:
    def __init__(self):
        self.active_requests = set()
    
    async def send(self, message):
        request_id = f"req_{uuid.uuid4()}"
        self.active_requests.add(request_id)
        
        try:
            result = await formation.chat(message, request_id=request_id)
            return result
        finally:
            self.active_requests.discard(request_id)
    
    async def cancel_all(self):
        for req_id in list(self.active_requests):
            await formation.cancel_request(req_id)
```

### 4. Graceful Degradation

```python
try:
    response = await formation.cancel_request(request_id)
except RequestNotFoundError:
    # Request already completed or never existed
    # Handle gracefully
    logger.info(f"Request {request_id} not found, may have completed")
```

---

## Troubleshooting

### Request Won't Cancel

**Problem:** DELETE returns success but processing continues

**Causes:**
1. Request is in long LLM call (wait for it to complete)
2. Request is in tool execution (wait for tool to return)
3. Request ID is wrong (check logs)

**Solution:**
- Wait a few seconds for next checkpoint
- Check `request_id` is correct
- Enable debug logging to see checkpoint flow

### Cancelled Too Late

**Problem:** Request cancelled but still incurred costs

**Explanation:**
- Checkpoint already passed
- LLM call already started
- Those costs are unavoidable

**Solution:**
- Cancel earlier in the flow
- Use timeouts proactively
- Monitor typical request durations

---

## Learn More

- [Async Operations](../deep-dives/async.md) - Background task management
- [Streaming](../deep-dives/streaming.md) - Cancel streaming responses
- [Observability](../deep-dives/observability.md) - Monitor cancellations
