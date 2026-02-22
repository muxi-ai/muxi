<h1 align="center">
  <img src="./assets/muxi-wordmark.svg" alt="MUXI" height="72" /><br>
  The Open-source AI Application Server
</h1>

<p align="center">​<br>
  <a href="./LICENSE.md"><img src="https://img.shields.io/badge/License-ELv2%20%2F%20Apache%202.0-c98b45.svg" alt="License" height="20"></a>
  <a href="https://github.com/muxi-ai/server/releases"><img src="https://img.shields.io/github/v/release/muxi-ai/server?label=Server&color=yellow" alt="Server" height="20"></a>
  <!-- <a href="https://github.com/muxi-ai/runtime/releases"><img src="https://img.shields.io/github/v/release/muxi-ai/runtime?label=Runtime&color=e65850" alt="Runtime" height="20"></a> -->
  <a href="https://github.com/muxi-ai/cli/releases"><img src="https://img.shields.io/github/v/release/muxi-ai/cli?label=CLI&color=07a9e6" alt="CLI" height="20"></a>
  <a href="https://github.com/muxi-ai/sdks"><img src="https://img.shields.io/badge/SDKs-12-9a75d1.svg" alt="Documentation" height="20"></a>
  <a href="https://agentformation.org"><img src="https://github.com/agent-formation/afs-assets/raw/refs/heads/main/badges/afs-compatible.svg" alt="AFS Compatible" height="20"></a>
</p>

<p align="center">
  <a href="https://github.com/muxi-ai/muxi/discussions"><img src="https://img.shields.io/badge/​-Community-222.svg?logo=github" alt="Discussions" height="20"></a>
  <a href="https://muxi.org/docs"><img src="https://img.shields.io/badge/​-Docs-e65850.svg?logo=readme&logoColor=white" alt="Docs" height="20"></a>
  <a href="https://linkedin.com/company/muxi-ai"><img src="./assets/badge-linkedin.svg" alt="Linkedin" height="20"></a>
  <a href="https://twitter.com/muxi_ai"><img src="./assets/badge-twitter.svg" alt="X/Twitter" height="20"></a>
</p>

<!-- <p align="center">
<a target="_blank" href="https://muxi.org/docs/quickstart" align="center">Quickstart</a>
• <a target="_blank" href="https://muxi.org/docs/how-it-works" align="center">How it Works</a>
• <a target="_blank" href="https://muxi.org/docs/cli-and-sdk" align="center">CLI & SDKs</a>
• <a target="_blank" href="https://muxi.org/docs/examples" align="center">Examples</a>
</p> -->

---

<p align="center">No one builds their own Nginx to deploy a website. No one should reinvent infrastructure to build AI.</p>

---

## Deploy Intelligence

MUXI (`/ˈmʌk.siː/`) is **production infrastructure for AI agents**. Not a framework. Not a wrapper. A **server** – where agents are native primitives with built-in orchestration, memory, observability, and scale.

| Concept | Docker | MUXI |
|---------|--------|------|
| **Engine** | Docker Engine | Server + Runtime |
| **Definition** | Dockerfile | Formation |
| **Registry** | Docker Hub | MUXI Registry |
| **CLI** | `docker` | `muxi` |

