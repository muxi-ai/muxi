---
title: SDKs
description: Official client libraries for MUXI
---
# SDKs

## Official client libraries for MUXI

Native SDKs for 12 languages. Build custom UIs, manage user credentials, handle observability events, and control formations programmatically.

## Why Use the SDKs?

The SDKs are how developers build products on top of MUXI:

| Use Case | What SDKs Enable |
|----------|------------------|
| **Custom Chat UIs** | Build your own chat interface instead of using the default |
| **Credential Management** | Let users add API keys/tokens via your UI (not via chat) |
| **Observability Dashboards** | Subscribe to 350+ event types for monitoring and debugging |
| **User Management** | Manage scheduled tasks, memory, and sessions per user |
| **Formation Control** | Deploy, start, stop, restart formations programmatically |

> [!TIP]
> **Security note:** When user credentials are required for tools (GitHub, Gmail, etc.), developers can configure whether users provide them via chat or via a dedicated credentials page. The SDK lets you build that credentials page.


## Quick Install


:::: cols=2

(python-sdk.md)[[card]]

<svg viewBox="0 0 32 32" xmlns="http://www.w3.org/2000/svg"><path d="m16.4 0c-1.3 0-2.2.1-3.3.3-3.2.6-3.8 1.7-3.8 3.9v3.3h7.6v1.7h-11.6c-2.2 0-4.2 1-4.8 3.6-.7 2.9-.7 4.7 0 7.7.6 2.2 1.8 3.9 4 3.9h3.1v-4.3c0-2.5 2.3-5 4.9-5h6.1c2.1 0 4.2-1.6 4.2-3.7v-7.2c0-2.1-1.5-3.6-3.6-3.9 0 0-1.5-.3-2.8-.3zm-4.2 3.4c.7 0 1.3.6 1.3 1.3s-.6 1.3-1.3 1.3-1.3-.6-1.3-1.3.6-1.3 1.3-1.3z" fill="#0277bd"/><path d="m15.6 32c1.3 0 2.2-.1 3.3-.3 3.2-.6 3.8-1.7 3.8-3.9v-3.3h-7.6v-1.7h11.5c2.2 0 4.2-1 4.8-3.6.7-2.9.7-4.7 0-7.7-.6-2.2-1.8-3.9-4-3.9h-3.1v4.3c0 2.5-2.3 5-4.9 5h-6.1c-2.1 0-4.2 1.6-4.2 3.7v7.2c0 2.1 1.5 3.6 3.6 3.9 0 0 1.5.3 2.8.3zm4.2-3.4c-.7 0-1.3-.6-1.3-1.3s.6-1.3 1.3-1.3 1.3.6 1.3 1.3-.6 1.3-1.3 1.3z" fill="#ffc107"/></svg>

#### Python

Works in scripts, servers, notebooks, and serverless. Python 3.10+

```
pip install muxi-sdk
```

Learn more & install ›

[[/card]]

(typescript-sdk.md)[[card]]

<svg viewBox="0 0 32 32" xmlns="http://www.w3.org/2000/svg"><rect fill="#3178c6" height="32" rx="3.1" width="32"/><rect fill="#3178c6" height="32" rx="3.1" width="32"/><path d="m19.8 25.5v3.1c.5.3 1.1.5 1.8.6s1.4.2 2.2.2 1.5 0 2.1-.2c.7-.1 1.3-.4 1.8-.7s.9-.8 1.2-1.3.4-1.2.4-2 0-1.1-.3-1.5-.4-.8-.7-1.1-.7-.6-1.1-.9-1-.5-1.5-.7c-.4-.2-.8-.3-1.1-.5s-.6-.3-.8-.5-.4-.3-.5-.5-.2-.4-.2-.6 0-.4.2-.6.3-.3.5-.4.4-.2.7-.3c.3 0 .6-.1 1-.1s.5 0 .8 0 .6 0 .9.2c.3 0 .6.2.9.3s.5.3.8.4v-2.9c-.5-.2-1-.3-1.6-.4s-1.2-.1-1.9-.1-1.4 0-2.1.2-1.3.4-1.8.7-.9.8-1.2 1.3-.4 1.2-.4 1.9.3 1.7.8 2.4 1.4 1.2 2.5 1.7c.4.2.8.3 1.2.5s.7.3 1 .5.5.4.6.6c.2.2.2.5.2.7s0 .4-.1.6-.2.3-.4.4-.4.2-.7.3c-.3 0-.6.1-1 .1-.7 0-1.3-.1-2-.4-.7-.2-1.3-.6-1.8-1.1zm-5.3-7.7h4v-2.6h-11.1v2.6h4v11.4h3.2v-11.4z" fill="#fff" fill-rule="evenodd"/></svg>

