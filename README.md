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

## MUXI V1: Deploy Intelligence

MUXI (`/ˈmʌk.siː/`) is the **AI application server**. Not a framework. Not a wrapper. A complete, self-hosted server stack where agents are native primitives and production capabilities are infrastructure, not application code.

Deploy complete **agent formations** with orchestration, auditable memory, group-based RBAC, tools, knowledge, proactive behavior, self-tuning, observability, and twelve SDKs already integrated.

> [!IMPORTANT]
> **MUXI V1 is the stable 1.x generation.** The Server, Runtime, CLI, Formation API, Server API, and all twelve SDKs move together with coordinated contracts for authentication, streaming, errors, idempotency, and response envelopes.

| Concept | Docker | MUXI |
|---------|--------|------|
| **Engine** | Docker Engine | Server + Runtime |
| **Definition** | Dockerfile | Formation |
| **Registry** | Docker Hub | MUXI Registry |
| **CLI** | `docker` | `muxi` |

> [!NOTE]
> MUXI introduced the [**Agent Formation Standard**](https://agentformation.org) – an open specification for portable, declarative AI systems. A formation packages agents, knowledge, memory, tools, skills, workflows, triggers, policies, and operational behavior into one deployable unit.


<!--
## Demo

TODO: Replace with terminal recording (asciinema/svg-term or ttygif, ~10-15s, ~800px width)
Recommended flow: muxi pull @muxi/hello-muxi -> muxi deploy -> muxi chat hello-muxi
<p align="center">
  <img src="./assets/demo.gif" alt="MUXI Demo" width="800">
</p>
-->

## Quick Start

**Install:**

```bash
brew install muxi-ai/tap/muxi                        # macOS
curl -fsSL https://muxi.org/install | sudo bash      # Linux
powershell -c "irm https://muxi.org/install | iex"   # Windows
```

**Run:**

```bash
muxi pull @muxi/hello-muxi   # pull a formation
muxi deploy                   # deploy it
muxi chat hello-muxi         # talk to it
```

Three commands. Your formation is running, stateful, and accessible through REST, SSE, MCP, and twelve SDKs.

📚 **Docs:** [Quickstart](https://muxi.org/docs/quickstart) | [Installation](https://muxi.org/docs/installation)

---

## Build on Top with SDKs

```python
from muxi import FormationClient

client = FormationClient(
    server_url="http://localhost:7890",
    formation_id="hello-muxi",
    client_key="<your-client-key>"
)

for chunk in client.chat_stream({ "message": "Hello!" }, user_id="user_123"):
    print(chunk.get("text", ""), end="")
```

Official SDKs are available for Python, TypeScript, Go, Ruby, PHP, C#, Java, Kotlin, Swift, Dart, Rust, and C++.

📚 **Docs:** [SDKs](https://muxi.org/docs/sdks) | [API Reference](https://muxi.org/docs/reference/api-reference)

---

## How MUXI Compares

| | MUXI | LangChain / LangGraph | CrewAI | AutoGen |
|---|---|---|---|---|
| **What it is** | Server infrastructure | Python libraries | Python framework | Multi-agent framework |
| **Deploy** | `muxi deploy` | Write your own | Write your own | Write your own |
| **Configure** | Declarative `.afs` files | Imperative code | Imperative code | Imperative code |
| **Multi-tenancy** | User isolation and per-user credentials | Application-owned | Application-owned | Application-owned |
| **RBAC** | Groups, inheritance, agents, MCP, and tool rules | Build it yourself | Build it yourself | Build it yourself |
| **Memory** | Layered, scoped, event-sourced platform | Compose your own | Task-oriented memory | Bring your own |
| **Proactiveness** | Heartbeats, channels, active hours, soul documents | Build it yourself | Build it yourself | Build it yourself |
| **Tools** | MCP indexed once and selected at runtime | Schemas enter application context | Schemas enter application context | Tool registration |
| **Operations** | Deploy, update, rollback, stream, observe | Build and operate it | Build and operate it | Build and operate it |

**MUXI is the infrastructure layer.** Frameworks help write agent logic. MUXI deploys and operates complete agent systems.

---

## What Ships with MUXI V1

### Systems that learn, remember, and act

- **Formation self-tuning** – operational evidence becomes reviewable revisions of `MUXI.md`, backed by experiments and watched metrics. You decide what gets applied. [Docs](https://muxi.org/docs/concepts/self-tuning)
- **Living, auditable memory** – layered context plus user/group/formation scopes, immutable events, provenance, rebuilds, selective forgetting, decay, knowledge graphs, Captain's Logs, lessons, and signed on-premises distillation. [Docs](https://muxi.org/docs/concepts/memory-system)
- **Proactive formations** – heartbeats, active hours, per-user channels, `SOUL.md`, notification routing, and built-in slash commands let formations initiate useful work instead of waiting for prompts. [Docs](https://muxi.org/docs/concepts/proactiveness)
- **Reasoning RAG** – navigate hierarchical document trees, combine tree and vector retrieval, and synchronize remote knowledge from cloud storage, HTTP, rsync, FTP, and SFTP. [Docs](https://muxi.org/docs/concepts/knowledge-and-rag)

### Enterprise control without a separate platform

- **Group-based RBAC** – inheritance plus agent, MCP-server, and individual-tool allow/deny rules, with fail-closed external membership resolution. [Docs](https://muxi.org/docs/concepts/access-control)
- **Multi-tenant isolation** – sessions, memory, artifacts, and encrypted credentials stay scoped to the user while one formation serves an entire product or organization. [Docs](https://muxi.org/docs/concepts/multi-tenancy)
- **Persistent artifact memory** – generated documents, spreadsheets, presentations, charts, and code are versioned, encrypted, user-scoped, and recallable across sessions. [Docs](https://muxi.org/docs/concepts/artifacts)
- **Production resilience** – circuit breakers, fallback models, exponential backoff, graceful degradation, and idempotency keys make retries safe without duplicating work. [Docs](https://muxi.org/docs/deep-dives/resilience)
- **Built-in observability** – hundreds of typed lifecycle events trace models, tools, memory, workflows, and triggers, with secret and entity-based PII redaction before export. [Docs](https://muxi.org/docs/deep-dives/observability)

### Agents that connect to everything

- **MCP in both directions** – connect large tool catalogs without injecting every schema into every prompt, and expose any formation as an MCP server to Claude Desktop, Cursor, or another MCP client. [Docs](https://muxi.org/docs/guides/connect-via-mcp)
- **Coding-agent delegation** – hand repository work to Claude Code, Droid, OpenCode, Pi, or a custom headless adapter in isolated asynchronous workspaces. [Docs](https://muxi.org/docs/concepts/coding-delegation)
- **Zero-token job monitoring** – deterministic polling tracks long-running MCP work without spending LLM tokens asking whether it finished. [Docs](https://muxi.org/docs/reference/tools)
- **Generative UI** – streamed and non-streamed responses can carry typed options, action links, and MCP resources that all twelve SDKs expose idiomatically. [Docs](https://muxi.org/docs/reference/response-ui-widgets)
- **Triggers and outbound routing** – schedules, webhooks, and external events activate formations, then transformers deliver results to Slack, Telegram, Discord, email, or custom destinations. [Docs](https://muxi.org/docs/reference/triggers)

### Agent-native execution

- **Intelligent orchestration** – the Overlord routes requests, matches SOPs, decomposes complex work, coordinates agents, and can replan workflows when intermediate results change the best path. [Docs](https://muxi.org/docs/concepts/agents-and-orchestration)
- **Skills and sandboxed compute** – use open `SKILL.md` packages with progressive disclosure and execute scripts in an isolated RCE sandbox with resource and timeout controls. [Docs](https://muxi.org/docs/concepts/skills)
- **Hierarchical model selection** – choose cloud, local, or OpenAI-compatible models at the formation, agent, SOP, trigger, skill, or workflow-step level, with aliases and fallback chains. [Docs](https://muxi.org/docs/concepts/llm-providers)
- **A2A collaboration** – delegate work across formations and external agent services with API-key, OAuth 2.0, HMAC, and OpenID authentication. [Docs](https://muxi.org/docs/reference/a2a)
- **Multimodal by default** – process images, PDFs, audio, video, Office documents, and structured data through the same formation runtime. [Docs](https://muxi.org/docs/concepts/knowledge-and-rag)

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

agents:
  - sales-assistant
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

- **Internal tool builders** – Deploying AI systems across an organization? MUXI gives you group-based RBAC, scoped memory, per-user credentials, SOPs, observability, and self-tuning out of the box.

- **Developers tired of framework hell** – Spent months on LangChain orchestration code? MUXI replaces it with YAML configuration and a single deploy command.


## Open Source & Self-Hosted

MUXI is **open-source** and **self-hostable**. Your data stays on your infrastructure. No vendor lock-in. No per-seat pricing. No usage limits.

- **Self-hostable** – run anywhere, owned by you
- **Access-controlled** – fail-closed group RBAC down to individual tools
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
