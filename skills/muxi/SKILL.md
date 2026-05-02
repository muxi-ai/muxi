---
name: muxi
description: >
  Guide users through the MUXI platform -- infrastructure for AI agents.
  Covers installation (CLI and server), server setup and configuration,
  CLI commands and workflows, secrets management, writing formations
  (Agent Formation Schema), deploying formations, registry operations,
  and using both the Server API and Formation API.
  Use when the user asks about MUXI setup, CLI commands, formation authoring,
  secrets, deployment, the registry, server configuration, agents, MCP tools,
  overlord, memory, or any "how do I..." question about MUXI.
license: Apache-2.0
metadata:
  author: muxi-ai
  version: "1.0"
---

# MUXI Platform

MUXI (Multiplexed eXtensible Intelligence, pronounced /muk-see/) is open-source production infrastructure purpose-built for AI agents. Not a framework. Not a wrapper. A server.

**Core philosophy:** Agents are native primitives -- declared in portable `.afs` files, orchestrated at the infrastructure layer, scaled like containers. No frameworks to fight. No queues to wrangle. Just infrastructure that understands what agents do.

Think: Flask is a framework. Nginx is infrastructure. MUXI is the Nginx for agents.

| | MUXI | LangChain / CrewAI |
|---|---|---|
| **Type** | Server infrastructure | Python library |
| **Deployment** | `muxi deploy` | Write deployment code |
| **Configuration** | Declarative `.afs` files | Imperative code |
| **Multi-tenancy** | Built-in isolation | Build yourself |
| **Observability** | 356 event types, 10+ export targets | Add external tools |
| **Async/Scheduling** | Native support | Add Celery/etc. |

**Key stats:** <100ms avg response, 88.9% test coverage, 92% semantic cache hit rate, 21 LLM providers / 300+ models via OneLLM.

**Licensing:** Server & Runtime = Elastic License 2.0 (free for commercial use, cannot offer MUXI itself as SaaS). CLI, SDKs, Formations, Schemas = Apache 2.0.

## Architecture

| Component | Analogy | Purpose | Language |
|-----------|---------|---------|----------|
| **Server** | Docker engine | Orchestration, routing, lifecycle | Go |
| **Runtime** | Docker images | Formation execution (SIF containers) | Python |
| **CLI** | CLI | Management and deployment | Go |
| **Registry** | Docker Hub | Distribution and discovery | PHP |
| **SDKs** | Client libs | Go, Python, TypeScript (+ 9 planned) | Various |

```
Your Application
      │  API / SDK / CLI
      ▼
MUXI Server (:7890)        ← Orchestration, routing, memory
      │
      ▼
MUXI Runtime (SIF)         ← Formation execution
      │
      ▼
LLM / MCP / External       ← AI models, tools, services
```

**Architecture rationale:** Go for the Server (single binary, excellent concurrency, low memory) handles the hot path (orchestration, routing, auth). Python for the Runtime (ML ecosystem, async-first) handles AI workloads. Formations run as SIF containers (Singularity Image Format) -- single-file distribution, no Docker daemon required on Linux.

**Request flow:** Client -> Server (7890) -> reverse proxy -> Formation (8000+) -> Overlord builds memory context -> routes to best agent -> agent uses tools/knowledge/LLM -> Overlord applies soul -> streams response -> updates memory.

**Use cases:** Customer support systems, internal tooling automation, document processing, data analysis platforms, booking/scheduling, SaaS AI features.

**Key concepts:**
- **Formation** = complete AI system config (agents + tools + memory + behavior)
- **Overlord** = the brain that manages memory, routes requests, applies soul
- **Agent** = specialized worker that uses tools and knowledge
- **MCP** = Model Context Protocol for connecting tools

## Installation

**macOS:** `brew install muxi-ai/tap/muxi`
**Linux:** `curl -fsSL https://muxi.org/install | sudo bash` (or without sudo for user-level)
**Windows:** `powershell -c "irm https://muxi.org/install | iex"`
**Docker:** `docker run -d --name muxi-server -p 7890:7890 -v muxi-data:/data ghcr.io/muxi-ai/server:latest`

Installs both `muxi-server` and `muxi` CLI. Verify: `muxi --version && muxi-server version`

CLI-only install: `curl -fsSL https://muxi.org/install | bash -s -- --cli-only`

## Server Setup

```bash
muxi-server init    # Generates auth credentials (save them!)
muxi-server start   # Starts on port 7890
curl http://localhost:7890/health  # Verify
```

Config at `~/.muxi/server/config.yaml`. See [references/server-config.md](references/server-config.md) for full reference.

## CLI Setup

```bash
muxi profiles add local
# Enter: URL (http://localhost:7890), Key ID, Secret Key
muxi remote list  # Test connection
```

