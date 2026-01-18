---
title: Schema Guide
description: Complete configuration reference for Agent Formation Schema
---

# Schema Guide

## Complete configuration reference for all .afs files

This guide documents every configuration option available in Agent Formation Schema files. Use it as a reference when building production formations.

> [!TIP]
> **Start minimal.** Most fields have sensible defaults. Add configuration only when you need to customize behavior.

## Schema Version

All configuration files **must** include:

```yaml
schema: "1.0.0"
```

---

## Formation Schema (`formation.afs`)

### Basic Information

```yaml
schema: "1.0.0"
id: "my-formation"
description: "What this formation does"

# Optional metadata
author: "Your Name <email@example.com>"
url: "https://github.com/you/formation"
license: "MIT"
version: "1.0.0"
```

| Field | Required | Default | Description |
|-------|----------|---------|-------------|
| `schema` | Yes | - | Schema version (`"1.0.0"`) |
| `id` | Yes | - | Unique identifier |
| `description` | Yes | - | What this formation does |
| `author` | No | - | Author name and email |
| `url` | No | - | Documentation or repo URL |
| `license` | No | `"Unlicense"` | License type |
| `version` | No | - | Formation version |

[More: Formation Schema Reference →](formation-schema.md)

---

### Server Configuration

```yaml
server:
  host: "0.0.0.0"
  port: 3000
  access_log: false
  api_keys:
    admin_key: "${{ secrets.ADMIN_KEY }}"
    client_key: "${{ secrets.CLIENT_KEY }}"
```

| Field | Default | Description |
|-------|---------|-------------|
| `server.host` | `"0.0.0.0"` | Bind address |
| `server.port` | `3000` | Port number |
| `server.access_log` | `false` | Enable HTTP access logging |
| `server.api_keys.admin_key` | Auto-generated | Admin API key |
| `server.api_keys.client_key` | Auto-generated | Client API key |

---

### LLM Configuration

```yaml
llm:
  api_keys:
    openai: "${{ secrets.OPENAI_API_KEY }}"
    anthropic: "${{ secrets.ANTHROPIC_API_KEY }}"

  settings:
    temperature: 0.7
    max_tokens: 4096
    timeout_seconds: 60
    max_retries: 3
    fallback_model: "openai/gpt-4o-mini"

  models:
    - text: "openai/gpt-4o"
    - vision: "openai/gpt-4o"
    - embedding: "openai/text-embedding-3-small"
    - audio: "openai/whisper-1"
```

#### LLM Settings

| Field | Default | Description |
|-------|---------|-------------|
| `temperature` | `0.7` | Randomness (0.0-2.0) |
| `max_tokens` | `4096` | Max response tokens |
| `timeout_seconds` | `60` | Request timeout |
| `max_retries` | `3` | Retry attempts |
| `fallback_model` | - | Model to use if primary fails |

#### Model Capabilities

| Capability | Description |
|------------|-------------|
| `text` | Text generation (required) |
| `vision` | Image understanding |
| `embedding` | Vector embeddings |
| `audio` | Speech-to-text |
| `video` | Video understanding |
| `documents` | Document processing |

---

### Overlord Configuration

```yaml
overlord:
  persona: |
    You are a helpful assistant. You communicate clearly
    and concisely. You never make up information.

  llm:
    text: "anthropic/claude-sonnet-4-20250514"
    settings:
      temperature: 0.3

  routing:
    enabled: true
    settings:
      temperature: 0.1
      max_tokens: 500

  workflow:
    enabled: true
    auto_decomposition: true
    complexity_threshold: 5.0
    plan_approval_threshold: 8.0

  clarification:
    enabled: true
    max_questions: 3
    question_style: "conversational"

  response:
    streaming: true
    format: "markdown"
```

#### Workflow Settings

| Field | Default | Description |
|-------|---------|-------------|
| `auto_decomposition` | `true` | Auto-split complex tasks |
| `complexity_threshold` | `5.0` | Min complexity for decomposition |
| `plan_approval_threshold` | `8.0` | Min complexity for human approval |

#### Clarification Settings

| Field | Default | Description |
|-------|---------|-------------|
| `enabled` | `true` | Allow clarifying questions |
| `max_questions` | `3` | Max questions per interaction |
| `question_style` | `"conversational"` | Style: `conversational`, `formal`, `bullet_points` |

[More: Persona Reference →](persona.md) | [Workflows Reference →](workflows.md)

---

### Memory Configuration

