---
title: Formation Schema
description: Understanding the Agent Formation Schema specification
---
# Formation Schema

## Understanding the Agent Formation Schema specification

Every MUXI formation is defined using the **Agent Formation Schema** (AFS), a YAML-based configuration format that describes agents, tools, memory, and behavior.

> [!NOTE]
> **File Extension:** AFS files use the `.afs` extension (or `.yaml` - they're identical). The `.afs` extension signals "this is an Agent Formation Schema file" while remaining 100% YAML-compatible.

## Official Specification

The authoritative schema specification is maintained at:

<a href="https://agentformation.org" target="_blank" class="btn">Visit AgentFormation.org ›</a>

This repository contains:

- Complete JSON Schema definitions
- Field specifications and defaults
- Usage examples
- Version history
- Community contributions

## What is a Formation?

A **formation** is a complete AI system configuration that includes:

- **Agents:** One or more AI agents with specific roles
- **Overlord:** The orchestrator that routes requests and manages workflows
- **Memory:** Working and persistent memory systems
- **Tools (MCP):** External capabilities via Model Context Protocol
- **LLM Configuration:** Language model settings and API keys
- **Behavior:** Soul, workflows, clarification, async processing

## Basic Structure

```yaml
# Required fields
schema: "1.0.0"               # Schema version
id: my-formation              # Unique identifier
description: "..."            # What this formation does

# LLM configuration
llm:
  models:
    - text: "openai/gpt-4"
  api_keys:
    openai: ${{secrets.OPENAI_API_KEY}}

# Overlord (orchestrator)
overlord:
  soul: "You are a helpful assistant..."
  workflow:
    auto_decomposition: true
    complexity_threshold: 7.0

# Agents auto-discovered from agents/ directory
agents: []

# Optional: Memory, MCP, Knowledge, etc.
```

With agent file:

```yaml
# agents/assistant.afs
schema: "1.0.0"
id: assistant
name: Assistant
description: Helpful assistant

system_message: "Your role description here..."
```

## Schema Versions

The schema follows semantic versioning (MAJOR.MINOR.PATCH):

| Version | Status | Notes |
|---------|--------|-------|
| 1.0.0   | Stable | Current production version |
| 0.x.x   | Legacy | Pre-release versions |

Specify the schema version in your formation:

```yaml
schema: "1.0.0"
```

MUXI validates your formation against this schema version.

## Key Sections

### Required Fields

```yaml
schema: "1.0.0"              # Schema version (required)
id: my-assistant             # Formation ID (required)
description: "A helpful AI"  # Description (required)
```

### LLM Configuration

```yaml
llm:
  models:
    text: openai/gpt-4           # Text generation model
    streaming: openai/gpt-4o-mini # Fast streaming model
    embedding: openai/text-embedding-3-small

  api_keys:
    openai: ${{secrets.OPENAI_API_KEY}}

  settings:
    temperature: 0.7
    max_tokens: 1000
```

### Overlord

```yaml
overlord:
  soul: "You are a professional assistant..."

  workflow:
    auto_decomposition: true
    complexity_threshold: 7.0
    max_parallel_tasks: 5

    timeouts:
      task_timeout: 300
      workflow_timeout: 3600

    retry:
      max_attempts: 3
      initial_delay: 1.0
      backoff_factor: 2.0

  clarification:
    style: "conversational"
    persist_learned_info: false
```

### Async Processing

```yaml
async:
  threshold_seconds: 30        # Auto-async after 30 seconds
  enable_estimation: true      # Let overlord estimate time
  webhook_url: "https://..."   # Default webhook
```

### Memory

```yaml
memory:
  buffer:
    size: 10                   # Recent message window
    vector_search: true

  persistent:
    connection_string: "postgresql://..."
    embedding_model: openai/text-embedding-3-small
```

### Agents

Agents are defined in `agents/*.afs` files:

```yaml
# agents/assistant.afs
schema: "1.0.0"
id: assistant
name: Assistant
description: General purpose agent

system_message: "You are a helpful assistant..."

llm_models:
  - text: "openai/gpt-4"
    settings:
      temperature: 0.7
```

### MCP Tools

```yaml
mcp:
  servers:
    - id: filesystem
      command: npx
      args: ["-y", "@modelcontextprotocol/server-filesystem"]
      config:
        allowed_directories: ["./workspace"]
```

## Secrets Management

Reference secrets using the `${{secrets.KEY_NAME}}` syntax:

```yaml
llm:
  api_keys:
    openai: ${{secrets.OPENAI_API_KEY}}

mcp:
  servers:
    - id: github
      env:
        GITHUB_TOKEN:
          secret: GITHUB_TOKEN  # References ${{secrets.GITHUB_TOKEN}}
```

Secrets are:
- **Never stored in formation files**
- **Injected at runtime** from environment or secrets manager
- **Encrypted** in transit and at rest

## Schema Validation

MUXI validates formations on startup:

### Valid Formation ✅

```yaml
schema: "1.0.0"
id: valid-formation
description: "A valid formation"

llm:
  models:
    text: openai/gpt-4

agents: []  # Auto-discovered from agents/
```

### Invalid Formation ❌

```yaml
# Missing required 'schema' field
id: invalid-formation
description: "Missing schema version"

llm:
  model: gpt-4  # Wrong field name (should be 'models')

agents: []      # Empty agents array not allowed
```

**Error:**
```
Formation validation failed:
- Missing required field: schema
- Field 'llm.model' not recognized (did you mean 'llm.models'?)
- Agents array cannot be empty
```

## Common Patterns

### Single Agent Formation

```yaml
# formation.afs
schema: "1.0.0"
id: simple-assistant
description: "Single general-purpose assistant"

llm:
  models:
    - text: "openai/gpt-4"

overlord:
  soul: "You are a helpful assistant."
  workflow:
    auto_decomposition: false

agents: []
```

```yaml
# agents/assistant.afs
schema: "1.0.0"
id: assistant
name: Assistant
description: General purpose helper

system_message: "General purpose helper. Your job is to ..."
```

### Multi-Agent Formation

```yaml
# formation.afs
schema: "1.0.0"
id: research-team
description: "Research team with specialized agents"

llm:
  models:
    - text: "openai/gpt-4"

overlord:
  soul: "You coordinate a research team."
  workflow:
    auto_decomposition: true
    complexity_threshold: 7.0

agents: []  # researcher.afs, analyst.afs, writer.afs in agents/
```

### Formation with Tools

```yaml
# formation.afs
schema: "1.0.0"
id: developer-assistant
description: "AI assistant with code tools"

llm:
  models:
    - text: "openai/gpt-4"

agents: []  # Auto-discovered from agents/
```

With agent file:

```yaml
# agents/developer.afs
schema: "1.0.0"
id: developer
name: Developer
description: Software development assistant

system_message: "Software development assistant"
```

And MCP file:

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

## Best Practices

### DO ✅

**Use semantic versioning:**
```yaml
schema: "1.0.0"  # Explicit version
```

**Reference secrets properly:**
```yaml
llm:
  api_keys:
    openai: ${{secrets.OPENAI_API_KEY}}  # Correct
```

**Provide clear descriptions:**
```yaml
id: customer-support
description: "Customer support agent with FAQ knowledge base and CRM integration"
```

**Use correct field paths:**
```yaml
overlord:
  workflow:              # Under overlord
    complexity_threshold: 7.0

async:                   # Top-level
  threshold_seconds: 30
```

### DON'T ❌

**Don't hardcode secrets:**
```yaml
llm:
  api_keys:
    openai: "sk-abc123..."  # ❌ NEVER do this!
```

**Don't use wrong field names:**
```yaml
overlord:
  soul:
    name: "Assistant"    # ❌ soul is a string, not object
    style: "professional"
```

**Don't nest fields incorrectly:**
```yaml
workflow:                # ❌ Should be overlord.workflow
  complexity_threshold: 7.0
```

**Don't forget required fields:**
```yaml
# ❌ Missing 'schema' and 'id'
description: "An incomplete formation"
```

## Validation Tools

### CLI Validation

```bash
muxi validate formation.afs
```

**Output:**
```
✅ Formation is valid
- Schema version: 1.0.0
- Agents: 3
- MCP servers: 2
- Memory: enabled (persistent)
```

## Learn More

- [Formation Schema Reference](../reference/formation-schema.md) - Full YAML reference
- **[Agent Formation Schema](https://github.com/agent-formation/afs-spec)** - Official specification
- [Soul Configuration](soul.md) - Configure formation identity
- [Workflows & Task Decomposition](../concepts/workflows-and-task-decomposition.md) - Complex task handling
- [Memory Systems](../concepts/memory-system.md) - Working and persistent memory
- [MCP Integration](../concepts/tools-and-mcp.md) - Add tools via Model Context Protocol
- [Formation Examples](examples/) - Complete working examples
