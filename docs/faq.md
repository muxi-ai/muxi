---
title: Common Questions and Use-cases
description: Find your question, get the answer
---

# Common Questions and Use-cases

## What can MUXI do? How do I...?

Find your question, get the answer. Each section links to deeper documentation.

---

## Building Agents

[[toggle How do I define an agent?]]

In YAML. Create an `.afs` file (Agent Formation Schema) with your agent's name, persona, LLM provider, and capabilities. No framework code required.

```yaml
name: support-agent
agents:
  support:
    persona: "Helpful customer support specialist"
    provider: openai/gpt-4o
    tools: [search, ticket-system]
    knowledge: ./docs/
```

**Learn more:** [Formation Schema](concepts/formation-schema.md) | [Quickstart](quickstart.md)

[[/toggle]]

[[toggle How do agents remember context?]]

MUXI has a **three-tier memory system**:

1. **Buffer** - Current conversation (last N messages)
2. **Working** - Session-level context and facts extracted mid-conversation
3. **Persistent** - Long-term memory stored in SQLite/PostgreSQL, survives restarts

Plus **User Synopsis Caching** - the LLM periodically synthesizes what it knows about a user, reducing token usage by 80%+ on long conversations.

**Learn more:** [Memory System](concepts/memory-system.md) | [Memory Internals](deep-dives/memory-internals.md) | [Add Memory](guides/add-memory.md)

[[/toggle]]

[[toggle How do I give agents access to tools and APIs?]]

Via **MCP (Model Context Protocol)**. MUXI supports 1,000+ pre-built tools: web search, databases, file systems, APIs, and more.

```yaml
tools:
  - mcp: brave-search
  - mcp: postgres
  - mcp: slack
```

Tools are loaded efficiently - schemas indexed once, not dumped into every request.

**Learn more:** [Tools & MCP](concepts/tools-and-mcp.md) | [Add Tools](guides/add-mcp-tools.md)

[[/toggle]]

[[toggle How do I give agents domain knowledge?]]

Point agents at documents. MUXI indexes PDFs, Markdown, Word, Excel, images, and more. Each agent can have its own knowledge set.

```yaml
agents:
  legal:
    knowledge: ./contracts/
  support:
    knowledge: ./help-docs/
```

Uses semantic search (meaning, not keywords) and incremental indexing (only re-indexes changed files).

**Learn more:** [Knowledge & RAG](concepts/knowledge-and-rag.md) | [Add Knowledge](guides/add-knowledge.md)

[[/toggle]]

[[toggle Can I define workflows or procedures for agents?]]

Yes - **Standard Operating Procedures (SOPs)**. Define step-by-step workflows that agents follow for specific tasks:

```yaml
sops:
  refund-request:
    steps:
      - Verify purchase in system
      - Check refund policy eligibility
      - Process refund or explain denial
```

Agents automatically follow SOPs when they match the user's request.

**Learn more:** [SOPs](concepts/standard-operating-procedures.md) | [Create SOPs](guides/create-sops.md)

[[/toggle]]

---

## Multi-Agent Systems

[[toggle How do multiple agents work together?]]

The **Overlord** orchestrates everything:

- **Intelligent Routing** - Requests go to the best agent automatically
- **Task Decomposition** - Complex requests broken into steps
- **Agent-to-Agent (A2A)** - Agents delegate to specialists across formations

You define agents with specialties; the Overlord figures out who handles what.

**Learn more:** [The Overlord](concepts/overlord.md) | [Agents & Orchestration](concepts/agents-and-orchestration.md) | [How Orchestration Works](deep-dives/how-orchestration-works.md)

[[/toggle]]

[[toggle Which LLMs are supported?]]

All of them. MUXI is **LLM-agnostic**:

- OpenAI (GPT-4o, GPT-4, etc.)
- Anthropic (Claude 3.5, Claude 3, etc.)
- Google (Gemini)
- Ollama (local models)
- Any OpenAI-compatible API

