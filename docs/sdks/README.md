---
title: SDKs
description: Official client libraries for MUXI
---
# SDKs

## Official client libraries for MUXI

Native SDKs for Python, TypeScript, and Go. All SDKs provide the same core functionality: chat, streaming, sessions, memory, triggers, and formation management.

## Quick Install

:::: cols=3

(python.md)[[card]]
#### Python
```bash
pip install muxi-client
```

[GitHub](https://github.com/muxi-ai/muxi-python)
[[/card]]

(typescript.md)[[card]]
#### TypeScript
```bash
npm install @muxi-ai/muxi-typescript
```

[GitHub](https://github.com/muxi-ai/muxi-typescript)
[[/card]]

(go.md)[[card]]
#### Go
```bash
go get github.com/muxi-ai/muxi-go
```

[GitHub](https://github.com/muxi-ai/muxi-go)
[[/card]]

::::

---

## Quick Start

[[tabs]]

[[tab Python]]
```python
from muxi import FormationClient

formation = FormationClient(
    server_url="http://localhost:7890",
    formation_id="my-assistant",
    client_key="your_client_key",
)

for event in formation.chat_stream({"message": "Hello!"}, user_id="user_123"):
    if event.get("type") == "text":
        print(event.get("text"), end="", flush=True)
```
[[/tab]]

[[tab TypeScript]]
```typescript
import { FormationClient } from "@muxi-ai/muxi-typescript";

const formation = new FormationClient({
  serverUrl: "http://localhost:7890",
  formationId: "my-assistant",
  clientKey: "your_client_key",
});

for await (const chunk of await formation.chatStream({ message: "Hello!" }, "user_123")) {
  if (chunk.type === "text") process.stdout.write(chunk.text);
}
```
[[/tab]]

[[tab Go]]
```go
import muxi "github.com/muxi-ai/muxi-go"

client := muxi.NewFormationClient(&muxi.FormationConfig{
    FormationID: "my-assistant",
    ServerURL:   "http://localhost:7890",
    ClientKey:   "your_client_key",
})

stream, errs := client.ChatStream(ctx, &muxi.ChatRequest{
    Message: "Hello!",
    UserID:  "user_123",
})
for chunk := range stream {
    if chunk.Type == "text" {
        fmt.Print(chunk.Text)
    }
}
```
[[/tab]]

[[/tabs]]

---

## Two Client Types

All SDKs provide two clients:

### FormationClient

For interacting with a running formation:

- Chat (streaming & non-streaming)
- Sessions & history
- Memory management
- Triggers & scheduled tasks
- Agent configuration

**Authentication:** Client key (`X-Muxi-Client-Key`) or Admin key (`X-Muxi-Admin-Key`)

### ServerClient

For managing the MUXI server:

- Deploy formations
- Start/stop/restart formations
- List formations
- Server health & logs

**Authentication:** HMAC signature (`key_id` + `secret_key`)

---

## Common Operations

### Chat (Streaming)

[[tabs]]

[[tab Python]]
```python
for event in formation.chat_stream({"message": "Hello!"}, user_id="user_123"):
    if event.get("type") == "text":
        print(event.get("text"), end="")
    elif event.get("type") == "done":
        break
```
[[/tab]]

[[tab TypeScript]]
```typescript
for await (const chunk of await formation.chatStream({ message: "Hello!" }, "user_123")) {
  if (chunk.type === "text") process.stdout.write(chunk.text);
  if (chunk.type === "done") break;
}
```
[[/tab]]

[[tab Go]]
```go
stream, errs := client.ChatStream(ctx, &muxi.ChatRequest{Message: "Hello!", UserID: "user_123"})
for chunk := range stream {
    if chunk.Type == "text" {
        fmt.Print(chunk.Text)
    }
}
```
[[/tab]]

[[/tabs]]

### Memory

[[tabs]]

[[tab Python]]
```python
# Get memories
memories = formation.get_memories(user_id="user_123")

# Add memory
formation.add_memory("User prefers Python", user_id="user_123")

# Clear buffer
formation.clear_user_buffer(user_id="user_123")
```
[[/tab]]

[[tab TypeScript]]
```typescript
// Get memories
const memories = await formation.getMemories("user_123");

// Add memory
await formation.addMemory("User prefers TypeScript", "user_123");

// Clear buffer
await formation.clearUserBuffer("user_123");
```
[[/tab]]

[[tab Go]]
```go
// Get memories
memories, _ := client.GetMemories(ctx, "user_123")

// Add memory
client.AddMemory(ctx, "User prefers Go", "user_123")

// Clear buffer
client.ClearUserBuffer(ctx, "user_123")
```
[[/tab]]

[[/tabs]]

### Server Management

[[tabs]]

[[tab Python]]
```python
from muxi import ServerClient

server = ServerClient(
    url="http://localhost:7890",
    key_id="muxi_pk_...",
    secret_key="muxi_sk_...",
)

# Deploy
server.deploy_formation(bundle_path="my-bot.tar.gz")

# List formations
formations = server.list_formations()

# Stop/start/restart
server.stop_formation(formation_id="my-bot")
server.start_formation(formation_id="my-bot")
```
[[/tab]]

[[tab TypeScript]]
```typescript
import { ServerClient } from "@muxi-ai/muxi-typescript";

const server = new ServerClient({
  url: "http://localhost:7890",
  keyId: "muxi_pk_...",
  secretKey: "muxi_sk_...",
});

// Deploy
await server.deployFormation({ bundlePath: "my-bot.tar.gz" });

// List formations
const formations = await server.listFormations();

// Stop/start/restart
await server.stopFormation("my-bot");
await server.startFormation("my-bot");
```
[[/tab]]

[[tab Go]]
```go
server := muxi.NewServerClient(&muxi.ServerConfig{
    URL:       "http://localhost:7890",
    KeyID:     "muxi_pk_...",
    SecretKey: "muxi_sk_...",
})

// Deploy
server.DeployFormation(ctx, &muxi.DeployRequest{BundlePath: "my-bot.tar.gz"})

// List formations
formations, _ := server.ListFormations(ctx)

// Stop/start/restart
server.StopFormation(ctx, "my-bot")
server.StartFormation(ctx, "my-bot")
```
[[/tab]]

[[/tabs]]

---

## Error Handling

All SDKs provide typed errors:

| Error | Meaning |
|-------|---------|
| `AuthenticationError` | Invalid API key |
| `AuthorizationError` | Insufficient permissions |
| `NotFoundError` | Resource doesn't exist |
| `ValidationError` | Invalid request data |
| `RateLimitError` | Too many requests |
| `ServerError` | Server-side error |
| `ConnectionError` | Network issue |

[[tabs]]

[[tab Python]]
```python
from muxi import MuxiError, AuthenticationError, RateLimitError

try:
    response = formation.chat_stream({"message": "Hello!"}, user_id="user_123")
except AuthenticationError:
    print("Invalid API key")
except RateLimitError as e:
    print(f"Rate limited, retry after {e.retry_after}s")
except MuxiError as e:
    print(f"Error: {e.code} - {e.message}")
```
[[/tab]]

[[tab TypeScript]]
```typescript
import { MuxiError, AuthenticationError, RateLimitError } from "@muxi-ai/muxi-typescript";

try {
  await formation.chatStream({ message: "Hello!" }, "user_123");
} catch (err) {
  if (err instanceof AuthenticationError) {
    console.log("Invalid API key");
  } else if (err instanceof RateLimitError) {
    console.log(`Rate limited, retry after ${err.retryAfter}s`);
  } else if (err instanceof MuxiError) {
    console.log(`Error: ${err.code} - ${err.message}`);
  }
}
```
[[/tab]]

[[tab Go]]
```go
resp, err := client.Chat(ctx, &muxi.ChatRequest{Message: "Hello!", UserID: "u1"})
if err != nil {
    var authErr *muxi.AuthenticationError
    var rateLimit *muxi.RateLimitError
    
    switch {
    case errors.As(err, &authErr):
        log.Println("Invalid API key")
    case errors.As(err, &rateLimit):
        log.Printf("Rate limited, retry after %d seconds", rateLimit.RetryAfter)
    default:
        log.Fatal(err)
    }
}
```
[[/tab]]

[[/tabs]]

---

## Configuration

| Setting | Python | TypeScript | Go | Default |
|---------|--------|------------|-----|---------|
| Timeout | `timeout=30` | `timeout: 30000` | 30s built-in | 30s |
| Retries | `max_retries=3` | `maxRetries: 3` | 3 built-in | 3 |
| Debug | `debug=True` | `debug: true` | - | Off |

---

## Learn More

- [Python SDK →](python.md)
- [TypeScript SDK →](typescript.md)
- [Go SDK →](go.md)
- [API Reference →](../reference/api-reference.md)
