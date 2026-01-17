---
title: Documentation
description: Complete guide to building AI agents with MUXI
---
# Documentation

## Getting Started

* [What is MUXI](what-is-muxi.md)
* [Quickstart](quickstart.md)
* [Installation](installation/README.md)
* [How It Works](how-it-works.md)
* [Use-Cases](use-cases.md)
* [Examples](examples/README.md)
* [How Do I...?](faq.md)
* [Glossary](glossary.md)
* [Changelog](changelog.md)

## Core Concepts

* Foundation
  * [How It All Fits](concepts/architecture.md)
  * [Formation Schema](concepts/formation-schema.md)
  * [The Overlord](concepts/overlord.md)

* Building Blocks
  * [Agents & Orchestration](concepts/agents-and-orchestration.md)
  * [Tools & MCP](concepts/tools-and-mcp.md)
  * [Memory System](concepts/memory-system.md)
  * [Sessions](concepts/sessions.md)
  * [Knowledge & RAG](concepts/knowledge-and-rag.md)

* Communication
  * [Clarifications](concepts/clarification.md)
  * [Overlord Persona](concepts/persona.md)
  * [Structured Output](concepts/structured-output.md)
  * [Artifacts](concepts/artifacts.md)

* Advanced Features
  * [Workflows](concepts/workflows-and-task-decomposition.md)
  * [Human-in-the-Loop](concepts/human-in-the-loop.md)
  * [Async Processing](concepts/async.md)
  * [Triggers & Webhooks](concepts/triggers-and-webhooks.md)
  * [Scheduled Tasks](concepts/scheduled-tasks.md)
  * [SOPs](concepts/standard-operating-procedures.md)

* Production
  * [LLM Providers](concepts/llm-providers.md)
  * [Secrets & Security](concepts/secrets-and-security.md)
  * [User Credentials](concepts/user-credentials.md)
  * [Multi-Tenancy](concepts/multi-tenancy.md)
  * [Multi-Identity Users](concepts/multi-identity.md)
  * [The Registry](concepts/registry.md)
  * [Observability](concepts/observability.md)

## Components

* [Server](server/README.md)
  * [Setup](server/setup.md)
  * [Configuration](server/configuration.md)
  * [Authentication](server/authentication.md)
  * [Managing Formations](server/managing-formations.md)
  * [Monitoring](server/monitoring.md)
  * [Production Checklist](server/production-checklist.md)
  * [Server API](api/server)
* [CLI](cli/README.md)
  * [Setup](cli/setup.md)
  * [Configuration](cli/configuration.md)
  * [Quick Reference](cli/cheatsheet.md)
* [SDKs](sdks/README.md)
  * [Python](sdks/python-sdk.md)
  * [TypeScript](sdks/typescript-sdk.md)
  * [Go](sdks/go-sdk.md)
* [Registry](registry/README.md)
  * [Your Account](registry/account.md)
  * [Publish Formations](registry/publish-formations.md)
  * [Versioning](registry/versioning.md)
* [Runtime](runtime/README.md)
  * [LLM Caching](reference/llm-caching.md)
  * [Request Cancellation](reference/request-cancellation.md)
  * [Session Restore](reference/session-restore.md)
  * [Embed in Your App](runtime/embed-in-your-app.md)
  * [Formation API](api/formation)


## Guides

* [Overview](guides/README.md)

* Basics
  * [Add Tools (MCP)](guides/add-mcp-tools.md)
  * [Add Memory](guides/add-memory.md)
  * [Add Knowledge](guides/add-knowledge.md)

* Building Features
  * [Create Triggers](guides/create-triggers.md)
  * [Create SOPs](guides/create-sops.md)
  * [Build Multi-Agent Teams](guides/build-multi-agent-systems.md)
  * [Build Custom UI](guides/build-custom-ui.md)

* Going Live
  * [Deploy to Production](guides/deploy-to-production.md)
  * [Set Up CI/CD](guides/setup-ci-cd.md)
  * [Set Up Monitoring](guides/setup-monitoring.md)
  * [Troubleshooting](guides/troubleshooting.md)

* [API Reference](reference/api-reference.md)

## Formation Reference

* [Overview](reference/README.md)
* [Examples](reference/examples.md)

* Schema Files
  * [Formation Schema](reference/formation-schema.md)
  * [Agents](reference/agents.md)
  * [Tools](reference/tools.md)
  * [Memory](reference/memory.md)
  * [Knowledge](reference/knowledge.md)
  * [Triggers](reference/triggers.md)
  * [SOPs](reference/sops.md)
  * [Secrets](reference/secrets.md)
  * [Persona](reference/persona.md)
  * [Workflows](reference/workflows.md)
  * [Approvals](reference/approvals.md)


## Deep Dives

* [Overview](deep-dives/README.md)
* [Request Lifecycle](deep-dives/request-lifecycle.md)
* [How Orchestration Works](deep-dives/how-orchestration-works.md)
* [Memory Internals](deep-dives/memory-internals.md)
* [Real-Time Streaming](deep-dives/real-time-streaming.md)
* [Response Formats](deep-dives/response-formats.md)
* [Async Operations](deep-dives/async-operations.md)
* [Multi-Tenancy](deep-dives/multi-tenancy.md)
* [Multi-Identity](deep-dives/multi-identity.md)
* [Observability](deep-dives/observability.md)
* [Event Types](deep-dives/observability-events.md)
* [Security Model](deep-dives/security-model.md)
* [Resilience](deep-dives/resilience.md)
* [Multi-Server Memory](deep-dives/multi-server-memory.md)

## Miscellaneous

* [Glossary](glossary.md)
* [Changelog](changelog.md)
* [Contributing](contributing.md)
* [OneLLM](https://github.com/muxi-ai/onellm)
* [FAISSx](https://github.com/muxi-ai/faissx)
