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
muxi up
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
| `timeout_seconds` | int | - | Request timeout |
| `retry_attempts` | int | - | Number of retries (0-10) |
| `connection_ttl` | int | - | Per-server idle TTL override (seconds). See [Connection Lifecycle](#connection-lifecycle). |
| `parameters` | object | - | Default tool arguments injected into every call. See [Default Parameters](#default-parameters). |
| `tools` | object | - | Filter the agent-visible tool surface advertised by this MCP server. See [Tool Filtering](#tool-filtering-allow--deny). |

### Tool Filtering (`allow` / `deny`)

Large MCP servers (Microsoft 365, Google Workspace, ms365-assistant, large internal catalogs) often expose 30+ tools, of which any given agent only needs a handful. Filtering trims the surface advertised to the LLM at registration time, so the planning prompt shrinks and the model is less likely to pick a wrong-but-plausible tool.

```yaml
# mcp/ms365-excel.afs
schema: "1.0.0"
id: ms365-excel
type: command
command: npx
args: ["-y", "@softeria/ms-365-mcp-server"]
auth:
  type: env
  ACCESS_TOKEN: "${{ user.credentials.MS365 }}"

tools:
  allow:
    - "list-excel-files"
    - "read-excel-*"
    - "update-excel-*"
```

#### Schema

| Field | Type | Description |
|-------|------|-------------|
| `tools.allow` | string[] | If set, **only** these tool names are exposed. fnmatch-style globs (`*`, `?`) supported. |
| `tools.deny` | string[] | These tool names are hidden. Same glob syntax. Applied **after** `allow`. |

> [!NOTE]
> `allow` / `deny` is the canonical vocabulary at every level; `whitelist` /
> `blacklist` remain accepted aliases. `allow` and `deny` may now be combined
> (deny is applied after allow) - the old strict either-or rule is relaxed.

`tools:` overrides cascade across four levels, most specific wins:

1. MCP registry catalog (`mcp.servers[].tools`)
2. **Agent attachment** - an agent's `mcp_servers` entry may be a `{id, tools}`
   mapping instead of a bare string
3. Group per-server
4. Group per-agent-per-server

```yaml
# agents/researcher.afs
mcp_servers:
  - id: web-search
    tools:
      allow: ["search"]
      deny: ["admin_*"]
```

An intentionally emptied shared catalog is respected as empty (no silent
fall-through to the unfiltered registry). `tools: {deny: "*"}` hides a server
entirely. See [Access Control](../concepts/access-control.md) for the group
levels.

#### How it's applied

```
Upstream MCP tools/list response
        ↓
   Filter (allow, then deny; fnmatch globs)
        ↓
Agent-visible tool registry (this is what the LLM ever sees)
```

The filter runs on every MCP `tools/list` refresh, so dynamic tool catalogs stay filtered after upstream redeploys. Filtered-out tools never appear in the planning prompt or in the agent's allowed-tool list — they are invisible end-to-end.

#### Common patterns

| Goal | Pattern |
|------|---------|
| Read-only access to a domain | `allow: ["list-*", "read-*", "search-*"]` |
| Block destructive operations | `deny: ["delete-*", "drop-*", "purge-*"]` |
| Single-tool agent | `allow: ["search-web"]` |
| Per-domain split of a large catalog | One MCP file per domain (excel/word/email/calendar), each with its own `allow` |

#### Per-domain split for large catalogs

For MCPs with >20 tools, the recommended pattern is to define **one MCP `.afs` file per domain**, each scoped to the appropriate agent, instead of giving every agent the full catalog. Example: `ms365-excel.afs` (allowed Excel tools only) → `ms365-excel-agent.afs`; `ms365-email.afs` → `ms365-email-agent.afs`. The full upstream MCP server still runs once per agent, but each agent's tool surface is a curated slice. See the [multi-agent guide](../guides/build-multi-agent-systems.md#per-agent-mcp-scoping-for-large-catalogs) for a worked example.

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

## Built-in Tools

Beyond MCP servers, MUXI registers built-in tools automatically when the
relevant feature is configured. Agents call them like any other tool.

| Tool | Available when | Purpose |
|------|----------------|---------|
| `generate_file` | always | Produce a file artifact in the sandbox (see [Artifacts](../concepts/artifacts.md)) |
| `get_artifact` / `get_artifact_content` / `get_artifact_history` | artifact memory retrieval | Recall, read, and version-walk previously produced artifacts |
| `watch_job` | the formation declares MCP servers and watch is not disabled | Poll a remote job and re-enter when it completes |
| `recall_history` | Captain's Log enabled | Time-anchored recall over the user's own log entries (`date_from`/`date_to` ISO dates, optional `query`, `limit`) |
| `record_lesson` | Captain's Log enabled | Record a durable lesson that is injected into agent prompts |
| `delegate_coding` | a `coding:` block exists | Delegate a coding task to an external CLI (see [Coding-Agent Delegation](../concepts/coding-delegation.md)); always async |

## Watching remote MCP tools (`watch_job`)

MCP-reachable work that outlives a turn (image/video generation, long renders,
batch jobs) can be collected instead of forgotten. The agent calls the built-in
`watch_job` tool, which registers a deterministic poll loop over any MCP tool the
calling user can see - the poll loop runs no LLM, so polling costs zero tokens.
`watch_job` is **always async**: it returns a job handle immediately.

Parameters: `tool` (`server.tool`, `server__tool`, or bare name), `args`,
`done_when` (`path` + `equals`/`in`), optional `result` selector and `label`.
There are no interval/timeout arguments - cadence and deadline are formation
config. Jobs appear on the `/jobs` surface; completion, timeout, or failure
re-enters the conversation (`route_class: watch`) via the notification router.
Polls run under the original user's permission context (fail-closed).

The `mcp.watch` sub-block configures the loop. `watch_job` is **on by default**
whenever the formation declares MCP servers; `mcp: {watch: false}` removes the
tool entirely.

```yaml
mcp:
  watch:
    interval: 30                  # seconds between polls (default 30)
    timeout: 7200                 # per-job deadline in seconds (default 7200)
    max_concurrent: 10            # per user (default 10)
    max_consecutive_failures: 3   # give up after N failed polls (default 3)
```

A bundled recognition SOP fragment teaches agents to spot job-shaped responses;
a formation-local `sops/watch_job.md` shadows it (an empty file removes it).
Group templates may raise the quota with `mcp: {watch: {max_concurrent: N}}`.

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
  connection_ttl: 300             # Idle connection TTL in seconds (default: 300)
  default_timeout_seconds: 30
  max_tool_iterations: 10
  max_tool_calls: 50
  max_repeated_errors: 3
```

## Connection Lifecycle

MCP connections follow a **lazy-persistent** lifecycle:

1. **Discovery:** On formation startup, the runtime connects to each MCP server, discovers its tools, then disconnects.
2. **First tool call:** The runtime reconnects and keeps the connection alive.
3. **Subsequent calls:** Reuse the existing connection. Each call resets the idle timer.
4. **Idle timeout:** A background reaper closes connections that have been idle longer than `connection_ttl`.
5. **Next call after timeout:** The runtime reconnects transparently.

A frequently-used server stays connected indefinitely. A rarely-used server closes after the TTL and reconnects on demand.

Set `connection_ttl: 0` to disable keep-alive and use ephemeral connections (connect/execute/disconnect per call).

### Per-server override

Override the global TTL for individual servers:

```yaml
# mcp/ms365.afs
schema: "1.0.0"
id: ms365
type: http
endpoint: "https://ms365.example.com/mcp"
connection_ttl: 600    # Keep this server alive longer (10 minutes)
```

```yaml
# mcp/one-off-tool.afs
schema: "1.0.0"
id: one-off-tool
type: http
endpoint: "https://tool.example.com/mcp"
connection_ttl: 0      # Always disconnect after each call
```

## Default Parameters

MCP servers support an optional `parameters` field: a flat key-value map injected into every tool call on that server. Use this for infrastructure constants that should never be left to LLM inference -- org-level drive IDs, tenant IDs, project keys, etc.

```yaml
# mcp/ms365.afs
schema: "1.0.0"
id: ms365-mcp
type: http
endpoint: "https://mcp.example.com/ms365"
parameters:
  driveId: "${{ secrets.ORG_DRIVE_ID }}"
  siteId: "contoso.sharepoint.com,guid1,guid2"
```

**How it works:**
- Parameters are injected at execution time, before the LLM planner runs
- If a tool call already provides a value for the same key, the explicit value wins
- Values support `${{ secrets.X }}` and `${{ user.credentials.X }}` interpolation
- Must be a flat map with scalar values (strings, numbers, booleans) -- no nested objects

**When to use:**
- Fixed org-wide identifiers (drive IDs, site IDs, tenant IDs)
- API version strings
- Any value that is constant across all tool calls for a given server

> [!TIP]
> **Move constants out of agent prompts.** If your agent's system message contains a hardcoded ID just so the LLM can pass it to tools, put it in `parameters` instead. The LLM never needs to see or propagate infrastructure config.

Works on both formation-level MCP servers (`mcp/*.afs`) and agent-level MCP servers (`mcp_servers` in agent `.afs` files).

## Agent-Specific Tools

> [!TIP]
> **Prefer per-agent tools.** Defining tools per-agent improves Overlord's agent selection and each agent's tool selection. Use global MCP servers only for tools ALL agents need.

Formation-level MCP servers (in `mcp/*.afs`, declared in `mcp.servers`) are available to all agents. Agents can reference them by string ID, or define agent-private tools inline:

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
  - web-search              # Reference formation-level MCP by ID
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
  - filesystem              # Reference formation-level MCP by ID
  - database                # Reference formation-level MCP by ID
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
