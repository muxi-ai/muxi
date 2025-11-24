<p align="center">
  <img src="./assets/muxi-logo.svg" alt="MUXI" width="200"/>
</p>

<h1 align="center">MUXI</h1>

<p align="center">
  <strong>Infrastructure Stack for AI Agents</strong><br>
  Declare. Deploy. Own.
</p>

<p align="center">
  <a href="https://github.com/muxi-ai/server">Server</a> •
  <a href="https://github.com/muxi-ai/cli">CLI</a> •
  <a href="./docs">Docs</a> •
  <a href="./examples">Examples</a> •
  <a href="https://discord.gg/muxi">Discord</a>
</p>

<p align="center">
  <a href="https://github.com/muxi-ai/muxi/blob/main/LICENSE"><img src="https://img.shields.io/badge/license-Apache%202.0-blue.svg" alt="License"></a>
  <a href="https://discord.gg/muxi"><img src="https://img.shields.io/discord/placeholder?color=7289da&label=discord" alt="Discord"></a>
  <a href="https://twitter.com/muxi_ai"><img src="https://img.shields.io/twitter/follow/muxi_ai?style=social" alt="Twitter"></a>
</p>

---

## What is MUXI?

MUXI (Multiplexed eXtensible Intelligence) is **open-source infrastructure stack that makes AI agents first-class primitives** – not scripts, not prompt chains, but deployable units with built-in orchestration, memory, and observability.

Think of it like Docker or Terraform, but for agents. You declare what an agent is, what it can do, and how it behaves. MUXI handles the rest: execution, coordination, persistence, and scale.

```yaml
# formation.yaml
name: research-assistant
model: claude-sonnet-4-20250514
memory: semantic
tools:
  - web-search
  - document-reader
instructions: |
  You help users research topics thoroughly.
  Always cite sources and flag uncertainty.
```

```bash
muxi up
```

That's it. Your agent is running, stateful, and accessible via API.

---

## Quick Start

### 1. Install MUXI

```bash
curl -fsSL https://muxi.org/install | sudo bash
```

This installs the MUXI CLI and starts the server.

### 2. Pull a Formation

Formations are agent templates – pull one from the registry or create your own.

```bash
muxi pull @examples/research-assistant
```

### 3. Deploy

```bash
muxi up
```

Your agent is now running at `http://localhost:7788/agents/research-assistant`

### 4. Chat

```bash
muxi chat research-assistant
```

Or use the API:

```bash
curl -X POST http://localhost:7788/agents/research-assistant/chat \
  -H "Content-Type: application/json" \
  -d '{"message": "What are the latest developments in quantum computing?"}'
```

**[Full documentation →](./docs/getting-started)**

---

## Why MUXI Exists

The AI agent ecosystem is fragmented. Building production agents today means stitching together a dozen tools, reinventing state management, and praying your prompt chains hold together. Every team solves the same problems differently. Nothing is portable. Nothing is observable. Nothing is yours.

**We're building the missing layer.**

| Today's Reality | MUXI Approach |
|-----------------|---------------|
| Agents are code patterns scattered across files | Agents are declared, versioned, deployable units |
| Memory is an afterthought (or a SaaS product) | Memory is native – semantic, episodic, built-in |
| Multi-agent = custom orchestration code | Multi-agent = configuration |
| Observability requires custom instrumentation | Every agent call is traceable by default |
| Vendor lock-in to model providers | Swap models with one line |
| "Self-hosted" means running someone else's SaaS locally | Actually self-hosted. Your infra. Your data. Period. |

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

## How It Works

```
┌──────────────────────────────────────────────────────────┐
│                       Your App / CLI                     │
└─────────────────────────┬────────────────────────────────┘
                          ▼
┌──────────────────────────────────────────────────────────┐
│                      MUXI Server                         │
│         API • Sessions • Memory • Orchestration          │
│                github.com/muxi-ai/server                 │
└─────────────────────────┬────────────────────────────────┘
                          ▼
┌──────────────────────────────────────────────────────────┐
│                        LLM APIs                          │
│          Anthropic • OpenAI • Ollama • Others            │
└──────────────────────────────────────────────────────────┘
```

The **MUXI Server** is where agents live. It handles execution, memory, tool calls, and multi-agent coordination. You interact with it via the **CLI** or **HTTP API**.

---

## What Makes MUXI Different

### Declarative Agents
Define agents in YAML. Version control them. Deploy anywhere. No code required for most use cases.

### Native Memory
Agents have built-in semantic and episodic memory that persists across sessions. No external vector DB required (but you can bring your own).

### Model Agnostic
Anthropic, OpenAI, local models via Ollama – swap with one config change. No code rewrites.

