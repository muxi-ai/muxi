---
title: MUXI Documentation
description: Production infrastructure for AI agents
doc-type: home
---

# MUXI Documentation

## Open-source, production infrastructure for AI agents

MUXI is **The Agent Server** - production infrastructure for running AI agents. Not a framework. Not a wrapper. A server.

- **Declare in YAML** - Define agents, tools, memory, and knowledge in `.afs` files
- **Deploy with one command** - `muxi deploy` and you're live
- **Scale with built-in orchestration** - The Overlord routes requests, coordinates agents, manages memory

Think: **Kubernetes for AI agents.** Self-hosted, LLM-agnostic, open-source.

(what-is-muxi.md)[[card]]

#### ▶ Watch the Demo

From zero to a multi-agent AI system in under 5 minutes. See MUXI in action.

[[/card]]

[Learn more about MUXI →](what-is-muxi.md)

---

## Get Started

:::: cols=3

(quickstart.md)[[card]]

#### Quickstart
Get your first agent running in 5 minutes.

[[/card]]

(how-it-works.md)[[card]]

#### How It Works
Understand the architecture and request flow.

[[/card]]

(examples/README.md)[[card]]

#### Examples
Real formations for common use cases.

[[/card]]

::::

---

## Install

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

[Full installation guide →](installation/README.md)

---

## Explore

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

## Learn

:::: cols=3

[[card]]

#### Core Concepts

- [Architecture](concepts/architecture.md)
- [The Overlord](concepts/overlord.md)
- [Formation Schema](concepts/formation-schema.md)
- [Memory System](concepts/memory-system.md)
- [Tools & MCP](concepts/tools-and-mcp.md)

[[/card]]

[[card]]

#### Guides

- [Add Tools (MCP)](guides/add-mcp-tools.md)
- [Add Memory](guides/add-memory.md)
- [Build Multi-Agent Teams](guides/build-multi-agent-systems.md)
- [Deploy to Production](guides/deploy-to-production.md)
- [Troubleshooting](guides/troubleshooting.md)

[[/card]]

[[card]]

#### Reference

- [Formation Schema](reference/formation-schema.md)
- [API Reference](reference/api-reference.md)
- [CLI Cheatsheet](cli/cheatsheet.md)
- [Deep Dives](deep-dives/README.md)
- [All Features](feature-overview.md)

[[/card]]

::::

---

## Popular Pages

[+] [Quickstart](quickstart.md) - 5 minutes to your first agent
[+] [Examples](examples/README.md) - See real formations
[+] [Formation Schema](concepts/formation-schema.md) - YAML configuration
[+] [Add Tools (MCP)](guides/add-mcp-tools.md) - Give agents capabilities
[+] [Deploy to Production](guides/deploy-to-production.md) - Go live