Different agents can use different providers in the same formation.

**Learn more:** [LLM Providers](concepts/llm-providers.md) | [Formation Schema](reference/formation-schema.md)

[[/toggle]]

---

## Production & Scale

[[toggle Can multiple users share one deployment?]]

Yes - **multi-tenancy** is built in. Each user gets:

- Isolated memory (no data leakage)
- Separate conversation history
- Their own API credentials for tools

One server, many users, complete isolation.

**Learn more:** [Multi-Tenancy](concepts/multi-tenancy.md) | [Multi-Tenancy Deep Dive](deep-dives/multi-tenancy.md)

[[/toggle]]

[[toggle How do I deploy to production?]]

One command:

```bash
muxi deploy
```

MUXI is a **single binary** - no dependencies, no Docker required (though Docker is supported). Supports GitOps workflows for PR-based deployments.

**Learn more:** [Deploy to Production](guides/deploy-to-production.md) | [Set Up CI/CD](guides/setup-ci-cd.md) | [Production Checklist](server/production-checklist.md)

[[/toggle]]

[[toggle How do I monitor my agents?]]

**Built-in observability**. Every agent action emits structured events:

- LLM calls (tokens, latency, cost)
- Tool usage
- Workflow steps
- Errors

Export to Datadog, Elastic, Splunk, OpenTelemetry, or webhooks - no sidecars needed.

**Learn more:** [Observability](deep-dives/observability.md) | [Observability Events](deep-dives/observability-events.md) | [Set Up Monitoring](guides/setup-monitoring.md)

[[/toggle]]

[[toggle Is it reliable enough for production?]]

Yes. **Enterprise resilience patterns** are baked in:

- Circuit breakers for failing dependencies
- Retries with exponential backoff
- Graceful degradation
- LLM response caching to reduce load and latency
- Auto-restart for crashed formations

**Learn more:** [Resilience](deep-dives/resilience.md) | [Security Model](deep-dives/security-model.md)

[[/toggle]]

---

## Real-Time & Async

[[toggle Can I stream responses in real-time?]]

Yes. MUXI supports:

- **SSE (Server-Sent Events)** - Stream tokens as the model thinks
- **WebSockets** - Bidirectional real-time communication

Perfect for chat UIs and live dashboards.

**Learn more:** [Real-Time Streaming](deep-dives/real-time-streaming.md) | [Response Formats](deep-dives/response-formats.md)

[[/toggle]]

[[toggle What about long-running tasks?]]

Use **async operations**. Long tasks run in the background with status updates. Users aren't left waiting.

**Learn more:** [Async Operations](deep-dives/async-operations.md)

[[/toggle]]

[[toggle Can external systems trigger agents?]]

Yes - **triggers and webhooks**:

- HTTP webhooks trigger agent actions
- Cron-style scheduled tasks
- Natural language scheduling ("remind me tomorrow")

**Learn more:** [Triggers & Webhooks](concepts/triggers-and-webhooks.md) | [Create Triggers](guides/create-triggers.md)

[[/toggle]]

---

## Security

[[toggle How are secrets and API keys handled?]]

**Encrypted at rest**. API keys never stored in plain text. Per-user credentials supported - each user can store their own keys for tools.

**Learn more:** [Secrets & Security](concepts/secrets-and-security.md)

[[/toggle]]

[[toggle How does authentication work?]]

**HMAC-signed requests** between CLI, SDKs, and server. Plus complete user isolation - data never leaks between users.

**Learn more:** [Authentication](server/authentication.md) | [Security Model](deep-dives/security-model.md)

[[/toggle]]

---

## Developer Experience

[[toggle How do I develop locally?]]

```bash
muxi dev
```

Runs your formation locally with hot reload. Change your `.afs` file, see changes immediately.

**Learn more:** [Quickstart](quickstart.md) | [CLI Cheatsheet](cli/cheatsheet.md)

