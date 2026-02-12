# SDK Reference

MUXI provides official SDKs in 12 languages. All SDKs follow the same conventions with two client types: **ServerClient** (formation management via HMAC auth) and **FormationClient** (chat and runtime API via key auth).

## Available SDKs

| Language | Package | Install |
|----------|---------|---------|
| **Go** | `github.com/muxi-ai/muxi-go` | `go get github.com/muxi-ai/muxi-go` |
| **Python** | `muxi-sdk` | `pip install muxi-sdk` |
| **TypeScript** | `@muxi/sdk` | `npm install @muxi/sdk` |
| **Ruby** | `muxi` | `gem install muxi` |
| **Java** | `ai.muxi:sdk` | Maven/Gradle |
| **Kotlin** | `ai.muxi:sdk` | Maven/Gradle |
| **Swift** | `MuxiSDK` | Swift Package Manager |
| **C#** | `Muxi.SDK` | `dotnet add package Muxi.SDK` |
| **PHP** | `muxi/sdk` | `composer require muxi/sdk` |
| **Dart** | `muxi` | `dart pub add muxi` |
| **Rust** | `muxi` | `cargo add muxi` |
| **C++** | `muxi` | CMake / vcpkg |

## Features (all SDKs)

- HMAC authentication for ServerClient, key-based auth for FormationClient
- Automatic idempotency headers (`X-Muxi-Idempotency-Key`) on every request
- Exponential backoff retries on 429/5xx errors (respects `Retry-After`)
- First-class streaming support (SSE) for chat, deploy, and logs
- Typed error handling (AuthenticationError, RateLimitError, NotFoundError, etc.)
- Request metadata (request_id, timestamp) for debugging

---

## Go SDK

### Installation

```bash
go get github.com/muxi-ai/muxi-go
```

### ServerClient (formation management)

```go
import "github.com/muxi-ai/muxi-go"

server := muxi.NewServerClient(&muxi.ServerConfig{
    URL:       os.Getenv("MUXI_SERVER_URL"),
    KeyID:     os.Getenv("MUXI_KEY_ID"),
    SecretKey: os.Getenv("MUXI_SECRET_KEY"),
})

// List formations
formations, _ := server.ListFormations(ctx)

// Deploy
result, _ := server.DeployFormation(ctx, &muxi.DeployRequest{
    BundlePath: "my-bot.tar.gz",
})

// Deploy with streaming progress
events, _ := server.DeployFormationStreaming(ctx, &muxi.DeployRequest{
    BundlePath: "my-bot.tar.gz",
})
for event := range events {
    fmt.Printf("[%s] %s\n", event.Stage, event.Message)
}

// Other: GetFormation, UpdateFormation, StopFormation, StartFormation,
//        RestartFormation, RollbackFormation, DeleteFormation, CancelUpdate,
//        Status, Health, GetServerLogs, GetFormationLogs, StreamFormationLogs
```

### FormationClient (chat and runtime)

```go
// Direct mode (development)
client := muxi.NewFormationClient(&muxi.FormationConfig{
    FormationID: "my-bot",
    URL:         "http://localhost:8001",
    ClientKey:   os.Getenv("MUXI_CLIENT_KEY"),
})

// Proxy mode (production, via MUXI server)
client = muxi.NewFormationClient(&muxi.FormationConfig{
    FormationID: "my-bot",
    ServerURL:   os.Getenv("MUXI_SERVER_URL"),
    ClientKey:   os.Getenv("MUXI_CLIENT_KEY"),
})

// Non-streaming chat
resp, _ := client.Chat(ctx, &muxi.ChatRequest{
    Message: "Hello!",
    UserID:  "user-123",
})
fmt.Println(resp.Message)

// Streaming chat
stream, _ := client.ChatStream(ctx, &muxi.ChatRequest{
    Message: "Tell me a story",
    UserID:  "user-123",
})
for chunk := range stream {
    if chunk.Type == "text" {
        fmt.Print(chunk.Text)
    }
}

// Audio chat
resp, _ = client.AudioChat(ctx, &muxi.AudioChatRequest{
    Files:  []muxi.FileAttachment{{Filename: "voice.m4a", Content: base64Data}},
    UserID: "user-123",
})
```

### Error Handling (Go)

