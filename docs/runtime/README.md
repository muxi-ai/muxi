---
title: Runtime
description: The execution environment for MUXI formations
---

# Runtime

## The engine that powers formations

The runtime is what actually runs your AI agents - executing logic, managing memory, calling LLMs, and running tools. Most users never interact with it directly; the server handles everything.


## What the Runtime Does

```plain
┌────────────────────────────────────────────┐
│                  Runtime                   │
├────────────────────────────────────────────┤
│  ┌──────────┐        ┌──────────────────┐  │
│  │ REST API │───────▶│     Overlord     │  │
│  │  Server  │        │  (Orchestrator)  │  │
│  └──────────┘        └────────┬─────────┘  │
│  ┌──────────┐         ┌───────┴────────┐   │
│  │  Memory  │         │  Agents 1...N  │   │
│  │  System  │         └───────┬────────┘   │
│  └──────────┘                 │            │
│                        ┌──────┴──────┐     │
│                        │  MCP Tools  │     │
│                        └─────────────┘     │
└────────────────────────────────────────────┘
```

- **Agent execution** - Runs your agent logic
- **Memory management** - Buffer, vector, persistent
- **LLM integration** - Calls OpenAI, Anthropic, etc.
- **Tool execution** - Runs MCP servers
- **API serving** - Exposes the formation API


## How It's Used

### By the Server

The server spawns a runtime instance for each formation:

```
MUXI Server (:7890)
    ├── Formation A → Runtime (:8001)
    ├── Formation B → Runtime (:8002)
    └── Formation C → Runtime (:8003)
```

### By the CLI (dev mode)

`muxi dev` runs the runtime directly for local development:

```bash
muxi dev
# Starts runtime on :8001
```


## Formation API

Each runtime exposes the same API:

| Endpoint | Description |
|----------|-------------|
| `GET /health` | Health check |
| `POST /v1/chat` | Send message |
| `POST /v1/sessions` | Create session |
| `GET /v1/sessions/{id}` | Get history |
| `GET /v1/agents` | List agents |
| `POST /v1/triggers/{name}` | Fire trigger |
| `GET /v1/events` | SSE event stream |


## Runtime Configuration

Part of server config:

```yaml
runtime:
  auto_download: true   # Download runtime if missing
  version: latest       # Or pin: "1.0.0"
```


## Technology

| Component | Stack |
|-----------|-------|
| Framework | Python + FastAPI |
| LLM | OneLLM (unified API) |
| Vectors | FAISS (via FAISSx) |
| Protocol | MCP for tools |


## Advanced: Embedding the Runtime

The runtime can be embedded directly in your Python application as a framework.

[+] [Embed in Your App](embed-in-your-app.md)

> [!WARNING]
> **Think twice before embedding.** Feature-wise, you gain nothing - the embedded runtime has the exact same capabilities as running via the Server. What you lose:
> - **No multi-formation orchestration** - Server manages multiple formations; embedded runs one
> - **No zero-downtime deployment** - Server handles rolling updates
> - **No health monitoring** - Server provides `/health`, auto-restart
> - **More deployment complexity** - You manage the Python process lifecycle
>
> ---
>
> **When embedding makes sense:** Custom hosting environments, serverless functions, or tight integration requirements where HTTP isn't viable.
>
> **For 99% of use cases:** Use the Server + SDKs instead.