CLI config at `~/.muxi/cli/config.yaml`. See [references/cli-reference.md](references/cli-reference.md) for all commands.

## Quickstart Workflow

```bash
muxi new formation my-assistant   # Scaffold formation
cd my-assistant
muxi secrets setup                # Enter API keys (encrypted)
muxi dev                          # Run locally at http://localhost:8001
muxi chat "Hello!"                # Test it
muxi deploy                       # Deploy to server
```

## Local Development (muxi up/down)

Think of `muxi up` / `muxi down` like `docker compose up` / `docker compose down` -- quick start/stop for local development without the full deploy cycle.

```bash
# Terminal 1: Start server (one-time)
muxi-server start

# Terminal 2: Start formation from its directory
cd my-formation
muxi up                      # Start formation (uses /draft/ URL prefix)
muxi down                    # Stop formation

# Or stop by ID from anywhere
muxi down my-bot
```

**Draft URL:** `http://localhost:7890/draft/{formation-id}`

| Aspect | `muxi up` | `muxi deploy` |
|--------|-----------|---------------|
| Speed | Instant | Bundles & uploads |
| Source | Runs from local directory | Copies to server storage |
| Persistence | In-memory (gone on restart) | Persisted & restored |
| URL prefix | `/draft/` | `/api/` |
| Rollback | No | Yes |
| Use case | Development | Production |

## Writing Formations

Formations use `.afs` files (100% YAML-compatible). The `.afs` extension signals "Agent Formation Schema".

### Directory Structure

```
my-formation/
├── formation.afs      # Main config (LLM, memory, overlord)
├── SOUL.md            # Optional: Overlord soul (takes precedence over inline)
├── agents/            # Agent definitions
│   └── assistant.afs
├── mcp/               # MCP tool configs
│   └── web-search.afs
├── knowledge/         # Documents for RAG
├── sops/              # Standard operating procedures
├── triggers/          # Webhook templates
├── skills/            # Reusable agent capabilities (SKILL.md per skill)
│   └── pdf-processing/
│       └── SKILL.md
├── secrets            # Required keys template (safe to commit)
├── secrets.enc        # Encrypted secrets (safe to commit)
└── .key               # Encryption key (NEVER commit!)
```

Components in `agents/`, `mcp/`, `a2a/` must be **explicitly declared** in `formation.afs`. Files in these directories without a matching entry are ignored.

### Minimal Formation

```yaml
# formation.afs
schema: "1.0.0"
id: my-assistant
description: A simple assistant

llm:
  api_keys:
    openai: "${{ secrets.OPENAI_API_KEY }}"
  models:
    - text: "openai/gpt-4o"

agents:
  - assistant              # Explicitly declare agents from agents/ directory
```

```yaml
# agents/assistant.afs
schema: "1.0.0"
id: assistant
name: Assistant
description: A helpful assistant
system_message: You are a helpful assistant.
```

### Formation Schema Key Sections

See [references/formation-schema.md](references/formation-schema.md) for the complete schema.

**Required fields:** `schema: "1.0.0"`, `id`, `description`

**Init hook (optional):**
```yaml
init: "mkdir -p /tmp/workspace"
# Or multiline:
init: |
  mkdir -p /tmp/workspace /tmp/cache
  cp seed-data.json /tmp/workspace/
```

Shell command that runs before any services start. Use for environment setup (creating directories, installing tools, seeding data). 120-second timeout, cwd = formation directory, non-zero exit fails the formation.

**LLM configuration:**
```yaml
llm:
  api_keys:
    openai: "${{ secrets.OPENAI_API_KEY }}"
    anthropic: "${{ secrets.ANTHROPIC_API_KEY }}"
  settings:
    temperature: 0.7
    max_tokens: 4096
  models:
    - text: "openai/gpt-4o"          # Text generation
    - embedding: "openai/text-embedding-3-large"  # Vector embeddings
    - vision: "openai/gpt-4o"        # Image understanding
    - audio: "openai/whisper-1"       # Speech-to-text
    - streaming: "openai/gpt-4o-mini" # Fast streaming
```

Providers: `openai/{model}`, `anthropic/{model}`, `google/{model}`, `ollama/{model}`

**Overlord (orchestration):**
```yaml
overlord:
  soul: |
    You are a helpful, professional assistant.
  llm:
    # `base` handles routing/task management; `synthesis` (optional) handles the final reply.
    # If `synthesis` is omitted, `base` is used for both stages.
    base:
      model: "openai/gpt-4o-mini"
      settings: { temperature: 0.2 }
    synthesis:                          # optional
      model: "anthropic/claude-haiku-4-5"
      settings: { temperature: 0.5 }
  response:
    format: "markdown"
    streaming: true
  workflow:
    auto_decomposition: true
    complexity_threshold: 7.0
    max_parallel_tasks: 5
  clarification:
    style: "conversational"
```

