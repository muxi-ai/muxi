---
title: CLI, SDKs & MCP
description: Tools for managing and integrating with MUXI
---

# CLI, SDKs & MCP

## Three ways to interact with MUXI

MUXI provides three interfaces: the **CLI** for managing your server and formations, **SDKs** for building applications, and **MCP** for connecting AI-native tools like Claude Desktop and Cursor.

:::: cols=2

[[card]]

#### CLI

**For:** Managing MUXI

- Deploy and manage formations
- Configure secrets
- Monitor logs and health
- Server administration

```bash
muxi deploy
muxi secrets setup
muxi logs my-assistant
```

[CLI Documentation →](cli/README.md)

[[/card]]

[[card]]

#### SDKs

**For:** Building applications

- Chat with your agents
- Stream responses
- Manage sessions and memory
- Integrate into your apps

```python
client = FormationClient(url="...")
for event in client.chat_stream({"message": "Hi"}):
    print(event.get("text"), end="")
```

[SDK Documentation →](sdks/README.md)

[[/card]]

[[card]]

#### MCP

**For:** AI-native tools

- Use from Claude Desktop or Cursor
- 33 tools auto-generated from your API
- Same auth as REST
- Zero wrapper code

```json
{
  "type": "streamable-http",
  "url": "http://localhost:8001/mcp",
  "headers": {
    "X-Muxi-Client-Key": "fmc_..."
  }
}
```

[MCP Guide →](guides/connect-via-mcp.md)

[[/card]]

::::


## Which should I use?

| I want to... | Use |
|--------------|-----|
| Deploy a formation | CLI |
| Set up API keys and secrets | CLI |
| Check formation logs | CLI |
| Start/stop formations | CLI |
| Build a chatbot UI | SDK |
| Integrate agents into my app | SDK |
| Stream responses to users | SDK |
| Manage user sessions | SDK |
| Use agents from Claude Desktop | MCP |
| Use agents from Cursor | MCP |
| Give an AI assistant access to my formation | MCP |

> [!TIP]
> Most projects use **both** CLI and SDKs: CLI for deployment and ops, SDKs for application code. Add MCP when you want AI assistants to use your formation as a tool.


## CLI Quick Start

### Install

[[tabs]]

[[tab macOS]]
```bash
brew install muxi-ai/tap/muxi
```
[[/tab]]

[[tab Linux]]
```bash
curl -fsSL https://muxi.org/install | sudo bash
```
[[/tab]]

[[tab Windows]]
```powershell
powershell -c "irm https://muxi.org/install | iex"
```
[[/tab]]

[[/tabs]]

### Essential Commands

```bash
# Create a new formation
muxi new formation my-assistant

# Set up secrets (API keys)
muxi secrets setup

# Run locally for development
muxi dev

# Deploy to production server
muxi deploy --profile production

# View logs
muxi logs my-assistant --follow

# List all formations
muxi remote list
```

[Full CLI Cheatsheet →](cli/cheatsheet.md)


## SDK Quick Start

### Install

[[tabs]]

[[tab Python]]
```bash
pip install muxi-client
```
[[/tab]]

[[tab TypeScript]]
```bash
npm install @muxi-ai/muxi-typescript
```
[[/tab]]

[[tab Go]]
```bash
go get github.com/muxi-ai/muxi-go
```
[[/tab]]

[[/tabs]]

### Chat with Your Agent

[[tabs]]

[[tab Python]]
```python
from muxi import FormationClient

client = FormationClient(url="http://localhost:8001")

for event in client.chat_stream({"message": "Hello!"}):
    if event.get("type") == "text":
        print(event.get("text"), end="")
```
[[/tab]]

[[tab TypeScript]]
```typescript
import { FormationClient } from "@muxi-ai/muxi-typescript";

const client = new FormationClient({
  url: "http://localhost:8001",
});

for await (const event of client.chatStream({ message: "Hello!" })) {
  if (event.type === "text") process.stdout.write(event.text);
}
```
[[/tab]]

[[tab Go]]
```go
client := muxi.NewFormationClient(&muxi.FormationConfig{
    URL: "http://localhost:8001",
})

stream, _ := client.ChatStream(ctx, &muxi.ChatRequest{
    Message: "Hello!",
})
for chunk := range stream {
    if chunk.Type == "text" {
        fmt.Print(chunk.Text)
    }
}
```
[[/tab]]

[[/tabs]]

[Full SDK Documentation →](sdks/README.md)


## Learn More

:::: cols=2

[[card]]
#### CLI
- [CLI Overview](cli/README.md)
- [CLI Setup](cli/setup.md)
- [CLI Configuration](cli/configuration.md)
- [CLI Cheatsheet](cli/cheatsheet.md)
[[/card]]

[[card]]
#### SDKs
- [SDK Overview](sdks/README.md)
- [Python SDK](sdks/python-sdk.md)
- [TypeScript SDK](sdks/typescript-sdk.md)
- [Go SDK](sdks/go-sdk.md)
[[/card]]

::::
