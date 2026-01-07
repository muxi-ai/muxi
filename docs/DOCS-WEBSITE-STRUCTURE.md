# MUXI Documentation Website Structure

**Goal:** Get developers to install the server, use the registry, CLI, and SDKs to build production AI systems.

**Audience:** Developers building AI applications, not contributors to MUXI internals.

**Principles:**
- Progressive disclosure (start simple, go deep)
- Action-oriented (what to do, not what things are)
- Concrete examples (runnable code, expected output)
- AI-digestible (clear structure, explicit relationships)
- **Cross-referenced** - Components appear where relevant, not just in their own sections

---

## Site Sections

```
muxi.org/docs/
├── index                    # Landing: "Deploy AI agents in 5 minutes"
├── quickstart               # From zero to running formation (uses: CLI, Server)
├── installation/            # Getting MUXI installed (Server, CLI)
├── server/                  # Server operation and management
├── formations/              # The core concept (uses: CLI for scaffolding)
├── cli/                     # Command-line reference
├── sdks/                    # Language SDKs (alternative to CLI)
├── registry/                # Finding and sharing formations (uses: CLI)
├── guides/                  # Task-oriented tutorials (uses: all components)
├── concepts/                # Mental models (for curious readers)
├── api/                     # Complete API reference (Server API, Formation API)
└── runtime/                 # (separate section) For embedders only
```

---

## Cross-Reference Matrix

Shows where each component appears across docs (not just in its dedicated section):

| Component | Primary Section | Also Appears In |
|-----------|-----------------|-----------------|
| **Server** | `/docs/server/` | quickstart, installation, guides/deploy, guides/monitoring, api/server-api |
| **CLI** | `/docs/cli/` | quickstart, installation, formations (scaffolding), registry, all guides |
| **SDKs** | `/docs/sdks/` | guides/custom-ui, guides/ci-cd, api reference |
| **Registry** | `/docs/registry/` | quickstart (pulling starter), guides/publishing |
| **Formations** | `/docs/formations/` | quickstart, all guides, cli/new |
| **Runtime** | `/docs/runtime/` | concepts/architecture (brief mention only) |

---

## 1. Landing Page (`/docs`)

**Purpose:** Orient visitors, answer "what is this?" and "should I care?"

```markdown
# MUXI Documentation

Deploy AI agents to production with one command.

## What is MUXI?

MUXI is infrastructure for running AI agents. You define agents in YAML files 
(formations), deploy them to a server, and access them via API or SDK.

Think: Docker for AI agents.

## Quick Links

- **[Quickstart →](/docs/quickstart)** - Running formation in 5 minutes
- **[Installation →](/docs/installation)** - Install server and CLI
- **[CLI Reference →](/docs/cli)** - Command-line tool
- **[SDKs →](/docs/sdks)** - Python, TypeScript, Go

## How It Works

1. **Define** - Write a formation file (YAML)
2. **Deploy** - `muxi deploy` to your server
3. **Use** - Chat via API, SDK, or CLI

[Get Started →](/docs/quickstart)
```

**Word count target:** Under 200 words. Link to details.

---

## 2. Quickstart (`/docs/quickstart`)

**Purpose:** Working formation in 5 minutes. Zero to "it works."

**Components touched:** Installation, CLI, Server, (optionally Registry)

**Structure:**

