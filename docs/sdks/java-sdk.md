---
title: Java SDK
description: Native Java client for MUXI formations
---
# Java SDK

**GitHub:** [`muxi-ai/muxi-java`](https://github.com/muxi-ai/muxi-java)  
**Maven Central:** [`org.muxi:muxi-java`](https://central.sonatype.com/artifact/org.muxi/muxi-java)

## Installation

```kotlin
implementation("org.muxi:muxi-java:<version>")
```

Use the latest published version from Maven Central.

## Formation Client

```java
import org.muxi.sdk.FormationClient;

FormationClient client = new FormationClient(
    "http://localhost:7890",
    "my-assistant",
    "fmc_...",
    null
);

System.out.println(client.health());
```

The fourth constructor argument is the optional admin key. Constructors with a
`mode` argument support `"draft"` for local draft routes. Use
`FormationClient.withBaseUrl(...)` for a direct Runtime `/v1` URL.

## Chat

```java
import com.google.gson.JsonObject;

JsonObject request = new JsonObject();
request.addProperty("message", "Hello!");
request.addProperty("session_id", "sess_abc123");

JsonObject response = client.chat(request, "user_123");
System.out.println(response.get("response").getAsString());
```

## Streaming

The Java SDK exposes raw `SseEvent` records:

```java
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

JsonObject request = new JsonObject();
request.addProperty("message", "Tell me a story");

client.chatStream(request, "user_123", event -> {
    if ("message".equals(event.event())) {
        JsonObject data = JsonParser.parseString(event.data()).getAsJsonObject();
        if (data.has("token") && data.get("token").isJsonPrimitive()) {
            System.out.print(data.get("token").getAsString());
        }
    }
});
```

`FormationClient.parseUiWidgets(event)` extracts widgets from a named `ui`
event. Stream error frames are raised as SDK exceptions.

## Sessions and Memory

```java
var sessions = client.getSessions("user_123", 50);
var messages = client.getSessionMessages("sess_abc123", "user_123");
var memories = client.getMemories("user_123");
client.addMemory("user_123", "preference", "User prefers Java");
client.clearUserBuffer("user_123");
```

## Server Client

```java
import org.muxi.sdk.ServerClient;

ServerClient server = new ServerClient(
    "http://localhost:7890",
    "muxi_pk_...",
    "muxi_sk_..."
);

JsonObject formations = server.listFormations();
server.stopFormation("my-assistant");
server.startFormation("my-assistant");
server.rollbackFormation("my-assistant");
```

Deploy and update methods accept a formation ID and the corresponding Server
RPC `JsonObject` payload.

## Error Handling

```java
import org.muxi.sdk.Errors.AuthenticationException;
import org.muxi.sdk.Errors.MuxiException;

try {
    client.chat(request, "user_123");
} catch (AuthenticationException error) {
    System.err.println(error.getMessage());
} catch (MuxiException error) {
    System.err.println(error.getErrorCode() + ": " + error.getMessage());
}
```

## Learn More

- [SDK Overview](README.md)
- [Streaming Responses](../deep-dives/real-time-streaming.md)
- [API Reference](../reference/api-reference.md)
