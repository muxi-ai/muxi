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

## Platform Support

| Platform | Support |
|----------|---------|
| Node.js | Full support (v18+) |
| Browser | Full support (modern browsers) |
| Deno | Compatible |
| Bun | Compatible |
| Edge runtimes | Compatible (Vercel Edge, Cloudflare Workers) |

The SDK uses the standard `fetch` API internally, making it compatible with any JavaScript runtime that supports `fetch`.

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

// Production (via server proxy)
const formation = new FormationClient({
  serverUrl: "http://localhost:7890",
  formationId: "my-assistant",
  clientKey: "your_client_key",
  adminKey: "your_admin_key",  // Optional, for admin operations
});

// Local development (with muxi up)
const formation = new FormationClient({
  serverUrl: "http://localhost:7890",
  formationId: "my-assistant",
  mode: "draft",  // Uses /draft/ prefix instead of /api/
  clientKey: "your_client_key",
});

// Direct connection (bypasses server proxy)
const formation = new FormationClient({
  formationId: "direct",
  baseUrl: "http://localhost:8001/v1",  // Direct to formation port
  clientKey: "your_client_key",
});
```

> [!TIP]
> **Local dev workflow:** Use `mode: "draft"` during development with `muxi up`, then remove it when deploying to production.

### Chat (Streaming)

```typescript
// Streaming chat (recommended)
for await (const chunk of formation.chatStream(
  { message: "Hello!" },
  "user_123"
)) {
  if (typeof chunk.token === "string") {
    process.stdout.write(chunk.token);
  }
}
```

Named `ui` and `done` events are returned with `type: "ui"` and
`type: "done"`. Stream errors are raised as SDK exceptions.

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

// Restore session (sessionId, userId, messages)
await formation.restoreSession("sess_abc123", "user_123", messages);
```

### Memory

```typescript
// Get memory config
const config = await formation.getMemoryConfig();

// Get memories for user
const memories = await formation.getMemories("user_123");

// Add a memory (userId, type, detail)
await formation.addMemory("user_123", "preference", "User prefers TypeScript");

// Delete a memory
await formation.deleteMemory("user_123", "mem_abc123");

// Clear user buffer
await formation.clearUserBuffer("user_123");

// Get buffer stats
const stats = await formation.getBufferStats();
```

### Triggers

```typescript
// Fire a synchronous trigger
const response = await formation.fireTrigger(
  "github-issue",
  {
    data: {
      repository: "muxi/runtime",
      issue: { number: 123, title: "Bug report" }
    },
    use_async: false
  },
  false,
  "user_123"
);

// Fire async trigger
await formation.fireTrigger(
  "daily-report",
  { data: { date: "2026-07-14" }, use_async: true },
  true,
  "user_123"
);
```

### Scheduler

```typescript
// List scheduled jobs
const jobs = await formation.getSchedulerJobs("user_123");

// Create a job (jobType, schedule, message, userId)
const job = await formation.createSchedulerJob(
  "prompt",           // jobType
  "0 9 * * *",        // schedule (cron)
  "Generate daily summary",  // message
  "user_123"          // userId
);

// Delete a job
await formation.deleteSchedulerJob("job_abc123");
```

### Event Streaming

```typescript
// Stream events for a user
for await (const event of formation.streamEvents("user_123")) {
  console.log(event);
}

// Stream logs (admin)
for await (const log of formation.streamLogs({ level: "info" })) {
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


## Webhook Handlers

Handle incoming async/trigger webhook callbacks with signature verification:

```typescript
import { webhook } from "@muxi-ai/muxi-typescript";

app.post("/webhooks/muxi", (req, res) => {
    const signature = req.headers["x-muxi-signature"] as string;
    
    // Verify signature (prevents spoofing and replay attacks)
    if (!webhook.verifySignature(req.rawBody, signature, WEBHOOK_SECRET)) {
        return res.status(401).send("Invalid signature");
    }
    
    // Parse into typed WebhookEvent
    const event = webhook.parse(req.rawBody);
    
    if (event.status === "completed") {
        for (const item of event.content) {
            if (item.type === "text") console.log(item.text);
        }
    } else if (event.status === "failed") {
        console.error(`Error: ${event.error?.message}`);
    } else if (event.status === "awaiting_clarification") {
        console.log(`Question: ${event.clarification?.question}`);
    }
    
    res.json({ received: true });
});
```

**WebhookEvent fields:**
- `requestId`, `status`, `timestamp`
- `content` - Array of `ContentItem` (type, text, file)
- `error` - `ErrorDetails` (code, message, trace)
- `clarification` - `Clarification` (question, clarificationRequestId)
- `formationId`, `userId`, `processingTime`, `raw`


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
  for await (const chunk of formation.chatStream(
    { message },
    "user_123"
  )) {
    if (typeof chunk.token === "string") {
      process.stdout.write(chunk.token);
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

### Browser Usage

The SDK works directly in browsers. Use the client key (not admin key) for browser applications:

```typescript
// In your frontend code
import { FormationClient } from "@muxi-ai/muxi-typescript";

const formation = new FormationClient({
  serverUrl: "https://api.yourapp.com",  // Your MUXI server
  formationId: "my-assistant",
  clientKey: "your_client_key",  // Safe to expose in browser
});

// Chat with streaming
async function chat(message: string, userId: string) {
  const response = [];
  for await (const chunk of formation.chatStream({ message }, userId)) {
    if (typeof chunk.token === "string") {
      response.push(chunk.token);
      // Update your UI here
    }
  }
  return response.join("");
}
```

> [!IMPORTANT]
> **Security:** Only use the `clientKey` in browser code. Never expose your `adminKey` in client-side applications. The client key has limited permissions (chat, sessions, memory) while admin operations require server-side code.

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
      for await (const chunk of formation.chatStream(
        { message: content },
        userId
      )) {
        if (typeof chunk.token === "string") {
          response += chunk.token;
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
      for await (const chunk of formation.chatStream(
        { message },
        userId
      )) {
        if (typeof chunk.token === "string") {
          controller.enqueue(encoder.encode(chunk.token));
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


## Response UI Widgets

A streamed response can carry optional [UI widgets](../reference/response-ui-widgets.md)
(choices, links, MCP UI resources). They arrive as a chunk of shape
`{ type: "ui", ui: UIWidget[] }`; the `UIWidget` and `UIOption` interfaces are
exported. Ignore widget types you don't render - the text always stands alone.

```typescript
for await (const chunk of formation.chatStream({ message: 'Which region?' }, 'user_123')) {
  if (chunk.type === 'ui') {
    for (const widget of chunk.ui) {
      if (widget.type === 'options') console.log(widget.prompt);
    }
  }
}
```

The echoed idempotency key is surfaced as `idempotency_key` on the unwrapped
response. See [Idempotency](../deep-dives/idempotency.md).

## Learn More

- [Python SDK](python-sdk.md)
- [Go SDK](go-sdk.md)
- [API Reference](../reference/api-reference.md)