```markdown
# Quickstart

Get a MUXI formation running in 5 minutes.

## Prerequisites

- macOS, Linux, or Windows
- Terminal access

## Step 1: Install MUXI (1 min)

**macOS (Homebrew):**
brew install muxi-ai/tap/muxi

**Linux:**
curl -fsSL https://muxi.org/install | sudo bash

**Windows (PowerShell):**
powershell -c "irm https://muxi.org/install | iex"

This installs both `muxi-server` and the `muxi` CLI.

Verify:
muxi --version
# muxi v1.0.0

> For detailed installation options, see [Installation →](/docs/installation)

## Step 2: Start the Server (30 sec)

muxi-server init    # First time only - generates credentials
muxi-server start

The server runs on port 7890. Leave it running.

## Step 3: Create a Formation (1 min)

Open a new terminal:

muxi new formation my-assistant
cd my-assistant

This creates:
my-assistant/
├── formation.afs    # Main configuration
├── agents/          # Agent definitions
└── secrets.example  # Required secrets

## Step 4: Configure Secrets (1 min)

muxi secrets setup

Follow the prompts to add your OpenAI API key (or other LLM provider).

## Step 5: Run Locally (1 min)

muxi dev

Output:
✓ Formation 'my-assistant' running
✓ API: http://localhost:8001
✓ Web: http://localhost:8001/chat

## Step 6: Test It (30 sec)

curl -X POST http://localhost:8001/v1/chat \
  -H "Content-Type: application/json" \
  -d '{"message": "Hello!"}'

Or open http://localhost:8001/chat in your browser.

## Alternative: Start from Registry

Skip steps 3-4 by pulling a pre-built formation:

muxi pull @muxi/starter-assistant
cd starter-assistant
muxi secrets setup
muxi dev

## Next Steps

- [Deploy to production →](/docs/guides/deploy-to-production) - Move beyond localhost
- [Add tools (MCP) →](/docs/guides/add-tools) - Give your agent superpowers
- [Add memory →](/docs/guides/add-memory) - Conversations that remember
- [Formation reference →](/docs/formations) - Complete YAML reference
- [Browse the registry →](https://registry.muxi.org) - Find community formations
```

**Word count target:** Under 500 words. Executable in 5 minutes.

---

## 3. Installation (`/docs/installation`)

**Purpose:** Complete installation reference for all platforms.

**Components:** Server, CLI (installed together)

**Pages:**

```
/docs/installation/
├── index.md           # Overview and quick install
├── macos.md           # macOS-specific (Homebrew recommended)
├── linux.md           # Linux-specific (curl script)
├── windows.md         # Windows-specific (PowerShell)
├── docker.md          # Docker deployment
└── from-source.md     # Building from source
```

**Index structure:**

```markdown
# Installation

Install the MUXI Server and CLI.

## Quick Install

**macOS (Homebrew - recommended):**
brew install muxi-ai/tap/muxi

**Linux:**
curl -fsSL https://muxi.org/install | sudo bash

For user-level install (no sudo):
curl -fsSL https://muxi.org/install | bash

**Windows (PowerShell):**
powershell -c "irm https://muxi.org/install | iex"

## What Gets Installed

| Binary | Description |
|--------|-------------|
| `muxi-server` | Orchestration server (port 7890) |
| `muxi` | Command-line tool |

## Install Locations

| Method | Binaries | Config |
|--------|----------|--------|
| Homebrew | `/opt/homebrew/bin` | `~/.muxi/` |
| Linux (sudo) | `/usr/local/bin` | `/etc/muxi/` |
| Linux (user) | `~/.local/bin` | `~/.muxi/` |
| Windows | `%LOCALAPPDATA%\MUXI\bin` | `%USERPROFILE%\.muxi\` |

## Verify Installation

muxi --version
muxi-server version

## After Installation

# Initialize server (generates auth credentials)
muxi-server init

# Start the server
muxi-server start

# Create your first formation
muxi new formation my-assistant

See [Quickstart →](/docs/quickstart) for the full walkthrough.

## Installation Options

| Flag | Description |
|------|-------------|
| `--non-interactive` | Skip prompts, use defaults |
| `--cli-only` | Install CLI only (no server) |

Example:
curl -fsSL https://muxi.org/install | bash -s -- --cli-only

## Platform-Specific Guides

- [macOS →](./macos) - Homebrew, manual install, Apple Silicon notes
- [Linux →](./linux) - Ubuntu, Debian, RHEL, production setup
- [Windows →](./windows) - PowerShell, WSL options
- [Docker →](./docker) - Container deployment

## System Requirements

| Component | Minimum | Recommended |
|-----------|---------|-------------|
| CPU | 2 cores | 4+ cores |
| RAM | 2 GB | 8 GB |
| Disk | 10 GB | 50 GB SSD |

## Next Steps

After installation:
1. [Quickstart →](/docs/quickstart) - Run your first formation
2. [Server Configuration →](/docs/server/configuration) - Customize server settings
3. [CLI Overview →](/docs/cli) - Learn the commands
```

---

## 4. Server (`/docs/server`)

**Purpose:** Server installation, configuration, and operations.

**Pages:**

