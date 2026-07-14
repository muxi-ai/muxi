---
title: C# SDK
description: Native .NET client for MUXI formations
---
# C# SDK

Use the C# SDK to call Formation APIs and manage MUXI Server from .NET.

**GitHub:** [`muxi-ai/muxi-csharp`](https://github.com/muxi-ai/muxi-csharp)  
**NuGet:** [`Muxi`](https://www.nuget.org/packages/Muxi)

## Installation

```bash
dotnet add package Muxi
```

## Formation Client

```csharp
using Muxi;

using var client = new FormationClient(new FormationConfig
{
    ServerUrl = "http://localhost:7890",
    FormationId = "my-assistant",
    ClientKey = "fmc_...",
    AdminKey = "fma_..."
});

var health = await client.HealthAsync();
Console.WriteLine(health);
```

For local drafts, set `Mode = "draft"`. For a direct Runtime connection, set
`BaseUrl` to the Runtime's `/v1` base URL.

## Chat

Formation methods return `JsonNode` values:

```csharp
var response = await client.ChatAsync(
    new Dictionary<string, object>
    {
        ["message"] = "Hello!",
        ["session_id"] = "sess_abc123"
    },
    userId: "user_123"
);

Console.WriteLine(response?["response"]);
```

## Streaming

The C# SDK exposes raw SSE events. Parse message-event data to read each token:

```csharp
using System.Text.Json;

await foreach (var evt in client.ChatStreamAsync(
    new Dictionary<string, object> { ["message"] = "Tell me a story" },
    userId: "user_123"))
{
    if (evt.Event == "message")
    {
        using var document = JsonDocument.Parse(evt.Data);
        if (document.RootElement.TryGetProperty("token", out var token) &&
            token.ValueKind == JsonValueKind.String)
        {
            Console.Write(token.GetString());
        }
    }
    else if (evt.Event == "done")
    {
        break;
    }
}
```

`FormationClient.ParseUiWidgets(evt)` extracts the optional widget array from
an `ui` event. Error frames are raised as SDK exceptions.

## Sessions and Requests

```csharp
var sessions = await client.GetSessionsAsync("user_123", limit: 50);
var messages = await client.GetSessionMessagesAsync("sess_abc123", "user_123");
var status = await client.GetRequestStatusAsync("req_abc123", "user_123");
await client.CancelRequestAsync("req_abc123", "user_123");
```

## Memory

```csharp
var memories = await client.GetMemoriesAsync("user_123");
await client.AddMemoryAsync("user_123", "preference", "User prefers C#");
await client.ClearUserBufferAsync("user_123");
```

## Server Client

```csharp
using var server = new ServerClient(new ServerConfig
{
    Url = "http://localhost:7890",
    KeyId = "muxi_pk_...",
    SecretKey = "muxi_sk_..."
});

var formations = await server.ListFormationsAsync();
await server.StopFormationAsync("my-assistant");
await server.StartFormationAsync("my-assistant");
await server.RollbackFormationAsync("my-assistant");
```

Deployment and update calls accept a formation ID and the corresponding Server
RPC payload.

## Error Handling

```csharp
try
{
    await client.ChatAsync(
        new Dictionary<string, object> { ["message"] = "Hello!" },
        userId: "user_123"
    );
}
catch (AuthenticationException ex)
{
    Console.Error.WriteLine(ex.Message);
}
catch (MuxiException ex)
{
    Console.Error.WriteLine($"{ex.ErrorCode}: {ex.Message}");
}
```

## Learn More

- [SDK Overview](README.md)
- [Streaming Responses](../deep-dives/real-time-streaming.md)
- [API Reference](../reference/api-reference.md)
