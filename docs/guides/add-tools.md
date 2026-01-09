---
title: Add Tools (MCP)
description: Give your agents capabilities with MCP servers
---

# Add Tools (MCP)

## Give your agents superpowers

Tools let agents interact with the real world - search the web, access files, query databases, call APIs. This guide adds web search to your formation.


## What You'll Build

By the end of this guide, your agent will:
1. Detect when you need information
2. Search the web using Brave Search
3. Synthesize results into a response

---

## Prerequisites

- A working formation (`muxi dev` succeeds)
- Brave Search API key ([get one free](https://brave.com/search/api/))

---

[[steps]]

[[step Create the MCP Configuration]]

```bash
cd my-formation
muxi new mcp web-search
```

This creates `mcps/web-search.afs`:

```yaml
# mcps/web-search.afs
id: web-search
server: "@anthropic/brave-search"
config:
  api_key: ${{ secrets.BRAVE_API_KEY }}
```

[[/step]]

[[step Add the Secret]]

```bash
muxi secrets set BRAVE_API_KEY
```

Enter your Brave API key when prompted.

[[/step]]

[[step Reference in Formation]]

Update your `formation.afs`:

```yaml
# formation.afs
schema: "1.0.0"
id: my-assistant

llm:
  models:
    text: openai/gpt-4o
  api_keys:
    openai: ${{ secrets.OPENAI_API_KEY }}

mcps:
  - $include: mcps/web-search.afs

agents:
  - id: assistant
    role: |
      You are a helpful assistant with web search capabilities.
      Use web search when you need current information.
    mcps:
      - web-search
```

[[/step]]

[[step Test It]]

```bash
muxi dev
```

Then try:

```
You: What are the latest AI developments this week?
Assistant: [searches the web] Based on my search, here are the latest developments...
```

[[/step]]

[[/steps]]

---

## How It Works

```mermaid
sequenceDiagram
    participant U as User
    participant A as Agent
    participant T as Web Search Tool
    participant W as Brave API

    U->>A: "What's new in AI?"
    A->>A: Decides search is needed
    A->>T: search("AI news")
    T->>W: API call
    W-->>T: Search results
    T-->>A: Formatted results
    A->>U: Synthesized response
```

> [!TIP]
> The agent decides when to use tools based on the request. You don't need to explicitly ask it to search.

---

## Popular Tools to Add

### File System

```yaml
mcps:
  - id: files
    server: "@anthropic/filesystem"
    config:
      allowed_directories:
        - /home/user/documents
```

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

## Agent-Specific Tools

Give different agents different tools:

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
    role: Research and gather information
    mcps:
      - web-search      # Can search

  - id: developer
    role: Code and database work
    mcps:
      - filesystem      # Can access files
      - database        # Can query database

  - id: writer
    role: Write content
    # No tools - pure writing focus
```

---

## Troubleshooting

[[toggle Tool not appearing]]
1. Check the MCP is referenced in `formation.afs`
2. Check the agent has the MCP in its `mcps` list
3. Restart with `muxi dev`
[[/toggle]]

[[toggle API errors]]
Check your API key:
```bash
muxi secrets get BRAVE_API_KEY
```

Verify it's valid by testing directly:
```bash
curl "https://api.search.brave.com/res/v1/web/search?q=test" \
  -H "X-Subscription-Token: YOUR_KEY"
```
[[/toggle]]

[[toggle Tool timing out]]
Some tools take time. Increase timeout in server config:
```yaml
server:
  write_timeout: 60s
```
[[/toggle]]

---

## Next Steps

[+] [Tools Reference](../reference/tools.md) - All configuration options
[+] [Add Memory](add-memory.md) - Persistent conversations
[+] [Multi-Agent Systems](multi-agent.md) - Specialized agent teams
