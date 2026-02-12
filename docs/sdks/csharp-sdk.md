---
title: C# SDK
description: Native .NET client for MUXI formations
---
# C# SDK

## .NET access to your agents

Build .NET applications that interact with MUXI formations. Full support for chat, streaming, async/await, and all Formation API operations.

**GitHub:** [`muxi-ai/muxi-csharp`](https://github.com/muxi-ai/muxi-csharp)  
**NuGet:** [`Muxi`](https://www.nuget.org/packages/Muxi)

## Installation

```bash
dotnet add package Muxi
```

Or via Package Manager:

```powershell
Install-Package Muxi
```

## Quick Start

```csharp
using Muxi;

var client = new FormationClient(
    serverUrl: "http://localhost:7890",
    formationId: "my-assistant",
    clientKey: "your_client_key"
);

// Check health
var health = await client.HealthAsync();
Console.WriteLine(health);
```

## Chat (Streaming)

```csharp
await foreach (var chunk in client.ChatStreamAsync(
    new ChatRequest { Message = "Hello!" },
    userId: "user_123"))
{
    if (chunk.Type == "text")
        Console.Write(chunk.Text);
    else if (chunk.Type == "done")
        break;
}
```

## Chat (Non-Streaming)

```csharp
var response = await client.ChatAsync(
    new ChatRequest { Message = "Hello!" },
    userId: "user_123"
);
Console.WriteLine(response.Response);
```

## Memory

```csharp
// Get memories
var memories = await client.GetMemoriesAsync("user_123");

// Add memory
await client.AddMemoryAsync("user_123", "preference", "User prefers C#");

// Clear buffer
await client.ClearUserBufferAsync("user_123");
```

## Sessions

```csharp
// List sessions
var sessions = await client.GetSessionsAsync("user_123");

// Get session messages
var messages = await client.GetSessionMessagesAsync("sess_abc123", "user_123");
```

## Server Client

For managing formations (deploy, start, stop):

```csharp
var server = new ServerClient(
    url: "http://localhost:7890",
    keyId: "muxi_pk_...",
    secretKey: "muxi_sk_..."
);

// List formations
var formations = await server.ListFormationsAsync();

// Deploy
await server.DeployFormationAsync("my-bot.tar.gz");

// Stop/start/restart
await server.StopFormationAsync("my-bot");
await server.StartFormationAsync("my-bot");
```

## Error Handling

```csharp
using Muxi.Exceptions;

try
{
    var response = await client.ChatAsync(
        new ChatRequest { Message = "Hello!" },
        userId: "user_123"
    );
}
catch (AuthenticationException e)
{
    Console.WriteLine($"Auth failed: {e.Message}");
}
catch (RateLimitException e)
{
    Console.WriteLine($"Rate limited, retry after: {e.RetryAfter}s");
}
catch (MuxiException e)
{
    Console.WriteLine($"Error: {e.Code} - {e.Message}");
}
```

## Learn More

- [Full documentation on GitHub](https://github.com/muxi-ai/muxi-csharp)
- [API Reference](../reference/api-reference.md)
