---
title: Formation Examples
description: Complete examples for common use cases
---

# Formation Examples

## Real-world formations you can use

Copy these examples as starting points for your own formations.

---

## Simple Assistant

The minimal production-ready assistant:

```yaml
schema: "1.0.0"
id: simple-assistant
name: Simple Assistant

llm:
  models:
    text: openai/gpt-4o
  api_keys:
    openai: ${{ secrets.OPENAI_API_KEY }}

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
schema: "1.0.0"
id: research-assistant
name: Research Assistant

llm:
  models:
    text: openai/gpt-4o
  api_keys:
    openai: ${{ secrets.OPENAI_API_KEY }}

agents:
  - id: researcher
    role: |
      You are a research specialist who:
      - Searches for accurate, up-to-date information
      - Cites sources for all claims
      - Provides balanced, objective analysis
    mcps:
      - web-search

mcps:
  - id: web-search
    server: "@anthropic/brave-search"
    config:
      api_key: ${{ secrets.BRAVE_API_KEY }}

overlord:
  response:
    streaming: true
```

---

## Customer Support

Support bot with knowledge base:

```yaml
schema: "1.0.0"
id: support-bot
name: Customer Support

llm:
  models:
    text: openai/gpt-4o
    embedding: openai/text-embedding-3-small
  api_keys:
    openai: ${{ secrets.OPENAI_API_KEY }}

agents:
  - id: support
    role: |
      You are a customer support agent for Acme Inc.
      Be helpful, professional, and empathetic.
      If you don't know something, say so and offer to escalate.
    knowledge:
      enabled: true
      sources:
        - path: knowledge/faq/
          description: Frequently asked questions
        - path: knowledge/docs/
          description: Product documentation
        - path: knowledge/troubleshooting/
          description: Troubleshooting guides

memory:
  buffer:
    size: 50
    vector_search: true
  persistent:
    enabled: true
    provider: sqlite

overlord:
  persona:
    style: professional
    tone: empathetic
  response:
    streaming: true
```

---

## Multi-Agent Content Team

Specialized agents working together:

```yaml
schema: "1.0.0"
id: content-team
name: Content Creation Team

llm:
  models:
    text: openai/gpt-4o
  api_keys:
    openai: ${{ secrets.OPENAI_API_KEY }}

agents:
  - id: researcher
    name: Research Specialist
    role: |
      Research topics thoroughly.
      Gather accurate, well-sourced information.
      Provide raw research for the writer.
    mcps:
      - web-search

  - id: writer
    name: Content Writer
    role: |
      Write clear, engaging content.
      Follow the user's style preferences.
      Create drafts based on research.

  - id: editor
    name: Editor
    role: |
      Review content for accuracy and clarity.
      Check grammar and style.
      Suggest improvements.

mcps:
  - id: web-search
    server: "@anthropic/brave-search"
    config:
      api_key: ${{ secrets.BRAVE_API_KEY }}

overlord:
  auto_decomposition: true
  complexity_threshold: 5.0
  response:
    streaming: true
```

---

## DevOps Assistant

System management with tools:

```yaml
schema: "1.0.0"
id: devops-assistant
name: DevOps Assistant

llm:
  models:
    text: openai/gpt-4o
  api_keys:
    openai: ${{ secrets.OPENAI_API_KEY }}

agents:
  - id: devops
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

mcps:
  - id: github
    server: "@anthropic/github"
    env:
      GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}

  - id: filesystem
    server: "@anthropic/filesystem"
    config:
      allowed_directories:
        - /home/deploy/apps
        - /var/log

overlord:
  response:
    streaming: true
```

---

## Enterprise Multi-Tenant

Production-ready with PostgreSQL and user isolation:

```yaml
schema: "1.0.0"
id: enterprise-assistant
name: Enterprise Assistant

llm:
  models:
    text: openai/gpt-4o
    embedding: openai/text-embedding-3-small
  api_keys:
    openai: ${{ secrets.OPENAI_API_KEY }}

agents:
  - id: assistant
    role: |
      You are an enterprise assistant.
      Maintain professionalism and confidentiality.
      Never share information between users.

memory:
  buffer:
    size: 100
    vector_search: true
  persistent:
    enabled: true
    provider: postgresql
    connection_string: ${{ secrets.POSTGRES_URI }}
    user_isolation: true

overlord:
  persona:
    style: professional
    tone: formal
  response:
    streaming: true

api_keys:
  admin: ${{ secrets.ADMIN_KEY }}
  client: ${{ secrets.CLIENT_KEY }}
```

---

## Webhook-Driven Bot

Formation with triggers for external events:

```yaml
schema: "1.0.0"
id: alert-responder
name: Alert Responder

llm:
  models:
    text: openai/gpt-4o
  api_keys:
    openai: ${{ secrets.OPENAI_API_KEY }}

agents:
  - id: responder
    role: |
      You analyze alerts and incidents.
      Provide clear summaries and action items.
      Prioritize based on severity.
    mcps:
      - database

mcps:
  - id: database
    server: "@anthropic/postgres"
    config:
      connection_string: ${{ secrets.DATABASE_URL }}
```

With trigger template `triggers/alert.md`:

```markdown
---
name: Alert Handler
tags: [alert, incident]
triggers:
  keywords: [new alert, incident report]
---

New alert from ${{ data.source }}:

**Severity**: ${{ data.severity }}
**Message**: ${{ data.message }}
**Time**: ${{ data.timestamp }}

Analyze this alert and provide:
1. Brief summary
2. Potential root causes
3. Recommended actions
4. Priority level (P0-P3)
```

---

## Next Steps

[+] [Schema Reference](schema.md) - All configuration options
[+] [Registry](../registry/README.md) - Find more formations
[+] [Quickstart](../quickstart.md) - Get started with examples