#### TypeScript

Works on your server (Node.js/Bun), in your browsers, and edge.
```
npm install @muxi/sdk
```

Learn more & install ›

[[/card]]

(go-sdk.md)[[card]]

<svg viewBox="0 0 32 32" xmlns="http://www.w3.org/2000/svg"><path d="m26.8 36.8-3-3-2 2 3 3c.5.5 1.4.5 2 0s.5-1.4 0-2zm2.8-16.8-3-3-2 2 3 3c.5.5 1.4.5 2 0s.5-1.4 0-2zm-24.1 16.8 3-3 2 2-3 3c-.5.5-1.4.5-2 0s-.5-1.4 0-2zm-3.1-16.8 3-3 2 2-3 3c-.5.5-1.4.5-2 0s-.5-1.4 0-2z" fill="#ffcc80"/><path d="m29.1 4.7c0-1.8-1.1-2.8-3-2.8s-3.5 2.4-3.5 4.2 1.8 1.4 2.8 1.4c1.9 0 3.7-1 3.7-2.8zm-26.2 0c0-1.8 1.1-2.8 3-2.8s3.5 2.4 3.5 4.2-1.8 1.4-2.8 1.4c-1.9 0-3.7-1-3.7-2.8z" fill="#4dd0e1"/><path d="m26.3 3.7c-.5 0-.9.4-.9.9s.4.9.9.9.9-.4.9-.9-.4-.9-.9-.9zm-20.6 0c-.5 0-.9.4-.9.9s.4.9.9.9.9-.4.9-.9-.4-.9-.9-.9z" fill="#424242"/><path d="m28.2 28.9c0 4.5-3 9.3-12.4 9.3s-11.8-4.9-11.8-9.3.9-5.4.9-9.3v-9.3c-.1-4.5 2.8-10.3 10.8-10.3s11.5 3.7 11.5 9.3-.2 5.1 0 9.3c.2 3.3.9 7.5.9 10.3z" fill="#4dd0e1"/><path d="m20.7 2.8c-2.1 0-3.7 1.7-3.7 3.7s1.7 3.7 3.7 3.7 3.7-1.7 3.7-3.7-1.7-3.7-3.7-3.7zm-9.3 0c-2.1 0-3.7 1.7-3.7 3.7s1.7 3.7 3.7 3.7 3.7-1.7 3.7-3.7-1.7-3.7-3.7-3.7z" fill="#f5f5f5"/><path d="m16 15.9c0 .5.4.9.9.9s.9-.4.9-.9v-2.8h-1.9v2.8zm-1.8 0c0 .5.4.9.9.9s.9-.4.9-.9v-2.8h-1.9v2.8z" fill="#eee"/><path d="m18.4 14c-.4 0-.6 0-.9-.2-.9-.3-1.9-.3-2.8 0-.3.1-.5.2-.9.2-1.2 0-1.4-.9-1.4-1.4 0-1.4 1.4-2.3 2.8-2.3h1.9c1.4 0 2.8.9 2.8 2.3s-.2 1.4-1.4 1.4z" fill="#ffcc80"/><path d="m18.8 5.6c-.5 0-.9.4-.9.9s.4.9.9.9.9-.4.9-.9-.4-.9-.9-.9zm-9.3 0c-.5 0-.9.4-.9.9s.4.9.9.9.9-.4.9-.9-.4-.9-.9-.9zm6.5 3.7c-1 0-1.9.4-1.9.9s.8.9 1.9.9 1.9-.4 1.9-.9-.8-.9-1.9-.9z" fill="#424242"/></svg>

#### Go

Works in services, CLIs, serverless, and embedded systems.

```
go get github.com/muxi-ai/muxi-go
```

