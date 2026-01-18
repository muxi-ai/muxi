---
title: Build Custom UI
description: Create a frontend that connects to your MUXI formation
---
# Build Custom UI

## Connect any frontend to your agents

Build a custom chat interface, dashboard, or app that talks to your MUXI formation. The API works with any framework - React, Vue, vanilla JS, or mobile apps.

## Overview

MUXI formations expose APIs that any frontend can use:

```
Your App  →  MUXI API  →  Formation
```

## API Endpoints

| Endpoint | Method | Description | API Reference |
|----------|--------|-------------|---------------|
| `/v1/chat` | POST | Send message | [API Docs →](api/formation#tag/Chat/POST/chat) |
| `/v1/sessions` | POST | Create session | [API Docs →](api/formation#tag/Sessions/POST/sessions) |
| `/v1/sessions/{id}` | GET | Get history | [API Docs →](api/formation#tag/Sessions/GET/sessions/{session_id}) |
| `/v1/agents` | GET | List agents | [API Docs →](api/formation#tag/Agents/GET/agents) |

## Required Headers

| Header | Description |
|--------|-------------|
| `X-Muxi-Client-Key` | Your formation client key (`fmc_...`) |
| `X-Muxi-User-Id` | Unique identifier for the user |
| `Content-Type` | `application/json` for POST requests |
| `Accept` | `text/event-stream` for streaming |

---

## React Example

### Chat Component

```tsx
import { useState } from 'react';

const MUXI_URL = 'http://localhost:8001';
const CLIENT_KEY = 'fmc_...';

interface Message {
  role: 'user' | 'assistant';
  content: string;
}

export function Chat({ userId }: { userId: string }) {
  const [messages, setMessages] = useState<Message[]>([]);
  const [input, setInput] = useState('');
  const [loading, setLoading] = useState(false);

  const send = async () => {
    if (!input.trim()) return;

    const userMessage = { role: 'user' as const, content: input };
    setMessages(prev => [...prev, userMessage]);
    setInput('');
    setLoading(true);

    try {
      const response = await fetch(`${MUXI_URL}/v1/chat`, {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
          'X-Muxi-Client-Key': CLIENT_KEY,
          'X-Muxi-User-Id': userId
        },
        body: JSON.stringify({ message: input })
      });

      const result = await response.json();
      
      if (result.success) {
        setMessages(prev => [...prev, {
          role: 'assistant',
          content: result.data.text
        }]);
      } else {
        console.error('Chat error:', result.error);
      }
    } catch (error) {
      console.error('Request failed:', error);
    } finally {
      setLoading(false);
    }
  };

  return (
    <div className="chat">
      <div className="messages">
        {messages.map((m, i) => (
          <div key={i} className={`message ${m.role}`}>
            {m.content}
          </div>
        ))}
        {loading && <div className="message loading">Thinking...</div>}
      </div>
      <div className="input-area">
        <input
          value={input}
          onChange={e => setInput(e.target.value)}
          onKeyDown={e => e.key === 'Enter' && send()}
          placeholder="Type a message..."
        />
        <button onClick={send} disabled={loading}>Send</button>
      </div>
    </div>
  );
}
```

### Streaming

For real-time typewriter effect, use Server-Sent Events:

```tsx
import { useState } from 'react';

export function StreamingChat({ userId }: { userId: string }) {
  const [messages, setMessages] = useState<Message[]>([]);
  const [currentResponse, setCurrentResponse] = useState('');
  const [input, setInput] = useState('');

  const sendStreaming = async () => {
    if (!input.trim()) return;

    setMessages(prev => [...prev, { role: 'user', content: input }]);
    setInput('');
    setCurrentResponse('');

    const response = await fetch(`${MUXI_URL}/v1/chat`, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'Accept': 'text/event-stream',
        'X-Muxi-Client-Key': CLIENT_KEY,
        'X-Muxi-User-Id': userId
      },
      body: JSON.stringify({ message: input })
    });

    const reader = response.body?.getReader();
    const decoder = new TextDecoder();
    let assistantMessage = '';

    while (reader) {
      const { done, value } = await reader.read();
      if (done) break;

      const chunk = decoder.decode(value);
      const lines = chunk.split('\n');

      for (const line of lines) {
        // Handle event type
        if (line.startsWith('event: done')) {
          // Stream complete
          setMessages(prev => [...prev, { role: 'assistant', content: assistantMessage }]);
          setCurrentResponse('');
          break;
        }
        
        // Handle data
        if (line.startsWith('data: ')) {
          try {
            const data = JSON.parse(line.slice(6));
            if (data.text) {
              assistantMessage += data.text;
              setCurrentResponse(assistantMessage);
            }
          } catch {
            // Skip malformed JSON
          }
        }
      }
    }
  };

  return (
    <div className="chat">
      <div className="messages">
        {messages.map((m, i) => (
          <div key={i} className={`message ${m.role}`}>{m.content}</div>
        ))}
        {currentResponse && (
          <div className="message assistant">{currentResponse}▌</div>
        )}
      </div>
      <input
        value={input}
        onChange={e => setInput(e.target.value)}
        onKeyDown={e => e.key === 'Enter' && sendStreaming()}
      />
    </div>
  );
}
```

---

## Session Management

Sessions maintain conversation context across messages:

```typescript
const MUXI_URL = 'http://localhost:8001';
const CLIENT_KEY = 'fmc_...';

// Create a new session
async function createSession(userId: string): Promise<string> {
  const response = await fetch(`${MUXI_URL}/v1/sessions`, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'X-Muxi-Client-Key': CLIENT_KEY,
      'X-Muxi-User-Id': userId
    }
  });
  const result = await response.json();
  return result.data.session_id;
}

// Send message with session context
async function sendWithSession(
  sessionId: string, 
  message: string, 
  userId: string
): Promise<string> {
  const response = await fetch(`${MUXI_URL}/v1/chat`, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'X-Muxi-Client-Key': CLIENT_KEY,
      'X-Muxi-User-Id': userId
    },
    body: JSON.stringify({
      message,
      session_id: sessionId
    })
  });
  const result = await response.json();
  return result.data.text;
}

// Get session history
async function getHistory(sessionId: string, userId: string) {
  const response = await fetch(`${MUXI_URL}/v1/sessions/${sessionId}`, {
    headers: {
      'X-Muxi-Client-Key': CLIENT_KEY,
      'X-Muxi-User-Id': userId
    }
  });
  return response.json();
}
```

---

## Using the SDK (Recommended)

The SDKs handle headers, errors, and streaming automatically:

[[tabs]]

[[tab TypeScript]]
```typescript
import { FormationClient } from '@muxi/sdk';

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
```
[[/tab]]

[[/tabs]]

---

## Learn More

- [Python SDK →](../sdks/python-sdk.md) - Full Python reference
- [TypeScript SDK →](../sdks/typescript-sdk.md) - Full TypeScript reference
- [API Reference →](../reference/api-reference.md) - Direct API access
- [Deep Dive: Streaming →](../deep-dives/real-time-streaming.md) - Real-time responses
- [Deep Dive: Response Formats →](../deep-dives/response-formats.md) - Understanding responses
