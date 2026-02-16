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
# formation.afs
schema: "1.0.0"
id: simple-assistant
description: A simple helpful assistant

llm:
  api_keys:
    openai: "${{ secrets.OPENAI_API_KEY }}"
  models:
    - text: "openai/gpt-4o"

overlord:
  response:
    streaming: true

agents: []
```

```yaml
# agents/assistant.afs
schema: "1.0.0"
id: assistant
name: Assistant
description: A helpful assistant

system_message: You are a helpful, friendly assistant.
```

## Research Assistant

Agent with web search capabilities:

```yaml
# formation.afs
schema: "1.0.0"
id: research-assistant
description: Research assistant with web search

llm:
  api_keys:
    openai: "${{ secrets.OPENAI_API_KEY }}"
  models:
    - text: "openai/gpt-4o"

overlord:
  response:
    streaming: true

agents: []
```

```yaml
# agents/researcher.afs
schema: "1.0.0"
id: researcher
name: Research Specialist
description: Research specialist

system_message: |
  You are a research specialist who:
  - Searches for accurate, up-to-date information
  - Cites sources for all claims
  - Provides balanced, objective analysis
```

With MCP file:

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

## Customer Support

Support bot with knowledge base:

```yaml
# formation.afs
schema: "1.0.0"
id: support-bot
description: Customer support with knowledge base

llm:
  api_keys:
    openai: "${{ secrets.OPENAI_API_KEY }}"
  models:
    - text: "openai/gpt-4o"
    - embedding: "openai/text-embedding-3-large"

memory:
  buffer:
    size: 50
    vector_search: true
  persistent:
    connection_string: "sqlite:///data/memory.db"

overlord:
  soul: |
    You are a professional, empathetic support representative.
  response:
    streaming: true

agents: []
```

```yaml
# agents/support.afs
schema: "1.0.0"
id: support
name: Support Agent
description: Customer support agent

system_message: |
  You are a customer support agent for Acme Inc.
  Be helpful, professional, and empathetic.
  If you don't know something, say so and offer to escalate.

knowledge:
  enabled: true
  sources:
    - path: knowledge/faq/
    - path: knowledge/docs/
    - path: knowledge/troubleshooting/
```

## Multi-Agent Content Team

Specialized agents working together:

```yaml
# formation.afs
schema: "1.0.0"
id: content-team
description: Content creation team with specialized agents

llm:
  api_keys:
    openai: "${{ secrets.OPENAI_API_KEY }}"
  models:
    - text: "openai/gpt-4o"

overlord:
  workflow:
    auto_decomposition: true
    complexity_threshold: 5.0
  response:
    streaming: true

agents: []
```

```yaml
# agents/researcher.afs
schema: "1.0.0"
id: researcher
name: Research Specialist
description: Gathers information from sources

system_message: |
  Research topics thoroughly.
  Gather accurate, well-sourced information.
  Provide raw research for the writer.
```

```yaml
# agents/writer.afs
schema: "1.0.0"
id: writer
name: Content Writer
description: Creates draft content

system_message: |
  Write clear, engaging content.
  Follow the user's style preferences.
  Create drafts based on research.
```

```yaml
# agents/editor.afs
schema: "1.0.0"
id: editor
name: Editor
description: Reviews and improves content

system_message: |
  Review content for accuracy and clarity.
  Check grammar and style.
  Suggest improvements.
```

With MCP file:

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

## DevOps Assistant

System management with GitHub tools:

```yaml
# formation.afs
schema: "1.0.0"
id: devops-assistant
description: DevOps assistant with GitHub access

llm:
  api_keys:
    openai: "${{ secrets.OPENAI_API_KEY }}"
  models:
    - text: "openai/gpt-4o"

overlord:
  workflow:
    plan_approval_threshold: 5  # Require approval for complex ops

agents: []
```

```yaml
# agents/devops.afs
schema: "1.0.0"
id: devops
name: DevOps Specialist
description: DevOps specialist

system_message: |
  You are a DevOps specialist who helps with:
  - Repository management
  - Code review
  - System troubleshooting

  Be careful with destructive operations.
  Always explain what you're doing.
```

With MCP files:

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

```yaml
# mcp/filesystem.afs
schema: "1.0.0"
id: filesystem
type: command
command: npx
args: ["-y", "@modelcontextprotocol/server-filesystem", "./repos"]
```

## Alert Responder

Async processing with webhooks:

```yaml
# formation.afs
schema: "1.0.0"
id: alert-responder
description: Incident response automation

llm:
  api_keys:
    openai: "${{ secrets.OPENAI_API_KEY }}"
  models:
    - text: "openai/gpt-4o"

agents: []
```

```yaml
# agents/responder.afs
schema: "1.0.0"
id: responder
name: Alert Responder
description: Analyzes alerts and incidents

system_message: |
  You analyze alerts and incidents.
  Provide clear summaries and action items.
  Prioritize based on severity.
```

And in formation.afs, add:

```yaml
async:
  threshold_seconds: 10
  webhook_url: "${{ secrets.WEBHOOK_URL }}"

overlord:
  response:
    streaming: false  # Batch responses for alerts
```

With MCP file:

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

agents: []

memory:
  buffer:
    size: 50
    vector_search: true
  persistent:
    connection_string: "${{ secrets.POSTGRES_URL }}"
    user_synopsis:
      enabled: true

overlord:
  soul: You are a professional enterprise assistant.
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

## Next Steps

- [Schema Reference](../reference/formation-schema.md) - Full schema documentation
- [Tools](../concepts/tools-and-mcp.md) - MCP configuration
- [Example Formations](examples/) - Runnable examples
