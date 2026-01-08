---
title: Tools & MCP
description: How agents interact with the world through MCP tools
---
# Tools & MCP

## How agents interact with the world through MCP tools


MUXI uses the **Model Context Protocol (MCP)** to connect agents with tools - web search, databases, file systems, APIs, and more. 1,000+ tools available, with intelligent loading that keeps token usage low.

---

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

### Official MCP Servers

| Tool | What It Does |
|------|--------------|
| `@anthropic/brave-search` | Web search |
| `@anthropic/filesystem` | Read/write files |
| `@anthropic/github` | GitHub API |
| `@anthropic/postgres` | PostgreSQL queries |
| `@anthropic/sqlite` | SQLite queries |
| `@anthropic/slack` | Slack integration |
| `@anthropic/google-drive` | Google Drive access |

### 1,000+ Community Tools

Browse at [registry.muxi.org/mcps](https://registry.muxi.org/mcps) - Stripe, Notion, Jira, Salesforce, and more.

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

This is why MUXI can offer 1,000+ MCP tools while keeping your costs low and responses fast.

---

## Multi-User Tool Access

Each user can store their own credentials:

```
User A: GitHub token → their repos
User B: GitHub token → their repos

Same formation, personalized access.
```

Agents automatically use the requesting user's credentials:

```yaml
mcps:
  - id: github
    server: "@anthropic/github"
    env:
      GITHUB_TOKEN: ${{ user.secrets.GITHUB_TOKEN }}
```

Credentials encrypted at rest. Complete isolation between users.

---

## Natural Language Scheduling

Agents understand time expressions:

```
User: "Check my email every hour"
User: "Remind me tomorrow at 2pm"
User: "Run this report every Monday"
```

No cron syntax required. MUXI parses natural language and creates scheduled tasks:

```yaml
# Equivalent to what MUXI creates:
triggers:
  - schedule: "0 * * * *"  # every hour
    action: check_email
```

Timezone-aware, recurring or one-time.

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
    mcps: [web-search]      # Can only search

  - id: developer
    mcps: [filesystem, database]  # Can access files and DB

  - id: writer
    # No tools - pure writing
```

Right tools for right agents. No accidental file access from the writer.

---

## Tool Security

### Path Restrictions

```yaml
mcps:
  - id: filesystem
    config:
      allowed_directories:
        - /home/user/documents
        - /tmp/workspace
        # NOT: /, /etc, /root
```

### Credential Isolation

Each tool gets only its required secrets:

```yaml
mcps:
  - id: github
    env:
      GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      # Cannot access DATABASE_URL
```

---

## Why This Matters

| Traditional Approach | MUXI Approach |
|---------------------|---------------|
| Build custom integrations | MCP standard protocol |
| Tool schemas in every request | Indexed, loaded on demand |
| Single set of credentials | Per-user credential storage |
| Manual scheduling code | Natural language scheduling |
| All tools for all agents | Agent-specific restrictions |

The result: **agents that act**, not just talk.

---

## Quick Setup

```yaml
mcps:
  - id: web-search
    server: "@anthropic/brave-search"
    config:
      api_key: ${{ secrets.BRAVE_API_KEY }}

agents:
  - id: researcher
    role: Research specialist
    mcps:
      - web-search
```

---

## Learn More

- [Configure tools](../reference/tools.md) - YAML syntax
- [Add Tools Guide](../guides/add-tools.md) - Step-by-step tutorial
