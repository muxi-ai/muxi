---
title: Kotlin SDK
description: Native Kotlin client for MUXI formations
---
# Kotlin SDK

**GitHub:** [`muxi-ai/muxi-kotlin`](https://github.com/muxi-ai/muxi-kotlin)

## Installation

```kotlin
implementation("org.muxi:muxi-kotlin:<version>")
```

Use the latest published version.

## Formation Client

```kotlin
import org.muxi.sdk.FormationClient
import org.muxi.sdk.FormationConfig

val client = FormationClient(FormationConfig(
    serverUrl = "http://localhost:7890",
    formationId = "my-assistant",
    clientKey = "fmc_...",
    adminKey = "fma_..."
))

println(client.health())
```

Set `mode = "draft"` for local drafts, or `baseUrl` for a direct Runtime `/v1`
connection.

## Chat

```kotlin
val response = client.chat(
    mapOf("message" to "Hello!", "session_id" to "sess_abc123"),
    userId = "user_123"
)
println(response)
```

## Streaming

The SDK exposes raw `SseEvent` values:

```kotlin
import kotlinx.serialization.json.Json
import kotlinx.serialization.json.contentOrNull
import kotlinx.serialization.json.jsonObject
import kotlinx.serialization.json.jsonPrimitive

client.chatStream(
    mapOf("message" to "Tell me a story"),
    userId = "user_123"
).collect { event ->
    if (event.event == "message") {
        val token = Json.parseToJsonElement(event.data)
            .jsonObject["token"]?.jsonPrimitive?.contentOrNull
        if (token != null) print(token)
    }
}
```

`parseUiWidgets(event)` extracts widgets from a named `ui`
event. Stream errors are raised as SDK exceptions.

## Sessions and Memory

```kotlin
val sessions = client.getSessions("user_123", limit = 50)
val messages = client.getSessionMessages("sess_abc123", "user_123")
val memories = client.getMemories("user_123")
client.addMemory("user_123", "preference", "User prefers Kotlin")
client.clearUserBuffer("user_123")
```

## Server Client

Construct `ServerClient` with `ServerConfig`, then use `listFormations`,
`stopFormation`, `startFormation`, and `rollbackFormation`. Deploy and update
methods accept a formation ID and the corresponding Server RPC payload.

## Learn More

- [SDK Overview](README.md)
- [Streaming Responses](../deep-dives/real-time-streaming.md)
- [API Reference](../reference/api-reference.md)
