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

## React Example

### Setup

```bash
npm install
```

### Chat Component

```tsx
import { useState } from 'react';

const MUXI_URL = 'http://localhost:8001';
const CLIENT_KEY = 'fmc_...';

interface Message {
  role: 'user' | 'assistant';
  content: string;
}

export function Chat() {
  const [messages, setMessages] = useState<Message[]>([]);
  const [input, setInput] = useState('');
  const [loading, setLoading] = useState(false);

  const send = async () => {
    if (!input.trim()) return;

    const userMessage = { role: 'user' as const, content: input };
    setMessages(prev => [...prev, userMessage]);
    setInput('');
    setLoading(true);

    const response = await fetch(`${MUXI_URL}/v1/chat`, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'X-Muxi-Client-Key': CLIENT_KEY
      },
      body: JSON.stringify({ message: input })
    });

    const data = await response.json();
    setMessages(prev => [...prev, {
      role: 'assistant',
      content: data.text
    }]);
    setLoading(false);
  };

  return (
    <div>
      <div className="messages">
        {messages.map((m, i) => (
          <div key={i} className={m.role}>
            {m.content}
          </div>
        ))}
        {loading && <div>Thinking...</div>}
      </div>
      <input
        value={input}
        onChange={e => setInput(e.target.value)}
        onKeyDown={e => e.key === 'Enter' && send()}
      />
      <button onClick={send}>Send</button>
    </div>
  );
}
```

### Streaming

```tsx
const sendStreaming = async () => {
  setLoading(true);

  const response = await fetch(`${MUXI_URL}/v1/chat`, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'Accept': 'text/event-stream',
      'X-Muxi-Client-Key': CLIENT_KEY
    },
    body: JSON.stringify({ message: input })
  });

  const reader = response.body?.getReader();
  const decoder = new TextDecoder();
  let assistantMessage = '';

  while (true) {
    const { done, value } = await reader!.read();
    if (done) break;

    const chunk = decoder.decode(value);
    const lines = chunk.split('\n');

    for (const line of lines) {
      if (line.startsWith('data: ')) {
        const data = JSON.parse(line.slice(6));
        assistantMessage += data.text;
        // Update UI with partial message
      }
    }
  }

  setLoading(false);
};
```

## Session Management

```tsx
// Create session
const createSession = async () => {
  const response = await fetch(`${MUXI_URL}/v1/sessions`, {
    method: 'POST',
    headers: { 'X-Muxi-Client-Key': CLIENT_KEY }
  });
  return response.json();
};

// Use session
const sendWithSession = async (sessionId: string, message: string) => {
  return fetch(`${MUXI_URL}/v1/chat`, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'X-Muxi-Client-Key': CLIENT_KEY
    },
    body: JSON.stringify({
      message,
      session_id: sessionId
    })
  });
};
```

## Using SDK

Simpler with the TypeScript SDK:

```tsx
import { Formation } from '@muxi/sdk';

const formation = new Formation({
  url: 'http://localhost:8001',
  clientKey: 'fmc_...'
});

// In component
const send = async (message: string) => {
  const response = await formation.chat(message);
  return response.text;
};
```

## Learn More

- [Python SDK →](sdks/python-sdk.md) - Full Python reference
- [TypeScript SDK →](sdks/typescript-sdk.md) - Full TypeScript reference
- [API Reference →](reference/api-reference.md) - Direct API access
- [Deep Dive: Streaming →](deep-dives/real-time-streaming.md) - Real-time responses
- [Deep Dive: Response Formats →](deep-dives/response-formats.md) - Understanding responses
