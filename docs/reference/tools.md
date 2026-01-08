---
title: Tools (MCP)
description: Give agents capabilities with MCP servers
---

# Tools (MCP)

## Give agents superpowers with MCP servers

Tools let agents interact with the world - search the web, read files, query databases, call APIs. MUXI uses the **Model Context Protocol (MCP)** for tool integration.

<!-- TODO: Add illustration of agent using tools -->
<!-- [Image placeholder: Agent using web search and database tools] -->

---

## Add Your First Tool

[[steps]]

[[step Add the MCP config]]

```yaml
# formation.afs
mcps:
  - id: web-search
    server: "@anthropic/brave-search"
    config:
      api_key: ${{ secrets.BRAVE_API_KEY }}
```
[[/step]]

[[step Set the secret]]

```bash
muxi secrets set BRAVE_API_KEY
```
[[/step]]

[[step Assign to agent]]

```yaml
agents:
  - id: researcher
    role: Research specialist
    mcps:
      - web-search
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

## MCP Configuration

```yaml
mcps:
  - id: web-search           # Unique identifier
    server: "@anthropic/brave-search"  # MCP server package
    config:                  # Server configuration
      api_key: ${{ secrets.BRAVE_API_KEY }}
    env:                     # Environment variables
      DEBUG: "false"
```

| Field | Required | Description |
|-------|----------|-------------|
| `id` | Yes | Unique identifier for this tool |
| `server` | Yes | MCP server package or command |
| `config` | No | Server-specific configuration |
| `env` | No | Environment variables for the server |

---

## Popular MCP Servers

### Official Servers

| Server | What It Does | Config Required |
|--------|--------------|-----------------|
| `@anthropic/brave-search` | Web search | `api_key` |
| `@anthropic/filesystem` | Read/write files | `allowed_directories` |
| `@anthropic/github` | GitHub API | `GITHUB_TOKEN` env |
| `@anthropic/postgres` | PostgreSQL queries | `connection_string` |
| `@anthropic/sqlite` | SQLite queries | `database_path` |

> [!TIP]
> Browse more servers at [registry.muxi.org/mcps](https://registry.muxi.org/mcps)

---

## Configuration Examples

### Web Search

```yaml
mcps:
  - id: web-search
    server: "@anthropic/brave-search"
    config:
      api_key: ${{ secrets.BRAVE_API_KEY }}
```

### File System

```yaml
mcps:
  - id: files
    server: "@anthropic/filesystem"
    config:
      allowed_directories:
        - /home/user/documents
        - /home/user/projects
```

> [!WARNING]
> Always restrict file access to specific directories. Never allow `/` or home directory root.

### Database

[[tabs]]

[[tab PostgreSQL]]
```yaml
mcps:
  - id: database
    server: "@anthropic/postgres"
    config:
      connection_string: ${{ secrets.DATABASE_URL }}
```
[[/tab]]

[[tab SQLite]]
```yaml
mcps:
  - id: database
    server: "@anthropic/sqlite"
    config:
      database_path: ./data/app.db
```
[[/tab]]

[[/tabs]]

### GitHub

```yaml
mcps:
  - id: github
    server: "@anthropic/github"
    env:
      GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

---

## Custom MCP Servers

Use any MCP-compatible server:

```yaml
mcps:
  - id: custom-tool
    server: npx @company/custom-mcp-server
    env:
      API_KEY: ${{ secrets.CUSTOM_API_KEY }}
```

Or a local server:

```yaml
mcps:
  - id: local-tool
    server: python /path/to/my_mcp_server.py
```

---

## Separate MCP Files

For complex configurations:

```bash
muxi new mcp web-search
```

Creates `mcps/web-search.afs`:

```yaml
# mcps/web-search.afs
id: web-search
server: "@anthropic/brave-search"
config:
  api_key: ${{ secrets.BRAVE_API_KEY }}
```

Reference in formation:

```yaml
mcps:
  - $include: mcps/web-search.afs
  - $include: mcps/database.afs
```

---

## Agent-Specific Tools

Restrict tools to specific agents:

```yaml
mcps:
  - id: web-search
    server: "@anthropic/brave-search"
  - id: filesystem
    server: "@anthropic/filesystem"
  - id: database
    server: "@anthropic/postgres"

agents:
  - id: researcher
    role: Research topics
    mcps:
      - web-search     # Only researcher can search

  - id: developer
    role: Code assistant
    mcps:
      - filesystem     # Only developer can access files
      - database       # Only developer can query database

  - id: writer
    role: Content writer
    # No tools - pure writing focus
```

---

## Troubleshooting

[[toggle Tool Not Available]]
Verify the server is installed:

```bash
npx @anthropic/brave-search --version
```

If not found, install it:

```bash
npm install -g @anthropic/brave-search
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

[[toggle Permission Denied]]
For filesystem tools, verify paths are in `allowed_directories`.

For databases, check connection credentials.
[[/toggle]]

---

## Next Steps

[+] [Secrets](secrets.md) - Manage tool credentials securely
[+] [Agents](agents.md) - Assign tools to agents
[+] [Add Tools Guide](../guides/add-tools.md) - Step-by-step tutorial