[[/toggle]]

[[toggle Are there SDKs?]]

Yes - **Python, TypeScript, and Go**:

```python
from muxi import Muxi
client = Muxi()
response = client.chat("Hello!")
```

**Learn more:** [Python SDK](sdks/python-sdk.md) | [TypeScript SDK](sdks/typescript-sdk.md) | [Go SDK](sdks/go-sdk.md)

[[/toggle]]

[[toggle Can I embed MUXI in my own app?]]

Yes. The **embeddable runtime** lets you run MUXI inside your application - no separate server process.

**Learn more:** [Embed in Your App](runtime/embed-in-your-app.md)

[[/toggle]]

---

## Registry & Sharing

[[toggle Can I use pre-built agents?]]

Yes. Pull formations from the **MUXI Registry**:

```bash
muxi pull @muxi/customer-support
muxi pull @muxi/code-reviewer
```

**Learn more:** [Registry](concepts/registry.md) | [Examples](examples/README.md)

[[/toggle]]

[[toggle Can I share my formations?]]

Yes. Publish to the registry:

```bash
muxi publish
```

Supports semantic versioning, private formations, and organization sharing.

**Learn more:** [Publish Formations](registry/publish-formations.md) | [Versioning](registry/versioning.md)

[[/toggle]]

---

## Business & Licensing

[[toggle Is MUXI really free?]]

Yes. **The core stack is always free and self-hostable.** No paywalls, no feature restrictions, no bait-and-switch licensing.

- Server, Runtime, CLI, SDKs - all open source
- Self-host on your infrastructure forever
- No usage limits, no "free tier"

**Learn more:** [Pricing](https://muxi.ai/pricing)

[[/toggle]]

[[toggle How do you make money?]]

MUXI follows the standard open-source infrastructure model:

1. **GitHub Sponsors** - Supporting OSS development
2. **Professional services** - Priority support and deployment help for production teams

The core product stays free. Revenue comes from optional services, not from restricting features.

[[/toggle]]

[[toggle What's the licensing model?]]

| Component | License | What it means |
|-----------|---------|---------------|
| Server & Runtime | Elastic License 2.0 | Free to use, can't resell as a competing service |
| CLI & SDKs | Apache 2.0 | Fully permissive, do anything |
| Formations | Apache 2.0 | Your agents are yours |

**You CAN:**
- Use freely for internal projects, products, research
- Build and sell products that use MUXI
- Deploy for clients and customers
- Self-host on your infrastructure

**You CAN'T:**
- Offer MUXI itself as a managed service (that's it)

[[/toggle]]

[[toggle What if you disappear tomorrow?]]

You're protected:

- **Self-hosted** - Runs on your infrastructure, not ours
- **Open source** - Fork and maintain the code yourself
- **Portable formats** - Formations are YAML, not proprietary
- **Standard protocols** - MCP and A2A work with other systems

MUXI is backed by VarOps LLC with a long-term commitment. Revenue comes from sustainable services, not VC burn rate.

[[/toggle]]

[[toggle How is this different from LangChain/CrewAI?]]

Different layer of the stack:

| | LangChain/CrewAI | MUXI |
|---|---|---|
| **What** | Python frameworks | Production server |
| **How** | Write code | Declare in YAML |
| **Deploy** | You figure it out | `muxi deploy` |
| **Memory** | BYO | Built-in (3-tier) |
| **Multi-tenant** | BYO | Built-in |
| **Observability** | BYO | Built-in |

LangChain helps you build agents in code. MUXI runs agents in production.

[[/toggle]]

---

## Next Steps

[>] [Quickstart](quickstart.md) - Build your first agent in 5 minutes
[+] [Installation](installation/README.md) - Get MUXI running on your machine
[+] [Architecture](concepts/architecture.md) - Understand how it all fits together
[+] [Examples](examples/README.md) - See real formations in action
