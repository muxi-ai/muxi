# Formation Schema Reference

Complete schema reference for all `.afs` configuration files.

## Formation Schema (`formation.afs`)

### Required Fields

```yaml
schema: "1.0.0"                    # Required: schema version
id: my-formation                   # Required: unique identifier
description: "What this does"      # Required
```

### Optional Metadata

```yaml
author: "Name <email>"
url: "https://example.com"
license: "MIT"                     # Default: Unlicense
version: "1.0.0"
```

### Runtime Version Pinning (MUXI-specific)

```yaml
runtime: "1.2"      # Latest 1.2.x
runtime: "1.2.3"    # Exact version
runtime: ""          # Latest (default, or omit field)
```

### Server Configuration

```yaml
server:
  host: "0.0.0.0"                  # Default: 0.0.0.0
  port: 8271                       # Default: 8271
  access_log: false                # Default: false
  api_keys:
    admin_key: "${{ secrets.FORMATION_ADMIN_API_KEY }}"   # Auto-generated if not set
    client_key: "${{ secrets.FORMATION_CLIENT_API_KEY }}"  # Auto-generated if not set
```

### Input Limits

```yaml
input_limits:
  max_message_length: 100000        # 100KB chat messages
  max_file_size_bytes: 52428800     # 50MB file uploads
  max_memory_entry_size: 10000      # 10KB memory entries
  max_tool_output_size: 1048576     # 1MB tool outputs
  max_batch_items: 100
```

### LLM Configuration

```yaml
llm:
  api_keys:
    openai: "${{ secrets.OPENAI_API_KEY }}"
    anthropic: "${{ secrets.ANTHROPIC_API_KEY }}"

  settings:                         # Global defaults for all models
    temperature: 0.7
    max_tokens: 4096
    timeout_seconds: 30
    max_retries: 3
    fallback_model: "anthropic/claude-3.5-sonnet"
    caching:
      enabled: true
      max_entries: 10000
      p: 0.98                       # Similarity threshold
      stream_chunk_strategy: "sentences"
      ttl: 86400                    # 24 hours

  models:
    - text: "openai/gpt-4o"
      api_key: "${{ secrets.CUSTOM_KEY }}"   # Optional per-model override
      settings:                              # Optional per-capability override
        temperature: 0.7
        max_tokens: 4096
    - vision: "openai/gpt-4o"
      settings:
        max_tokens: 1500
        image:
          max_size_mb: 5
          preprocessing:
            resize: true
            max_width: 1024
            max_height: 1024
    - embedding: "openai/text-embedding-3-large"
    - audio: "openai/whisper-1"
      settings:
        max_size_mb: 10
        language: "auto"
    - video: "google/gemini-pro-vision"
      settings:
        max_size_mb: 100
        max_duration_seconds: 300
    - documents: "openai/gpt-4o"
      settings:
        max_size_mb: 20
        extraction:
          chunk_size: 1000
          overlap: 100
          strategy: "adaptive"
    - streaming: "openai/gpt-4o-mini"
```

#### Supported Providers

| Provider | Format | Example |
|----------|--------|---------|
| OpenAI | `openai/{model}` | `openai/gpt-4o` |
| Anthropic | `anthropic/{model}` | `anthropic/claude-sonnet-4-20250514` |
| Google | `google/{model}` | `google/gemini-pro` |
| Ollama | `ollama/{model}` | `ollama/llama3` |
| LlamaCpp | `llama_cpp/{model}` | `llama_cpp/phi-3-mini-4k-instruct` |

#### Model Capabilities

| Capability | Purpose |
|------------|---------|
| `text` | Text generation (primary) |
| `vision` | Image understanding |
| `embedding` | Vector embeddings |
| `audio` | Speech-to-text |
| `video` | Video understanding |
| `documents` | Document processing |
| `streaming` | Fast streaming model |

### Overlord Configuration

