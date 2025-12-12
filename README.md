<p align="center">
  <img src="./muxi-logo.svg" alt="MUXI" width="128"/>
</p>

<h1 align="center">MUXI</h1>

<p align="center">
  <strong>Open-Source Infrastructure for AI Agents</strong>
</p>

<p align="center">
  <a href="./LICENSE.md"><img src="https://img.shields.io/badge/License-ELv2%20%2F%20Apache%202.0-c98b45.svg" alt="License"></a>
  <a href="https://github.com/muxi-ai/community"><img src="https://img.shields.io/badge/GitHub-Discussions-black.svg?logo=github" alt="Community"></a>
  <a href="https://github.com/muxi-ai/community"><img src="https://img.shields.io/badge/‌-‌@muxi__ai-blue.svg?logo=x" alt="Community"></a>
</p>

---

MUXI (/muk-siː/, Multiplexed eXtensible Intelligence) is an open-source project that makes agents native primitives — not ad hoc scripts or chained prompts — but infrastructure-level processes with built-in orchestration, observability, and scale.

MUXI runs **Agent Formations** –  complete AI systems packaged as **deployable units**. Agents, knowledge, tools, SOPs, triggers, and settings defined in YAML. Production infrastructure with memory, multi-tenancy, observability, and intelligent orchestration built in.

Agents, knowledge, A2A, and MCP (Model Context Protocol) tools defined in portable `.afs` files, together with SOPs, triggers, and settings defined in YAML. Production infrastructure with memory, multi-tenancy, observability, and intelligent orchestration built in.


> [!TIP]
> MUXI introduced the [Agent Formation Standard](https://github.com/agent-formation) – an open spec for declarative AI systems. Agents, knowledge, A2A, and MCP (Model Context Protocol) tools defined in portable `.afs` files.
>
> **Like a Dockerfile for containers, but for agent intelligence.**


## Example

```bash
# create a formation
muxi new formation customer-support
```

```yaml
# formation.afs
schema: "1.0.0"
id: "customer-support"
description: |
  Helps customers with product support and refunds
agents:
  - id: "sales-assistant"
    knowledge:
      - ./knowledge/product-info.docx
      - ./knowledge/shipping-rates.csv
  - id: "refund-handler"
    knowledge:
      - ./knowledge/policies/*
mcp:
  servers:
  - id: "shopify"
    auth:
      type: "bearer"
      token: "${{ secrets.SHOPIFY_TOKEN }}"
  - id: "stripe"
  ...
```

```bash
# deploy the formation
muxi deploy
```

That's it. Your agent is running, stateful, and accessible via API.

---

## Our creed

**AI infrastructure should be open, not owned by big tech.**

We believe AI systems should be:

- **Self-hostable** - Run anywhere, owned by you
- **Observable** - See what's happening, always
- **Declarative** - Version-controlled and reproducible
- **Open** - No secrets, no lock-in, no surveillance

If you build it, you should control it.

---

## What we're building

Modern AI workflows still treat agents as code patterns or framework constructs.

We think they deserve their own foundation – a layer where:

- Agents are declared, not improvised
- Coordination and reasoning aren't bolted on
- Memory, context, and tools feel native
- Any model can be used without vendor lock-in
- The system itself understands how agents operate

We're building infrastructure that reflects this philosophy.

---

## Quick Start

```bash
# Install
curl -fsSL https://muxi.org/install | bash

# Pull a formation
muxi pull @examples/research-assistant

# Deploy
muxi deploy

# Chat
muxi chat research-assistant
```

**[Full documentation →](./docs)**

---

## Documentation

| Looking for... | Go to |
|----------------|-------|
| How MUXI works | [ARCHITECTURE.md](./ARCHITECTURE.md) |
| Contributing guidelines | [CONTRIBUTING.md](./CONTRIBUTING.md) |
| Development setup | [DEVELOPMENT.md](./DEVELOPMENT.md) |
| Git workflow | [GIT-WORKFLOW.md](./GIT-WORKFLOW.md) |
| All repositories | [REPOSITORIES.md](./REPOSITORIES.md) |
| Licensing | [LICENSE.md](./LICENSE.md) |
| Roadmap | [Roadmap →](https://github.com/orgs/muxi-ai/projects/1) |

---

## Get Involved

- ⭐ Star this repo to follow progress
- 💬 Join the [Community](https://github.com/muxi-ai/community) for discussion
- 🐦 Follow [@muxi_ai](https://twitter.com/muxi_ai) for updates
- 🛠️ See [CONTRIBUTING.md](./CONTRIBUTING.md) to contribute

---

## Who's Behind This

MUXI is created by [**Ran Aroussi** (𝕏)](https://x.com/aroussi), author of the [📚 **Production-Grade Agentic AI**](http://productionaibook.com) book, and [🧑‍💻 **open-source developer**](https://github.com/ranaroussi) whose tools are used by millions of developers daily.

---

## License

MUXI uses a dual-license model.

Server and Runtime are [ELv2](https://www.elastic.co/licensing/elastic-license), while the rest of the MUXI Stack is licensed under [Apache 2.0](https://www.apache.org/licenses/LICENSE-2.0).

**TL;DR:**
Use MUXI freely for your products and business. The only restriction: don't resell MUXI itself as a hosted service.

See [LICENSE.md](./LICENSE.md) for details.

---

<p align="center">
  <a href="https://github.com/muxi-ai">GitHub</a> •
  <a href="https://twitter.com/muxi_ai">Twitter</a> •
  <a href="https://github.com/muxi-ai/community">Community</a>
</p>
