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

Create `mcp/web-search.yaml`:

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

[[step Assign to agent]]

In your agent config (inline or `agents/*.yaml`):

```yaml
agents:
  - id: researcher
    role: Research specialist
    mcps:
      - web-search  # References the MCP id
```
[[/step]]

[[step Test it]]

```bash
muxi dev
# Then: "Search for recent AI news"
```
[[/step]]

[[/steps]]

---

## MCP Server Types

### Command-based (Local)

Runs a local process:

```yaml
# mcp/local-tool.yaml
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
# mcp/remote-tool.yaml
schema: "1.0.0"
id: remote-tool
type: http
endpoint: "https://api.example.com/mcp"
timeout_seconds: 30
auth:
  type: bearer
  token: "${{ secrets.API_TOKEN }}"
```

---

## MCP File Fields

| Field | Required | Description |
|-------|----------|-------------|
| `schema` | Yes | Schema version (`"1.0.0"`) |
| `id` | Yes | Unique identifier |
| `type` | Yes | `command` or `http` |
| `command` | Yes (command) | Command to execute |
| `args` | No | Command arguments |
| `endpoint` | Yes (http) | Remote server URL |
| `timeout_seconds` | No | Request timeout |
| `auth` | No | Authentication config |

---

## Popular MCP Servers

| Server | What It Does | Type |
|--------|--------------|------|
| `@modelcontextprotocol/server-brave-search` | Web search | command |
| `@modelcontextprotocol/server-filesystem` | Read/write files | command |
| `@modelcontextprotocol/server-github` | GitHub API | command |
| `@modelcontextprotocol/server-postgres` | PostgreSQL | command |
| `@modelcontextprotocol/server-sqlite` | SQLite | command |

---

## Configuration Examples

### Web Search

```yaml
# mcp/web-search.yaml
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
# mcp/filesystem.yaml
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
# mcp/postgres.yaml
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
# mcp/sqlite.yaml
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
# mcp/github.yaml
schema: "1.0.0"
id: github
type: command
command: npx
args: ["-y", "@modelcontextprotocol/server-github"]
auth:
  type: env
  GITHUB_TOKEN: "${{ secrets.GITHUB_TOKEN }}"
```

---

## Formation-Level MCP Settings

Configure global MCP behavior in `formation.yaml`:

```yaml
mcp:
  default_timeout_seconds: 30
  max_tool_iterations: 10
  max_tool_calls: 50
  max_repeated_errors: 3
```

---

## Agent-Specific Tools

Assign tools to specific agents:

```yaml
# formation.yaml
agents:
  - id: researcher
    role: Research topics
    mcps:
      - web-search     # Can search the web

  - id: developer
    role: Code assistant
    mcps:
      - filesystem     # Can access files
      - database       # Can query database

  - id: writer
    role: Content writer
    # No mcps - pure writing focus
```

---

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

---

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

---

## Next Steps

- [Secrets](secrets.md) - Manage tool credentials
- [Agents](agents.md) - Assign tools to agents
- [Add Tools Guide](../guides/add-tools.md) - Step-by-step tutorial
