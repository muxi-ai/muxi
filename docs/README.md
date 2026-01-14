---
title: MUXI Documentation
description: Production infrastructure for AI agents
doc-type: home
---

# MUXI Documentation

## Production infrastructure for AI agents

MUXI is infrastructure for running AI agents in production. Define multi-agent systems in YAML, deploy with one command, scale with built-in orchestration.

(what-is-muxi.md)[[card]]

#### ▶ Watch the Demo

From zero to a multi-agent AI system in under 5 minutes. See MUXI in action.

[[/card]]

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
- [Memory System](concepts/memory.md)
- [Tools & MCP](concepts/tools.md)

[[/card]]

[[card]]

#### Guides

- [Add Tools (MCP)](guides/add-tools.md)
- [Add Memory](guides/add-memory.md)
- [Build Multi-Agent Teams](guides/multi-agent.md)
- [Deploy to Production](guides/deploy.md)
- [Troubleshooting](guides/troubleshooting.md)

[[/card]]

[[card]]

#### Reference

- [Formation Schema](reference/schema.md)
- [API Reference](reference/api.md)
- [CLI Cheatsheet](cli/cheatsheet.md)
- [Deep Dives](deep-dives/README.md)
- [All Features](features.md)

[[/card]]

::::

---

## Popular Pages

[+] [Quickstart](quickstart.md) - 5 minutes to your first agent
[+] [Examples](examples/README.md) - See real formations
[+] [Formation Schema](concepts/formation-schema.md) - YAML configuration
[+] [Add Tools (MCP)](guides/add-tools.md) - Give agents capabilities
[+] [Deploy to Production](guides/deploy.md) - Go live
