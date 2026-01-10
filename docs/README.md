---
title: MUXI Documentation
description: Production infrastructure for AI agents - deploy with zero downtime
doc-type: home
---

# MUXI Documentation

## Production infrastructure for AI agents

<!-- TODO: Add hero video showing deployment flow -->
<!-- [Video placeholder: 60s demo of creating and deploying a formation] -->

MUXI is infrastructure for running AI agents in production. Define multi-agent systems using the **Agent Formation Schema** (`.afs` files - it's just YAML), deploy with zero downtime, scale with built-in orchestration.

**Think: Kubernetes for AI agents.**

- ✅ Zero-downtime deployments
- ✅ Multi-agent orchestration
- ✅ Built-in memory & RAG
- ✅ Production-ready security

> [!TIP]
> New to MUXI? Start with the [Quickstart](quickstart.md) to get running in 5 minutes.

**Popular:**

- [Quickstart](quickstart.md)
- [Examples](examples/README.md)
- [Formation Schema](concepts/formation-schema.md)
- [Zero-Downtime Updates](server/formations.md#zero-downtime-updates)


## Get Started

:::: cols=3

(quickstart.md)[[card]]

#### Quickstart
Get your first formation running in 5 minutes.

[[/card]]

(installation/README.md)[[card]]

#### Installation
Install MUXI on macOS, Linux, Windows, or Docker.

[[/card]]

(reference/README.md)[[card]]

#### Formations
Learn how to define AI systems in YAML.

[[/card]]

::::

---

## See It In Action

:::: cols=3

(examples/01-simple-chatbot/README.md)[[card]]

#### Simple Chatbot
⭐ Beginner • 2 min

Basic assistant with single agent.

[[/card]]

(examples/02-customer-support/README.md)[[card]]

#### Customer Support
⭐⭐ Intermediate • 5 min

Memory + knowledge + persona.

[[/card]]

(examples/05-multi-agent-team/README.md)[[card]]

#### Multi-Agent Team
⭐⭐⭐ Advanced • 10 min

3 specialized agents working together.

[[/card]]

::::

[View all 5 examples →](examples/README.md)

---

## How It Works

```
1. Define    →    2. Deploy    →    3. Use
   (YAML)           (CLI)           (API/SDK)
```

### Define a formation

```yaml
# formation.afs
schema: "1.0.0"
id: my-assistant

llm:
  models:
    - text: "openai/gpt-4o"

agents: []  # Auto-discovered from agents/ directory
```

With agent file:

```yaml
# agents/assistant.afs
schema: "1.0.0"
id: assistant
name: Assistant
description: Helpful assistant

system_message: You are a helpful assistant.
```

### Deploy to your server

```bash
muxi deploy
```

### Use via API or SDK

[[tabs]]

[[tab curl]]
```bash
curl -X POST http://localhost:8001/v1/chat \
  -H "Content-Type: application/json" \
  -H "X-Muxi-Client-Key: $CLIENT_KEY" \
  -d '{"message": "Hello!"}'
```
[[/tab]]

[[tab Python]]
```python
from muxi import FormationClient

formation = FormationClient(
    server_url="http://localhost:7890",
    formation_id="my-assistant",
    client_key="...",
)
for event in formation.chat_stream({"message": "Hello!"}, user_id="user_123"):
    if event.get("type") == "text":
        print(event.get("text"), end="")
```
[[/tab]]

[[tab TypeScript]]
```typescript
import { FormationClient } from "@muxi-ai/muxi-typescript";

const formation = new FormationClient({
  serverUrl: "http://localhost:7890",
  formationId: "my-assistant",
  clientKey: "...",
});
for await (const chunk of await formation.chatStream({ message: "Hello!" }, "user_123")) {
  if (chunk.type === "text") process.stdout.write(chunk.text);
}
```
[[/tab]]

[[tab Go]]
```go
client := muxi.NewFormationClient(&muxi.FormationConfig{
    FormationID: "my-assistant",
    ServerURL:   "http://localhost:7890",
    ClientKey:   "...",
})
stream, _ := client.ChatStream(ctx, &muxi.ChatRequest{Message: "Hello!", UserID: "user_123"})
for chunk := range stream {
    if chunk.Type == "text" {
        fmt.Print(chunk.Text)
    }
}
```
[[/tab]]

[[/tabs]]

---

## Why MUXI?

:::: cols=4

[[card]]

#### 🚀 Zero Downtime
Blue-green deployments. Update formations without dropping requests.

[[/card]]

[[card]]

#### 🤖 Multi-Agent
Auto-decompose complex tasks across specialized agents.

[[/card]]

[[card]]

#### 🧠 Memory & RAG
Built-in vector search, persistent memory, knowledge bases.

[[/card]]

[[card]]

#### 🔐 Production Ready
HMAC auth, secrets management, per-user credentials.

[[/card]]

::::

---

## Explore by Topic

:::: cols=2

(server/README.md)[[card]]

#### Server
Orchestration platform for running formations.

[[/card]]

(cli/README.md)[[card]]

#### CLI
Command-line tool for managing MUXI.

[[/card]]

(sdks/README.md)[[card]]

#### SDKs
Python, TypeScript, and Go client libraries.

[[/card]]

(registry/README.md)[[card]]

#### Registry
Discover and share formations.

[[/card]]

::::

---

## Learn More

:::: cols=3

[[card]]

#### 🎓 Core Concepts
- [Formation Schema](concepts/formation-schema.md)
- [The Overlord](concepts/overlord.md)
- [Workflows](concepts/workflows.md)
- [Memory System](concepts/memory.md)

[[/card]]

[[card]]

#### 🛠️ Guides
- [Add Tools (MCP)](guides/add-tools.md)
- [Build Multi-Agent Teams](guides/multi-agent.md)
- [Deploy to Production](guides/deploy.md)
- [Troubleshooting](guides/troubleshooting.md)

[[/card]]

[[card]]

#### 📚 Reference
- [Agent Formation Schema](reference/schema.md)
- [Server Configuration](server/configuration.md)
- [CLI Commands](cli/cheatsheet.md)
- [Deep Dives](deep-dives/README.md)

[[/card]]

::::

---

## Next Steps

**Just starting?**
[→ 5-minute Quickstart](quickstart.md)

**Want to see code?**
[→ Browse Examples](examples/README.md)

**Ready to deploy?**
[→ Install MUXI](installation/README.md)

**Building something?**
[→ Read the Guides](guides/README.md)