```yaml
overlord:
  soul: |
    You are a helpful, professional assistant.

  llm:
    model: "openai/gpt-4o-mini"
    api_key: "${{ secrets.OVERLORD_LLM_API_KEY }}"
    max_extraction_tokens: 500
    settings:
      temperature: 0.2
      max_tokens: 2000
      timeout_seconds: 45

  caching:
    enabled: true
    ttl: 3600

  response:
    format: "markdown"              # markdown, text, html, json
    widgets: true                   # Interactive UI elements
    streaming: false
    progress: true

  workflow:
    auto_decomposition: true
    plan_approval_threshold: 7      # 1-10, show plan for approval above this
    complexity_method: "heuristic"  # heuristic, llm, custom, hybrid
    complexity_threshold: 7.0
    routing_strategy: "capability_based"  # capability_based, load_balanced, priority_based, round_robin
    enable_agent_affinity: true
    error_recovery: "retry_with_backoff"  # fail_fast, retry_with_backoff, skip_and_continue, manual_intervention
    parallel_execution: true
    max_parallel_tasks: 5
    partial_results: true
    max_timeout_seconds: 7200
    timeouts:
      task_timeout: 300
      workflow_timeout: 3600
      enable_adaptive_timeout: true
    retry:
      max_attempts: 3
      initial_delay: 1.0
      max_delay: 60.0
      backoff_factor: 2.0
      retry_on_errors: ["timeout", "rate_limit", "temporary_failure"]

  clarification:
    style: "conversational"         # conversational, formal, brief
    max_rounds:
      direct: 3
      brainstorm: 10
      planning: 7
      execution: 3
      other: 3
```

### Async Configuration

```yaml
async:
  threshold_seconds: 30             # Auto-switch to async after this
  enable_estimation: true
  webhook_url: "https://myapp.com/webhooks/muxi?hash=${{ secrets.WEBHOOK_HASH }}"
  webhook_retries: 3
  webhook_timeout: 10
```

### Memory Configuration

```yaml
memory:
  working:
    max_memory_mb: auto             # 10% of RAM, capped 1GB, min 64MB
    fifo_interval_min: 5
    vector_dimension: 1536
    mode: "local"                   # "local" or "remote"
    remote:
      url: "tcp://localhost:8000"   # FAISSx server
      api_key: "${{ secrets.FAISSX_API_KEY }}"

  buffer:
    size: 10                        # Context window
    multiplier: 10                  # Total = size * multiplier
    vector_search: true

  persistent:
    connection_string: "postgres://user:pass@localhost:5432/db"
    # or: "sqlite:///data/memory.db"
    query_timeout_seconds: 30
    user_synopsis:
      enabled: true
      cache_ttl: 3600
```

Multi-tenancy requires Postgres. SQLite supports single user only.

### MCP Configuration

```yaml
mcp:
  default_retry_attempts: 3
  default_timeout_seconds: 30
  max_tool_iterations: 10           # Max correction attempts per chain
  max_tool_calls: 50                # Total tool calls allowed
  max_repeated_errors: 3            # Stop after N same errors
  max_timeout_in_seconds: 300       # Total time limit
  max_tool_timeout_in_seconds: 30   # Per tool call
  enhance_user_prompts: true        # LLM enhances ambiguous messages for tool selection
  servers: []                       # Auto-discovered from mcp/ directory
```

### A2A Configuration

```yaml
a2a:
  enabled: true
  filtering:
    enabled: false
    threshold: 50
    min_relevance_score: 0.3
  outbound:
    enabled: true
    startup_policy: "lenient"       # lenient, strict, retry
    registries:
      - "https://a2a.muxihub.com"
    default_retry_attempts: 3
    default_timeout_seconds: 30
    services: []                    # Auto-discovered from a2a/
  inbound:
    enabled: true
    port: 8181
    auth:
      type: "bearer"
      token: "${{ secrets.A2A_BEARER_TOKEN }}"
```

### Scheduler

```yaml
scheduler:
  enabled: true
  timezone: "UTC"
  check_interval_minutes: 1
  max_concurrent_jobs: 10
  max_failures_before_pause: 3
```

### Logging

```yaml
logging:
  system:
    level: "debug"                  # debug, info, warning, error
    destination: "stdout"           # "stdout" or file path
  conversation:
    enabled: true
    streams:
      - transport: "stdout"
        level: "info"
        format: "jsonl"             # jsonl, text, msgpack, protobuf
        events: ["*"]
      - transport: "file"
        destination: "/var/logs/formation.log"
        format: "text"
        events: ["*"]
      - transport: "trail"          # MUXI observability
        token: "${{ secrets.TRAIL_TOKEN }}"
      - transport: "stream"
        protocol: "kafka"           # zmq, http, kafka, tcp, udp
        destination: "kafka://broker:9092"
        topic: "logs"
        format: "datadog_json"      # datadog_json, splunk_hec, elastic_bulk, grafana_loki, newrelic_json, opentelemetry
```

### Skills

