---
title: Tools (MCP)
description: Give agents capabilities with MCP servers
---

# Tools (MCP)

## Give agents superpowers with MCP servers

Tools let agents interact with the world - search the web, read files, query databases, call APIs. MUXI uses the **Model Context Protocol (MCP)** for tool integration.


## Add Your First Tool

[[steps]]

[[step Create the MCP config file]]

```bash
mkdir -p mcp
```

Create `mcp/web-search.afs`:

```yaml
schema: "1.0.0"
id: web-search
description: Brave web search
type: command
command: npx
args: ["-y", "@modelcontextprotocol/server-brave-search"]
auth:
  type: env
  BRAVE_API_KEY: "${{ secrets.BRAVE_API_KEY }}"
```
[[/step]]

[[step Set the secret]]

```bash
muxi secrets set BRAVE_API_KEY
```
[[/step]]

[[step Test it]]

All agents in the formation automatically have access to MCP servers defined in `mcp/*.afs`.

```bash
muxi dev
# Then: "Search for recent AI news"
```
[[/step]]

[[/steps]]

## MCP Server Types

### Command-based (Local)

Runs a local process:

```yaml
# mcp/local-tool.afs
schema: "1.0.0"
id: local-tool
type: command
command: npx
args: ["-y", "@modelcontextprotocol/server-brave-search"]
timeout_seconds: 30
auth:
  type: env
  API_KEY: "${{ secrets.API_KEY }}"
```

### HTTP-based (Remote)

Calls a remote MCP server:

```yaml
# mcp/remote-tool.afs
schema: "1.0.0"
id: remote-tool
type: http
endpoint: "https://api.example.com/mcp"
timeout_seconds: 30
auth:
  type: bearer
  token: "${{ secrets.API_TOKEN }}"
```

## MCP File Fields

### Required Fields

| Field | Type | Description |
|-------|------|-------------|
| `schema` | string | Schema version (`"1.0.0"`) |
| `id` | string | Unique identifier |
| `description` | string | What this MCP server does |
| `type` | enum | `command` (stdio) or `http` |

### Command Type Fields

| Field | Type | Description |
|-------|------|-------------|
| `command` | string | Command to execute (`npx`, `python`, `node`) |
| `args` | array | Command arguments |
| `env` | object | Environment variables for the command |
| `cwd` | string | Working directory for the command |

### HTTP Type Fields

| Field | Type | Description |
|-------|------|-------------|
| `endpoint` | string | HTTP endpoint URL |
| `protocol` | enum | `http` or `https` |
| `method` | enum | `GET`, `POST`, `PUT`, `DELETE` (default: `POST`) |
| `headers` | object | Additional HTTP headers |

### Common Optional Fields

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `active` | bool | `true` | Enable/disable this server |
| `timeout_seconds` | int | - | Request timeout |
| `retry_attempts` | int | - | Number of retries (0-10) |

### Capabilities

```yaml
capabilities:
  tools: ["list_repos", "create_issue"]  # Tool names provided
  streaming: false                        # Supports streaming?
  async: false                            # Supports async?
```

### Health Check (HTTP only)

```yaml
health_check:
  enabled: true
  endpoint: "/health"
  interval_seconds: 60
  timeout_seconds: 5
```

### Rate Limiting

```yaml
rate_limiting:
  requests_per_minute: 60
  requests_per_hour: 1000
  burst_limit: 10
```

### Metadata

```yaml
metadata:
  version: "1.0.0"
  author: "Your Team"
  homepage: "https://example.com"
  documentation: "https://docs.example.com"
  tags: ["search", "web"]
```

## Popular MCP Servers

| Server | What It Does | Type |
|--------|--------------|------|
| `@modelcontextprotocol/server-brave-search` | Web search | command |
| `@modelcontextprotocol/server-filesystem` | Read/write files | command |
| `@modelcontextprotocol/server-github` | GitHub API | command |
| `@modelcontextprotocol/server-postgres` | PostgreSQL | command |
| `@modelcontextprotocol/server-sqlite` | SQLite | command |

## Configuration Examples

### Web Search

