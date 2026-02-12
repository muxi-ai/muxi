<h3 align="center">
  <img src="./assets/muxi-wordmark.svg" alt="MUXI" height="64"/><br>
  <strong>The Open-source AI Application Server</strong>
</h3>

<p align="center">
  <a href="./LICENSE.md"><img src="https://img.shields.io/badge/License-ELv2%20%2F%20Apache%202.0-c98b45.svg" alt="License"></a>
  <a href="https://muxi.org/docs"><img src="https://img.shields.io/badge/Docs-muxi.org-teal.svg" alt="Documentation"></a>
  <a href="https://github.com/muxi-ai/muxi/discussions"><img src="https://img.shields.io/badge/GitHub-Discussions-black.svg?logo=github" alt="Discussions"></a>
  <a href="https://twitter.com/muxi_ai"><img src="https://img.shields.io/badge/‌-‌@muxi__ai-blue.svg?logo=x" alt="Twitter"></a>
</p>

---

No one builds their own Nginx to deploy a website. No one should reinvent infrastructure to build AI.

MUXI (`/ˈmʌk.siː/`) is **production infrastructure for AI agents**. Not a framework. Not a wrapper. A **server** — where agents are native primitives with built-in orchestration, memory, observability, and scale.

| Concept | Docker | MUXI |
|---------|--------|------|
| **Engine** | Docker Engine | Server + Runtime |
| **Definition** | Dockerfile | Formation |
| **Registry** | Docker Hub | MUXI Registry |
| **CLI** | `docker` | `muxi` |


```bash
muxi pull @muxi/hello-world   # pull a formation
muxi deploy                   # deploy it
muxi chat hello-world         # talk to it
```

Three commands. Your agent is running, stateful, and accessible via API.

**Then build on top with SDKs:**

```python
from muxi import FormationClient

client = FormationClient(
    server_url="http://localhost:7890",
    formation_id="hello-world"
)

for chunk in client.chat_stream({ "message": "Hello!", "user_id": "user_123" }):
    print(chunk.get("text", ""), end="")
```

> [!TIP]
> MUXI introduced the [**Agent Formation Standard**](https://github.com/agent-formation) — an open spec for declarative AI systems. Agents, knowledge, A2A, and MCP tools defined in portable `.afs` files.


## Why MUXI?

| | MUXI | Frameworks (LangChain, CrewAI, ...) |
|---|---|---|
| **What it is** | Server infrastructure | Python libraries |
| **Deploy** | `muxi deploy` | Write your own deployment |
| **Configure** | Declarative `.afs` files | Imperative code |
| **Multi-tenancy** | Built-in isolation | Build it yourself |
| **Memory** | Three-tier (buffer + persistent + vector) | Bring your own |
| **Tools** | 1,000+ via MCP, loaded once | Per-call schema injection |
| **Observability** | 349 event types, 10+ export targets | Add external tools |

**Key numbers:** <100ms avg response &bull; 80%+ test coverage &bull; 21 LLM providers, 300+ models &bull; 92% semantic cache hit rate


## Example

```yaml
# formation.afs — define your AI system (it's just YAML)
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
# agents/sales-assistant.afs — define an agent
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
muxi deploy   # that's it — running, stateful, API-accessible
```


## Quick Start

**macOS:**
```bash
brew install muxi-ai/tap/muxi
```

**Linux:**
```bash
curl -fsSL https://muxi.org/install | sudo bash
```

**Windows:**
```powershell
powershell -c "irm https://muxi.org/install | iex"
```

**[Full documentation ->](https://muxi.org/docs/quickstart)**


## Who Is MUXI For?

- **Platform builders** — Building a SaaS with AI features? MUXI handles orchestration, memory, and multi-tenancy so you can focus on your product.

- **Internal tool builders** — Deploying AI assistants for your team? MUXI gives you SOPs, observability, and enterprise-grade infrastructure out of the box.

- **Developers tired of framework hell** — Spent months on LangChain orchestration code? MUXI replaces it with YAML configuration and a single deploy command.


## Documentation

| Looking for... | Go to |
|----------------|-------|
| Getting started | [Quickstart ->](https://muxi.org/docs/quickstart) |
| SDKs (12 languages) | [SDKs ->](https://muxi.org/docs/sdks) |
| How MUXI works | [ARCHITECTURE.md](https://github.com/muxi-ai/muxi/blob/main/ARCHITECTURE.md) |
| Contributing | [CONTRIBUTING.md](https://github.com/muxi-ai/muxi/blob/main/CONTRIBUTING.md) |
| All repositories | [REPOSITORIES.md](https://github.com/muxi-ai/muxi/blob/main/REPOSITORIES.md) |
| Licensing | [LICENSE.md](https://github.com/muxi-ai/muxi/blob/main/LICENSE.md) |
| Roadmap | [Roadmap ->](https://github.com/orgs/muxi-ai/projects/1) |


## Open Source & Self-Hosted

MUXI is **open-source** and **self-hostable**. Your data stays on your infrastructure. No vendor lock-in. No per-seat pricing. No usage limits.

- **Self-hostable** — run anywhere, owned by you
- **Observable** — see what's happening, always
- **Declarative** — version-controlled and reproducible
- **Open** — no lock-in, no surprises


## Agent Skills

This repo includes an [Agent Skill](https://agentskills.io) that gives any compatible AI agent (Claude Code, Cursor, GitHub Copilot, VS Code, etc.) deep knowledge of the MUXI platform.

See [`skills/`](./skills/) for setup instructions.


## Get Involved

- **Star** this repo to follow progress
- **[Discussions](https://github.com/muxi-ai/muxi/discussions)** – ask questions, share ideas, get help
- **[Roadmap](https://github.com/orgs/muxi-ai/projects/1)** – see what's coming
- **[@muxi_ai](https://twitter.com/muxi_ai)** – follow for updates
- **[CONTRIBUTING.md](https://muxi.org/contributing)** – contribute code


## Who's Behind This

MUXI is created by [**Ran Aroussi**](https://x.com/aroussi), author of [**Production-Grade Agentic AI**](http://productionaibook.com) and [open-source developer](https://github.com/ranaroussi) whose tools are used by millions of developers daily.


## License

Server and Runtime are [ELv2](https://www.elastic.co/licensing/elastic-license). Everything else (CLI, SDKs, formations, schemas) is [Apache 2.0](https://www.apache.org/licenses/LICENSE-2.0).

**TL;DR:** Use MUXI freely for your products and business. The only restriction: don't resell MUXI itself as a hosted service. See [LICENSE.md](https://muxi.org/licensing) for details.

---

<p align="center">
  <a href="https://github.com/muxi-ai">GitHub</a> &bull;
  <a href="https://twitter.com/muxi_ai">Twitter</a> &bull;
  <a href="https://github.com/muxi-ai/muxi/discussions">Discussions</a>
</p>