```go
resp, err := client.Chat(ctx, req)
if err != nil {
    switch e := err.(type) {
    case *muxi.AuthenticationError:
        log.Fatal("Invalid credentials")
    case *muxi.RateLimitError:
        log.Printf("Retry after %d seconds", e.RetryAfter)
    case *muxi.NotFoundError:
        log.Printf("Not found: %s", e.Message)
    case *muxi.ValidationError:
        log.Printf("Invalid: %s", e.Message)
    }
}
```

---

## Python SDK

### Installation

```bash
pip install muxi-client
```

### Sync Client

```python
from muxi import ServerClient, FormationClient

# Server management
server = ServerClient(
    url="https://server.example.com",
    key_id="<key_id>",
    secret_key="<secret_key>",
)
print(server.status())
formations = server.list_formations()

# Deploy
result = server.deploy_formation(bundle_path="my-bot.tar.gz")

# Formation chat
client = FormationClient(
    server_url="https://server.example.com",
    formation_id="my-bot",
    client_key="<client_key>",
)

# Non-streaming
resp = client.chat({"message": "Hello!", "user_id": "user-123"})
print(resp.message)

# Streaming
for chunk in client.chat_stream({"message": "Tell me a story", "user_id": "user-123"}):
    if chunk.get("type") == "text":
        print(chunk.get("text", ""), end="")
```

### Async Client

```python
import asyncio
from muxi import AsyncServerClient, AsyncFormationClient

async def main():
    server = AsyncServerClient(
        url="https://server.example.com",
        key_id="<key_id>",
        secret_key="<secret_key>",
    )
    print(await server.status())

    client = AsyncFormationClient(
        server_url="https://server.example.com",
        formation_id="my-bot",
        client_key="<client_key>",
    )
    async for chunk in await client.chat_stream({"message": "hi", "user_id": "u1"}):
        print(chunk)

asyncio.run(main())
```

### Direct Formation Connection (Python)

```python
# Development: connect directly to formation
client = FormationClient(
    url="http://localhost:8001",       # Direct to formation
    client_key="<client_key>",
)

# Or explicit base URL
client = FormationClient(
    base_url="http://localhost:9012/v1",
    client_key="<client_key>",
)
```

---

## TypeScript SDK

### Installation

```bash
npm install @muxi-ai/muxi-typescript
```

### ServerClient

```typescript
import { ServerClient } from "@muxi-ai/muxi-typescript";

const server = new ServerClient({
  url: "https://server.example.com",
  keyId: process.env.MUXI_KEY_ID!,
  secretKey: process.env.MUXI_SECRET_KEY!,
});

const status = await server.status();
const formations = await server.listFormations();

// Streaming deploy
for await (const event of server.deployFormationStream({ bundlePath: "bot.tar.gz" })) {
  console.log(`[${event.stage}] ${event.message}`);
}
```

### FormationClient

```typescript
import { FormationClient } from "@muxi-ai/muxi-typescript";

const client = new FormationClient({
  serverUrl: "https://server.example.com",
  formationId: "my-bot",
  clientKey: process.env.MUXI_CLIENT_KEY!,
});

// Non-streaming
const resp = await client.chat({ message: "Hello!", userId: "user-123" });

// Streaming
for await (const chunk of client.chatStream({ message: "Hello!", userId: "user-123" })) {
  if (chunk.type === "text") process.stdout.write(chunk.text);
}
```

### Direct Connection (TypeScript)

```typescript
const client = new FormationClient({
  baseUrl: "http://localhost:8001/v1",  // Direct to formation
  clientKey: "<client_key>",
});
```

---

## All FormationClient Methods

Available across all SDKs (adapted to language conventions):

