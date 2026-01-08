---
title: Streaming Responses
description: Real-time response streaming via Server-Sent Events
---

# Streaming Responses

## Real-time responses as they're generated

MUXI streams responses using Server-Sent Events (SSE), reducing time-to-first-token from seconds to milliseconds.

---

## Why Streaming?

| Metric | Without Streaming | With Streaming |
|--------|-------------------|----------------|
| Time to first token | 2-10 seconds | ~500ms |
| User experience | Wait... wait... wall of text | Typewriter effect |
| Memory usage | Buffer entire response | Chunk by chunk |

---

## Enable Streaming

```yaml
# formation.afs
overlord:
  response:
    streaming: true
```

---

## SSE Format

Responses arrive as Server-Sent Events:

```
event: chunk
data: {"text": "Hello", "agent": "assistant"}

event: chunk
data: {"text": " there", "agent": "assistant"}

event: chunk
data: {"text": "!", "agent": "assistant"}

event: done
data: {"session_id": "sess_abc123"}
```

---

## Using Streaming

[[tabs]]

[[tab curl]]
```bash
curl -N http://localhost:8001/v1/chat \
  -H "Accept: text/event-stream" \
  -H "X-Muxi-Client-Key: fmc_..." \
  -d '{"message": "Tell me a story"}'
```
[[/tab]]

[[tab Python]]
```python
for chunk in formation.chat_stream("Tell me a story"):
    print(chunk.text, end="", flush=True)
print()  # Newline at end
```
[[/tab]]

[[tab TypeScript]]
```typescript
for await (const chunk of formation.chatStream('Tell me a story')) {
  process.stdout.write(chunk.text);
}
console.log();  // Newline at end
```
[[/tab]]

[[tab Go]]
```go
stream, _ := formation.ChatStream("Tell me a story")
for chunk := range stream.Chunks {
    fmt.Print(chunk.Text)
}
fmt.Println()  // Newline at end
```
[[/tab]]

[[tab JavaScript (Browser)]]
```javascript
const response = await fetch('http://localhost:8001/v1/chat', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'Accept': 'text/event-stream',
    'X-Muxi-Client-Key': 'fmc_...'
  },
  body: JSON.stringify({ message: 'Tell me a story' })
});

const reader = response.body.getReader();
const decoder = new TextDecoder();

while (true) {
  const { done, value } = await reader.read();
  if (done) break;
  
  const chunk = decoder.decode(value);
  // Parse SSE format and update UI
  console.log(chunk);
}
```
[[/tab]]

[[/tabs]]

---

## Event Types

| Event | When | Data |
|-------|------|------|
| `chunk` | Text generated | `{"text": "...", "agent": "..."}` |
| `tool_start` | Tool invoked | `{"tool": "...", "args": {...}}` |
| `tool_end` | Tool completed | `{"tool": "...", "result": "..."}` |
| `error` | Error occurred | `{"error": "...", "code": "..."}` |
| `done` | Stream complete | `{"session_id": "..."}` |

---

## Tool Events

When an agent uses tools, you see the process:

```
event: chunk
data: {"text": "Let me search for that..."}

event: tool_start
data: {"tool": "web-search", "args": {"query": "AI trends 2025"}}

event: tool_end
data: {"tool": "web-search", "result": "Found 10 results..."}

event: chunk
data: {"text": "Based on my research, "}

event: chunk
data: {"text": "here are the latest AI trends..."}
```

This lets you show users what's happening:
- "Searching the web..."
- "Querying database..."
- "Reading file..."

---

## React Integration

```tsx
function Chat() {
  const [messages, setMessages] = useState<string[]>([]);
  const [currentMessage, setCurrentMessage] = useState('');
  
  const sendMessage = async (text: string) => {
    setMessages(prev => [...prev, `You: ${text}`]);
    setCurrentMessage('');
    
    for await (const chunk of formation.chatStream(text)) {
      setCurrentMessage(prev => prev + chunk.text);
    }
    
    setMessages(prev => [...prev, `Assistant: ${currentMessage}`]);
    setCurrentMessage('');
  };
  
  return (
    <div>
      {messages.map((m, i) => <p key={i}>{m}</p>)}
      {currentMessage && <p>Assistant: {currentMessage}▌</p>}
    </div>
  );
}
```

---

## Connection Management

### Timeouts

Configure server timeouts for long responses:

```yaml
server:
  write_timeout: 60s
```

### Keep-Alive

MUXI sends periodic heartbeats:

```
: heartbeat

event: chunk
data: {"text": "..."}
```

Heartbeats prevent proxy/load balancer timeouts.

### Client Reconnection

If connection drops, resume with Last-Event-ID:

```bash
curl -H "Last-Event-ID: 42" ...
```

---

## Fallback to Non-Streaming

If streaming fails or is disabled:

```bash
curl http://localhost:8001/v1/chat \
  -H "Accept: application/json" \
  -d '{"message": "Hello"}'
```

Returns complete JSON:

```json
{
  "text": "Hello! How can I help?",
  "agent": "assistant",
  "session_id": "sess_abc123"
}
```

---

## Next Steps

[+] [Build Custom UI](../guides/custom-ui.md) - Frontend streaming integration
[+] [Async Operations](async.md) - Background task processing
