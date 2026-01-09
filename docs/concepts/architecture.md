---
title: Architecture
description: How MUXI components work together
---
# Architecture

## How MUXI components work together


MUXI is a layered system: CLI and SDKs talk to Servers, which manage Formations running on Runtimes. The Registry distributes formations.


## System Overview

```
┌────────────────────────────────────────────────┐
│                MUXI Ecosystem                  │
├────────────────────────────────────────────────┤
│                                                │
│      ┌──────────┐        ┌──────────────┐      │
│      │   CLI    │───────▶│    Server    │      │
│      └──────────┘   ┌───▶│   (:7890)    │      │
│           │         │    └──────┬───────┘      │
│           │         │           │              │
│      ┌──────────┐   │    ┌──────┴───────┐      │
│      │   SDKs   │───┘    │  Formations  │      │
│      └──────────┘        │ (:8001-8999) │      │
│           │              └──────┬───────┘      │
│           │                     │              │
│      ┌──────────┐        ┌──────┴───────┐      │
│      │ Registry │        │   Runtime    │      │
│      └──────────┘        └──────────────┘      │
│                                                │
└────────────────────────────────────────────────┘
```

---

## Components

:::: cols=2

[[card]]
#### Server
**Orchestration platform**

Manages formation lifecycle, routes requests, handles auth, monitors health. Written in Go.

Port: 7890
[[/card]]

[[card]]
#### Runtime
**Execution environment**

Runs agent logic, manages memory, calls LLMs, executes tools. Written in Python (FastAPI).

Ports: 8001-8999
[[/card]]

[[card]]
#### CLI
**Command-line tool**

Creates formations, deploys to servers, manages secrets. Written in Go.
[[/card]]

[[card]]
#### SDKs
**Client libraries**

Python, TypeScript, Go. Chat with formations, manage sessions, fire triggers.
[[/card]]

[[card]]
#### Registry
**Distribution hub**

Share and discover formations. Version management. Like npm for agents.
[[/card]]

::::

---

## Request Flow

```mermaid
sequenceDiagram
    participant C as Client
    participant S as Server
    participant F as Formation
    participant L as LLM

    C->>S: POST /api/my-formation/v1/chat
    S->>S: Authenticate (HMAC or API key)
    S->>F: Proxy request
    F->>F: Load context (memory, knowledge)
    F->>F: Select agent
    F->>L: Send prompt
    L-->>F: Response
    F->>F: Update memory
    F-->>S: Response
    S-->>C: Response
```

---

## Formation Structure

Everything your AI needs in one directory:

```
my-formation/
├── formation.afs      # Main configuration
├── agents/            # Agent definitions
│   ├── researcher.afs
│   └── writer.afs
├── mcps/              # Tool configurations
│   └── web-search.afs
├── sops/              # Standard procedures
│   └── onboarding.md
├── triggers/          # Webhook templates
│   └── github-issue.md
├── knowledge/         # RAG sources
│   └── docs/
├── secrets.enc        # Encrypted credentials
└── secrets    # Template
```

---

## Inside a Formation

```
┌───────────────────────────────────────────────────┐
│                 Formation Runtime                 │
├───────────────────────────────────────────────────┤
│                                                   │
│      ┌────────────────┐      ┌────────────────┐   │
│      │   Overlord     │──────│     Memory     │   │
│      │ (Orchestrator) │      │    (3-tier)    │   │
│      └────────┬───────┘      └────────────────┘   │
│               │                                   │
│    ┌──────────┴───┬──────┐                        │
│    ↓      ↓       ↓      ↓                        │
│  Agent  Agent   Agent  Agent                      │
│    │      │       │      │                        │
│    └──────┴───┬───┴──────┘                        │
│               │                                   │
│        ┌──────┴──────┐                            │
│        │  MCP Tools  │                            │
│        └─────────────┘                            │
│                                                   │
└───────────────────────────────────────────────────┘
```

---

## Deployment Models

### Local Development

```
muxi dev → Runtime (localhost:8001)
```

### Single Server

```
Server (:7890)
  ├── Formation A (:8001)
  ├── Formation B (:8002)
  └── Formation C (:8003)
```

### Multi-Server

```
Load Balancer
     ├── Server-East (:7890) → Formations
     └── Server-West (:7890) → Formations
```

---

## Technology Stack

| Component | Technology | Why |
|-----------|------------|-----|
| Server | Go | Single binary, fast, concurrent |
| Runtime | Python + FastAPI | AI ecosystem, async, OneLLM |
| CLI | Go | Cross-platform, single binary |
| SDKs | Python, TS, Go | Native experience per language |

---

## Design Principles

> [!IMPORTANT]
> MUXI is **infrastructure**, not a framework. It deploys anywhere and integrates with anything.

1. **YAML configuration** - Simple, versionable, shareable
2. **Encrypted secrets** - Not environment variables
3. **MCP for tools** - Standard protocol, any server works
4. **Multi-agent** - Specialized agents, coordinated work
5. **Portable** - Works on any cloud, any platform

---

## Next Steps

[+] [Request Lifecycle](../deep-dives/request-lifecycle.md) - Detailed request flow
[+] [Security Model](../deep-dives/security.md) - Authentication layers
[+] [Reference](../reference/README.md) - Building formations