> [!NOTE]
> MUXI introduced the [**Agent Formation Standard**](https://github.com/agent-formation) – an open spec for declarative AI systems. Agents, knowledge, A2A, and MCP tools defined in portable `.afs` files.


## Demo

<!-- TODO: Replace with terminal recording (asciinema/svg-term or ttygif, ~10-15s, ~800px width) -->
<!-- Recommended flow: muxi pull @muxi/hello-world -> muxi deploy -> muxi chat hello-world -->
<p align="center">
  <img src="./assets/demo.gif" alt="MUXI Demo" width="800">
</p>


## Quick Start

**Install:**

```bash
brew install muxi-ai/tap/muxi                        # macOS
curl -fsSL https://muxi.org/install | sudo bash      # Linux
powershell -c "irm https://muxi.org/install | iex"   # Windows
```

**Run:**

```bash
muxi pull @muxi/hello-world   # pull a formation
muxi deploy                   # deploy it
muxi chat hello-world         # talk to it
```

Three commands. Your agent is running, stateful, and accessible via API.

📚 **Docs:** [Quickstart](https://muxi.org/docs/quickstart) | [Installation](https://muxi.org/docs/installation)

---

## Build on Top with SDKs

```python
from muxi import FormationClient

client = FormationClient(
    server_url="http://localhost:7890",
    formation_id="hello-world"
)

for chunk in client.chat_stream({ "message": "Hello!", "user_id": "user_123" }):
    print(chunk.get("text", ""), end="")
```

SDKs available for Python, TypeScript, Go, and 9 more languages.

📚 **Docs:** [SDKs](https://muxi.org/docs/sdks) | [API Reference](https://muxi.org/docs/reference/api-reference)

---

## How MUXI Compares

| | MUXI | LangChain / LangGraph | CrewAI | AutoGen |
|---|---|---|---|---|
| **What it is** | Server infrastructure | Python libraries | Python framework | Multi-agent framework |
| **Deploy** | `muxi deploy` | Write your own | Write your own | Write your own |
| **Configure** | Declarative `.afs` files | Imperative code | Imperative code | Imperative code |
| **Multi-tenancy** | Built-in isolation | Build it yourself | Not supported | Not supported |
| **Memory** | Three-tier (buffer + persistent + vector) | Bring your own | Short-term only | Bring your own |
| **Tools** | 1,000+ via MCP, loaded once | Per-call schema injection | Per-call injection | Per-call injection |
| **Observability** | 349 event types, 10+ export targets | LangSmith (paid) | CrewAI+ (paid) | Limited |
| **LLM Providers** | 21 providers, 300+ models | Multiple | Multiple | Multiple |

**Key numbers:** <100ms avg response &bull; 80%+ test coverage &bull; 92% semantic cache hit rate

---

## Features

- **Declarative agents** – `.afs` files, version-controlled, auto-discovered. [Docs](https://muxi.org/docs/concepts/agents-and-orchestration)
- **Three-tier memory** – buffer, persistent, and semantic memory built in. [Docs](https://muxi.org/docs/concepts/memory-system)
- **1,000+ MCP tools** – GitHub, Slack, Stripe, databases, and more. [Docs](https://muxi.org/docs/concepts/tools-and-mcp)
- **Multi-tenant** – per-user isolation, RBAC, OAuth. [Docs](https://muxi.org/docs/concepts/multi-tenancy)
- **Observability** – 349 event types, real-time streaming, 10+ export targets. [Docs](https://muxi.org/docs/concepts/observability)
- **Intelligent orchestration** – Overlord routes to specialists, decomposes tasks. [Docs](https://muxi.org/docs/concepts/overlord)
- **Async processing** – triggers, webhooks, scheduled tasks. [Docs](https://muxi.org/docs/concepts/async)
- **Any LLM** – 21 providers, 300+ models, no lock-in. [Docs](https://muxi.org/docs/concepts/llm-providers)

---

## Example Formation

```yaml
# formation.afs -- define your AI system (it's just YAML)
schema: "1.0.0"
id: "customer-support"
description: Helps customers with product support and refunds

llm:
  models:
    - text: "openai/gpt-4o"
  api_keys:
    openai: "${{ secrets.OPENAI_API_KEY }}"

agents: []  # auto-discovered from agents/*.afs
```

```yaml
# agents/sales-assistant.afs -- define an agent
schema: "1.0.0"
id: sales-assistant
name: Sales Assistant
description: Helps customers with product questions

system_message: Help customers find products and answer questions.

knowledge:
  sources:
    - path: knowledge/product-info.docx
    - path: knowledge/shipping-rates.csv
```

```bash
muxi deploy   # that's it -- running, stateful, API-accessible
```

📚 **Docs:** [Formation Schema](https://muxi.org/docs/concepts/formation-schema) | [Examples](https://muxi.org/docs/examples)

---

## Who Is MUXI For?

- **Platform builders** – Building a SaaS with AI features? MUXI handles orchestration, memory, and multi-tenancy so you can focus on your product.

- **Internal tool builders** – Deploying AI assistants for your team? MUXI gives you SOPs, observability, and enterprise-grade infrastructure out of the box.

- **Developers tired of framework hell** – Spent months on LangChain orchestration code? MUXI replaces it with YAML configuration and a single deploy command.


## Open Source & Self-Hosted

MUXI is **open-source** and **self-hostable**. Your data stays on your infrastructure. No vendor lock-in. No per-seat pricing. No usage limits.

- **Self-hostable** – run anywhere, owned by you
- **Observable** – see what's happening, always
- **Declarative** – version-controlled and reproducible
- **Open** – no lock-in, no surprises

---

## Documentation

| Looking for... | Go to |
|----------------|-------|
| Getting started | [Quickstart →](https://muxi.org/docs/quickstart) |
| SDKs (12 languages) | [SDKs →](https://muxi.org/docs/sdks) |
| How MUXI works | [ARCHITECTURE.md](https://github.com/muxi-ai/muxi/blob/main/ARCHITECTURE.md) |
| Contributing | [CONTRIBUTING.md](https://github.com/muxi-ai/muxi/blob/main/CONTRIBUTING.md) |
| All repositories | [REPOSITORIES.md](https://github.com/muxi-ai/muxi/blob/main/REPOSITORIES.md) |
| Licensing | [LICENSE.md](https://github.com/muxi-ai/muxi/blob/main/LICENSE.md) |
| Roadmap | [Roadmap →](https://github.com/orgs/muxi-ai/projects/1) |

---

## Agent Skills

This repo includes an [Agent Skill](https://agentskills.io) that gives any compatible AI agent (Claude Code, Cursor, GitHub Copilot, VS Code, etc.) deep knowledge of the MUXI platform.

See [`skills/`](./skills/) for setup instructions.

---

## Get Involved

- **Star** this repo to follow progress
- **[Discussions](https://github.com/muxi-ai/muxi/discussions)** – ask questions, share ideas, get help
- **[Roadmap](https://github.com/orgs/muxi-ai/projects/1)** – see what's coming
- **[@muxi_ai](https://twitter.com/muxi_ai)** – follow for updates
- **[CONTRIBUTING.md](https://muxi.org/go/contributing)** – contribute code

---

## Who's Behind This

MUXI is created by [**Ran Aroussi**](https://x.com/aroussi), author of [**Production-Grade Agentic AI**](http://productionaibook.com) (665 pages) and an [open-source developer](https://github.com/ranaroussi) whose tools are used by millions of developers daily.

> [!IMPORTANT]
> MUXI is built on the principles from **Production-Grade Agentic AI** – a comprehensive guide to building enterprise AI agent systems in production. [**Download the PDF version here ›**](http://productionaibook.com).

---

## License

Server and Runtime are [ELv2](https://www.elastic.co/licensing/elastic-license). Everything else (CLI, SDKs, formations, schemas) is [Apache 2.0](https://www.apache.org/licenses/LICENSE-2.0).

**TL;DR:** Use MUXI freely for your products and business. The only restriction: don't resell MUXI itself as a hosted service. See [LICENSE.md](https://muxi.org/go/licensing) for details.

---

<p align="center">
  <a href="https://github.com/muxi-ai">GitHub</a> &bull;
  <a href="https://twitter.com/muxi_ai">Twitter</a> &bull;
  <a href="https://github.com/muxi-ai/muxi/discussions">Discussions</a>
</p>

<p align="center">
  <sub>For AI/LLM agents: <a href="https://muxi.org/llms.txt">muxi.org/llms.txt</a></sub>
</p>
