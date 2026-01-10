---
title: Agent Formation Schema
description: Complete specification for formation.yaml files
---

# Agent Formation Schema

## The formation file format

Every formation starts with a `formation.yaml` (or `formation.afs`) file that defines agents, tools, memory, and behavior.


## Copy-paste starter

The smallest valid `formation.yaml`:

```yaml
schema: "1.0.0"
id: my-assistant
description: A simple assistant

llm:
  api_keys:
    openai: "${{ secrets.OPENAI_API_KEY }}"
  models:
    - text: "openai/gpt-4o"

agents:
  - id: assistant
    role: You are a helpful assistant.
```

---

## Top-Level Structure

```yaml
schema: "1.0.0"                    # Required: Schema version
id: my-formation                   # Required: Unique identifier
description: A helpful AI assistant # Required: Description

llm: {...}                         # LLM configuration
agents: [...]                      # At least one agent
memory: {...}                      # Memory configuration
mcp: {...}                         # MCP tool settings
overlord: {...}                    # Orchestration settings
server: {...}                      # Server configuration
async: {...}                       # Async behavior
scheduler: {...}                   # Scheduled tasks
a2a: {...}                         # Agent-to-agent
user_credentials: {...}            # User credential handling
```

---

## LLM Configuration

```yaml
llm:
  api_keys:
    openai: "${{ secrets.OPENAI_API_KEY }}"
    anthropic: "${{ secrets.ANTHROPIC_API_KEY }}"

  settings:
    temperature: 0.7
    max_tokens: 4096
    timeout_seconds: 30

  models:
    - text: "openai/gpt-4o"
    - embedding: "openai/text-embedding-3-large"
    - vision: "openai/gpt-4o"
      settings:
        max_tokens: 1500
```

### Model Capabilities

| Capability | Purpose | Example |
|------------|---------|---------|
| `text` | Text generation | `openai/gpt-4o` |
| `embedding` | Vector embeddings | `openai/text-embedding-3-large` |
| `vision` | Image analysis | `openai/gpt-4o` |
| `audio` | Transcription | `openai/whisper-1` |
| `streaming` | Progress updates | `openai/gpt-4o-mini` |

### Supported Providers

| Provider | Model Format | Example |
|----------|--------------|---------|
| OpenAI | `openai/{model}` | `openai/gpt-4o` |
| Anthropic | `anthropic/{model}` | `anthropic/claude-sonnet-4-20250514` |
| Google | `google/{model}` | `google/gemini-pro` |
| Ollama | `ollama/{model}` | `ollama/llama3` |

---

## Agents

Agents can be defined inline or in separate files in `agents/`:

```yaml
agents:
  - id: assistant
    name: AI Assistant
    description: General-purpose assistant
    role: |
      You are a helpful assistant.
    mcps:
      - web-search
    knowledge:
      enabled: true
      sources:
        - path: knowledge/docs/
```

### Agent Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | string | Yes | Unique identifier |
| `name` | string | No | Display name |
| `description` | string | No | What the agent does |
| `role` | string | Yes | System prompt |
| `mcps` | list | No | Tool IDs this agent can use |
| `knowledge` | object | No | RAG configuration |
| `llm_models` | list | No | Override formation LLM |

---

## Memory

```yaml
memory:
  buffer:
    size: 50
    multiplier: 10
    vector_search: true

  working:
    max_memory_mb: auto
    mode: local  # or "remote" for FAISSx

  persistent:
    connection_string: "postgres://user:pass@localhost/db"
    # or: "sqlite:///data/memory.db"
    user_synopsis:
      enabled: true
      cache_ttl: 3600
```

---

## MCP Configuration

MCP servers are defined in separate files in `mcp/` directory:

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

```yaml
# mcp/api-service.yaml
schema: "1.0.0"
id: api-service
type: http
endpoint: "https://api.example.com/mcp"
auth:
  type: bearer
  token: "${{ secrets.API_TOKEN }}"
```

Formation-level MCP settings:

```yaml
mcp:
  default_timeout_seconds: 30
  max_tool_iterations: 10
  max_tool_calls: 50
```

---

## Overlord (Orchestration)

```yaml
overlord:
  persona: |
    You are a helpful, professional assistant.

  llm:
    model: "openai/gpt-4o-mini"
    settings:
      temperature: 0.2

  workflow:
    auto_decomposition: true
    complexity_threshold: 7.0
    plan_approval_threshold: 8
    max_parallel_tasks: 5

  response:
    format: markdown
    streaming: true

  clarification:
    style: conversational
    max_rounds:
      direct: 3
      brainstorm: 10
```

---

## Server Configuration

```yaml
server:
  host: "0.0.0.0"
  port: 8001

  api_keys:
    admin_key: "${{ secrets.ADMIN_KEY }}"
    client_key: "${{ secrets.CLIENT_KEY }}"
```

---

## Async Configuration

```yaml
async:
  threshold_seconds: 30
  enable_estimation: true
  webhook_url: "https://myapp.com/webhooks/muxi"
  webhook_retries: 3
```

---

## Include Directive

Reference external files:

```yaml
agents:
  - $include: agents/researcher.yaml
  - $include: agents/writer.yaml
```

---

## Secrets Reference

Use `${{ secrets.KEY }}` syntax:

```yaml
llm:
  api_keys:
    openai: "${{ secrets.OPENAI_API_KEY }}"

# In mcp/*.yaml files:
auth:
  type: env
  API_KEY: "${{ secrets.API_KEY }}"
```

---

## Validation

Validate your formation:

```bash
muxi validate
```

---

## Complete Example

```yaml
schema: "1.0.0"
id: research-assistant
description: AI research and writing team
version: "1.0.0"

llm:
  api_keys:
    openai: "${{ secrets.OPENAI_API_KEY }}"
  models:
    - text: "openai/gpt-4o"
    - embedding: "openai/text-embedding-3-large"

agents:
  - id: researcher
    name: Research Specialist
    description: Gathers information from web sources
    role: |
      Research topics thoroughly with web search.
      Always cite your sources.
    mcps:
      - web-search

  - id: writer
    name: Content Writer
    description: Creates polished content
    role: Write clear, engaging content.

memory:
  buffer:
    size: 50
    vector_search: true
  persistent:
    connection_string: "sqlite:///data/memory.db"

overlord:
  persona: You are a professional research assistant.
  workflow:
    auto_decomposition: true
    complexity_threshold: 7.0

mcp:
  default_timeout_seconds: 30

server:
  api_keys:
    admin_key: "${{ secrets.ADMIN_KEY }}"
    client_key: "${{ secrets.CLIENT_KEY }}"
```

With MCP server file:

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

## Next Steps

- [Examples](examples.md) - More complete examples
- [Agents](agents.md) - Agent configuration
- [Tools](tools.md) - MCP configuration