### MCP Native
Full [Model Context Protocol](https://modelcontextprotocol.io) support. Connect to any MCP server. Your agents get access to thousands of tools.

### Observable by Default
Every agent invocation produces structured traces. See what your agent did, why, and how long it took.

### Actually Self-Hostable
No phone-home. No required cloud services. Run it on your laptop or your Kubernetes cluster. It's the same.

---

## Repository Map

This is the **meta repository** – documentation, examples, and coordination across MUXI projects.

| Repository | Description | Status |
|------------|-------------|--------|
| [`muxi-ai/server`](https://github.com/muxi-ai/server) | Self-hostable agent server | 🟢 Active |
| [`muxi-ai/cli`](https://github.com/muxi-ai/cli) | Command-line interface | 🟢 Active |
| [`muxi-ai/runtime`](https://github.com/muxi-ai/runtime) | Python runtime (for embedding) | 🟢 Active |
| [`muxi-ai/sdk-python`](https://github.com/muxi-ai/sdk-python) | Python SDK | 🟡 Coming soon |
| [`muxi-ai/sdk-typescript`](https://github.com/muxi-ai/sdk-typescript) | TypeScript SDK | 🟡 Coming soon |

> **Note:** Most users should use the **Server + CLI**. The [Runtime](https://github.com/muxi-ai/runtime) is for developers who want to embed MUXI directly into Python applications. Using the runtime doesn't give you more power or features – it's simply a different integration pattern.

**In this repo:**

```
muxi/
├── docs/                 # Documentation
│   ├── getting-started/  # Quick start guides
│   ├── concepts/         # Core concepts explained
│   ├── reference/        # API & config reference
│   └── guides/           # How-to guides
├── examples/             # Working examples & formations
├── CONTRIBUTING.md       # How to contribute
├── CHANGELOG.md          # Unified changelog
└── ROADMAP.md            # What's coming
```

---

## Examples

### Basic Agent

```yaml
# formation.yaml
name: assistant
model: claude-sonnet-4-20250514
instructions: You are a helpful assistant.
```

### Agent with Memory

```yaml
name: personal-assistant
model: claude-sonnet-4-20250514
memory:
  type: semantic
  persistence: sqlite
instructions: |
  You are a personal assistant. Remember user preferences
  and context from previous conversations.
```

### Agent with Tools

```yaml
name: researcher
model: claude-sonnet-4-20250514
memory: semantic
tools:
  - web-search
  - calculator
mcp_servers:
  - url: https://mcp.example.com/arxiv
instructions: |
  You help users research topics. Use web search for current
  information and arxiv for academic papers.
```

### Multi-Agent System

```yaml
name: support-team
agents:
  - name: triage
    model: claude-haiku-3-5-latest
    instructions: Classify incoming requests and route to specialists.
    
  - name: technical
    model: claude-sonnet-4-20250514
    tools: [code-executor, documentation-search]
    instructions: Handle technical support questions.
    
  - name: billing
    model: claude-sonnet-4-20250514
    tools: [stripe-api, invoice-lookup]
    instructions: Handle billing and account questions.

routing:
  entry: triage
  rules:
    - condition: category == "technical"
      target: technical
    - condition: category == "billing"
      target: billing
```

**[Browse all examples →](./examples)**

---

## Current Status

MUXI is in **active development**. The server and CLI are functional and being used in production by early adopters. We're stabilizing APIs before the broader public launch.

**What's ready:**
- Server with agent execution, memory, and tool support
- CLI for deployment and management
- MCP integration
- Basic multi-agent orchestration

**What's coming:**
- Formation registry (shareable agent templates)
- Advanced orchestration patterns
- SDKs for Python and TypeScript
- Hosted option (for those who want it)

See the **[Roadmap](./ROADMAP.md)** for details.

---

## Get Involved

### Follow Development
- ⭐ Star this repo to follow progress
- 🐦 Follow [@muxi_ai](https://twitter.com/muxi_ai) for updates
- 💬 Join the [Discord](https://discord.gg/muxi) for discussion

### Contribute
We welcome contributions. See **[CONTRIBUTING.md](./CONTRIBUTING.md)** for guidelines.

- Report bugs and request features via [Issues](https://github.com/muxi-ai/muxi/issues)
- Submit PRs to any MUXI repository
- Help improve documentation
- Share formations you've built

### Support the Project
MUXI is community-funded. If you find it valuable:
- [Become a GitHub Sponsor](https://github.com/sponsors/muxi-ai)
- Tell others about the project

---

## Who's Behind This

MUXI is created by [Ran Aroussi](https://github.com/ranaroussi), author of [yfinance](https://github.com/ranaroussi/yfinance) and other open-source tools used by millions of developers.

This project is backed by the belief that AI infrastructure should be open, not controlled by big tech. If you build it, you should own it.

---

## License

Apache 2.0 – Use it, modify it, ship it. See [LICENSE](./LICENSE) for details.

---

<p align="center">
  <strong>Infrastructure for the next generation of AI agents.</strong><br>
  <a href="https://github.com/muxi-ai">GitHub</a> •
  <a href="https://twitter.com/muxi_ai">Twitter</a> •
  <a href="https://discord.gg/muxi">Discord</a>
</p>
