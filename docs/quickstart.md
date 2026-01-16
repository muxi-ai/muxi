---
title: Quickstart
description: Get a MUXI formation running in 5 minutes
youtube-video: placeholder
---

# Quickstart

## Go from zero to a running AI agent in 5 minutes

<!-- TODO: Add video walkthrough -->
<!-- [Video placeholder: Complete quickstart walkthrough] -->

This quickstart gets you from zero to a working AI agent in 5 minutes. You'll install MUXI, create a formation, and test it.

> [!NOTE]
> **Prerequisites:**
> - Terminal access (macOS, Linux, or Windows)
> - OpenAI API key ([get one here](https://platform.openai.com/api-keys))
> - 5 minutes

## Get started in 5 minutes

[[steps]]

[[step Install MUXI]]

[[tabs]]

[[tab macOS]]
```bash
brew install muxi-ai/tap/muxi
```

**Expected output:**

```
==> Downloading https://github.com/muxi-ai/muxi/releases/...
==> Installing muxi
🍺  /opt/homebrew/bin/muxi
```
[[/tab]]

[[tab Linux]]
```bash
curl -fsSL https://muxi.org/install | sudo bash
```

**Expected output:**

```
[INFO] Downloading MUXI...
[INFO] Installing to /usr/local/bin
[INFO] MUXI installed successfully
```
[[/tab]]

[[tab Windows]]
```powershell
powershell -c "irm https://muxi.org/install | iex"
```

**Expected output:**

```
Downloading MUXI...
Installing to C:\Program Files\muxi
MUXI installed successfully
```
[[/tab]]

[[/tabs]]

Verify installation:

```bash
muxi --version
```

**Expected:** `muxi version 1.0.0` (or higher)

> [!TIP]
> **Troubleshooting:**
> - Command not found? Restart your terminal
> - macOS: `brew update && brew install muxi-ai/tap/muxi`
> - Linux: Check PATH includes `/usr/local/bin`

[[/step]]

[[step Start the Server]]

**First time only** - generate credentials:

```bash
muxi-server init
```

**Expected output:**

```
Generated credentials:
  Key ID:     muxi_key_abc123...
  Secret:     muxi_secret_xyz789...

⚠️  Save these credentials securely!
Config saved to: ~/.muxi/server/config.yaml
```

> [!WARNING]
> **Save these credentials!** You'll need them to deploy formations remotely.

Now start the server:

```bash
muxi-server start
```

**Expected output:**

```
[INFO] MUXI Server starting...
[INFO] Listening on :7890
[INFO] Server ready
```

**Leave this terminal open** - the server must stay running.

> [!TIP]
> **Troubleshooting:**
> - Port already in use? `muxi-server --port 7891`
> - Check logs: `muxi-server logs`

[[/step]]

[[step Create a Formation]]

Open a **new terminal**:

```bash
muxi new formation my-assistant
cd my-assistant
```

This creates:

```
my-assistant/
├── formation.afs      # Main configuration
├── agents/            # Agent definitions
├── secrets            # Required secrets template
└── .gitignore
```

[[/step]]

[[step Configure Secrets]]

```bash
muxi secrets setup
```

Enter your OpenAI API key when prompted:

```
Setting up secrets for my-assistant...

Required secrets:
  OPENAI_API_KEY (from llm.api_keys)

Enter OPENAI_API_KEY: sk-...

✓ Secrets encrypted and saved
```

[[/step]]

[[step Run Locally]]

```bash
muxi dev
```

Output:

```
✓ Formation 'my-assistant' running
✓ API: http://localhost:8001
✓ Web: http://localhost:8001/chat
```

[[/step]]

[[step Test It]]

[[tabs]]

[[tab Python]]
```bash
pip install muxi-client
```

```python
from muxi import FormationClient

client = FormationClient(url="http://localhost:8001")

# Streaming chat
for event in client.chat_stream({"message": "Hello!"}):
    if event.get("type") == "text":
        print(event.get("text"), end="")
```
[[/tab]]

[[tab TypeScript]]
```bash
npm install @muxi-ai/muxi-typescript
```

```typescript
import { FormationClient } from "@muxi-ai/muxi-typescript";

const client = new FormationClient({
  url: "http://localhost:8001",
});

// Streaming chat
for await (const event of client.chatStream({ message: "Hello!" })) {
  if (event.type === "text") process.stdout.write(event.text);
}
```
[[/tab]]

[[tab Go]]
```bash
go get github.com/muxi-ai/muxi-go
```

```go
client := muxi.NewFormationClient(&muxi.FormationConfig{
    URL: "http://localhost:8001",
})

// Streaming chat
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

[[tab curl]]
```bash
curl -X POST http://localhost:8001/v1/chat \
  -H "Content-Type: application/json" \
  -d '{"message": "Hello!"}'
```
[[/tab]]

[[tab Browser]]
Open `http://localhost:8001/chat` in your browser for an interactive chat UI.
[[/tab]]

[[/tabs]]

> [!TIP]
> See the [complete chat API reference](api/formation#tag/Chat/POST/chat) for streaming, async, and advanced options.

[[/step]]

[[/steps]]


## Start from Registry

Pull a pre-built formation instead of creating from scratch:

```bash
muxi pull @muxi/starter
cd starter-assistant
muxi secrets setup
muxi dev
```

> [!TIP]
> Browse [registry.muxi.org](https://registry.muxi.org) to discover community formations.

---

## What You Built

```mermaid
graph LR
    A[Your App] -->|HTTP/SDK| B[MUXI Server :7890]
    B --> C[Formation :8001]
    C --> D[Agent: assistant]
    D --> E[OpenAI GPT-5]
```

You now have:

- A **MUXI Server** managing formations
- A **Formation** with one agent
- An **API** ready for integration

> [!TIP]
> **Want to understand how this all works?** See [How MUXI Works](how-it-works.md) for the full architecture and request flow.

---

## Common First Issues

[[toggle Port already in use]]
```bash
# Find what's using the port
lsof -i :7890

# Use a different port
muxi-server --port 7891
```
[[/toggle]]

[[toggle Command not found after install]]
Restart your terminal, or check your PATH:
```bash
# macOS/Linux
echo $PATH | grep -E "(homebrew|local/bin)"

# Add to PATH if needed
export PATH="$PATH:/usr/local/bin"
```
[[/toggle]]

[[toggle Server won't start]]
```bash
# Check if already running
ps aux | grep muxi-server

# View logs
muxi-server logs
```
[[/toggle]]

[[toggle API key errors]]
```bash
# Verify secret is set
muxi secrets get OPENAI_API_KEY

# Re-run setup
muxi secrets setup
```
[[/toggle]]

For more issues, see the [Troubleshooting Guide](guides/troubleshooting.md).

---

## Next Steps

:::: cols=2

(guides/deploy-to-production.md)[[card]]
#### Deploy to Production
Move beyond localhost to a real server.
[[/card]]

(guides/add-mcp-tools.md)[[card]]
#### Add Tools
Give your agent web search, file access, and more.
[[/card]]

(guides/add-memory.md)[[card]]
#### Add Memory
Make conversations persist across sessions.
[[/card]]

(reference/README.md)[[card]]
#### Formation Reference
Complete YAML configuration guide.
[[/card]]

::::
