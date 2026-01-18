---
title: Build Custom UI
description: Create a frontend that connects to your MUXI formation
---
# Build Custom UI

## Connect any frontend to your agents

Build a custom chat interface, dashboard, or app that talks to your MUXI formation. Works with React, Vue, vanilla JS, mobile apps - anything that can make HTTP requests.


## Quick Start

The TypeScript SDK works in browsers, Node.js, and all major frameworks:

```bash
npm install @muxi-ai/muxi-typescript
```

```typescript
import { FormationClient } from '@muxi-ai/muxi-typescript';

const formation = new FormationClient({
  serverUrl: 'http://localhost:7890',
  formationId: 'my-assistant',
  clientKey: 'fmc_...'
});

// Chat
const response = await formation.chat({ message: 'Hello!' }, 'user-123');

// Streaming
for await (const chunk of formation.chatStream({ message: 'Tell me a story' }, 'user-123')) {
  console.log(chunk.text);
}
```


## SDK Examples

[[tabs]]

[[tab Browser (vanilla)]]
```html
<!DOCTYPE html>
<html>
<head>
  <title>MUXI Chat</title>
</head>
<body>
  <div id="messages"></div>
  <input id="input" placeholder="Type a message..." />
  <button id="send">Send</button>

  <script type="module">
    import { FormationClient } from 'https://esm.sh/@muxi-ai/muxi-typescript';

    const formation = new FormationClient({
      serverUrl: 'http://localhost:7890',
      formationId: 'my-assistant',
      clientKey: 'fmc_...'
    });

    const userId = 'user-' + Math.random().toString(36).slice(2);
    const messagesDiv = document.getElementById('messages');
    const input = document.getElementById('input');

    document.getElementById('send').onclick = async () => {
      const message = input.value;
      if (!message) return;

      messagesDiv.innerHTML += `<p><b>You:</b> ${message}</p>`;
      input.value = '';

      // Streaming response
      let response = '';
      const responseP = document.createElement('p');
      responseP.innerHTML = '<b>Assistant:</b> ';
      messagesDiv.appendChild(responseP);

      for await (const chunk of formation.chatStream({ message }, userId)) {
        response += chunk.text || '';
        responseP.innerHTML = `<b>Assistant:</b> ${response}`;
      }
    };
  </script>
</body>
</html>
```
[[/tab]]

[[tab React]]
```tsx
import { useState } from 'react';
import { FormationClient } from '@muxi-ai/muxi-typescript';

const formation = new FormationClient({
  serverUrl: 'http://localhost:7890',
  formationId: 'my-assistant',
  clientKey: 'fmc_...'
});

export function Chat({ userId }: { userId: string }) {
  const [messages, setMessages] = useState<Array<{ role: string; content: string }>>([]);
  const [input, setInput] = useState('');
  const [streaming, setStreaming] = useState('');

  const send = async () => {
    if (!input.trim()) return;

    setMessages(prev => [...prev, { role: 'user', content: input }]);
    setInput('');
    setStreaming('');

    for await (const chunk of formation.chatStream({ message: input }, userId)) {
      setStreaming(prev => prev + (chunk.text || ''));
    }

    setMessages(prev => [...prev, { role: 'assistant', content: streaming }]);
    setStreaming('');
  };

  return (
    <div>
      {messages.map((m, i) => (
        <div key={i} className={m.role}>{m.content}</div>
      ))}
      {streaming && <div className="assistant">{streaming}▌</div>}
      <input value={input} onChange={e => setInput(e.target.value)} onKeyDown={e => e.key === 'Enter' && send()} />
      <button onClick={send}>Send</button>
    </div>
  );
}
```
[[/tab]]

[[tab Node.js]]
```typescript
import { FormationClient } from '@muxi-ai/muxi-typescript';

const formation = new FormationClient({
  serverUrl: 'http://localhost:7890',
  formationId: 'my-assistant',
  clientKey: 'fmc_...'
});

// Simple chat
const response = await formation.chat(
  { message: 'Hello!' },
  'user-123'
);
console.log(response.text);

// Streaming
for await (const chunk of formation.chatStream(
  { message: 'Tell me a story' },
  'user-123'
)) {
  process.stdout.write(chunk.text);
}
console.log();
```
[[/tab]]

[[tab Python]]
```python
from muxi import FormationClient

formation = FormationClient(
    server_url="http://localhost:7890",
    formation_id="my-assistant",
    client_key="fmc_..."
)

# Simple chat
response = formation.chat({"message": "Hello!"}, user_id="user-123")
print(response.text)

# Streaming
for chunk in formation.chat_stream({"message": "Tell me a story"}, user_id="user-123"):
    print(chunk.text, end="", flush=True)
print()
```
[[/tab]]

[[/tabs]]

> [!TIP]
> **CDN delivery:** Use `https://esm.sh/@muxi-ai/muxi-typescript` to import directly in browsers without a build step.


## Session Management

Sessions maintain conversation context:

[[tabs]]

[[tab TypeScript]]
```typescript
// Create session
const sessions = await formation.getSessions('user-123');

// Chat with session
const response = await formation.chat(
  { message: 'Hello!', session_id: 'sess_abc123' },
  'user-123'
);

// Get session history
const history = await formation.getSessionMessages('sess_abc123', 'user-123');
```
[[/tab]]

[[tab Python]]
```python
# Create session
sessions = formation.get_sessions("user-123")

# Chat with session
response = formation.chat(
    {"message": "Hello!", "session_id": "sess_abc123"},
    user_id="user-123"
)

# Get session history
history = formation.get_session_messages("sess_abc123", "user-123")
```
[[/tab]]

[[/tabs]]


## API Reference

For direct API access (without SDK), see the endpoint reference:

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/v1/chat` | POST | Send message |
| `/v1/sessions` | POST | Create session |
| `/v1/sessions/{id}` | GET | Get session history |
| `/v1/agents` | GET | List agents |

**Required headers:**
- `X-Muxi-Client-Key` - Your client key (`fmc_...`)
- `X-Muxi-User-Id` - User identifier
- `Content-Type: application/json`
- `Accept: text/event-stream` (for streaming)

[Full API Documentation →](../reference/api-reference.md)


## Learn More

- [TypeScript SDK →](../sdks/typescript-sdk.md) - Full reference
- [Python SDK →](../sdks/python-sdk.md) - Full reference
- [Streaming Deep Dive →](../deep-dives/real-time-streaming.md) - SSE internals
- [Response Formats →](../deep-dives/response-formats.md) - Understanding responses
