---
title: Deep Dives
description: Technical deep-dives into MUXI internals
---

# Deep Dives

## Under the hood of MUXI

Technical documentation for understanding how MUXI works internally. Read these when you need to debug complex issues, build integrations, or contribute to MUXI.


## Architecture & Flow

:::: cols=2

(request-lifecycle.md)[[card]]
#### Request Lifecycle
How requests flow from client through server to formation and back. Timing, phases, and error handling.
[[/card]]

(orchestration.md)[[card]]
#### Multi-Agent Orchestration
How the Overlord coordinates agents, routes requests, and manages complex workflows.
[[/card]]

::::


## Real-Time Features

:::: cols=2

(streaming.md)[[card]]
#### Streaming Responses
Server-Sent Events (SSE) implementation, chunk format, and client handling.
[[/card]]

(async.md)[[card]]
#### Async Operations
Background task processing for long-running operations and webhooks.
[[/card]]

::::


## Users & Security

:::: cols=2

(multi-user.md)[[card]]
#### Multi-User Support
User isolation, session management, and multi-tenant architectures.
[[/card]]

(security.md)[[card]]
#### Security Model
Authentication layers, encrypted secrets, and security best practices.
[[/card]]

::::


## Observability

(observability.md)[[card]]

#### Events & Monitoring
349 typed events, logging configuration, and integration with monitoring platforms.

[[/card]]


## When to Read These

| Situation | Start Here |
|-----------|------------|
| Debugging slow requests | [Request Lifecycle](request-lifecycle.md) |
| Understanding agent routing | [Orchestration](../deep-dives/how-orchestration-works.md) |
| Implementing streaming UI | [Streaming](../deep-dives/real-time-streaming.md) |
| Building multi-tenant app | [Multi-User](multi-user.md) |
| Security audit | [Security](../deep-dives/security-model.md) |
| Setting up monitoring | [Observability](observability.md) |

> [!NOTE]
> These docs assume familiarity with MUXI basics. Start with the [Quickstart](../quickstart.md) and [Reference](../reference/README.md) first.
