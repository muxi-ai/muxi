---
title: Agent Formation Schema
description: Complete specification for .afs formation files
---

# Agent Formation Schema

## The .afs file format

Agent Formation Schema (`.afs`) is MUXI's configuration format. Every formation starts with a `formation.afs` file that defines agents, tools, memory, and behavior.

---

## Minimal Formation

The smallest valid `formation.afs`:

```yaml
schema: "1.0.0"
id: my-assistant

llm:
  models:
    text: openai/GPT-5

agents:
  - id: assistant
    role: helpful assistant
```

---

## Top-Level Structure

```yaml
schema: "1.0.0"                    # Required: Schema version
id: my-formation                   # Required: Unique identifier
name: My Formation                 # Optional: Display name
description: A helpful AI assistant # Optional: Description

llm: {...}                         # Required: LLM configuration
agents: [...]                      # Required: At least one agent
memory: {...}                      # Optional: Memory configuration
mcps: [...]                        # Optional: MCP tool servers
overlord: {...}                    # Optional: Orchestration settings
workflow: {...}                    # Optional: Workflow configuration
api_keys: {...}                    # Optional: Formation API keys
```

---

## LLM Configuration

```yaml
llm:
  models:
    text: openai/GPT-5                    # Text generation
    embedding: openai/text-embedding-3-small # Embeddings (for memory/knowledge)

  api_keys:
    openai: ${{ secrets.OPENAI_API_KEY }}
    anthropic: ${{ secrets.ANTHROPIC_API_KEY }}

  defaults:
    temperature: 0.7
    max_tokens: 4096
```

### Supported Providers

| Provider | Model Format | Example |
|----------|--------------|---------|
| OpenAI | `openai/{model}` | `openai/GPT-5` |
| Anthropic | `anthropic/{model}` | `anthropic/claude-sonnet-4-20250514` |
| Google | `google/{model}` | `google/gemini-pro` |
| Ollama | `ollama/{model}` | `ollama/llama3` |

---

## Agents

```yaml
agents:
  - id: assistant                  # Required: Unique identifier
    name: AI Assistant             # Optional: Display name
    role: |                        # Required: System prompt
      You are a helpful assistant.
    model: openai/GPT-5           # Optional: Override default LLM
    temperature: 0.7               # Optional: 0-1
    mcps:                          # Optional: Tools for this agent
      - web-search
      - filesystem
    knowledge:                     # Optional: RAG sources
      enabled: true
      sources:
        - path: knowledge/docs/
          description: Documentation
```

### Agent Fields

| Field | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `id` | string | Yes | - | Unique identifier |
| `name` | string | No | id value | Display name |
| `role` | string | Yes | - | System prompt |
| `model` | string | No | llm.models.text | LLM model |
| `temperature` | float | No | 0.7 | Creativity (0-1) |
| `mcps` | list | No | [] | Tool IDs this agent can use |
| `knowledge` | object | No | disabled | RAG configuration |

---

## Memory

```yaml
memory:
  buffer:
    size: 50                       # Messages to keep
    multiplier: 10                 # Capacity multiplier
    vector_search: true            # Semantic search
    embedding_model: openai/text-embedding-3-small

  working:
    max_memory_mb: 10              # Tool output cache size
    fifo_interval_min: 5           # Cleanup interval

  persistent:
    enabled: true
    provider: postgresql           # sqlite or postgresql
    connection_string: ${{ secrets.POSTGRES_URI }}
    user_isolation: true           # Separate memory per user
```

---

## MCP Servers

```yaml
mcps:
  - id: web-search                 # Required: Unique identifier
    server: "@anthropic/brave-search" # Required: MCP package
    config:                        # Optional: Server config
      api_key: ${{ secrets.BRAVE_API_KEY }}
    env:                           # Optional: Environment variables
      DEBUG: "false"
```

---

## Overlord (Orchestration)

```yaml
overlord:
  auto_decomposition: true         # Break complex tasks into steps
  complexity_threshold: 7.0        # Score for workflow mode

  persona:
    name: Assistant
    style: professional            # professional, casual, technical
    tone: helpful
    traits:
      - knowledgeable
      - efficient

  response:
    format: markdown               # markdown, json, text, html
    streaming: true
```

---

## Workflow

```yaml
workflow:
  requires_approval: true          # Ask before complex tasks
  approval_threshold: 10           # Complexity score for approval
  max_parallel_tasks: 10           # Concurrent task limit
  task_timeout: 300                # Seconds per task
```

---

## API Keys

```yaml
api_keys:
  admin: ${{ secrets.FORMATION_ADMIN_KEY }}
  client: ${{ secrets.FORMATION_CLIENT_KEY }}
```

---

## Include Directive

Reference external files:

```yaml
agents:
  - $include: agents/researcher.afs
  - $include: agents/writer.afs

mcps:
  - $include: mcps/web-search.afs
  - $include: mcps/database.afs
```

---

## Secrets Reference

Use `${{ secrets.KEY }}` syntax:

```yaml
llm:
  api_keys:
    openai: ${{ secrets.OPENAI_API_KEY }}

mcps:
  - id: database
    config:
      connection_string: ${{ secrets.DATABASE_URL }}
    env:
      API_TOKEN: ${{ secrets.API_TOKEN }}
```

---

## Validation

Validate your formation:

```bash
muxi validate
```

Or validate during deploy:

```bash
muxi deploy --validate
```

---

## Complete Example

```yaml
schema: "1.0.0"
id: research-assistant
name: Research Assistant
description: AI research and writing team

llm:
  models:
    text: openai/GPT-5
    embedding: openai/text-embedding-3-small
  api_keys:
    openai: ${{ secrets.OPENAI_API_KEY }}

agents:
  - id: researcher
    name: Research Specialist
    role: Research topics thoroughly with web search
    mcps:
      - web-search

  - id: writer
    name: Content Writer
    role: Write clear, engaging content

mcps:
  - id: web-search
    server: "@anthropic/brave-search"
    config:
      api_key: ${{ secrets.BRAVE_API_KEY }}

memory:
  buffer:
    size: 50
    vector_search: true
  persistent:
    enabled: true
    provider: sqlite

overlord:
  auto_decomposition: true
  complexity_threshold: 7.0
  persona:
    style: professional
  response:
    streaming: true

api_keys:
  admin: ${{ secrets.ADMIN_KEY }}
  client: ${{ secrets.CLIENT_KEY }}
```

---

## Next Steps

[+] [Examples](examples.md) - More complete examples
[+] [Agents](agents.md) - Agent configuration
[+] [Tools](tools.md) - MCP configuration
