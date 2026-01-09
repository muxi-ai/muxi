---
title: Feature Overview
description: Everything MUXI can do, at a glance
---

# Feature Overview

## A quick reference to all MUXI capabilities

MUXI provides everything you need to build and deploy production AI agents: multi-agent orchestration, memory systems, tool integration, security, and more. This page lists all features with links to detailed documentation.

> [!NOTE]
> Prerequisites: MUXI installed and a running server (see Quickstart/Installation).


## Agents & Intelligence

| Feature | Description | Learn More |
|---------|-------------|------------|
| **Multi-Agent Systems** | Specialized agents that collaborate on complex tasks | [Agents & Orchestration](concepts/agents.md) |
| **Intelligent Routing** | Overlord automatically routes requests to the best agent | [Agents & Orchestration](concepts/agents.md) |
| **Task Decomposition** | Complex requests broken into steps automatically | [How Orchestration Works](deep-dives/orchestration.md) |
| **Agent Collaboration (A2A)** | Agents delegate to specialists across formations | [Agents & Orchestration](concepts/agents.md) |
| **Standard Operating Procedures** | Predefined workflows agents follow for specific tasks | [Create SOPs](guides/sops.md) |
| **LLM-Agnostic** | Use OpenAI, Anthropic, Google, Ollama, or any provider | [Agent Formation Schema](reference/schema.md) |

---

## Memory & Context

| Feature | Description | Learn More |
|---------|-------------|------------|
| **Three-Tier Memory** | Buffer, working, and persistent memory layers | [Memory System](concepts/memory.md) |
| **Semantic Search** | Find relevant context by meaning, not keywords | [Memory System](concepts/memory.md) |
| **User Synopsis Caching** | LLM-synthesized user profiles reduce tokens by 80%+ | [Memory Internals](deep-dives/memory.md) |
| **Persistent Memory** | Conversations survive restarts (SQLite/PostgreSQL) | [Add Memory](guides/add-memory.md) |
| **Multi-User Isolation** | Each user gets completely separate memory | [Multi-Tenancy](deep-dives/multi-tenancy.md) |

---

## Knowledge & RAG

| Feature | Description | Learn More |
|---------|-------------|------------|
| **Document Ingestion** | Index PDFs, Markdown, Word, Excel, and more | [Knowledge & RAG](concepts/knowledge.md) |
| **Multimodal Support** | Images, diagrams, and charts analyzed by vision models | [Knowledge & RAG](concepts/knowledge.md) |
| **Agent-Specific Knowledge** | Different agents access different document sets | [Add Knowledge](guides/add-knowledge.md) |
| **Incremental Indexing** | Only changed files re-indexed on restart | [Knowledge & RAG](concepts/knowledge.md) |

---

## Tools & MCP

| Feature | Description | Learn More |
|---------|-------------|------------|
| **1,000+ MCP Tools** | Web search, databases, APIs, file systems, and more | [Tools & MCP](concepts/tools.md) |
| **Context-Efficient Loading** | Tool schemas indexed once, not dumped into every request | [Tools & MCP](concepts/tools.md) |
| **Per-User Credentials** | Each user stores their own API keys for tools | [Tools & MCP](concepts/tools.md) |
| **Agent-Specific Tools** | Restrict which agents can use which tools | [Add Tools](guides/add-tools.md) |
| **Natural Language Scheduling** | "Remind me tomorrow" creates scheduled tasks | [Tools & MCP](concepts/tools.md) |

---

## Triggers & Automation

| Feature | Description | Learn More |
|---------|-------------|------------|
| **Webhook Triggers** | External systems trigger agent actions via HTTP | [Create Triggers](guides/triggers.md) |
| **Scheduled Tasks** | Cron-style or natural language scheduling | [Triggers](reference/triggers.md) |
| **Event-Driven** | Agents respond to system events automatically | [Observability](deep-dives/observability.md) |

---

## Real-Time & Streaming

| Feature | Description | Learn More |
|---------|-------------|------------|
| **SSE Streaming** | Server-sent events for real-time responses | [Real-Time Streaming](deep-dives/streaming.md) |
| **WebSocket Support** | Bidirectional real-time communication | [Real-Time Streaming](deep-dives/streaming.md) |
| **Async Operations** | Long tasks run in background with status updates | [Async Operations](deep-dives/async.md) |

---

## Security & Secrets

| Feature | Description | Learn More |
|---------|-------------|------------|
| **Encrypted Secrets** | API keys encrypted at rest, never in plain text | [Secrets & Security](concepts/secrets.md) |
| **HMAC Authentication** | Signed requests between CLI, SDKs, and server | [Authentication](server/authentication.md) |
| **User Isolation** | Complete data separation between users | [Multi-Tenancy](deep-dives/multi-tenancy.md) |
| **Path Restrictions** | Limit file system access per tool | [Security Model](deep-dives/security.md) |

---

## Deployment & Operations

| Feature | Description | Learn More |
|---------|-------------|------------|
| **One Command Deploy** | `muxi deploy` pushes to production | [Deploy to Production](guides/deploy.md) |
| **Single Binary Server** | No dependencies, just download and run | [Installation](installation/README.md) |
| **GitOps Workflow** | Version control formations, PR reviews for changes | [Set Up CI/CD](guides/ci-cd.md) |
| **Built-in Observability** | Metrics, logs, and traces out of the box | [Observability](deep-dives/observability.md) |
| **Auto-Restart** | Crashed formations restart automatically | [Server Configuration](server/configuration.md) |

---

## Registry & Sharing

| Feature | Description | Learn More |
|---------|-------------|------------|
| **Pull Formations** | `muxi pull @muxi/starter` gets you started instantly | [Registry](concepts/registry.md) |
| **Publish Formations** | Share your formations with the community | [Publish Formations](registry/publishing.md) |
| **Semantic Versioning** | Version control with instant rollbacks | [Versioning](registry/versioning.md) |
| **Private Formations** | Keep formations private or share with your org | [Your Account](registry/account.md) |

---

## Developer Experience

| Feature | Description | Learn More |
|---------|-------------|------------|
| **Local Development** | `muxi dev` runs formations locally with hot reload | [Quickstart](quickstart.md) |
| **Native SDKs** | Python, TypeScript, and Go libraries | [SDKs](sdks/README.md) |
| **CLI** | Full control from the command line | [CLI Cheatsheet](cli/cheatsheet.md) |
| **Embeddable Runtime** | Run MUXI inside your own application | [Embed in Your App](runtime/embedding.md) |

---

## Next Steps

[+] [Quickstart](quickstart.md) - Build your first agent in 5 minutes
[+] [Installation](installation/README.md) - Get MUXI running on your machine
[+] [Architecture](concepts/architecture.md) - Understand how it all fits together