```yaml
memory:
  buffer:
    size: 50
    multiplier: 2
    vector_search: true

  working:
    enabled: true
    max_entries: 1000
    ttl_hours: 168  # 7 days

  persistent:
    enabled: true
    provider: "sqlite"  # or "postgres"
    connection_string: "${{ secrets.DATABASE_URL }}"
```

| Layer | Purpose | Default |
|-------|---------|---------|
| `buffer` | Recent messages | 50 messages |
| `working` | Active session facts | 1000 entries, 7 days TTL |
| `persistent` | Long-term storage | SQLite |

[More: Memory Reference →](memory.md)

---

### MCP Configuration

```yaml
mcp:
  default_timeout_seconds: 30
  max_tool_iterations: 10
  max_tool_calls: 50
  max_repeated_errors: 3
  max_timeout_in_seconds: 120
```

| Field | Default | Description |
|-------|---------|-------------|
| `default_timeout_seconds` | `30` | Default tool timeout |
| `max_tool_iterations` | `10` | Max correction attempts |
| `max_tool_calls` | `50` | Max total tool calls |
| `max_repeated_errors` | `3` | Stop after N same errors |
| `max_timeout_in_seconds` | `120` | Total chain timeout |

[More: Tools (MCP) Reference →](tools.md)

---

### Async Configuration

```yaml
async:
  enabled: true
  max_workers: 10
  default_timeout_seconds: 300
  notification_webhook: "https://your-app.com/webhook"
```

---

### Scheduler Configuration

```yaml
scheduler:
  enabled: true
  timezone: "UTC"
  max_jobs_per_user: 100
```

---

### A2A Configuration

```yaml
a2a:
  enabled: true
  outbound:
    enabled: true
    timeout_seconds: 30
    max_retries: 3
  inbound:
    enabled: true
    require_auth: true
```

[More: A2A Services Reference →](a2a.md)

---

### Input Limits

```yaml
input_limits:
  max_message_length: 100000       # 100KB
  max_file_size_bytes: 52428800    # 50MB
  max_memory_entry_size: 10000     # 10KB
  max_tool_output_size: 1048576    # 1MB
  max_batch_items: 100
```

---

### Logging Configuration

```yaml
logging:
  level: "info"
  destinations:
    console:
      enabled: true
    file:
      enabled: true
      path: "logs/formation.log"
    webhook:
      enabled: false
      url: "https://logs.example.com"
```

---

## Agent Schema (`agents/*.afs`)

### Basic Information

```yaml
schema: "1.0.0"
id: "researcher"
name: "Research Specialist"
description: "Gathers accurate information from multiple sources"

system_message: |
  You are a research specialist. Your job is to:
  - Find accurate, well-sourced information
  - Cite your sources
  - Admit when you're uncertain
```

| Field | Required | Description |
|-------|----------|-------------|
| `schema` | Yes | Schema version |
| `id` | Yes | Unique identifier |
| `name` | No | Display name |
| `description` | Yes | What this agent does (used for routing) |
| `system_message` | No | System prompt |

[More: Agents Reference →](agents.md)

---

### Role & Specialization

```yaml
role: "specialist"  # specialist, generalist, coordinator, researcher, executor

specialization:
  domain: "customer-support"
  subdomain: "technical-support"
  expertise_level: "expert"  # junior, intermediate, senior, expert
  keywords: ["help", "issue", "troubleshoot", "debug"]
```

> [!IMPORTANT]
> The `description`, `role`, and `specialization.keywords` fields are used by the Overlord to route requests. Be specific!

---

### System Behavior

```yaml
system_behavior:
  constraints:
    - "Only answer questions about the product"
    - "Never share internal pricing"
  capabilities:
    - "Product knowledge"
    - "Troubleshooting"
  interaction_style: "friendly"  # formal, casual, technical, friendly, professional
  response_length: "moderate"    # concise, moderate, detailed, comprehensive
  error_handling: "user-friendly" # graceful, explicit, technical, user-friendly
```

---

### Agent LLM Overrides

```yaml
llm:
  settings:
    temperature: 0.3
    max_tokens: 2000
  models:
    - text: "anthropic/claude-sonnet-4-20250514"
```

---

### Agent Knowledge

```yaml
knowledge:
  files:
    - "knowledge/product-faq.md"
    - "knowledge/troubleshooting.md"
  directories:
    - "knowledge/docs/"
  auto_update: true
  embedding_model: "openai/text-embedding-3-small"
  chunk_size: 1000
  chunk_overlap: 100
```

[More: Knowledge Reference →](knowledge.md)

