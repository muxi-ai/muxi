---
title: Java SDK
description: Native Java client for MUXI formations
---
# Java SDK

## Java access to your agents

Build Java applications that interact with MUXI formations. Full support for chat, streaming, and all Formation API operations.

**GitHub:** [`muxi-ai/muxi-java`](https://github.com/muxi-ai/muxi-java)  
**Maven Central:** [`org.muxi:muxi-java`](https://central.sonatype.com/artifact/org.muxi/muxi-java)

## Installation

### Gradle

```kotlin
implementation("org.muxi:muxi-java:0.20260212.0")
```

### Maven

```xml
<dependency>
    <groupId>org.muxi</groupId>
    <artifactId>muxi-java</artifactId>
    <version>0.20260212.0</version>
</dependency>
```

## Quick Start

```java
import org.muxi.sdk.FormationClient;

FormationClient client = new FormationClient(
    "http://localhost:7890",
    "my-assistant",
    "your_client_key"
);

// Check health
System.out.println(client.health());
```

## Chat (Streaming)

```java
client.chatStream(new ChatRequest("Hello!"), "user_123", event -> {
    if ("text".equals(event.getType())) {
        System.out.print(event.getText());
    } else if ("done".equals(event.getType())) {
        return false; // Stop streaming
    }
    return true; // Continue
});
```

## Chat (Non-Streaming)

```java
ChatResponse response = client.chat(new ChatRequest("Hello!"), "user_123");
System.out.println(response.getResponse());
```

## Memory

```java
// Get memories
List<Memory> memories = client.getMemories("user_123");

// Add memory
client.addMemory("user_123", "preference", "User prefers Java");

// Clear buffer
client.clearUserBuffer("user_123");
```

## Sessions

```java
// List sessions
List<Session> sessions = client.getSessions("user_123");

// Get session messages
List<Message> messages = client.getSessionMessages("sess_abc123", "user_123");
```

## Server Client

For managing formations (deploy, start, stop):

```java
import org.muxi.sdk.ServerClient;

ServerClient server = new ServerClient(
    "http://localhost:7890",
    "muxi_pk_...",
    "muxi_sk_..."
);

// List formations
List<Formation> formations = server.listFormations();

// Deploy
server.deployFormation("my-bot.tar.gz");

// Stop/start/restart
server.stopFormation("my-bot");
server.startFormation("my-bot");
```

## Error Handling

```java
import org.muxi.sdk.exceptions.*;

try {
    ChatResponse response = client.chat(new ChatRequest("Hello!"), "user_123");
} catch (AuthenticationException e) {
    System.out.println("Auth failed: " + e.getMessage());
} catch (RateLimitException e) {
    System.out.println("Rate limited, retry after: " + e.getRetryAfter() + "s");
} catch (MuxiException e) {
    System.out.println("Error: " + e.getCode() + " - " + e.getMessage());
}
```

## Learn More

- [Full documentation on GitHub](https://github.com/muxi-ai/muxi-java)
- [API Reference](../reference/api-reference.md)
