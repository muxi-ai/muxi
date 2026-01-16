---
title: What is MUXI?
description: Production-ready infrastructure for AI agents
youtube-video: placeholder
---

# What is MUXI?

## The Agent Server

<!-- TODO: Replace with actual video embed -->
<!-- [Video: From zero to a multi-agent AI system in under 5 minutes] -->

MUXI is **production-ready infrastructure for AI agents**. Not a framework. Not a wrapper. A server.

Think of it this way:
- Websites have **web servers** (Nginx, Apache)
- APIs have **application servers** (Express, FastAPI)
- Agents now have **MUXI**

```
┌─────────────────────────────────────────────────────────────┐
│                    The MUXI Stack                           │
├─────────────────────────────────────────────────────────────┤
│   SDKs        Python, TypeScript, Go, Swift, Java...        │
├─────────────────────────────────────────────────────────────┤
│   APIs        REST endpoints for chat, deploy, stream       │
├─────────────────────────────────────────────────────────────┤
│   Runtime     Agents, memory, tools, orchestration          │
├─────────────────────────────────────────────────────────────┤
│   Server      Multi-formation deployments, auth, routing    │
└─────────────────────────────────────────────────────────────┘
```

---

## The Problem

Building a demo agent is easy:

```python
response = openai.chat("Hello, world!")
```

Building a **production** agent system requires:

- Multi-agent orchestration and coordination
- Memory management (short-term, long-term, semantic)
- Tool integration (APIs, databases, file systems)
- Multi-tenant isolation and security
- Observability and debugging
- Deployment, scaling, and lifecycle management

**Most teams spend 3-6 months building this infrastructure before shipping a single feature.**

---

## The Solution

MUXI treats agents as **native infrastructure primitives**. Declare them in YAML, deploy with one command, manage like containers.

```yaml
# formation.afs - A complete AI system definition
schema: "1.0.0"
id: customer-support

llm:
  models:
    - text: openai/gpt-4o

agents:
  - id: triage
    role: "Route customer inquiries"
  - id: technical
    role: "Handle technical issues"
  - id: billing
    role: "Handle billing questions"

memory:
  persistent:
    enabled: true

mcp:
  servers:
    - id: zendesk
    - id: stripe
```

```bash
muxi deploy
# That's it. You're live.
```

---

## Key Capabilities

:::: cols=2

[[card]]

#### LLM-Agnostic

Use OpenAI, Anthropic, Google, Azure, AWS Bedrock, or local models (Ollama, llama.cpp). Swap providers in seconds. Mix models per agent. Automatic failover when providers fail.

[Learn more →](concepts/llm-providers.md)

[[/card]]

[[card]]

#### Multi-Tenant by Design

Each user stores their own credentials – OAuth tokens, API keys, connection strings. Complete session isolation. One formation serves thousands of users with personalized experiences.

[Learn more →](concepts/multi-tenancy.md)

[[/card]]

[[card]]

#### Intelligent Orchestration

The Overlord automatically breaks down complex requests into subtasks. No predefined workflows – agents analyze complexity, identify dependencies, and execute in optimal order.

[Learn more →](concepts/agents-and-orchestration.md)

[[/card]]

[[card]]

#### Agent Collaboration (A2A)

Agents within your formation work together seamlessly. Delegate tasks across formations. Collaborate with external agents from other organizations via A2A protocol.

[Learn more →](concepts/agents-and-orchestration.md)

[[/card]]

[[card]]

#### 1,000+ MCP Tools

Access GitHub, Slack, Stripe, databases, file systems, and more through Model Context Protocol. Unlike typical implementations, MUXI indexes tool schemas once – not dumped into every context window.

[Learn more →](concepts/tools-and-mcp.md)

[[/card]]

[[card]]

#### Three-Tier Memory

Buffer memory for immediate context. Persistent memory across sessions. Vector memory for semantic retrieval. User synopsis caching reduces token usage by 80%+.

[Learn more →](concepts/memory-system.md)

[[/card]]

[[card]]

#### Knowledge & RAG

Pre-load agents with domain knowledge from files and URLs. PDFs, markdown, Office docs, images – agents retrieve relevant context without fine-tuning. Update knowledge by updating files.

[Learn more →](concepts/knowledge-and-rag.md)

[[/card]]

[[card]]

#### Real-Time Streaming

Stream responses as agents think, not after completion. SSE and WebSocket support. Token-by-token delivery for chat interfaces. Progress updates for multi-step operations.

[Learn more →](deep-dives/real-time-streaming.md)

[[/card]]

::::

---

## Built for Production

MUXI isn't a prototype tool – it's production infrastructure with the numbers to prove it.

| Metric | Value |
|--------|-------|
| Response Time | <100ms |
| Test Coverage | 88.9% |
| Observability Events | 349 typed events |
| LLM Cache Hit Rate | ~72% |

---

## Declare Once, Deploy Everywhere

MUXI created the [Agent Formation Schema](https://agentformation.org) – an open spec for declarative AI systems. Agents, knowledge, tools, and settings defined in portable `.afs` files.

**Like a Dockerfile for containers, but for agent intelligence.**

Formations are:
- **Portable** – Run anywhere MUXI runs
- **Versionable** – Git-friendly YAML files
- **Shareable** – Push to the registry, pull anywhere

---

## The Formation Registry

Discover and share formations through the [MUXI Registry](https://registry.muxi.org). Like Docker Hub, but for AI agents.

```bash
# Pull a pre-built formation
muxi pull @muxi/customer-support

# Publish your own
muxi publish my-agent:1.0.0
```

Browse community formations, install with one command, customize for your needs.

---

## Open Source & Self-Hosted

MUXI is **open-source** and **self-hostable**. Your data stays on your infrastructure. No vendor lock-in. No per-seat pricing. No usage limits.

- Full source code on [GitHub](https://github.com/muxi-ai)
- Single binary installation
- Works on Linux, macOS, Windows, Docker
- Enterprise features available

---

## How It Compares

| | MUXI | Frameworks (LangChain, CrewAI) | Cloud AI (Bedrock, Vertex) |
|---|---|---|---|
| **What it is** | Infrastructure | Library | Managed service |
| **Deployment** | `muxi deploy` | You build it | Vendor-managed |
| **Multi-tenancy** | Built-in | You build it | Limited |
| **Self-hosted** | Yes | N/A | No |
| **LLM choice** | Any provider | Any provider | Vendor models |
| **Observability** | 349 events | You build it | Vendor tools |

**MUXI is infrastructure, not a framework.** Frameworks help you write agent logic. MUXI runs agents in production.

---

## Next Steps

:::: cols=2

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
See real formations for common use cases.
[[/card]]

(installation/README.md)[[card]]
#### Installation
Install on macOS, Linux, Windows, or Docker.
[[/card]]

::::