```
/docs/server/
├── index.md           # What is MUXI Server?
├── configuration.md   # Server config options
├── authentication.md  # HMAC auth setup
├── formations.md      # Managing deployed formations
├── monitoring.md      # Health checks, logs, metrics
├── production.md      # Production deployment best practices
└── troubleshooting.md # Common server issues
```

**Index structure:**

```markdown
# MUXI Server

The orchestration platform for running MUXI formations.

## Overview

MUXI Server:
- Deploys and manages formations
- Routes requests to the right formation
- Handles authentication (HMAC)
- Monitors health and restarts crashed formations

Think of it as "Docker + Nginx + PM2" for AI agents.

## Quick Start

# Install (see Installation for all options)
brew install muxi-ai/tap/muxi

# Initialize (creates auth credentials)
muxi-server init

# Start
muxi-server start

# Verify
curl http://localhost:7890/health

## Architecture

┌─────────────────────────────────────┐
│ MUXI Server (Port 7890)             │
│                                     │
│  /rpc/*  → Management API (HMAC)    │
│  /api/*  → Formation Proxy          │
└─────────────────────────────────────┘
               │
               ↓
┌─────────────────────────────────────┐
│ Formations (Ports 8000+)            │
│  • my-assistant (8001)              │
│  • support-bot (8002)               │
└─────────────────────────────────────┘

## Server Commands

muxi-server init      # Generate config and credentials
muxi-server start     # Start in foreground
muxi-server stop      # Stop the server
muxi-server status    # Check server status
muxi-server version   # Show version

## Managing Formations via CLI

Once the server is running, use the CLI to manage formations:

muxi deploy                    # Deploy current formation
muxi formation list            # List all formations
muxi formation stop <id>       # Stop a formation
muxi formation restart <id>    # Restart a formation
muxi logs --follow             # Stream formation logs

See [CLI Reference →](/docs/cli) for all commands.

## Configuration

Server config lives in `~/.muxi/server/config.yaml`:

server:
  port: 7890
  host: 0.0.0.0

auth:
  enabled: true
  # credentials generated by `muxi-server init`

formations:
  port_range: [8000, 9000]
  auto_restart: true

See [Configuration →](./configuration) for all options.

## Next

- [Configuration →](./configuration) - All server options
- [Authentication →](./authentication) - HMAC setup
- [Production →](./production) - Production deployment guide
```

---

## 5. Formations (`/docs/formations`)

**Purpose:** Complete reference for formation files. The core concept.

**Pages:**

```
/docs/formations/
├── index.md           # What is a formation?
├── schema.md          # Complete YAML reference
├── agents.md          # Defining agents
├── memory.md          # Memory configuration
├── tools.md           # MCP integration
├── secrets.md         # Secret management
├── triggers.md        # Scheduled/webhook triggers
├── a2a.md             # Agent-to-agent communication
└── examples.md        # Complete formation examples
```

**Index structure:**

```markdown
# Formations

A formation is a complete AI system definition in YAML.

## Overview

Formations define:
- **Agents** - AI personas with roles and capabilities
- **Memory** - Short-term, long-term, and semantic memory
- **Tools** - MCP servers for external capabilities
- **Triggers** - Scheduled jobs and webhooks

## Basic Structure

# formation.afs
schema: "1.0.0"
id: my-assistant
name: My Assistant

llm:
  models:
    text: openai/gpt-4o

agents:
  - id: assistant
    role: helpful assistant

## File Organization

my-formation/
├── formation.afs      # Main configuration
├── agents/            # Agent definitions (optional)
│   └── researcher.afs
├── mcps/              # MCP server configs (optional)
│   └── web-search.afs
├── sops/              # Standard operating procedures
│   └── onboarding.md
├── secrets.enc        # Encrypted secrets
└── secrets.example    # Template for required secrets

## Quick Reference

| Section | Purpose | Required |
|---------|---------|----------|
| `schema` | Schema version | Yes |
| `id` | Unique identifier | Yes |
| `llm` | Language model config | Yes |
| `agents` | Agent definitions | Yes |
| `memory` | Memory configuration | No |
| `mcps` | MCP server integrations | No |
| `triggers` | Scheduled jobs | No |

## Next

- [Schema Reference →](./schema) - Complete YAML spec
- [Agents →](./agents) - Agent configuration
- [Examples →](./examples) - Real formation files
```

