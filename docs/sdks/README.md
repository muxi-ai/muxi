---
title: SDKs
description: Client libraries for Python, TypeScript, and Go
---

# SDKs

## Native client libraries for every language

Build applications that interact with MUXI formations using idiomatic code in your language of choice.


## Available SDKs

:::: cols=3

(python.md)[[card]]
#### Python
`pip install muxi-sdk`

Async support, streaming, type hints.
[[/card]]

(typescript.md)[[card]]
#### TypeScript
`npm install @muxi/sdk`

Full typing, React hooks, streaming.
[[/card]]

(go.md)[[card]]
#### Go
`go get github.com/muxi-ai/sdk-go`

Channels, context, zero dependencies.
[[/card]]

::::

---

## Quick Start

[[tabs]]

[[tab Python]]
```python
from muxi import Formation

formation = Formation(
    url="http://localhost:8001",
    client_key="fmc_..."
)

response = formation.chat("Hello!")
print(response.text)
```
[[/tab]]

[[tab TypeScript]]
```typescript
import { Formation } from '@muxi/sdk';

const formation = new Formation({
  url: 'http://localhost:8001',
  clientKey: 'fmc_...'
});

const response = await formation.chat('Hello!');
console.log(response.text);
```
[[/tab]]

[[tab Go]]
```go
import "github.com/muxi-ai/sdk-go/muxi"

formation := muxi.NewFormation(muxi.Config{
    URL:       "http://localhost:8001",
    ClientKey: "fmc_...",
})

response, _ := formation.Chat("Hello!")
fmt.Println(response.Text)
```
[[/tab]]

[[/tabs]]

---

## Common Operations

### Chat with Streaming

[[tabs]]

[[tab Python]]
```python
for chunk in formation.chat_stream("Tell me a story"):
    print(chunk.text, end="", flush=True)
```
[[/tab]]

[[tab TypeScript]]
```typescript
for await (const chunk of formation.chatStream('Tell me a story')) {
  process.stdout.write(chunk.text);
}
```
[[/tab]]

[[tab Go]]
```go
stream, _ := formation.ChatStream("Tell me a story")
for chunk := range stream.Chunks {
    fmt.Print(chunk.Text)
}
```
[[/tab]]

[[/tabs]]

### Session Management

[[tabs]]

[[tab Python]]
```python
# Create session
session = formation.create_session()

# Chat with session context
response = formation.chat(
    "Remember my name is Alice",
    session_id=session.id
)

# Later...
response = formation.chat(
    "What's my name?",
    session_id=session.id
)
# "Your name is Alice"
```
[[/tab]]

[[tab TypeScript]]
```typescript
// Create session
const session = await formation.createSession();

// Chat with session context
await formation.chat('Remember my name is Alice', {
  sessionId: session.id
});

// Later...
const response = await formation.chat("What's my name?", {
  sessionId: session.id
});
// "Your name is Alice"
```
[[/tab]]

[[tab Go]]
```go
// Create session
session, _ := formation.CreateSession()

// Chat with session context
formation.ChatWithOptions("Remember my name is Alice", muxi.ChatOptions{
    SessionID: session.ID,
})

// Later...
response, _ := formation.ChatWithOptions("What's my name?", muxi.ChatOptions{
    SessionID: session.ID,
})
// "Your name is Alice"
```
[[/tab]]

[[/tabs]]

### Target Specific Agent

[[tabs]]

[[tab Python]]
```python
response = formation.chat(
    "Research AI trends",
    agent="researcher"
)
```
[[/tab]]

[[tab TypeScript]]
```typescript
const response = await formation.chat('Research AI trends', {
  agent: 'researcher'
});
```
[[/tab]]

[[tab Go]]
```go
response, _ := formation.ChatWithOptions("Research AI trends", muxi.ChatOptions{
    Agent: "researcher",
})
```
[[/tab]]

[[/tabs]]

