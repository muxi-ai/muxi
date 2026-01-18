---
title: SDKs
description: Official client libraries for MUXI
---
# SDKs

## Official client libraries for MUXI

Native SDKs for Python, TypeScript, and Go. Build custom UIs, manage user credentials, handle observability events, and control formations programmatically.

## Why Use the SDKs?

The SDKs are how developers build products on top of MUXI:

| Use Case | What SDKs Enable |
|----------|------------------|
| **Custom Chat UIs** | Build your own chat interface instead of using the default |
| **Credential Management** | Let users add API keys/tokens via your UI (not via chat) |
| **Observability Dashboards** | Subscribe to 350+ event types for monitoring and debugging |
| **User Management** | Manage scheduled tasks, memory, and sessions per user |
| **Formation Control** | Deploy, start, stop, restart formations programmatically |

> **Security note:** When user credentials are required for tools (GitHub, Gmail, etc.), developers can configure whether users provide them via chat or via a dedicated credentials page. The SDK lets you build that credentials page.

---

## Quick Install

:::: cols=3

(python.md)[[card]]

#### Python
```bash
pip install muxi-client
```

[>] [`github.com/muxi-ai/muxi-python`](https://github.com/muxi-ai/muxi-python)

[[/card]]

(typescript.md)[[card]]
#### TypeScript
```bash
npm install @muxi-ai/muxi-typescript
```

[>] [GitHub](https://github.com/muxi-ai/muxi-typescript)

[[/card]]

(go.md)[[card]]
#### Go
```bash
go get github.com/muxi-ai/muxi-go
```

[>] [GitHub](https://github.com/muxi-ai/muxi-go)

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

### Multi-Identity Users

Link multiple identifiers (email, Slack ID, etc.) to a single user:

[[tabs]]

[[tab Python]]
```python
# Link email, Slack ID, and internal ID to same user
result = formation.associate_user_identifiers(
    identifiers=[
        "alice@email.com",
        {"identifier": "U12345ABC", "type": "slack"},
        ("user_123", "internal"),
    ],
    user_id=None,  # Create new user (or pass existing user ID)
)
print(f"User ID: {result['muxi_user_id']}")

# Now all identifiers resolve to the same user
formation.chat("Hello", user_id="alice@email.com")  # Same user
formation.chat("Hello", user_id="U12345ABC")        # Same user
formation.chat("Hello", user_id="user_123")         # Same user
```
[[/tab]]

[[tab TypeScript]]
```typescript
// Link identifiers to same user
const result = await formation.associateUserIdentifiers({
  identifiers: [
    "alice@email.com",
    { identifier: "U12345ABC", type: "slack" },
  ],
  userId: null,  // Create new user
});
console.log(`User ID: ${result.muxiUserId}`);
```
[[/tab]]

[[tab Go]]
```go
result, _ := client.AssociateUserIdentifiers(ctx, &muxi.AssociateRequest{
    Identifiers: []interface{}{
        "alice@email.com",
        muxi.TypedIdentifier{Identifier: "U12345ABC", Type: "slack"},
    },
    UserID: nil,
})
fmt.Printf("User ID: %s\n", result.MuxiUserID)
```
[[/tab]]

[[/tabs]]

**Why?** Users interact via multiple channels (Slack, email, web). Multi-identity ensures they get the same memory and context everywhere.

[Learn more: Multi-Identity Users →](../concepts/multi-identity.md)

### Session Restore

Restore conversation history from your external storage:

[[tabs]]

[[tab Python]]
```python
# Restore session from your database
messages = your_db.get_messages(session_id="sess_abc123")

formation.restore_session(
    session_id="sess_abc123",
    messages=[
        {"role": "user", "content": "Hello", "timestamp": "..."},
        {"role": "assistant", "content": "Hi!", "timestamp": "..."},
    ],
    user_id="alice@email.com"
)

# User continues with full context
```
[[/tab]]

[[tab TypeScript]]
```typescript
await formation.restoreSession({
  sessionId: "sess_abc123",
  messages: [
    { role: "user", content: "Hello", timestamp: "..." },
    { role: "assistant", content: "Hi!", timestamp: "..." },
  ],
  userId: "alice@email.com",
});
```
[[/tab]]

[[tab Go]]
```go
client.RestoreSession(ctx, &muxi.RestoreRequest{
    SessionID: "sess_abc123",
    Messages: []muxi.Message{
        {Role: "user", Content: "Hello", Timestamp: "..."},
        {Role: "assistant", Content: "Hi!", Timestamp: "..."},
    },
    UserID: "alice@email.com",
})
```
[[/tab]]

[[/tabs]]

**Why?** MUXI's buffer is ephemeral. For persistent chat history (like ChatGPT's sidebar), persist messages yourself and restore when users return.

[Learn more: Sessions →](../concepts/sessions.md)

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