---

## 6. CLI Reference (`/docs/cli`)

**Purpose:** Complete CLI command reference.

**Pages:**

```
/docs/cli/
├── index.md           # Overview and common commands
├── new.md             # muxi new *
├── deploy.md          # muxi deploy
├── secrets.md         # muxi secrets *
├── profile.md         # muxi profile *
├── formation.md       # muxi formation *
├── server.md          # muxi server *
├── registry.md        # muxi push/pull/search
├── agent.md           # muxi agent *
├── chat.md            # muxi chat
└── completion.md      # Shell completions
```

**Index structure:**

```markdown
# CLI Reference

The `muxi` command-line tool for formation development and deployment.

## Installation

Installed automatically with MUXI. Verify with:

muxi --version

## Common Commands

### Development

muxi new formation <name>    # Create new formation
muxi validate                # Validate formation files
muxi dev                     # Run locally
muxi secrets setup           # Configure secrets

### Deployment

muxi deploy                  # Deploy to server
muxi formation list          # List deployed formations
muxi formation stop <id>     # Stop formation
muxi formation restart <id>  # Restart formation

### Registry

muxi push                    # Publish to registry
muxi pull @user/formation    # Download from registry
muxi search <query>          # Search registry

### Interaction

muxi chat                    # Interactive chat session
muxi logs --follow           # Stream formation logs

## Global Flags

| Flag | Description |
|------|-------------|
| `--profile <name>` | Server profile to use |
| `--formation <id>` | Formation to target |
| `--output json` | JSON output format |
| `--debug` | Debug logging |

## Configuration

CLI configuration lives in `~/.muxi/cli/`:

~/.muxi/cli/
├── config.yaml        # Global settings
├── servers.yaml       # Server profiles
├── formations.yaml    # Formation credentials
└── registries.yaml    # Registry auth

## Command Groups

- [new →](./new) - Create formations and components
- [deploy →](./deploy) - Deploy to servers
- [secrets →](./secrets) - Manage secrets
- [profile →](./profile) - Server profiles
- [registry →](./registry) - Push, pull, search
```

---

## 7. SDKs (`/docs/sdks`)

**Purpose:** SDK installation and usage for each language.

**Pages:**

```
/docs/sdks/
├── index.md           # Overview and comparison
├── python.md          # Python SDK
├── typescript.md      # TypeScript/JavaScript SDK
├── go.md              # Go SDK
└── others.md          # PHP, Ruby, Swift, Kotlin, etc.
```

**Index structure:**

```markdown
# SDKs

Client libraries for integrating MUXI into your applications.

## Available SDKs

| Language | Package | Status |
|----------|---------|--------|
| Python | `pip install muxi-client` | Stable |
| TypeScript | `npm install @muxi/client` | Stable |
| Go | `go get github.com/muxi-ai/muxi-go` | Stable |
| PHP | `composer require muxi/client` | Beta |
| Ruby | `gem install muxi` | Beta |

## Quick Example

**Python:**
from muxi import FormationClient

client = FormationClient(
    server_url="https://api.example.com:7890",
    formation_id="my-assistant",
    client_key="fc_..."
)

response = client.chat("Hello!")
print(response.message)

**TypeScript:**
import { FormationClient } from '@muxi/client';

const client = new FormationClient({
  serverUrl: 'https://api.example.com:7890',
  formationId: 'my-assistant',
  clientKey: 'fc_...',
});

const response = await client.chat('Hello!');
console.log(response.message);

## Two Clients

SDKs provide two client types:

| Client | Purpose | Auth |
|--------|---------|------|
| `ServerClient` | Server management (deploy, list, stop) | HMAC |
| `FormationClient` | Formation usage (chat, health) | API key |

## SDK Guides

- [Python SDK →](./python)
- [TypeScript SDK →](./typescript)
- [Go SDK →](./go)
```

**Python page structure:**