> **Breaking change:** The flat `overlord.llm.model` / `overlord.llm.settings` shape is no longer accepted. Place those fields inside a `base:` block. Formations that have not migrated will fail validation.

> The overlord's soul can also be defined in a `SOUL.md` file next to `formation.afs`. If present, `SOUL.md` takes precedence over the inline `soul` field.

**Memory (four layers):**
```yaml
memory:
  buffer:
    size: 50
    vector_search: true
  # Persistent memory enabled by default (SQLite, memory.db in formation dir).
  # To use PostgreSQL (required for multi-tenancy):
  persistent:
    connection_string: "postgres://user:pass@localhost:5432/db"
    # To explicitly disable: persistent: false
    user_synopsis:
      enabled: true
      cache_ttl: 3600
```

Layers: Buffer (recent messages) -> Working (session state, FAISSx) -> User Synopsis (LLM-synthesized profile) -> Persistent (long-term, Postgres/SQLite, auto-enabled).

**MCP tool settings:**
```yaml
mcp:
  max_tool_iterations: 10
  max_tool_calls: 50
  max_repeated_errors: 3
  max_timeout_in_seconds: 300
  servers:
    - web-search            # Explicitly declare MCP servers from mcp/ directory
    - filesystem
```

### Agent Schema (`agents/*.afs`)

```yaml
schema: "1.0.0"
id: researcher
name: Research Specialist
description: Gathers information from multiple sources  # Used for routing
role: researcher

system_message: |
  Research topics thoroughly. Always cite sources.

specialization:
  domain: "research"
  keywords: ["research", "search", "find"]

# Override formation LLM (highest priority)
llm_models:
  - text: "anthropic/claude-sonnet-4-20250514"
    settings: { temperature: 0.3 }

# Agent-specific tools: string refs for formation-level MCPs, inline dicts for private
mcp_servers:
  - web-search              # Reference formation-level MCP by ID
  - id: agent-private-tool  # Agent-private inline definition
    type: http
    endpoint: "https://example.com/mcp"
    auth:
      type: api_key
      header: "X-API-Key"
      key: "${{ secrets.PRIVATE_TOOL_KEY }}"

# Agent-specific skills (private to this agent)
skills:
  - ticket-handling

# Agent-specific knowledge (RAG)
knowledge:
  files: ["knowledge/faq.md"]
  directories: ["knowledge/docs/"]
```

### MCP Server Schema (`mcp/*.afs`)

**Command-based:**
```yaml
schema: "1.0.0"
id: web-search
type: command
command: npx
args: ["-y", "@modelcontextprotocol/server-brave-search"]
auth:
  type: env
  BRAVE_API_KEY: "${{ secrets.BRAVE_API_KEY }}"
```

**HTTP-based:**
```yaml
schema: "1.0.0"
id: remote-tools
type: http
endpoint: "https://mcp.example.com/tools"
auth:
  type: bearer
  token: "${{ secrets.MCP_TOKEN }}"
```

Auth types: `env`, `bearer`, `basic`, `api_key`.

**Tool filtering (whitelist or blacklist — mutually exclusive):**
```yaml
tools:
  whitelist:           # only expose these tools to the LLM
    - "search_*"
    - "get_*"
    - "create_issue"   # literal name (exact match)

# OR

tools:
  blacklist:           # expose everything except these
    - "delete_*"
    - "force_push_branch"
```

Patterns use POSIX `fnmatch` (`*`, `?`, `[abc]`). Applied at registration time — filtered tools are invisible to the LLM and cannot be planned. Particularly useful for large catalogs (Microsoft 365, Google Workspace) or to keep destructive verbs out of the agent's plannable surface.

### Override Hierarchy (highest to lowest)
1. Agent-specific (`agents/*.afs` -> `llm_models`)
2. Overlord (`formation.afs` -> `overlord.llm`)
3. Formation defaults (`formation.afs` -> `llm`)

## Secrets Management

MUXI uses encrypted files, not environment variables. Secrets never appear in process environment, shell history, or logs.

```bash
muxi secrets setup        # Scan formation, prompt for all required secrets
muxi secrets set API_KEY  # Set one secret
muxi secrets list         # List keys
muxi secrets get API_KEY  # Get value
muxi secrets delete KEY   # Remove
```

**Files:** `secrets.enc` (encrypted, safe to commit), `secrets` (template, safe to commit), `.key` (encryption key, NEVER commit).

**Encryption:** Fernet (AES-128-CBC + HMAC-SHA256). Portable across Python and Go runtimes.

