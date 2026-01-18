---
title: Request Cancellation
description: Stop long-running agent requests and free resources
---
# Request Cancellation

## Stop long-running agent requests and free resources

Cancel in-flight requests to stop processing, free up resources, and prevent unnecessary LLM costs.


## Quick Start

[[tabs]]

[[tab Python]]
```python
from muxi import Muxi

client = Muxi()

# Start a request
request_id = "my-request-123"
task = client.chat_async(
    message="Write a very long story...",
    request_id=request_id,
    user_id="user123"
)

# Cancel it
client.cancel_request(request_id, user_id="user123")
```
[[/tab]]

[[tab TypeScript]]
```typescript
import { Muxi } from '@muxi/sdk';

const client = new Muxi();

// Start a request
const requestId = 'my-request-123';
const task = client.chat({
  message: 'Write a very long story...',
  requestId,
  userId: 'user123'
});

// Cancel it
await client.cancelRequest(requestId, { userId: 'user123' });
```
[[/tab]]

[[tab Go]]
```go
client := muxi.NewClient()

// Start a request
requestID := "my-request-123"
task := client.ChatAsync(ctx, &muxi.ChatRequest{
    Message:   "Write a very long story...",
    RequestID: requestID,
    UserID:    "user123",
})

// Cancel it
client.CancelRequest(ctx, requestID, "user123")
```
[[/tab]]

[[tab cURL]]
```bash
curl -X DELETE 'http://localhost:8001/v1/requests/my-request-123' \
  -H 'X-Muxi-Client-Key: YOUR_CLIENT_KEY' \
  -H 'X-Muxi-User-Id: user123'
```
[[/tab]]

[[/tabs]]

---

## How It Works

### Graceful Termination

```
1. User calls cancelRequest()
         ↓
2. Request marked as "cancelled"
         ↓
3. Processing continues until next checkpoint
         ↓
4. Checkpoint detects cancellation
         ↓
5. Processing stops, resources freed
```

**Key point:** Cancellation is graceful, not immediate. Processing stops at the next safe checkpoint.

### Checkpoints

Cancellation is checked after every long-running operation:

| Operation | Typical Duration | Checkpoint |
|-----------|------------------|------------|
| LLM calls | 1-30+ seconds | After each call |
| MCP tool invocations | 1-60+ seconds | After call returns |
| A2A agent requests | 5-60+ seconds | After call returns |
| Task decomposition | 2-10 seconds | After LLM call |

---

## Use Cases

### Cancel After Timeout

[[tabs]]

[[tab Python]]
```python
from muxi import Muxi
import asyncio

client = Muxi()

async def chat_with_timeout(message: str, timeout_seconds: int = 30):
    request_id = f"req_{uuid.uuid4()}"
    
    try:
        response = await asyncio.wait_for(
            client.chat_async(message, request_id=request_id),
            timeout=timeout_seconds
        )
        return response
    except asyncio.TimeoutError:
        # Timeout - cancel the request
        await client.cancel_request(request_id)
        raise TimeoutError(f"Request timed out after {timeout_seconds}s")
```
[[/tab]]

[[tab TypeScript]]
```typescript
import { Muxi } from '@muxi/sdk';

const client = new Muxi();

async function chatWithTimeout(message: string, timeoutMs = 30000) {
  const requestId = `req_${Date.now()}`;
  
  const timeoutPromise = new Promise((_, reject) => {
    setTimeout(async () => {
      await client.cancelRequest(requestId);
      reject(new Error(`Request timed out after ${timeoutMs}ms`));
    }, timeoutMs);
  });
  
  return Promise.race([
    client.chat({ message, requestId }),
    timeoutPromise
  ]);
}
```
[[/tab]]

[[tab Go]]
```go
func chatWithTimeout(ctx context.Context, message string, timeout time.Duration) (*muxi.Response, error) {
    requestID := fmt.Sprintf("req_%d", time.Now().UnixNano())
    
    ctx, cancel := context.WithTimeout(ctx, timeout)
    defer cancel()
    
    response, err := client.Chat(ctx, &muxi.ChatRequest{
        Message:   message,
        RequestID: requestID,
    })
    
    if ctx.Err() == context.DeadlineExceeded {
        client.CancelRequest(context.Background(), requestID, userID)
        return nil, fmt.Errorf("request timed out after %v", timeout)
    }
    
    return response, err
}
```
[[/tab]]