```markdown
# Python SDK

## Installation

pip install muxi-client

Requires Python 3.9+.

## FormationClient

For interacting with deployed formations.

from muxi import FormationClient

client = FormationClient(
    server_url="https://api.example.com:7890",
    formation_id="my-assistant",
    client_key="fc_..."
)

### Chat

# Synchronous
response = client.chat("Hello!")
print(response.message)

# Streaming
for chunk in client.chat_stream("Tell me a story"):
    print(chunk.text, end="")

### Health Check

health = client.health()
print(health.status)  # "healthy"

## ServerClient

For server management operations.

from muxi import ServerClient

server = ServerClient(
    url="https://api.example.com:7890",
    key_id="MUXI_KEY",
    secret_key="sk_..."
)

### Deploy Formation

result = server.deploy_formation(
    bundle_path="my-formation.tar.gz",
    metadata={"id": "my-assistant"}
)

### List Formations

formations = server.list_formations()
for f in formations:
    print(f"{f.id}: {f.status}")

## Async Support

from muxi import AsyncFormationClient

client = AsyncFormationClient(...)

response = await client.chat("Hello!")

async for chunk in client.chat_stream("Tell me a story"):
    print(chunk.text, end="")

## Error Handling

from muxi.exceptions import AuthenticationError, RateLimitError

try:
    response = client.chat("Hello")
except RateLimitError as e:
    print(f"Wait {e.retry_after} seconds")
except AuthenticationError:
    print("Invalid API key")

## Configuration

| Option | Default | Description |
|--------|---------|-------------|
| `timeout` | 30s | Request timeout |
| `max_retries` | 3 | Retry attempts |
| `debug` | False | Debug logging |

## Complete Reference

See [API Reference →](/docs/api/python-sdk) for all methods.
```

---

## 8. Registry (`/docs/registry`)

**Purpose:** Finding, downloading, and publishing formations.

**Pages:**

```
/docs/registry/
├── index.md           # What is the registry?
├── searching.md       # Finding formations
├── pulling.md         # Downloading formations
├── publishing.md      # Publishing your formations
└── organizations.md   # Organization accounts
```

**Index structure:**

```markdown
# Registry

The MUXI Registry is a hub for discovering and sharing formations.

**URL:** [registry.muxi.org](https://registry.muxi.org)

## Overview

- **Browse** formations at registry.muxi.org
- **Pull** formations with `muxi pull @user/formation`
- **Push** your formations with `muxi push`

## Quick Start

### Find a Formation

Browse [registry.muxi.org](https://registry.muxi.org) or search:

muxi search "customer support"

### Use a Formation

muxi pull @muxi/starter-assistant
cd starter-assistant
muxi secrets setup
muxi dev

### Publish Your Formation

muxi login
muxi push

## Formation References

Formations are referenced as `@user/name[:version]`:

@muxi/starter-assistant       # Latest version
@muxi/starter-assistant:1.0   # Specific version
@acme/support-bot             # Organization formation

## Guides

- [Searching →](./searching) - Finding the right formation
- [Pulling →](./pulling) - Downloading and customizing
- [Publishing →](./publishing) - Sharing your work
```

---

## 9. Guides (`/docs/guides`)

**Purpose:** Task-oriented tutorials. "How do I...?"

**Pages:**

```
/docs/guides/
├── index.md                    # Guide index
├── deploy-to-production.md     # Production deployment
├── add-tools.md                # Adding MCP servers
├── add-memory.md               # Configuring memory
├── multi-agent.md              # Multiple collaborating agents
├── multi-user.md               # Multi-tenant setup
├── webhooks.md                 # Webhook triggers
├── scheduled-jobs.md           # Scheduled tasks
├── custom-ui.md                # Building custom interfaces
├── ci-cd.md                    # CI/CD integration
├── monitoring.md               # Observability setup
└── troubleshooting.md          # Common issues
```

**Example guide structure (`add-tools.md`):**

