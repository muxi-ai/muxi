---
title: Swift SDK
description: Native Swift client for MUXI formations
---
# Swift SDK

**GitHub:** [`muxi-ai/muxi-swift`](https://github.com/muxi-ai/muxi-swift)

## Installation

Add the package in Xcode or `Package.swift`:

```swift
.package(url: "https://github.com/muxi-ai/muxi-swift.git", from: "0.1.0")
```

## Formation Client

```swift
import Muxi

let client = try FormationClient(config: FormationConfig(
    formationId: "my-assistant",
    serverUrl: "http://localhost:7890",
    clientKey: "fmc_...",
    adminKey: "fma_..."
))

let health = try await client.health()
print(health ?? [:])
```

Set `mode: "draft"` for local draft routes. Set `baseUrl` to a Runtime's `/v1`
URL for a direct connection.

## Chat

```swift
let response = try await client.chat(
    ["message": "Hello!", "session_id": "sess_abc123"],
    userId: "user_123"
)
print(response?["response"] ?? "")
```

## Streaming

The SDK exposes raw `SseEvent` values:

```swift
for try await event in await client.chatStream(
    ["message": "Tell me a story"],
    userId: "user_123"
) {
    guard event.event == "message",
          let data = event.data.data(using: .utf8),
          let payload = try JSONSerialization.jsonObject(with: data) as? [String: Any],
          let token = payload["token"] as? String else { continue }
    print(token, terminator: "")
}
```

`parseUiWidgets(event)` extracts widgets from a named `ui` event. Stream error
frames are raised as SDK errors.

## Sessions and Memory

```swift
let sessions = try await client.getSessions("user_123", limit: 50)
let messages = try await client.getSessionMessages("sess_abc123", userId: "user_123")
let memories = try await client.getMemories("user_123")
try await client.addMemory("user_123", type: "preference", detail: "User prefers Swift")
try await client.clearUserBuffer("user_123")
```

## Server Client

```swift
let server = ServerClient(config: ServerConfig(
    url: "http://localhost:7890",
    keyId: "muxi_pk_...",
    secretKey: "muxi_sk_..."
))

let formations = try await server.listFormations()
try await server.stopFormation("my-assistant")
try await server.startFormation("my-assistant")
try await server.rollbackFormation("my-assistant")
```

Deploy and update methods accept a formation ID and the corresponding Server
RPC payload dictionary.

## Error Handling

```swift
do {
    _ = try await client.chat(["message": "Hello!"], userId: "user_123")
} catch let MuxiError.authentication(_, message, _, _) {
    print("Authentication failed: \(message)")
} catch let error as MuxiError {
    print(error.localizedDescription)
}
```

## Platform Support

- iOS 15+
- macOS 12+
- tvOS 15+
- watchOS 8+
- Linux with Swift 5.9+

## Learn More

- [SDK Overview](README.md)
- [Streaming Responses](../deep-dives/real-time-streaming.md)
- [API Reference](../reference/api-reference.md)
