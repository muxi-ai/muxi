---
title: TypeScript SDK
description: Type-safe client for MUXI formations in TypeScript and JavaScript
---
# TypeScript SDK

## Type-safe access to your agents

Build web and Node.js applications that interact with MUXI formations. Full TypeScript support with type definitions for all API operations.

**GitHub:** [`muxi-ai/muxi-typescript`](https://github.com/muxi-ai/muxi-typescript)

## Installation

```bash
npm install @muxi-ai/muxi-typescript
# or
yarn add @muxi-ai/muxi-typescript
```

## Quick Start

```typescript
import { FormationClient } from "@muxi-ai/muxi-typescript";

const formation = new FormationClient({
  serverUrl: "http://localhost:7890",
  formationId: "my-assistant",
  clientKey: "your_client_key",
});

console.log(await formation.health());
```

## Formation Client

### Initialize

```typescript
import { FormationClient } from "@muxi-ai/muxi-typescript";

// Via server proxy (recommended)
const formation = new FormationClient({
  serverUrl: "http://localhost:7890",
  formationId: "my-assistant",
  clientKey: "your_client_key",
  adminKey: "your_admin_key",  // Optional, for admin operations
});

// Direct connection (for local dev)
const formation = new FormationClient({
  baseUrl: "http://localhost:8001/v1",  // Direct to formation
  clientKey: "your_client_key",
});
```

### Chat (Streaming)

```typescript
// Streaming chat (recommended)
for await (const chunk of await formation.chatStream(
  { message: "Hello!" },
  "user_123"
)) {
  if (chunk.type === "text") {
    process.stdout.write(chunk.text);
  }
}
```

Chunk types: `text`, `tool_call`, `tool_result`, `agent_handoff`, `thinking`, `error`, `done`.

### Agents

```typescript
// List agents (requires admin key)
const agents = await formation.getAgents();
agents.forEach(agent => console.log(agent.id, agent.name));

// Get specific agent
const agent = await formation.getAgent("researcher");
```

### Sessions

```typescript
// List sessions
const sessions = await formation.getSessions("user_123");
sessions.forEach(s => console.log(s.session_id));

// Get session messages
const history = await formation.getSessionMessages("sess_abc123", "user_123");
for (const message of history.messages) {
  console.log(`${message.role}: ${message.content}`);
}

// Restore session
await formation.restoreSession("sess_abc123", messages, "user_123");
```

### Memory

```typescript
// Get memory config
const config = await formation.getMemoryConfig();

// Get memories for user
const memories = await formation.getMemories("user_123");

// Add a memory
await formation.addMemory("User prefers TypeScript", "user_123");

// Clear user buffer
await formation.clearUserBuffer("user_123");

// Get buffer stats
const stats = await formation.getBufferStats();
```

### Triggers

```typescript
const response = await formation.fireTrigger("github-issue", {
  data: {
    repository: "muxi/runtime",
    issue: {
      number: 123,
      title: "Bug report"
    }
  }
}, "user_123");
```

### Scheduler

```typescript
// List scheduled jobs
const jobs = await formation.getSchedulerJobs("user_123");

// Create a job
const job = await formation.createSchedulerJob({
  title: "Daily report",
  schedule: "0 9 * * *",  // 9am daily
  prompt: "Generate daily summary"
}, "user_123");

// Delete a job
await formation.deleteSchedulerJob("job_abc123", "user_123");
```

### Event Streaming

```typescript
// Stream events for a user
for await (const event of await formation.streamEvents("user_123")) {
  console.log(event);
}

// Stream logs (admin)
for await (const log of await formation.streamLogs({ level: "info" })) {
  console.log(log);
}
```


## Server Client

For managing formations (deploy, start, stop):

```typescript
import { ServerClient } from "@muxi-ai/muxi-typescript";

const server = new ServerClient({
  url: "http://localhost:7890",
  keyId: "muxi_pk_...",
  secretKey: "muxi_sk_...",
});

// Check server status
console.log(await server.status());

// List formations
const formations = await server.listFormations();

// Deploy a formation (with streaming progress)
for await (const event of await server.streamDeployFormation({
  bundlePath: "my-bot.tar.gz"
})) {
  console.log(event);
}

// Stop/start/restart
await server.stopFormation("my-bot");
await server.startFormation("my-bot");
await server.restartFormation("my-bot");
```


## Error Handling

```typescript
import {
  MuxiError,
  AuthenticationError,
  AuthorizationError,
  NotFoundError,
  ValidationError,
  RateLimitError,
  ServerError,
  ConnectionError,
} from "@muxi-ai/muxi-typescript";

try {
  const stream = await formation.chatStream({ message: "Hello!" }, "user_123");
  for await (const chunk of stream) {
    // ...
  }
} catch (err) {
  if (err instanceof AuthenticationError) {
    console.log("Auth failed:", err.message);
  } else if (err instanceof RateLimitError) {
    console.log(`Rate limited, retry after: ${err.retryAfter}s`);
  } else if (err instanceof MuxiError) {
    console.log(`Error: ${err.code} - ${err.message}`);
  }
}
```

All errors include:
- `code` - Error code string
- `message` - Human-readable message
- `statusCode` - HTTP status code
- `retryAfter` - Seconds to wait (for rate limits)


## Configuration

```typescript
const formation = new FormationClient({
  serverUrl: "http://localhost:7890",
  formationId: "my-assistant",
  clientKey: "your_client_key",
  timeout: 30000,      // Request timeout (ms)
  maxRetries: 3,       // Retry count for 429/5xx
  debug: true,         // Enable debug logging
});
```


## Examples

### Chat Bot (Node.js)

```typescript
import { FormationClient } from "@muxi-ai/muxi-typescript";
import * as readline from "readline";

const formation = new FormationClient({
  serverUrl: "http://localhost:7890",
  formationId: "my-assistant",
  clientKey: "your_client_key",
});

const rl = readline.createInterface({
  input: process.stdin,
  output: process.stdout,
});

async function chat(message: string) {
  process.stdout.write("Assistant: ");
  for await (const chunk of await formation.chatStream(
    { message },
    "user_123"
  )) {
    if (chunk.type === "text") {
      process.stdout.write(chunk.text);
    }
  }
  console.log();
}

function prompt() {
  rl.question("You: ", async (input) => {
    if (input.toLowerCase() === "quit") {
      rl.close();
      return;
    }
    await chat(input);
    prompt();
  });
}

prompt();
```

### React Hook

```typescript
import { useState, useCallback } from "react";
import { FormationClient } from "@muxi-ai/muxi-typescript";

const formation = new FormationClient({
  serverUrl: "http://localhost:7890",
  formationId: "my-assistant",
  clientKey: "your_client_key",
});

export function useChat(userId: string) {
  const [messages, setMessages] = useState<Array<{ role: string; content: string }>>([]);
  const [isLoading, setIsLoading] = useState(false);

  const sendMessage = useCallback(async (content: string) => {
    setMessages(prev => [...prev, { role: "user", content }]);
    setIsLoading(true);

    let response = "";
    try {
      for await (const chunk of await formation.chatStream(
        { message: content },
        userId
      )) {
        if (chunk.type === "text") {
          response += chunk.text;
          setMessages(prev => [
            ...prev.slice(0, -1),
            { role: "assistant", content: response },
          ]);
        }
      }
    } finally {
      setIsLoading(false);
    }
  }, [userId]);

  return { messages, sendMessage, isLoading };
}
```

### Next.js API Route

```typescript
// app/api/chat/route.ts
import { FormationClient } from "@muxi-ai/muxi-typescript";

const formation = new FormationClient({
  serverUrl: process.env.MUXI_SERVER_URL!,
  formationId: process.env.MUXI_FORMATION_ID!,
  clientKey: process.env.MUXI_CLIENT_KEY!,
});

export async function POST(req: Request) {
  const { message, userId } = await req.json();

  const encoder = new TextEncoder();
  const stream = new ReadableStream({
    async start(controller) {
      for await (const chunk of await formation.chatStream(
        { message },
        userId
      )) {
        if (chunk.type === "text") {
          controller.enqueue(encoder.encode(chunk.text));
        }
      }
      controller.close();
    },
  });

  return new Response(stream, {
    headers: { "Content-Type": "text/plain; charset=utf-8" },
  });
}
```


## Learn More

- [Python SDK](python.md)
- [Go SDK](go.md)
- [API Reference](reference/api-reference.md)