```markdown
# Add Tools to Your Formation

Give your agent capabilities like web search, file access, and database queries.

**Time:** 10 minutes

## Overview

MUXI uses MCP (Model Context Protocol) servers to give agents tools.

By the end of this guide, your agent will be able to search the web.

## Prerequisites

- A working formation (see [Quickstart](/docs/quickstart))

## Step 1: Create MCP Configuration

muxi new mcp web-search

This creates `mcps/web-search.afs`:

# mcps/web-search.afs
id: web-search
server: "@anthropic/brave-search"
config:
  api_key: ${BRAVE_API_KEY}

## Step 2: Add Secret

muxi secrets set BRAVE_API_KEY

Enter your Brave Search API key when prompted.

## Step 3: Reference in Formation

Edit `formation.afs`:

mcps:
  - $include: mcps/web-search.afs

## Step 4: Test

muxi dev

Then ask your agent:
> Search for the latest news about AI

The agent will use the web search tool automatically.

## Available MCP Servers

| Server | Capability |
|--------|------------|
| `@anthropic/brave-search` | Web search |
| `@anthropic/filesystem` | File operations |
| `@muxi/postgres` | PostgreSQL queries |
| `@muxi/slack` | Slack integration |

Browse more at [registry.muxi.org/mcps](https://registry.muxi.org/mcps).

## Next Steps

- [Add memory →](./add-memory)
- [MCP reference →](/docs/formations/tools)
```

---

## 10. Concepts (`/docs/concepts`)

**Purpose:** Mental models for curious readers. Not required to use MUXI.

**Pages:**

```
/docs/concepts/
├── index.md           # Concept overview
├── architecture.md    # How MUXI works
├── agents.md          # What are AI agents?
├── memory.md          # Memory systems explained
├── orchestration.md   # Multi-agent coordination
├── security.md        # Security model
└── versioning.md      # ScalVer versioning
```

**Keep these short and conceptual.** Link to reference docs for specifics.

---

## 11. API Reference (`/docs/api`)

**Purpose:** Complete API documentation for programmatic access.

**Pages:**

```
/docs/api/
├── index.md           # API overview
├── authentication.md  # Auth methods (HMAC, API keys)
├── server-api.md      # Server management endpoints
├── formation-api.md   # Formation runtime endpoints
├── errors.md          # Error codes and handling
├── python-sdk.md      # Full Python SDK reference
├── typescript-sdk.md  # Full TypeScript SDK reference
└── go-sdk.md          # Full Go SDK reference
```

---

## 12. Runtime (Separate Section)

**Purpose:** For embedders and contributors. Not for typical users.

**Location:** `/docs/runtime/` (clearly separated)

```markdown
# Runtime Documentation

**Note:** This section is for developers embedding MUXI Runtime in applications
or contributing to MUXI. Most users should see [Getting Started](/docs/quickstart).

[Continue to Runtime Docs →](/docs/runtime/overview)
```

This section contains the detailed internals from `runtime/docs/`.

---

## Navigation Structure

```yaml
# docs navigation
- Home: index.md
- Quickstart: quickstart.md                    # Uses: CLI, Server, (Registry)
- Installation:
  - Overview: installation/index.md            # Installs: Server + CLI
  - macOS: installation/macos.md
  - Linux: installation/linux.md
  - Windows: installation/windows.md
  - Docker: installation/docker.md
- Server:
  - Overview: server/index.md
  - Configuration: server/configuration.md
  - Authentication: server/authentication.md
  - Monitoring: server/monitoring.md
  - Production: server/production.md
- Formations:
  - Overview: formations/index.md              # Uses: CLI for scaffolding
  - Schema Reference: formations/schema.md
  - Agents: formations/agents.md
  - Memory: formations/memory.md
  - Tools (MCP): formations/tools.md
  - Secrets: formations/secrets.md
  - Examples: formations/examples.md
- CLI:
  - Overview: cli/index.md
  - new: cli/new.md                            # Creates formations, agents, etc.
  - deploy: cli/deploy.md                      # Deploys to Server
  - secrets: cli/secrets.md
  - formation: cli/formation.md                # Server operations
  - registry: cli/registry.md                  # Registry operations
- SDKs:
  - Overview: sdks/index.md                    # Alternative to CLI for apps
  - Python: sdks/python.md
  - TypeScript: sdks/typescript.md
  - Go: sdks/go.md
- Registry:
  - Overview: registry/index.md                # Uses: CLI for push/pull
  - Searching: registry/searching.md
  - Publishing: registry/publishing.md
- Guides:                                      # Cross-cutting tutorials
  - All Guides: guides/index.md
  - Deploy to Production: guides/deploy.md     # Uses: Server, CLI
  - Add Tools: guides/add-tools.md             # Uses: CLI, Formations
  - Add Memory: guides/add-memory.md           # Uses: CLI, Formations
  - Multi-Agent: guides/multi-agent.md         # Uses: Formations
  - Custom UI: guides/custom-ui.md             # Uses: SDKs
  - CI/CD: guides/ci-cd.md                     # Uses: CLI, SDKs, Server
  - Monitoring: guides/monitoring.md           # Uses: Server, CLI
- API Reference:
  - Overview: api/index.md
  - Authentication: api/authentication.md
  - Server API: api/server-api.md              # Server endpoints
  - Formation API: api/formation-api.md        # Runtime endpoints
- Concepts:
  - Overview: concepts/index.md
  - Architecture: concepts/architecture.md     # How it all fits together
- Runtime (Advanced): runtime/index.md         # For embedders only
```

