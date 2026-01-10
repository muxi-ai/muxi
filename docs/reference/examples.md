---
title: Formation Examples
description: Complete examples for common use cases
---

# Formation Examples

## Real-world formations you can use

Copy these examples as starting points for your own formations.


## Simple Assistant

The minimal production-ready assistant:

```yaml
schema: "1.0.0"
id: simple-assistant
description: A simple helpful assistant

llm:
  api_keys:
    openai: "${{ secrets.OPENAI_API_KEY }}"
  models:
    - text: "openai/gpt-4o"

agents:
  - id: assistant
    role: You are a helpful, friendly assistant.

overlord:
  response:
    streaming: true
```

---

## Research Assistant

Agent with web search capabilities:

```yaml
# formation.yaml
schema: "1.0.0"
id: research-assistant
description: Research assistant with web search

llm:
  api_keys:
    openai: "${{ secrets.OPENAI_API_KEY }}"
  models:
    - text: "openai/gpt-4o"

agents:
  - id: researcher
    description: Research specialist
    role: |
      You are a research specialist who:
      - Searches for accurate, up-to-date information
      - Cites sources for all claims
      - Provides balanced, objective analysis
    mcps:
      - web-search

overlord:
  response:
    streaming: true
```

With MCP file:

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

---

## Customer Support

Support bot with knowledge base:

```yaml
schema: "1.0.0"
id: support-bot
description: Customer support with knowledge base

llm:
  api_keys:
    openai: "${{ secrets.OPENAI_API_KEY }}"
  models:
    - text: "openai/gpt-4o"
    - embedding: "openai/text-embedding-3-large"

agents:
  - id: support
    description: Customer support agent
    role: |
      You are a customer support agent for Acme Inc.
      Be helpful, professional, and empathetic.
      If you don't know something, say so and offer to escalate.
    knowledge:
      enabled: true
      sources:
        - path: knowledge/faq/
        - path: knowledge/docs/
        - path: knowledge/troubleshooting/

memory:
  buffer:
    size: 50
    vector_search: true
  persistent:
    connection_string: "sqlite:///data/memory.db"

overlord:
  persona: |
    You are a professional, empathetic support representative.
  response:
    streaming: true
```

---

## Multi-Agent Content Team

Specialized agents working together:

```yaml
# formation.yaml
schema: "1.0.0"
id: content-team
description: Content creation team with specialized agents

llm:
  api_keys:
    openai: "${{ secrets.OPENAI_API_KEY }}"
  models:
    - text: "openai/gpt-4o"

agents:
  - id: researcher
    name: Research Specialist
    description: Gathers information from sources
    role: |
      Research topics thoroughly.
      Gather accurate, well-sourced information.
      Provide raw research for the writer.
    mcps:
      - web-search

  - id: writer
    name: Content Writer
    description: Creates draft content
    role: |
      Write clear, engaging content.
      Follow the user's style preferences.
      Create drafts based on research.

  - id: editor
    name: Editor
    description: Reviews and improves content
    role: |
      Review content for accuracy and clarity.
      Check grammar and style.
      Suggest improvements.

overlord:
  workflow:
    auto_decomposition: true
    complexity_threshold: 5.0
  response:
    streaming: true
```

With MCP file:

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

---

## DevOps Assistant

System management with GitHub tools:

```yaml
# formation.yaml
schema: "1.0.0"
id: devops-assistant
description: DevOps assistant with GitHub access

llm:
  api_keys:
    openai: "${{ secrets.OPENAI_API_KEY }}"
  models:
    - text: "openai/gpt-4o"

agents:
  - id: devops
    description: DevOps specialist
    role: |
      You are a DevOps specialist who helps with:
      - Repository management
      - Code review
      - System troubleshooting

      Be careful with destructive operations.
      Always explain what you're doing.
    mcps:
      - github
      - filesystem

overlord:
  workflow:
    plan_approval_threshold: 5  # Require approval for complex ops
```

With MCP files:

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

```yaml
# mcp/filesystem.yaml
schema: "1.0.0"
id: filesystem
type: command
command: npx
args: ["-y", "@modelcontextprotocol/server-filesystem", "./repos"]
```

---

## Alert Responder

Async processing with webhooks:

```yaml
# formation.yaml
schema: "1.0.0"
id: alert-responder
description: Incident response automation

llm:
  api_keys:
    openai: "${{ secrets.OPENAI_API_KEY }}"
  models:
    - text: "openai/gpt-4o"

agents:
  - id: responder
    description: Analyzes alerts and incidents
    role: |
      You analyze alerts and incidents.
      Provide clear summaries and action items.
      Prioritize based on severity.
    mcps:
      - database

async:
  threshold_seconds: 10
  webhook_url: "${{ secrets.WEBHOOK_URL }}"

overlord:
  response:
    streaming: false  # Batch responses for alerts
```

With MCP file:

```yaml
# mcp/database.yaml
schema: "1.0.0"
id: database
type: command
command: npx
args: ["-y", "@modelcontextprotocol/server-postgres"]
auth:
  type: env
  DATABASE_URL: "${{ secrets.DATABASE_URL }}"
```

---

## Enterprise Ready

Full production setup:

```yaml
schema: "1.0.0"
id: enterprise-assistant
description: Production-ready enterprise assistant
version: "1.0.0"

llm:
  api_keys:
    openai: "${{ secrets.OPENAI_API_KEY }}"
  settings:
    temperature: 0.7
    max_tokens: 4096
  models:
    - text: "openai/gpt-4o"
    - embedding: "openai/text-embedding-3-large"

agents:
  - id: assistant
    name: Enterprise Assistant
    description: General-purpose enterprise assistant
    role: |
      You are an enterprise assistant.
      Be professional and thorough.

memory:
  buffer:
    size: 50
    vector_search: true
  persistent:
    connection_string: "${{ secrets.POSTGRES_URL }}"
    user_synopsis:
      enabled: true

overlord:
  persona: You are a professional enterprise assistant.
  workflow:
    auto_decomposition: true
    complexity_threshold: 7.0
  response:
    format: markdown
    streaming: true
  clarification:
    style: formal

server:
  api_keys:
    admin_key: "${{ secrets.ADMIN_KEY }}"
    client_key: "${{ secrets.CLIENT_KEY }}"

logging:
  system:
    level: info
  conversation:
    enabled: true
```

---

## Next Steps

- [Schema Reference](schema.md) - Full schema documentation
- [Tools](tools.md) - MCP configuration
- [Example Formations](../examples/) - Runnable examples
