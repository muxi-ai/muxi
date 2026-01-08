# MUXI Architecture

An architectural overview of the MUXI ecosystem for developers and contributors.

---

## Overview

**MUXI is infrastructure for AI agents** - a complete platform for deploying and managing agents in production.

| Component | Analogy | Purpose |
|-----------|---------|---------|
| **Server** | Docker engine | Orchestration, routing, lifecycle management |
| **Runtime** | Docker images | Execution environment for formations |
| **Registry** | Docker Hub | Distribution and discovery of formations |
| **CLI** | CLI  `¯\_(ツ)_/¯` | Management and deployment tool |

**Philosophy:** Agents deserve infrastructure as first-class primitives, not scripts hacked together with workflow tools.

---

## Architecture Diagram

```
┌───────────────────────────────────────────────┐
│                Your Application               │
│             (Web app, API, Script)            │
└───────────────────────┬───────────────────────┘
                        │  API / SDK / CLI
                        ▼
┌───────────────────────────────────────────────┐
│                  MUXI Server                  │
│       Orchestration • Routing • Memory        │
│                   Port 7890                   │
└───────────────────────┬───────────────────────┘
                        │
                        ▼
┌───────────────────────────────────────────────┐
│                  MUXI Runtime                 │
│            Formation Execution (SIF)          │
└───────────────────────┬───────────────────────┘
                        │
                        ▼
┌───────────────────────────────────────────────┐
│                   LLM / MCP                   │
│               External Services               │
└───────────────────────────────────────────────┘
```

---

## Repositories

| Repository | Language | Description |
|------------|----------|-------------|
| [server](https://github.com/muxi-ai/server) | Go | Orchestration platform |
| [runtime](https://github.com/muxi-ai/runtime) | Python | Formation execution environment |
| [cli](https://github.com/muxi-ai/cli) | Go | Command-line interface |
| [registry](https://github.com/muxi-ai/registry) | PHP | Formation distribution hub |
| [sdks](https://github.com/muxi-ai/sdks) | Various | 12 SDKs |
| [install](https://github.com/muxi-ai/install) | Shell | Installation scripts |
| [homebrew-tap](https://github.com/muxi-ai/homebrew-tap) | Ruby | Homebrew formulae |
| [afs-spec](https://github.com/agent-formation/afs-spec) | YAML | Agent Formation (org) |

---

## Core Components

### Server

The **orchestration platform** - the heart of MUXI.

- Formation lifecycle management (deploy, start, stop, restart)
- HTTP reverse proxy (routes requests to formations)
- Port allocation and process management
- HMAC authentication for the management API
- Session and memory coordination

**Technology:** Go (single binary, zero dependencies)<br>
**Default Port:** 7890

---

### Runtime

The **execution environment** where formations run.

- Execute formation code
- Provide the formation API contract
- Manage LLM connections
- Handle tool execution

**Technology:** Python + FastAPI, packaged as SIF (Singularity Image Format)

**API Contract:**
```
GET  /health    # Health check
POST /chat      # Main interaction endpoint
GET  /info      # Formation metadata
```

**Why SIF?**
- Single-file distribution (no Docker daemon required)
- Lightweight and secure
- HPC-friendly (Singularity/Apptainer native)

---

### Runtime Runner (Optional)

A **bridge component** for running the Runtime on non-Linux systems.

| Platform | Runtime Runner Needed? |
|----------|------------------------|
| Linux | No - Runtime runs natively |
| macOS | Yes - wraps SIF execution |
| Windows | Yes - wraps SIF execution |

---

### CLI

The **command-line tool** for managing MUXI.

```bash
muxi deploy              # Deploy a formation
muxi formation           # List running formations
muxi logs                # View logs
muxi chat                # Interactive chat
muxi push                # Push to registry
muxi pull @me/assistant  # Pull from registry
```

---

### Registry

The **distribution hub** for formations - like Docker Hub for AI agents.

- Push and pull formations
- Version management
- Public and private formations
- Search and discovery

---

### Schemas

**Formation specification** defining the YAML structure.

Used by Server, CLI, and SDKs for validation.

---

## Request Flow

```
Client Request
      │
      ▼
┌─────────────────┐
│  MUXI Server    │  ← Receives request on port 7890
│  (Port 7890)    │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Reverse Proxy  │  ← Routes to correct formation
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Runtime (SIF)  │  ← Formation executes
│  (Port 8001+)   │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│    LLM API      │  ← Calls Anthropic/OpenAI/etc.
└─────────────────┘
```

---

## Repository Dependencies

```
                    ┌─────────────┐
                    │   schemas   │
                    └──────┬──────┘
                           │
              ┌────────────┼────────────┐
              │            │            │
              ▼            ▼            ▼
      ┌────────────┐ ┌────────────┐ ┌────────────┐
      │   server   │ │    cli     │ │    sdks    │
      └──────┬─────┘ └─────┬──────┘ └─────┬──────┘
             │             │              │
             │             └──────┬───────┘
             ▼                    ▼
     ┌────────────────┐    Server API (HMAC)
     │ runtime-runner │
     │  (non-Linux)   │
     └───────┬────────┘
             ▼
       ┌───────────┐
       │  runtime  │
       └───────────┘

Installation:
     ┌────────────┐      ┌──────────────┐
     │  install   │      │ homebrew-tap │
     └─────┬──────┘      └──────┬───────┘
           └───────┬────────────┘
                   ▼
          Installs Server + CLI

Distribution:
       ┌────────────┐
       │  registry  │
       └────────────┘
```

---

## Design Decisions

### 1. Separate Repositories

Each component evolves independently:

- Server can release without affecting CLI
- Runtime API stabilizes separately
- Focused responsibility per repo

### 2. Runtime as SIF Containers

- No Python pollution on the server
- Single-file distribution
- Container isolation without Docker daemon
- HPC-friendly (Singularity native)

### 3. Go for Server & CLI

- Single binary, zero dependencies
- Cross-platform compilation
- Fast startup, low memory

### 4. Declarative Formations

- YAML-based configuration
- Version controllable
- No code required for most use cases

### 5. Model Agnostic

- Swap LLM providers with config change
- No vendor lock-in
- Support for local models (Ollama)

---

## Design Principles

### Agents as Infrastructure

Formations are first-class infrastructure primitives, not scripts or notebooks. They have lifecycle management, observability, and scale built in.

### Self-Hostable

No phone-home. No required cloud services. Run on your laptop or your Kubernetes cluster. Your data stays yours.

### Observable by Default

Every agent invocation produces structured traces. See what your agent did, why, and how long it took.

### Separation of Concerns

Each component has a focused responsibility:

- **Server** = orchestration
- **Runtime** = execution
- **CLI** = management
- **Registry** = distribution

---

## License

| Component | License |
|-----------|---------|
| Server, Runtime | [Elastic License 2.0](https://www.elastic.co/licensing/elastic-license) |
| CLI, SDKs, Schemas, etc. | [Apache 2.0](https://www.apache.org/licenses/LICENSE-2.0) |

See [LICENSE.md](./LICENSE.md) for details.

---

## Next Steps

- **Want to contribute?** See [CONTRIBUTING.md](./CONTRIBUTING.md)
- **Setting up dev environment?** See [DEVELOPMENT.md](./DEVELOPMENT.md)
- **Understanding git workflow?** See [GIT-WORKFLOW.md](./GIT-WORKFLOW.md)