---

## Writing Guidelines

### Voice and Tone

- **Direct:** "Install MUXI" not "You might want to install MUXI"
- **Active:** "The server starts" not "The server is started"
- **Concrete:** "port 7890" not "the default port"
- **Concise:** Cut every unnecessary word

### Structure Rules

1. **Lead with action** - Show what to do before explaining why
2. **One idea per section** - Break long sections into subsections
3. **Code first** - Show the code, then explain it
4. **Expected output** - Always show what success looks like
5. **Link, don't embed** - Reference other pages instead of duplicating

### Code Examples

- **Complete and runnable** - No "..." or placeholders
- **Show output** - Include expected terminal output
- **Real values** - Use realistic (but fake) API keys, URLs
- **Copy-paste ready** - Reader can run immediately

### Page Length

| Page Type | Target Length |
|-----------|---------------|
| Landing | Under 200 words |
| Quickstart | Under 400 words |
| Reference | As needed (scannable) |
| Guide | 500-1000 words |
| Concept | 300-500 words |

---

## AI Optimization

For LLM-friendly documentation:

1. **Clear headings** - Hierarchical, descriptive H1/H2/H3
2. **Explicit relationships** - "This requires X" not "make sure you have X"
3. **Structured data** - Tables for comparisons, lists for options
4. **No ambiguity** - Specify versions, paths, exact commands
5. **Self-contained pages** - Each page understandable without context

### llms.txt

Create `/llms.txt` at site root:

```
# MUXI Documentation

> MUXI is infrastructure for deploying AI agents to production.

## Quick Start
- Install: curl -sSL https://muxi.org/install | bash
- Create: muxi new formation my-assistant
- Run: cd my-assistant && muxi dev

## Key Concepts
- Formation: YAML definition of an AI system
- Agent: AI persona within a formation
- MCP: Tool integration protocol
- Registry: Formation distribution hub

## Documentation Structure
- /docs/quickstart - 5-minute getting started
- /docs/formations - Formation reference
- /docs/cli - CLI commands
- /docs/sdks - Python, TypeScript, Go SDKs
- /docs/guides - Task-oriented tutorials

## Common Tasks
- Deploy formation: muxi deploy
- Add tools: muxi new mcp <name>
- Publish: muxi push
- Download: muxi pull @user/formation
```

---

## Implementation Priority

### Phase 1: Core (Week 1)
1. Landing page
2. Quickstart
3. Installation (all platforms)
4. CLI overview + key commands

### Phase 2: Reference (Week 2)
1. Formations (all pages)
2. CLI (all commands)
3. SDKs (Python, TypeScript)

### Phase 3: Guides (Week 3)
1. Deploy to production
2. Add tools
3. Add memory
4. Multi-agent

### Phase 4: Polish (Week 4)
1. Registry
2. API reference
3. Concepts
4. Search optimization
5. llms.txt

---

## Success Metrics

- **Time to first formation:** Under 5 minutes
- **Quickstart completion rate:** >80%
- **Search success rate:** >90% find what they need
- **Support ticket reduction:** Fewer "how do I..." questions

---

## Related Documents

- [runtime/docs/full-sitemap.md](../runtime/docs/full-sitemap.md) - Internal docs structure
- [cli/docs/DESIGN.md](../cli/docs/DESIGN.md) - CLI design spec
- [sdks/SDK-DESIGN.md](../sdks/SDK-DESIGN.md) - SDK design philosophy
- [server/docs/README.md](../server/docs/README.md) - Server documentation
