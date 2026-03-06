---
title: Connect via MCP
description: Use your MUXI formation from Claude Desktop, Cursor, and other MCP clients
---

# Connect via MCP

## Use your formation from any MCP client

Every MUXI formation exposes an MCP (Model Context Protocol) server at `/mcp`. This lets you connect directly from Claude Desktop, Cursor, or any MCP-compatible client -- same agents, same memory, same tools, no code required.


## What You Get

The MCP interface exposes your formation's client API as MCP tools:

| Tool | What it does |
|------|-------------|
| `chat` | Send a message to your formation |
| `list_sessions` / `get_session` | Browse conversation history |
| `search_memories` / `create_memory` | Read and write long-term memory |
| `get_buffer_memory` | View recent conversation context |
| `list_sops` | List available SOPs |
| `list_triggers` | List configured triggers |
| `list_requests` / `get_request_status` | Track async requests |
| `list_credential_services` | View available credential services |
| ...and more | All 33 client endpoints as tools |

Admin and internal endpoints are never exposed.


## Authentication

MCP uses the same `X-Muxi-Client-Key` as the REST API. Pass it in your transport headers:

```json
{
  "url": "http://localhost:8001/mcp",
  "headers": {
    "X-Muxi-Client-Key": "fmc_..."
  }
}
```

Without a valid key, tool calls return 401. Listing tools (the MCP handshake) works without auth, but you cannot do anything useful.


## Claude Desktop

Add your formation to `claude_desktop_config.json`:

[[tabs]]

[[tab macOS]]
```
~/Library/Application Support/Claude/claude_desktop_config.json
```
[[/tab]]

[[tab Windows]]
```
%APPDATA%\Claude\claude_desktop_config.json
```
[[/tab]]

[[/tabs]]

```json
{
  "mcpServers": {
    "my-formation": {
      "type": "streamable-http",
      "url": "http://localhost:8001/mcp",
      "headers": {
        "X-Muxi-Client-Key": "fmc_..."
      }
    }
  }
}
```

Restart Claude Desktop. Your formation's tools appear in the tools menu.

> [!NOTE]
> Claude Desktop's `headers` support in `claude_desktop_config.json` requires a recent version. If your version doesn't support it, use the Claude API programmatically instead (see below).


## Cursor

Add the MCP server in Cursor's settings:

1. Open **Settings** > **MCP Servers**
2. Add a new server:
   - **Name:** `my-formation`
   - **Type:** `streamable-http`
   - **URL:** `http://localhost:8001/mcp`
3. Add the header `X-Muxi-Client-Key: fmc_...`

Your formation's tools are now available to Cursor's AI assistant.


## Programmatic (Python)

Use the FastMCP client library:

```bash
pip install fastmcp
```

```python
import asyncio
from fastmcp import Client
from fastmcp.client.transports.http import StreamableHttpTransport

transport = StreamableHttpTransport(
    "http://localhost:8001/mcp/",
    headers={"X-Muxi-Client-Key": "fmc_..."},
)

async def main():
    async with Client(transport) as client:
        # List available tools
        tools = await client.list_tools()
        print(f"{len(tools)} tools available")

        # Chat with your formation
        result = await client.call_tool("chat", {
            "message": "What can you help me with?",
        })
        print(result.content[0].text)

        # List sessions
        result = await client.call_tool("list_sessions", {
            "X-Muxi-User-ID": "user-123",
        })
        print(result.content[0].text)

asyncio.run(main())
```

> [!TIP]
> The `X-Muxi-User-ID` parameter is passed as a tool argument, not a transport header. It identifies which user's data to access (sessions, memories, etc.).


## Claude API (Programmatic)

When calling the Claude API directly, pass MCP server config in your request:

```python
import anthropic

client = anthropic.Anthropic()

response = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=1024,
    mcp_servers=[{
        "type": "url",
        "url": "http://localhost:8001/mcp",
        "name": "my-formation",
        "custom_headers": {
            "X-Muxi-Client-Key": "fmc_...",
        },
    }],
    messages=[{"role": "user", "content": "List my recent sessions"}],
)
```


## How It Works

The MCP server is auto-generated from your formation's REST API using `FastMCP.from_fastapi()`. When an MCP client calls a tool:

1. Client sends tool call to `/mcp` with auth headers
2. FastMCP translates it to an internal REST API call (e.g., `POST /v1/chat`)
3. Your auth headers are forwarded to the REST endpoint
4. The REST endpoint authenticates and processes the request
5. The response is returned as an MCP tool result

This means MCP and REST always behave identically -- same auth, same data, same results.

```
MCP Client ──headers──▶ /mcp ──forwards──▶ /v1/chat ──▶ Overlord ──▶ Response
                                              ▲
REST Client ──headers──────────────────────────┘
```


## MCP vs REST vs SDK

| | MCP | REST API | SDK |
|---|---|---|---|
| **Best for** | AI-native tools (Claude, Cursor) | Custom integrations | Application code |
| **Auth** | `X-Muxi-Client-Key` header | Same | Handled by SDK |
| **Streaming** | Not yet | SSE via `/v1/chat` | Built-in |
| **Setup** | Config file or transport | HTTP client | `pip install` / `npm install` |

> [!TIP]
> MCP is ideal when you want an AI assistant to use your formation as a tool. For building your own applications, use the SDK or REST API.


## Troubleshooting

**Tools not appearing?**
- Verify the formation is running and `/mcp` is reachable: `curl http://localhost:8001/mcp/`
- Check that `fastmcp>=3.0.0` is installed in the runtime environment

**401 on tool calls?**
- Ensure `X-Muxi-Client-Key` is in your transport headers (not just tool arguments)
- Verify the key matches your formation's `client_key`

**Tool call fails with 500?**
- Check the formation logs for the underlying error
- The MCP tool returns the same error the REST endpoint would


## Next Steps

- [Authentication](../server/authentication.md) -- API key setup
- [Tools & MCP](../concepts/tools-and-mcp.md) -- how agents use MCP tools (outbound)
- [Build Custom UI](build-custom-ui.md) -- REST/SDK integration
- [API Reference](../reference/api-reference.md) -- full endpoint documentation