**Referencing in YAML:**
```yaml
api_key: "${{ secrets.OPENAI_API_KEY }}"        # Formation secrets
token: "${{ user.credentials.github }}"          # Per-user credentials
```

**If `.key` is lost:** `rm secrets.enc && muxi secrets setup` (re-enter all values).

## Deployment

```bash
muxi deploy                       # Deploy to default profile
muxi deploy --profile production  # Specific profile
muxi deploy --validate            # Validate only
muxi bump minor                   # Bump version before update
muxi remote rollback my-bot       # Rollback to previous version
```

Updates use zero-downtime blue-green deployment. The old version keeps running until the new one passes health checks.

**CI/CD:**
```bash
export MUXI_SERVER_URL=https://muxi.example.com:7890
export MUXI_KEY_ID=$CI_MUXI_KEY_ID
export MUXI_SECRET_KEY=$CI_MUXI_SECRET
muxi deploy
```

## Registry

```bash
muxi search "customer support"     # Search
muxi pull @muxi/hello-muxi            # Pull formation
muxi pull @muxi/hello-muxi@1.0.0      # Specific version
muxi login                         # Authenticate
muxi push                          # Publish
```

## SDKs

Official SDKs for 12 languages: Go, Python, TypeScript, Ruby, Java, Kotlin, Swift, C#, PHP, Dart, Rust, and C++. All provide two client types:
- **ServerClient** -- formation management (HMAC auth, deploy/start/stop/rollback)
- **FormationClient** -- chat and runtime API (key auth, streaming, memory, sessions)

```bash
go get github.com/muxi-ai/muxi-go
pip install muxi-client
npm install @muxi-ai/muxi-typescript
```

**Quick examples:**

```go
// Go -- streaming chat
client := muxi.NewFormationClient(&muxi.FormationConfig{
    FormationID: "my-bot",
    ServerURL:   os.Getenv("MUXI_SERVER_URL"),
    ClientKey:   os.Getenv("MUXI_CLIENT_KEY"),
})
stream, _ := client.ChatStream(ctx, &muxi.ChatRequest{Message: "Hello!", UserID: "u1"})
for chunk := range stream {
    if chunk.Type == "text" { fmt.Print(chunk.Text) }
}
```

```python
# Python -- streaming chat (also has async: AsyncFormationClient)
from muxi import FormationClient
client = FormationClient(server_url="https://server.example.com", formation_id="my-bot", client_key="<key>")
for chunk in client.chat_stream({"message": "Hello!", "user_id": "u1"}):
    if chunk.get("type") == "text": print(chunk["text"], end="")
```

```typescript
// TypeScript -- streaming chat
import { FormationClient } from "@muxi-ai/muxi-typescript";
const client = new FormationClient({ serverUrl: "https://server.example.com", formationId: "my-bot", clientKey: "<key>" });
for await (const chunk of client.chatStream({ message: "Hello!", userId: "u1" })) {
  if (chunk.type === "text") process.stdout.write(chunk.text);
}
```

All SDKs include: auto-idempotency, exponential backoff retries, typed errors, SSE streaming. See [references/sdks.md](references/sdks.md) for the full API.

## Common Patterns

### Multi-Agent Team

```yaml
# formation.afs
overlord:
  soul: You coordinate a research team.
  workflow:
    auto_decomposition: true
    complexity_threshold: 7.0
    max_parallel_tasks: 5
agents:
  - researcher
  - analyst
  - writer
```

The Overlord automatically decomposes complex tasks and routes to the right agents.

### Tool Context Contamination Solution

MUXI does NOT dump all tool schemas into every request. It builds a capability registry on init and passes ONLY relevant tools per request (~90% token reduction).

### Self-Healing Tool Chaining

Agents analyze tool failures and take corrective action automatically (e.g., creating missing directories before retrying file writes).

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Command not found | Restart terminal; check PATH |
| Port 7890 in use | `lsof -i :7890` or use `--port 7891` |
| Connection refused | `muxi-server status`; verify profile URL |
| Auth failed | Verify key ID/secret; re-run `muxi-server init` |
| Missing secrets | `muxi secrets setup` in formation directory |
| macOS code signing | `xattr -d com.apple.quarantine /usr/local/bin/muxi-server` |

## Reference Files

For detailed reference material, see:
- [references/formation-schema.md](references/formation-schema.md) - Complete formation, agent, MCP schema
- [references/cli-reference.md](references/cli-reference.md) - All CLI commands and flags
- [references/server-config.md](references/server-config.md) - Server configuration and API
- [references/formation-api.md](references/formation-api.md) - Runtime API endpoints
- [references/sdks.md](references/sdks.md) - Go, Python, TypeScript SDK reference
- [references/examples.md](references/examples.md) - Complete formation examples