### Fire Triggers

[[tabs]]

[[tab Python]]
```python
response = formation.trigger(
    "github-issue",
    data={
        "repository": "muxi/runtime",
        "issue": {"number": 123, "title": "Bug report"}
    }
)
```
[[/tab]]

[[tab TypeScript]]
```typescript
const response = await formation.trigger('github-issue', {
  data: {
    repository: 'muxi/runtime',
    issue: { number: 123, title: 'Bug report' }
  }
});
```
[[/tab]]

[[tab Go]]
```go
response, _ := formation.Trigger("github-issue", muxi.TriggerData{
    "repository": "muxi/runtime",
    "issue": map[string]interface{}{
        "number": 123,
        "title":  "Bug report",
    },
})
```
[[/tab]]

[[/tabs]]

---

## Server Client

For server management operations (deploy, restart, etc.):

[[tabs]]

[[tab Python]]
```python
from muxi import ServerClient

server = ServerClient(
    url="http://localhost:7890",
    key_id="MUXI_...",
    secret_key="sk_..."
)

# List formations
formations = server.list_formations()

# Deploy
server.deploy("/path/to/formation")

# Restart
server.restart_formation("my-assistant")
```
[[/tab]]

[[tab TypeScript]]
```typescript
import { ServerClient } from '@muxi/sdk';

const server = new ServerClient({
  url: 'http://localhost:7890',
  keyId: 'MUXI_...',
  secretKey: 'sk_...'
});

// List formations
const formations = await server.listFormations();

// Deploy
await server.deploy('/path/to/formation');

// Restart
await server.restartFormation('my-assistant');
```
[[/tab]]

[[tab Go]]
```go
server := muxi.NewServerClient(muxi.ServerConfig{
    URL:       "http://localhost:7890",
    KeyID:     "MUXI_...",
    SecretKey: "sk_...",
})

// List formations
formations, _ := server.ListFormations()

// Deploy
server.Deploy("/path/to/formation")

// Restart
server.RestartFormation("my-assistant")
```
[[/tab]]

[[/tabs]]

---

## Authentication

### Formation Client (Chat)

Uses simple API keys:

```python
Formation(
    client_key="fmc_..."    # Limited access (chat only)
    # or
    admin_key="fma_..."     # Full access
)
```

### Server Client (Management)

Uses HMAC signatures (handled automatically by SDK):

```python
ServerClient(
    key_id="MUXI_...",
    secret_key="sk_..."
)
```

---

## Error Handling

[[tabs]]

[[tab Python]]
```python
from muxi import MuxiError, AuthenticationError, FormationError

try:
    response = formation.chat("Hello!")
except AuthenticationError:
    print("Invalid API key")
except FormationError as e:
    print(f"Formation error: {e}")
except MuxiError as e:
    print(f"MUXI error: {e}")
```
[[/tab]]

[[tab TypeScript]]
```typescript
import { MuxiError, AuthenticationError, FormationError } from '@muxi/sdk';

try {
  const response = await formation.chat('Hello!');
} catch (error) {
  if (error instanceof AuthenticationError) {
    console.log('Invalid API key');
  } else if (error instanceof FormationError) {
    console.log(`Formation error: ${error.message}`);
  }
}
```
[[/tab]]

[[tab Go]]
```go
response, err := formation.Chat("Hello!")
if err != nil {
    switch e := err.(type) {
    case *muxi.AuthenticationError:
        log.Println("Invalid API key")
    case *muxi.FormationError:
        log.Printf("Formation error: %v", e)
    }
}
```
[[/tab]]

[[/tabs]]

---

## Next Steps

[+] [Python SDK](python.md) - Full Python reference
[+] [TypeScript SDK](typescript.md) - Full TypeScript reference
[+] [Go SDK](go.md) - Full Go reference
[+] [Build Custom UI](../guides/custom-ui.md) - Frontend integration guide