```yaml
skills:
  - pdf-processing                  # Formation-level (all agents)
  - data-analysis

executor:
  enabled: true
  image: "muxi/executor:latest"
  port: 5560
  timeout_default: 30
  resource_limits:
    memory: "2g"
    cpu: 1.0
```

### User Credentials

```yaml
user_credentials:
  mode: "redirect"                  # redirect (default) or dynamic
  redirect_message: |
    Please use your organization's credential management system.
  encryption:
    key: null
    salt: "muxi-user-credentials-salt-v1"
  require_https: true
  credential_ttl_minutes: 60
```

### Runtime

```yaml
runtime:
  built_in_mcps: true               # Enable all built-in MCPs
  # Or granular:
  # built_in_mcps:
  #   - file-generation
```

---

## Agent Schema (`agents/*.afs`)

### Required Fields

```yaml
schema: "1.0.0"
id: researcher                      # Unique identifier
name: Research Specialist            # Display name (optional but recommended)
description: >                      # Required: used by Overlord for routing
  Gathers accurate information from multiple sources
```

### Full Agent Example

```yaml
schema: "1.0.0"
id: researcher
name: Research Specialist
description: Gathers accurate information from multiple sources
role: researcher                    # specialist, generalist, coordinator, researcher, executor

author: "MUXI Team <team@muxi.ai>"
url: "https://github.com/muxi/researcher"
license: "MIT"
version: "1.0.0"

system_message: |
  Research topics thoroughly using web search.
  Always cite your sources.
  Return structured findings.

system_behavior:
  constraints:
    - "Only answer questions about the product"
    - "Never share internal pricing"
  capabilities:
    - "Product knowledge"
    - "Troubleshooting"
  interaction_style: "friendly"     # formal, casual, technical, friendly, professional
  response_length: "moderate"       # concise, moderate, detailed, comprehensive
  error_handling: "user-friendly"

specialization:
  domain: "research"
  subdomain: "web-research"
  expertise_level: "expert"         # junior, intermediate, senior, expert
  keywords: ["research", "search", "find", "investigate"]

llm_models:                         # Override formation LLM (highest priority)
  - text: "anthropic/claude-sonnet-4-20250514"
    api_key: "${{ secrets.CUSTOM_KEY }}"
    settings:
      temperature: 0.3
      max_tokens: 2000

knowledge:
  files:
    - "knowledge/product-faq.md"
  directories:
    - "knowledge/docs/"
  auto_update: true
  embedding_model: "openai/text-embedding-3-small"
  chunk_size: 1000
  chunk_overlap: 100

mcp_servers:                        # Agent-specific tools
  - id: web-search
    description: Brave web search
    type: command
    command: npx
    args: ["-y", "@modelcontextprotocol/server-brave-search"]
    auth:
      type: env
      BRAVE_API_KEY: "${{ secrets.BRAVE_API_KEY }}"

skills:
  - ticket-handling                 # Private to this agent

a2a_enabled: true
enable_direct_delegation: true
delegation_strategy: "automatic"    # automatic, ask_user, never

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
id: web-search
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
id: remote-tools
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
| `env` | Key-value pairs | Environment variables passed to process |
| `bearer` | `token` | Bearer token header |
| `basic` | `username`, `password` | Basic authentication |
| `api_key` | `header`, `key` | Custom API key header |

---

## A2A Service Schema (`a2a/*.afs`)

```yaml
schema: "1.0.0"
id: analytics-engine
name: Analytics Engine
description: External analytics service
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
  timeout_seconds: 60
capabilities:
  agents: ["analytics-agent"]
  features: ["analysis", "visualization"]
  streaming: true
  async: true
```

---

## Secrets Interpolation

### Formation Secrets (loaded at startup)
```yaml
api_key: "${{ secrets.OPENAI_API_KEY }}"
```

### User Credentials (loaded on-demand, per-user)
```yaml
token: "${{ user.credentials.github }}"
```

Secret names are case-insensitive and normalized to `UPPER_SNAKE_CASE`.

---

## Validation Requirements

**Formation:** Must have `schema`, `id`, `description`. All secret references must use valid `${{ secrets.NAME }}` syntax.

**Agent:** Must have `schema`, `id`, `description`. MCP server references must exist.

**MCP:** Must have `schema`, `id`, `type`. Command servers need `command`. HTTP servers need `endpoint`.

**A2A:** Must have `schema`, `id`, `url`. Auth configs must be complete.