Learn more & install ›

[[/card]]

::::

**Also available:** [Ruby](ruby-sdk.md) | [PHP](php-sdk.md) | [C#](csharp-sdk.md) | [Java](java-sdk.md) | [Kotlin](kotlin-sdk.md) | [Swift](swift-sdk.md) | [Dart](dart-sdk.md) | [Rust](rust-sdk.md) | [C++](cpp-sdk.md)

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


## Local Development

When developing locally with `muxi up`, use the `mode="draft"` parameter to route requests through the `/draft/` endpoint:

[[tabs]]

[[tab Python]]
```python
# Local development (with muxi up)
formation = FormationClient(
    server_url="http://localhost:7890",
    formation_id="my-assistant",
    mode="draft",  # Uses /draft/ prefix
    client_key="your_client_key",
)
```
[[/tab]]

[[tab TypeScript]]
```typescript
// Local development (with muxi up)
const formation = new FormationClient({
  serverUrl: "http://localhost:7890",
  formationId: "my-assistant",
  mode: "draft",  // Uses /draft/ prefix
  clientKey: "your_client_key",
});
```
[[/tab]]

[[tab Go]]
```go
// Local development (with muxi up)
client := muxi.NewFormationClient(&muxi.FormationConfig{
    FormationID: "my-assistant",
    ServerURL:   "http://localhost:7890",
    Mode:        "draft",  // Uses /draft/ prefix
    ClientKey:   "your_client_key",
})
```
[[/tab]]

[[/tabs]]

**Workflow:**
1. Run `muxi up` to start your formation in draft mode
2. Use `mode="draft"` in your SDK client during development
3. Run `muxi deploy` to deploy to production
4. Remove `mode="draft"` (or don't set it) - defaults to `mode="live"` which uses `/api/`

> [!TIP]
> The `mode` parameter only affects URL routing. All other functionality is identical between draft and live modes.


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

> [!TIP]
> **Browser usage:** The TypeScript SDK's FormationClient works directly in browsers. Use the `clientKey` for browser apps - it's safe to expose and has limited permissions. Keep the `adminKey` server-side only.

### ServerClient

For managing the MUXI server:

- Deploy formations
- Start/stop/restart formations
- List formations
- Server health & logs

**Authentication:** HMAC signature (`key_id` + `secret_key`)


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

# Add memory (user_id, mem_type, detail)
formation.add_memory(user_id="user_123", mem_type="preference", detail="User prefers Python")

# Clear buffer
formation.clear_user_buffer(user_id="user_123")
```
[[/tab]]

[[tab TypeScript]]
```typescript
// Get memories
const memories = await formation.getMemories("user_123");

// Add memory (userId, type, detail)
await formation.addMemory("user_123", "preference", "User prefers TypeScript");

// Clear buffer
await formation.clearUserBuffer("user_123");
```
[[/tab]]

[[tab Go]]
```go
// Get memories
memories, _ := client.GetMemories(ctx, "user_123")

// Add memory (ctx, userId, type, detail)
client.AddMemory(ctx, "user_123", "preference", "User prefers Go")

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


## Configuration

| Setting | Python | TypeScript | Go | Default |
|---------|--------|------------|-----|---------|
| Timeout | `timeout=30` | `timeout: 30000` | 30s built-in | 30s |
| Retries | `max_retries=3` | `maxRetries: 3` | 3 built-in | 3 |
| Debug | `debug=True` | `debug: true` | - | Off |


## Learn More

**Core SDKs:**
- [Python SDK →](python-sdk.md)
- [TypeScript SDK →](typescript-sdk.md)
- [Go SDK →](go-sdk.md)

**Additional SDKs:**
- [Ruby SDK →](ruby-sdk.md)
- [PHP SDK →](php-sdk.md)
- [C# SDK →](csharp-sdk.md)
- [Java SDK →](java-sdk.md)
- [Kotlin SDK →](kotlin-sdk.md)
- [Swift SDK →](swift-sdk.md)
- [Dart SDK →](dart-sdk.md)
- [Rust SDK →](rust-sdk.md)
- [C++ SDK →](cpp-sdk.md)

**Reference:**
- [API Reference →](../reference/api-reference.md)