| Category | Methods |
|----------|---------|
| **Chat** | `Chat`, `ChatStream`, `AudioChat`, `AudioChatStream` |
| **Config** | `Health`, `GetStatus`, `GetConfig`, `GetFormationInfo` |
| **Agents** | `GetAgents`, `GetAgent` |
| **Secrets** | `GetSecrets`, `GetSecret`, `SetSecret`, `DeleteSecret` |
| **MCP** | `GetMCPServers`, `GetMCPServer`, `GetMCPTools` |
| **Sessions** | `GetSessions`, `GetSession`, `GetSessionMessages`, `RestoreSession` |
| **Requests** | `GetRequests`, `GetRequestStatus`, `CancelRequest`, `StreamRequest` |
| **Users** | `ResolveUser`, `GetUserIdentifiers`, `LinkUserIdentifier`, `UnlinkUserIdentifier` |
| **Credentials** | `ListCredentials`, `GetCredential`, `CreateCredential`, `DeleteCredential` |
| **Memory** | `GetMemoryConfig`, `GetMemories`, `AddMemory`, `DeleteMemory`, `GetUserBuffer`, `ClearUserBuffer`, `ClearSessionBuffer`, `ClearAllBuffers`, `GetBufferStats` |
| **Scheduler** | `GetSchedulerConfig`, `GetSchedulerJobs`, `CreateSchedulerJob`, `DeleteSchedulerJob` |
| **Triggers/SOPs** | `GetTriggers`, `GetTrigger`, `FireTrigger`, `GetSOPs`, `GetSOP` |
| **Overlord/LLM** | `GetOverlordConfig`, `GetOverlordPersona`, `GetLLMSettings` |
| **Async/A2A** | `GetAsyncConfig`, `GetAsyncJobs`, `CancelAsyncJob`, `GetA2AConfig` |
| **Logging/Audit** | `GetLoggingConfig`, `GetLoggingDestinations`, `GetAuditLog`, `ClearAuditLog` |
| **Streaming** | `StreamEvents`, `StreamLogs` |

## All ServerClient Methods

| Method | Description |
|--------|-------------|
| `DeployFormation` / `DeployFormationStreaming` | Deploy new formation |
| `ListFormations` | List all formations |
| `GetFormation` | Get formation details |
| `UpdateFormation` / `UpdateFormationStreaming` | Update (blue-green) |
| `StopFormation` | Stop running formation |
| `StartFormation` / `StartFormationStreaming` | Start stopped formation |
| `RestartFormation` / `RestartFormationStreaming` | Restart formation |
| `RollbackFormation` / `RollbackFormationStreaming` | Rollback to previous |
| `DeleteFormation` | Delete formation |
| `CancelUpdate` | Cancel in-progress update |
| `Status` | Server status |
| `Health` / `Ping` | Health/connectivity check |
| `GetServerLogs` | Server audit logs |
| `GetFormationLogs` / `StreamFormationLogs` | Formation logs |

---

## Error Types (all SDKs)

| Error | HTTP | When |
|-------|------|------|
| `AuthenticationError` | 401 | Invalid/missing credentials |
| `AuthorizationError` | 403 | Insufficient permissions |
| `NotFoundError` | 404 | Resource not found |
| `ConflictError` | 409 | Resource already exists |
| `ValidationError` | 422 | Invalid request |
| `RateLimitError` | 429 | Rate limit exceeded (has `retry_after`) |
| `ServerError` | 5xx | Server-side error |
| `ConnectionError` | - | Network issue |

---

## Configuration

### Environment Variables (all SDKs)

| Variable | Description |
|----------|-------------|
| `MUXI_SERVER_URL` | Server URL for ServerClient |
| `MUXI_KEY_ID` | HMAC key ID |
| `MUXI_SECRET_KEY` | HMAC secret key |
| `MUXI_FORMATION_URL` | Direct formation URL |
| `MUXI_ADMIN_KEY` | Formation admin key |
| `MUXI_CLIENT_KEY` | Formation client key |
| `MUXI_DEBUG` | Enable debug logging |

### Connection Modes

1. **Proxy mode** (production): `server_url` + `formation_id` -> routes via `server:7890/api/{id}/v1/`
2. **Direct mode** (development): `url` -> connects directly to `formation:8001/v1/`
3. **Explicit base URL**: `base_url` -> use exact URL provided

### Timeouts

- Regular requests: 30s default (configurable)
- Streaming: no timeout (infinite, until context cancelled or stream ends)

### Retries

- Default: disabled (`max_retries: 0`)
- Retryable: 408, 429, 500, 502, 503, 504
- Backoff: 500ms initial, doubles each retry, max 30s, ±10% jitter
- 429: respects `Retry-After` header
- POST requests are never retried (safety)

---

## Streaming Chunk Types

| Type | Description |
|------|-------------|
| `text` | Text content |
| `tool_call` | Tool invocation |
| `tool_result` | Tool result |
| `agent_handoff` | Agent delegation |
| `thinking` | Reasoning content |
| `error` | Error during stream |
| `done` | Stream complete |