---

### Agent MCP Servers

```yaml
mcp_servers:
  - id: web-search
    description: Brave web search
    type: command
    command: npx
    args: ["-y", "@modelcontextprotocol/server-brave-search"]
    auth:
      type: env
      BRAVE_API_KEY: "${{ secrets.BRAVE_API_KEY }}"
```

---

### A2A Settings

```yaml
a2a_enabled: true
enable_direct_delegation: true
delegation_strategy: "automatic"  # automatic, ask_user, never
```

---

### Rate Limiting

```yaml
rate_limiting:
  requests_per_minute: 60
  requests_per_hour: 1000
  requests_per_day: 10000
  burst_limit: 10

timeout:
  default: 300
  max: 600
```

---

## MCP Server Schema (`mcp/*.afs`)

### Command-Based Server

```yaml
schema: "1.0.0"
id: "web-search"
description: "Brave web search"
type: command

command: npx
args: ["-y", "@modelcontextprotocol/server-brave-search"]
cwd: "./"
timeout_seconds: 30
retry_attempts: 3

env:
  DEBUG: "true"

auth:
  type: env
  BRAVE_API_KEY: "${{ secrets.BRAVE_API_KEY }}"
```

### HTTP-Based Server

```yaml
schema: "1.0.0"
id: "remote-tools"
description: "Remote MCP server"
type: http

endpoint: "https://mcp.example.com/tools"
method: POST
timeout_seconds: 30
retry_attempts: 3

headers:
  X-Custom-Header: "value"

auth:
  type: bearer
  token: "${{ secrets.MCP_TOKEN }}"

health_check:
  enabled: true
  endpoint: "/health"
  interval_seconds: 60
  timeout_seconds: 5
```

### Authentication Types

| Type | Fields | Description |
|------|--------|-------------|
| `env` | Key-value pairs | Environment variables |
| `bearer` | `token` | Bearer token |
| `basic` | `username`, `password` | Basic auth |
| `api_key` | `header`, `key` | API key header |

[More: Tools (MCP) Reference →](tools.md)

---

## A2A Service Schema (`a2a/*.afs`)

```yaml
schema: "1.0.0"
id: "analytics-engine"
name: "Analytics Engine"
description: "External analytics service"

endpoint: "https://analytics.example.com"
protocol: https
version: "1.0.0"

timeout_seconds: 30
retry_attempts: 3
retry_delay_seconds: 1
retry_backoff_multiplier: 2.0

auth:
  type: bearer
  token: "${{ secrets.ANALYTICS_TOKEN }}"

rate_limiting:
  requests_per_minute: 100
  burst_limit: 20

health_check:
  enabled: true
  endpoint: "/health"
  interval_seconds: 60

circuit_breaker:
  enabled: true
  failure_threshold: 5
  success_threshold: 2
  timeout_seconds: 60

capabilities:
  agents: ["analytics-agent", "reporting-agent"]
  features: ["analysis", "visualization"]
  streaming: true
  async: true
```

[More: A2A Services Reference →](a2a.md)

---

## Secrets Interpolation

### Formation Secrets

```yaml
api_key: "${{ secrets.OPENAI_API_KEY }}"
```

### User Credentials

```yaml
token: "${{ user.credentials.GITHUB_TOKEN }}"
```

> [!NOTE]
> **Secrets** are formation-wide, loaded at startup.
> **User credentials** are per-user, loaded on-demand.

[More: Secrets Reference →](secrets.md)

---

## Override Hierarchy

Configuration is resolved in this order (highest priority first):

1. **Agent-specific settings** (`agents/*.afs`)
2. **Overlord settings** (`formation.afs` → `overlord:`)
3. **Formation defaults** (`formation.afs` → `llm:`)

**Example:**
```yaml
# formation.afs
llm:
  settings:
    temperature: 0.7  # Default for all agents

# agents/creative.afs
llm:
  settings:
    temperature: 1.0  # Override for this agent only
```

---

## Validation Requirements

### Formation
- Must have `schema`, `id`, `description`
- Must have at least one LLM model
- All secret references must use valid syntax

### Agent
- Must have `schema`, `id`, `description`
- MCP server references must exist

### MCP
- Must have `schema`, `id`, `type`
- Command servers must have `command`
- HTTP servers must have `endpoint`

---

## Learn More

- [Formation Overview](README.md) - Quick start
- [Agents Reference](agents.md) - Agent configuration
- [Tools Reference](tools.md) - MCP configuration
- [Examples](examples.md) - Real-world formations
