---
title: TypeScript SDK
description: Type-safe client for MUXI formations in TypeScript and JavaScript
---
# TypeScript SDK

## Type-safe access to your agents

Build web and Node.js applications that interact with MUXI formations. Full TypeScript support with type definitions for all API operations.

## Installation

```bash
npm install @muxi/sdk
# or
yarn add @muxi/sdk
```

## Quick Start

```typescript
import { Formation } from '@muxi/sdk';

const formation = new Formation({
  url: 'http://localhost:8001',
  clientKey: 'fmc_...'
});

const response = await formation.chat('Hello!');
console.log(response.text);
```

## Formation Client

### Initialize

```typescript
import { Formation } from '@muxi/sdk';

// With client key
const formation = new Formation({
  url: 'http://localhost:8001',
  clientKey: 'fmc_...'
});

// With admin key
const formation = new Formation({
  url: 'http://localhost:8001',
  adminKey: 'fma_...'
});
```

### Chat

```typescript
// Simple message
const response = await formation.chat('Hello!');
console.log(response.text);

// With options
const response = await formation.chat('Research AI', {
  agent: 'researcher',
  sessionId: 'sess_123',
  userId: 'user_456'
});
```

### Streaming

```typescript
const stream = formation.chatStream('Tell me a story');

for await (const chunk of stream) {
  process.stdout.write(chunk.text);
}
```

### Sessions

```typescript
// Create session
const session = await formation.createSession();
console.log(session.id); // sess_abc123

// Chat with session
const response = await formation.chat('Hello!', {
  sessionId: session.id
});

// Get session history
const history = await formation.getSession(session.id);
for (const message of history.messages) {
  console.log(`${message.role}: ${message.content}`);
}
```

### Agents

```typescript
// List agents
const agents = await formation.listAgents();
agents.forEach(a => console.log(a.id, a.name));

// Target specific agent
const response = await formation.chat('Research this', {
  agent: 'researcher'
});
```

### Triggers

```typescript
const response = await formation.trigger('github-issue', {
  data: {
    repository: 'muxi/runtime',
    issue: {
      number: 123,
      title: 'Bug report'
    }
  }
});
```

### Multi-User

```typescript
// User isolation
await formation.chat('Remember my preference', {
  userId: 'user_123'
});

// Different user
await formation.chat("What's my preference?", {
  userId: 'user_456'
});
```

## Server Client

### Initialize

```typescript
import { ServerClient } from '@muxi/sdk';

const server = new ServerClient({
  url: 'http://localhost:7890',
  keyId: 'MUXI_...',
  secretKey: 'sk_...'
});
```

### List Formations

```typescript
const formations = await server.listFormations();
formations.forEach(f => {
  console.log(f.id, f.status, f.port);
});
```

### Deploy Formation

```typescript
const result = await server.deploy('/path/to/formation');
console.log(result.port);
```

### Stop/Start/Restart

```typescript
await server.stopFormation('my-assistant');
await server.startFormation('my-assistant');
await server.restartFormation('my-assistant');
```

### Delete Formation

```typescript
await server.deleteFormation('my-assistant');
```

## Error Handling

```typescript
import { 
  MuxiError, 
  AuthenticationError, 
  FormationError 
} from '@muxi/sdk';

try {
  const response = await formation.chat('Hello!');
} catch (error) {
  if (error instanceof AuthenticationError) {
    console.log('Invalid API key');
  } else if (error instanceof FormationError) {
    console.log(`Formation error: ${error.message}`);
  } else if (error instanceof MuxiError) {
    console.log(`MUXI error: ${error.message}`);
  }
}
```

## Configuration

```typescript
new Formation({
  url: 'http://localhost:8001',
  clientKey: 'fmc_...',
  timeout: 30000,      // Request timeout (ms)
  maxRetries: 3,       // Retry on failure
});
```

## Types

```typescript
interface ChatResponse {
  text: string;
  agent: string;
  sessionId: string;
  metadata?: Record<string, any>;
}

interface Session {
  id: string;
  createdAt: Date;
  messages: Message[];
}

interface Message {
  role: 'user' | 'assistant';
  content: string;
  timestamp: Date;
}
```

## Examples

### Chat Bot (Node.js)

```typescript
import { Formation } from '@muxi/sdk';
import * as readline from 'readline';

const formation = new Formation({ url: '...', clientKey: '...' });
const session = await formation.createSession();

const rl = readline.createInterface({
  input: process.stdin,
  output: process.stdout
});

const chat = async () => {
  rl.question('You: ', async (input) => {
    if (input === '/exit') {
      rl.close();
      return;
    }
    
    const response = await formation.chat(input, {
      sessionId: session.id
    });
    console.log(`Assistant: ${response.text}`);
    chat();
  });
};

chat();
```

### React Hook

```typescript
import { useState } from 'react';
import { Formation } from '@muxi/sdk';

const formation = new Formation({ url: '...', clientKey: '...' });

export function useChat() {
  const [messages, setMessages] = useState<Message[]>([]);
  const [loading, setLoading] = useState(false);

  const send = async (text: string) => {
    setLoading(true);
    setMessages(prev => [...prev, { role: 'user', content: text }]);
    
    const response = await formation.chat(text);
    setMessages(prev => [...prev, { 
      role: 'assistant', 
      content: response.text 
    }]);
    setLoading(false);
  };

  return { messages, send, loading };
}
```
