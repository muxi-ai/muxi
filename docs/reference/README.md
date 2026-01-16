---
title: Formations
description: Define complete AI systems in YAML
---

# Formations

## Define complete AI systems in YAML

A formation is everything your AI needs: agents, tools, memory, knowledge, and workflows - all in one portable package.

<!-- TODO: Add diagram/illustration -->
<!-- [Image placeholder: Visual of formation components] -->


## What's in a Formation?

```
my-formation/
├── formation.afs      # Main configuration
├── agents/            # Agent definitions (auto-discovered)
├── mcp/               # Tool configurations (auto-discovered)
├── sops/              # Standard procedures (auto-discovered)
├── triggers/          # Webhook templates (auto-discovered)
├── knowledge/         # RAG sources
├── secrets.enc        # Encrypted credentials
└── secrets.example    # Template for required secrets
```

---

## Basic Formation

The simplest formation that works:

```yaml
# formation.afs
schema: "1.0.0"
id: my-assistant
name: My Assistant

llm:
  models:
    - text: "openai/gpt-4o"
  api_keys:
    openai: "${{ secrets.OPENAI_API_KEY }}"

agents: []  # Auto-discovered from agents/ directory
```

> [!TIP]
> File extension `.afs` stands for **Agent Formation Schema**. You can also use `.afs` or `.yml`.

---

## Formation Components

:::: cols=2

(agents.md)[[card]]
#### Agents
AI personas with roles and capabilities. Define specialists like researchers, writers, or support staff.
[[/card]]

(tools.md)[[card]]
#### Tools (MCP)
Give agents superpowers - web search, file access, databases, and any MCP server.
[[/card]]

(memory.md)[[card]]
#### Memory
Three-tier memory system: buffer, vector search, and persistent storage.
[[/card]]

(knowledge.md)[[card]]
#### Knowledge
RAG from your documents - PDFs, markdown, and more.
[[/card]]

(triggers.md)[[card]]
#### Triggers
Webhooks that invoke your agents from external systems.
[[/card]]

(sops.md)[[card]]
#### SOPs
Standard operating procedures for consistent workflows.
[[/card]]

::::

---

## Create Your First Formation

[[steps]]

[[step Generate the structure]]

```bash
muxi new formation my-assistant
cd my-assistant
```
[[/step]]

[[step Configure your secrets]]

```bash
muxi secrets setup
```

Enter API keys when prompted.
[[/step]]

[[step Run locally]]

```bash
muxi dev
```
[[/step]]

[[step Deploy to server]]

```bash
muxi deploy
```
[[/step]]

[[/steps]]

---

## Quick Reference

| Section | Purpose | Required |
|---------|---------|----------|
| `schema` | Schema version | Yes |
| `id` | Unique identifier | Yes |
| `llm` | Language model config | Yes |
| `agents` | Agent definitions | Yes (at least one) |
| `memory` | Memory configuration | No |
| `mcps` | MCP tool integrations | No |
| `triggers` | Webhook triggers | No |
| `knowledge` | RAG sources | No |

---

## Complete Example

```yaml
schema: "1.0.0"
id: research-assistant
name: Research Assistant
description: AI research and writing team

llm:
  models:
    - text: "openai/gpt-4o"
    - embedding: "openai/text-embedding-3-small"
  api_keys:
    openai: "${{ secrets.OPENAI_API_KEY }}"

memory:
  buffer:
    size: 50
    vector_search: true
  persistent:
    enabled: true
    provider: sqlite

overlord:
  workflow:
    auto_decomposition: true
  response:
    streaming: true

# MCP servers auto-discovered from mcp/ directory
# Agents auto-discovered from agents/ directory
agents: []
```

With separate agent files:

```yaml
# agents/researcher.afs
schema: "1.0.0"
id: researcher
name: Researcher
description: Research topics with web search

system_message: "You are a research specialist. Your job is to gather accurate information..."
```

```yaml
# agents/writer.afs
schema: "1.0.0"
id: writer
name: Writer
description: Creates content

system_message: "You are a content writer. Your job is to create clear, engaging content..."
```

---

## Next Steps

[+] [Schema Reference](schema.md) - Complete YAML specification
[+] [Agents](agents.md) - Configure AI personas
[+] [Tools](tools.md) - Add MCP integrations
[+] [Examples](examples.md) - Real-world formations
