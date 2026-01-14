---
title: MUXI Documentation
description: Production infrastructure for AI agents
doc-type: home
---

# MUXI Documentation

## Open-source, production infrastructure for AI agents

MUXI is **The Agent Server** - production infrastructure for running AI agents. It treats agents as native primitives, and packaged as deployable units.

<!-- Not a framework. Not a wrapper. A server. -->

[[boxed float-right]]

### Watch the Demo

[![image info](https://placehold.co/600x400)](http://google.com.au/)

From zero to a multi-agent AI system in under 5 minutes. See MUXI in action.

[[/boxed]]

- **Define everything in YAML** - Agents, tools, memory, knowledge, triggers – as a [deployable unit](concepts/formation-schema.md). Zero framework code.
- **Ship with one command** - `muxi deploy`. Done.
- **Let the orchestration roll** - Smart [Overlord](concepts/overlord.md) that routes, decomposes, clarifies, and coordinates.
- **Actually production-ready** - Multi-tenancy, observability, and enterprise features included, not bolted on.

Think: **Docker for AI agents.**

Self-hosted, LLM-agnostic, Open-source.


[>] [Learn more about MUXI →](what-is-muxi.md)

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
- [How Do I...?](faq.md)

[[/card]]

::::

---

## Popular Pages

[+] [Quickstart](quickstart.md) - 5 minutes to your first agent
[+] [Examples](examples/README.md) - See real formations
[+] [Formation Schema](concepts/formation-schema.md) - YAML configuration
[+] [Add Tools (MCP)](guides/add-mcp-tools.md) - Give agents capabilities
[+] [Deploy to Production](guides/deploy-to-production.md) - Go live