[[/tabs]]

### Cancel Streaming Request

[[tabs]]

[[tab Python]]
```python
from muxi import Muxi

client = Muxi()

request_id = "streaming-123"
chunks_received = 0

for chunk in client.chat_stream(message="Write a long essay...", request_id=request_id):
    print(chunk.text, end="")
    chunks_received += 1
    
    # Cancel after receiving 10 chunks
    if chunks_received >= 10:
        client.cancel_request(request_id)
        print("\n[Cancelled]")
        break
```
[[/tab]]

[[tab TypeScript]]
```typescript
import { Muxi } from '@muxi/sdk';

const client = new Muxi();

const requestId = 'streaming-123';
let chunksReceived = 0;

for await (const chunk of client.chatStream({ message: 'Write a long essay...', requestId })) {
  process.stdout.write(chunk.text);
  chunksReceived++;
  
  // Cancel after receiving 10 chunks
  if (chunksReceived >= 10) {
    await client.cancelRequest(requestId);
    console.log('\n[Cancelled]');
    break;
  }
}
```
[[/tab]]

[[/tabs]]

### Cancel Button in UI

[[tabs]]

[[tab React]]
```tsx
import { useState } from 'react';
import { Muxi } from '@muxi/sdk';

const client = new Muxi();

function ChatInterface() {
  const [requestId, setRequestId] = useState<string | null>(null);
  const [isProcessing, setIsProcessing] = useState(false);

  const sendMessage = async (message: string) => {
    const id = `req_${Date.now()}`;
    setRequestId(id);
    setIsProcessing(true);
    
    try {
      const response = await client.chat({ message, requestId: id });
      // Handle response...
    } finally {
      setIsProcessing(false);
      setRequestId(null);
    }
  };

  const cancelRequest = async () => {
    if (requestId) {
      await client.cancelRequest(requestId);
      setIsProcessing(false);
      setRequestId(null);
    }
  };

  return (
    <div>
      {/* Chat UI */}
      {isProcessing && (
        <button onClick={cancelRequest}>Cancel</button>
      )}
    </div>
  );
}
```
[[/tab]]

[[/tabs]]

---

## Response Behavior

### Cancelled Response

```json
{
  "content": "",
  "status": "cancelled",
  "request_id": "req_123"
}
```

### Idempotent

Cancelling the same request multiple times is safe - subsequent calls return "already cancelled".

### No Partial Results

Cancelled requests return empty content. Why? Consistency - all or nothing. Incomplete data can be misleading.

---

## Limitations

### Cannot Cancel Mid-Operation

| Cannot cancel during | Can cancel between |
|---------------------|-------------------|
| Active LLM call | LLM calls |
| MCP tool execution | Tool invocations |
| Database transaction | Agent decisions |

Once an LLM call starts, it runs to completion. Cancellation takes effect at the next checkpoint.

### Streaming Partial Results

Already-sent chunks cannot be recalled:

```
Chunk 1: "Hello"  ✓ Sent
Chunk 2: "there"  ✓ Sent
[User cancels]
Chunk 3: "friend" ✗ Not sent

User received: "Hello there" (cannot unsend)
```

---

## Cost Savings

Cancellation saves money by preventing subsequent LLM calls:

```
Long task started: 10,000 tokens expected
User cancels after first LLM call: 1,500 tokens used
         ↓
Remaining 8,500 tokens NOT consumed
         ↓
Saved: ~$0.17 (at $0.02/1K tokens)
```

---

## Best Practices

1. **Always show cancel button** for long operations
2. **Use timeouts** - don't let requests run forever
3. **Track request IDs** - keep a map of active requests
4. **Handle gracefully** - request may complete before cancel arrives

---

## Learn More

- [Async Operations](../deep-dives/async-operations.md) - Background task management
- [Streaming](../deep-dives/real-time-streaming.md) - Cancel streaming responses
- [Observability](../deep-dives/observability.md) - Monitor cancellations
