---
title: Swift SDK
description: Native Swift client for MUXI formations
---
# Swift SDK

## Swift access to your agents

Build Swift applications for iOS, macOS, and server-side Swift that interact with MUXI formations. Full support for chat, streaming with async/await, and all Formation API operations.

**GitHub:** [`muxi-ai/muxi-swift`](https://github.com/muxi-ai/muxi-swift)

## Installation

### Swift Package Manager

Add to your `Package.swift`:

```swift
dependencies: [
    .package(url: "https://github.com/muxi-ai/muxi-swift.git", from: "0.1.0")
]
```

Or in Xcode: File > Add Package Dependencies > Enter the repository URL.

## Quick Start

```swift
import Muxi

let client = FormationClient(
    serverURL: "http://localhost:7890",
    formationID: "my-assistant",
    clientKey: "your_client_key"
)

// Check health
let health = try await client.health()
print(health)
```

## Chat (Streaming)

```swift
for try await event in client.chatStream(message: "Hello!", userID: "user_123") {
    switch event.type {
    case "text":
        print(event.text ?? "", terminator: "")
    case "done":
        break
    default:
        continue
    }
}
```

## Chat (Non-Streaming)

```swift
let response = try await client.chat(message: "Hello!", userID: "user_123")
print(response.response)
```

## Memory

```swift
// Get memories
let memories = try await client.getMemories(userID: "user_123")

// Add memory
try await client.addMemory(userID: "user_123", type: "preference", detail: "User prefers Swift")

// Clear buffer
try await client.clearUserBuffer(userID: "user_123")
```

## Sessions

```swift
// List sessions
let sessions = try await client.getSessions(userID: "user_123")

// Get session messages
let messages = try await client.getSessionMessages(sessionID: "sess_abc123", userID: "user_123")
```

## Server Client

For managing formations (deploy, start, stop):

```swift
import Muxi

let server = ServerClient(
    url: "http://localhost:7890",
    keyID: "muxi_pk_...",
    secretKey: "muxi_sk_..."
)

// List formations
let formations = try await server.listFormations()

// Deploy
try await server.deployFormation(bundlePath: "my-bot.tar.gz")

// Stop/start/restart
try await server.stopFormation(formationID: "my-bot")
try await server.startFormation(formationID: "my-bot")
```

## Error Handling

```swift
import Muxi

do {
    let response = try await client.chat(message: "Hello!", userID: "user_123")
} catch MuxiError.authentication(let message) {
    print("Auth failed: \(message)")
} catch MuxiError.rateLimit(let retryAfter) {
    print("Rate limited, retry after: \(retryAfter)s")
} catch MuxiError.api(let code, let message) {
    print("Error: \(code) - \(message)")
}
```

## Platform Support

- iOS 15.0+
- macOS 12.0+
- tvOS 15.0+
- watchOS 8.0+
- Linux (Swift 5.9+)

## Learn More

- [Full documentation on GitHub](https://github.com/muxi-ai/muxi-swift)
- [API Reference](../reference/api-reference.md)
