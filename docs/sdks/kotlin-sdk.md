---
title: Kotlin SDK
description: Native Kotlin client for MUXI formations
---
# Kotlin SDK

## Idiomatic Kotlin access to your agents

Build Kotlin applications that interact with MUXI formations. Full support for chat, streaming with coroutines, and all Formation API operations.

**GitHub:** [`muxi-ai/muxi-kotlin`](https://github.com/muxi-ai/muxi-kotlin)  
**Maven Central:** [`org.muxi:muxi-kotlin`](https://central.sonatype.com/artifact/org.muxi/muxi-kotlin)

## Installation

### Gradle (Kotlin DSL)

```kotlin
implementation("org.muxi:muxi-kotlin:0.20260212.0")
```

### Gradle (Groovy)

```groovy
implementation 'org.muxi:muxi-kotlin:0.20260212.0'
```

## Quick Start

```kotlin
import org.muxi.sdk.FormationClient

val client = FormationClient(
    serverUrl = "http://localhost:7890",
    formationId = "my-assistant",
    clientKey = "your_client_key"
)

// Check health
println(client.health())
```

## Chat (Streaming with Coroutines)

```kotlin
client.chatStream(ChatRequest(message = "Hello!"), userId = "user_123")
    .collect { event ->
        when (event.type) {
            "text" -> print(event.text)
            "done" -> return@collect
        }
    }
```

## Chat (Non-Streaming)

```kotlin
val response = client.chat(ChatRequest(message = "Hello!"), userId = "user_123")
println(response.response)
```

## Memory

```kotlin
// Get memories
val memories = client.getMemories("user_123")

// Add memory
client.addMemory("user_123", "preference", "User prefers Kotlin")

// Clear buffer
client.clearUserBuffer("user_123")
```

## Sessions

```kotlin
// List sessions
val sessions = client.getSessions("user_123")

// Get session messages
val messages = client.getSessionMessages("sess_abc123", "user_123")
```

## Server Client

For managing formations (deploy, start, stop):

```kotlin
import org.muxi.sdk.ServerClient

val server = ServerClient(
    url = "http://localhost:7890",
    keyId = "muxi_pk_...",
    secretKey = "muxi_sk_..."
)

// List formations
val formations = server.listFormations()

// Deploy
server.deployFormation("my-bot.tar.gz")

// Stop/start/restart
server.stopFormation("my-bot")
server.startFormation("my-bot")
```

## Error Handling

```kotlin
import org.muxi.sdk.exceptions.*

try {
    val response = client.chat(ChatRequest(message = "Hello!"), userId = "user_123")
} catch (e: AuthenticationException) {
    println("Auth failed: ${e.message}")
} catch (e: RateLimitException) {
    println("Rate limited, retry after: ${e.retryAfter}s")
} catch (e: MuxiException) {
    println("Error: ${e.code} - ${e.message}")
}
```

## Response UI Widgets

A streamed response can carry optional [UI widgets](../reference/response-ui-widgets.md)
(choices, links, MCP UI resources) on a `ui` event. Extract them with the
top-level `parseUiWidgets(...)`; it returns an empty list for any non-`ui` or
malformed event, so unknown widgets are safely ignored.

## Idempotency

The client auto-generates an `X-Muxi-Idempotency-Key` on every request, so a
retry of a successful non-streaming mutation replays the original response.
Streaming and failed requests execute again. Cached responses expose the echoed
`idempotency_key` on the unwrapped result. See
[Idempotency](../deep-dives/idempotency.md).

## Learn More

- [Full documentation on GitHub](https://github.com/muxi-ai/muxi-kotlin)
- [API Reference](../reference/api-reference.md)
