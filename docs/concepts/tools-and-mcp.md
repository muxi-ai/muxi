---
title: Tools & MCP
description: How agents interact with the world through MCP tools
---
# Tools & MCP

## How agents interact with the world through MCP tools


MUXI speaks the **Model Context Protocol (MCP)** to connect agents with tools—web search, databases, file systems, APIs, and more. Any compliant MCP server works: Anthropic's, community/open-source servers, or your own. No registry or vendor lock-in required.


## How MCP Works

```mermaid
graph LR
    A[Agent] -->|"search for X"| O[Overlord]
    M -->|MCP protocol| T[Tool Server]
    T -->|Brave API| W[Web]
    W -->|results| T
    T -->|formatted| O
    O -->|context| A
    A -->|response| U[User]
```

1. **Agent decides** it needs a tool (e.g., web search)
2. **Overlord invokes** the MCP server
3. **Tool executes** the action (API call, query, etc.)
4. **Results return** to the agent
5. **Agent synthesizes** a response

The agent decides when to use tools - you don't need to explicitly request them.

---

## Available Tools

### MCP server types (where they live in a formation)

| Server type | When to use | Where in formation structure |
|-------------|-------------|------------------------------|
| **command (CLI)** | Run a local/server-side process (e.g., `npx @modelcontextprotocol/server-json-rpc`, Bash, Python) | `mcp/your-tool.afs` |
| **http** | Call a remote MCP server over HTTPS (any provider or your own) | `mcp/your-tool.afs` |

Example — command/CLI MCP:

```yaml
# mcp/local-tools.afs
schema: "1.0.0"
id: local-tools
type: command
command: npx
args: ["-y", "@modelcontextprotocol/server-json-rpc"]
timeout_seconds: 60
auth:
  type: env
  API_KEY: "${{ secrets.API_KEY }}"
```

Example — HTTP MCP:

```yaml
# mcp/web-tools.afs
schema: "1.0.0"
id: web-tools
type: http
endpoint: "https://external.com/mcp"
timeout_seconds: 30
retry_attempts: 3
auth:
  type: bearer
  token: "${{ secrets.MCP_TOKEN }}"
```

> Use any MCP server address you control. There is no required registry; just point to the server URL.

> [!TIP]
> **Start with one tool per agent.** Add tools incrementally and test each one. Too many tools confuse the agent about which to use.

---

## MCP Without the Bloat

> [!TIP]
> This solves the biggest problem with MCP today: **context window hogging**.

Traditional MCP implementations dump all tool schemas into every context window. Add 10 tools and you've burned 10,000+ tokens before the user even says hello.

MUXI takes a different approach:

```
Traditional MCP:
  10 tools × 1,000 tokens each = 10,000 tokens per request
  User message + tool schemas = bloated context

MUXI:
  Tool definitions loaded once at startup
  Schemas indexed for semantic lookup
  Subagents pull only what they need at runtime
  ~90% token reduction
```

The result: **use dozens of tools without burning your context window**.

---

## Multi-User Tool Access

Each user can store their own credentials:

```
User A: GitHub token → their repos
User B: GitHub token → their repos

Same formation, personalized access.
```

In your MCP config file, reference user secrets:

```yaml
# mcp/github.afs
schema: "1.0.0"
id: github
type: command
command: npx
args: ["-y", "@modelcontextprotocol/server-github"]
auth:
  type: env
  GITHUB_TOKEN: "${{ user.secrets.GITHUB_TOKEN }}"
```

Credentials encrypted at rest. Complete isolation between users.

**Formation structure:**
- Server definitions live in `mcp/*.afs` files (auto-discovered)
- Per-user secrets referenced as `user.secrets.*`
- All agents have access to formation-level MCP servers
- Agent-specific tools use `mcp_servers:` in agent files

---

## Agent-Specific Tools

Formation-level MCP servers (in `mcp/*.afs`) are available to all agents. For agent-specific tools, define `mcp_servers` in the agent file:

```yaml
# agents/researcher.afs
schema: "1.0.0"
id: researcher
name: Researcher
description: Research specialist

system_message: Research specialist with web search.

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

system_message: Code assistant.

mcp_servers:
  - id: filesystem
    description: File access
    type: command
    command: npx
    args: ["-y", "@modelcontextprotocol/server-filesystem"]
```

Or define formation-level MCP servers in `mcp/` directory (available to all agents):

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

```yaml
# mcp/filesystem.afs
schema: "1.0.0"
id: filesystem
type: command
command: npx
args: ["-y", "@modelcontextprotocol/server-filesystem", "./workspace"]
```

```yaml
# mcp/database.afs
schema: "1.0.0"
id: database
type: command
command: npx
args: ["-y", "@modelcontextprotocol/server-postgres"]
auth:
  type: env
  DATABASE_URL: "${{ secrets.DATABASE_URL }}"
```

Right tools for right agents. No accidental file access from the writer.

---

## Tool Security

### Path Restrictions

Restrict filesystem access in the MCP command args:

```yaml
# mcp/filesystem.afs
schema: "1.0.0"
id: filesystem
type: command
command: npx
args:
  - "-y"
  - "@modelcontextprotocol/server-filesystem"
  - "/home/user/documents"
  - "/tmp/workspace"
  # NOT: /, /etc, /root
```

### Credential Isolation

Each tool gets only its required secrets:

```yaml
# mcp/github.afs - only gets GITHUB_TOKEN
auth:
  type: env
  GITHUB_TOKEN: "${{ secrets.GITHUB_TOKEN }}"
  # Cannot access DATABASE_URL
```

---

## Tool Chaining & Error Recovery

Agents don't give up on first error - they intelligently retry and recover.

### Automatic Error Recovery

When a tool fails, agents analyze the error and attempt to fix it:

```
User: "Create a GitHub repo called 'my-project'"
Agent: Calls create_repo("my-project")
GitHub: Error - "Repository already exists"
Agent: Analyzes error
Agent: Calls delete_repo("my-project")
Agent: Calls create_repo("my-project")
Success!
```

### Safety Mechanisms

Configure in `formation.afs`:

```yaml
mcp:
  max_tool_iterations: 10     # Max retry attempts per chain
  max_tool_calls: 50          # Total tool calls across all chains
  max_repeated_errors: 3      # Stop if same error repeats
  max_timeout_in_seconds: 120 # 2 minutes max
```

---

## Why This Matters

| Traditional Approach | MUXI Approach |
|---------------------|---------------|
| Build custom integrations | MCP standard protocol |
| Tool schemas in every request | Indexed, loaded on demand |
| Single set of credentials | Per-user credential storage |
| All tools for all agents | Agent-specific restrictions |

The result: **agents that act**, not just talk.

---

## Quick Setup

Create `mcp/web-search.afs`:

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

All agents in the formation automatically have access to MCP servers in `mcp/`. Or create an agent file:

```yaml
# agents/researcher.afs
schema: "1.0.0"
id: researcher
name: Researcher
description: Research specialist

system_message: Research specialist with web access.
```

---

## Learn More

- [Configure tools](../reference/tools.md) - YAML syntax
- [Add Tools Guide](../guides/add-mcp-tools.md) - Step-by-step tutorial