```yaml
# mcp/web-search.afs
schema: "1.0.0"
id: web-search
type: command
command: npx
args: ["-y", "@modelcontextprotocol/server-brave-search"]
auth:
  type: env
  BRAVE_API_KEY: "${{ secrets.BRAVE_API_KEY }}"
```

### File System

```yaml
# mcp/filesystem.afs
schema: "1.0.0"
id: filesystem
type: command
command: npx
args: ["-y", "@modelcontextprotocol/server-filesystem", "./documents", "./projects"]
timeout_seconds: 30
```

> [!WARNING]
> Always restrict file access to specific directories in the args.

### Database

[[tabs]]

[[tab PostgreSQL]]
```yaml
# mcp/postgres.afs
schema: "1.0.0"
id: database
type: command
command: npx
args: ["-y", "@modelcontextprotocol/server-postgres"]
auth:
  type: env
  DATABASE_URL: "${{ secrets.DATABASE_URL }}"
```
[[/tab]]

[[tab SQLite]]
```yaml
# mcp/sqlite.afs
schema: "1.0.0"
id: database
type: command
command: npx
args: ["-y", "@modelcontextprotocol/server-sqlite", "--db", "./data/app.db"]
```
[[/tab]]

[[/tabs]]

### GitHub

```yaml
# mcp/github.afs
schema: "1.0.0"
id: github
type: command
command: npx
args: ["-y", "@modelcontextprotocol/server-github"]
auth:
  type: env
  GITHUB_TOKEN: "${{ secrets.GITHUB_TOKEN }}"
```

## Formation-Level MCP Settings

Configure global MCP behavior in `formation.afs`:

```yaml
mcp:
  default_timeout_seconds: 30
  max_tool_iterations: 10
  max_tool_calls: 50
  max_repeated_errors: 3
```

## Agent-Specific Tools

> [!TIP]
> **Prefer per-agent tools.** Defining tools per-agent improves Overlord's agent selection and each agent's tool selection. Use global MCP servers only for tools ALL agents need.

Formation-level MCP servers (in `mcp/*.afs`) are available to all agents. For agent-specific tools, define `mcp_servers` in the agent file:

```yaml
# agents/researcher.afs
schema: "1.0.0"
id: researcher
name: Researcher
description: Research topics

system_message: |
  You are a research specialist.
  Your job is to gather accurate information...

mcp_servers:
  - id: web-search
    description: Web search
    type: command
    command: npx
    args: ["-y", "@modelcontextprotocol/server-brave-search"]
```

```yaml
# agents/developer.afs
schema: "1.0.0"
id: developer
name: Developer
description: Code assistant

system_message: |
  You are a software developer.
  Your job is to write, review, and debug code...

mcp_servers:
  - id: filesystem
    description: File access
    type: command
    command: npx
    args: ["-y", "@modelcontextprotocol/server-filesystem"]
```

## Authentication Types

### Environment Variables

```yaml
auth:
  type: env
  API_KEY: "${{ secrets.API_KEY }}"
  DEBUG: "true"
```

### Bearer Token

```yaml
auth:
  type: bearer
  token: "${{ secrets.TOKEN }}"
```

### Basic Auth

```yaml
auth:
  type: basic
  username: "${{ secrets.USERNAME }}"
  password: "${{ secrets.PASSWORD }}"
```

### API Key Header

```yaml
auth:
  type: api_key
  header: "X-API-Key"
  key: "${{ secrets.API_KEY }}"
```

## Troubleshooting

[[toggle Tool Not Available]]
Verify the server is installed:

```bash
npx @modelcontextprotocol/server-brave-search --version
```
[[/toggle]]

[[toggle Connection Failed]]
Check server logs:

```bash
muxi logs --mcp
```

Common issues:
- Invalid API key
- Network connectivity
- Server not running
[[/toggle]]

## Next Steps

- [Secrets](../concepts/secrets-and-security.md) - Manage tool credentials
- [Agents](../concepts/agents-and-orchestration.md) - Assign tools to agents
- [Add Tools Guide](../guides/add-mcp-tools.md) - Step-by-step tutorial
