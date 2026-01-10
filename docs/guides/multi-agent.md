---
title: Multi-Agent Systems
description: Build systems with multiple specialized agents that collaborate
---
# Multi-Agent Systems

## Create teams of specialized agents

Build formations where multiple agents with different skills work together. A researcher gathers information, a writer creates content, a reviewer checks quality - MUXI orchestrates them automatically.

## Overview

Multi-agent systems divide work among specialists:

```
         User Request
              ↓
      ┌───────────────┐
      │   Overlord    │  ← Coordinates
      └───────────────┘
              ↓
    ┌─────────┼─────────┐
    ↓         ↓         ↓
Researcher  Writer   Reviewer
```

> [!NOTE]
> The Overlord routes requests automatically. SOP matches take priority, then explicit agent selection, then complexity-based routing.

## Step 1: Define Agents

```yaml
agents:
  - id: researcher
    name: Research Specialist
    role: |
      You research topics thoroughly.
      Gather accurate, well-sourced information.
    mcps:
      - web-search

  - id: writer
    name: Content Writer
    role: |
      You write clear, engaging content.
      Follow style guidelines.

  - id: reviewer
    name: Content Reviewer
    role: |
      You review content for accuracy.
      Check clarity and tone.
```

## Step 2: Add Tools

Create MCP files in `mcp/` directory:

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

## Step 3: Configure Orchestration

```yaml
# formation.yaml
overlord:
  workflow:
    auto_decomposition: true
    complexity_threshold: 5.0
```

## Step 4: Test

```bash
muxi dev
```

Complex request:

```
You: Research AI trends and write a blog post about them
```

MUXI:
1. Routes to researcher (gathers info)
2. Routes to writer (creates draft)
3. (Optionally) Routes to reviewer

## Agent Specialization

### By Role

```yaml
agents:
  - id: support
    role: Customer support specialist
  - id: sales
    role: Sales advisor
  - id: technical
    role: Technical expert
```

### By Tools

```yaml
agents:
  - id: researcher
    mcps: [web-search]
  - id: data-analyst
    mcps: [database]
  - id: developer
    mcps: [github, filesystem]
```

### By Knowledge

```yaml
agents:
  - id: product-expert
    knowledge:
      sources:
        - path: knowledge/product/
  - id: policy-expert
    knowledge:
      sources:
        - path: knowledge/policies/
```

## Explicit Agent Selection

Override automatic routing:

```bash
# CLI
muxi chat --agent researcher "Find info on X"

# API
curl -d '{"message": "...", "agent": "researcher"}'
```

## Best Practices

1. **Clear roles** - Non-overlapping responsibilities
2. **Right tools** - Match tools to agent purpose
3. **Focused knowledge** - Relevant sources per agent
4. **Test routing** - Verify correct agent selection

## Learn More

[+] [Agents Reference](../reference/agents.md) - Configuration
[+] [Agents Concept](../concepts/agents.md) - Architecture
[+] [Workflows Reference](../reference/workflows.md) - Routing configuration
[+] [Deep Dive: Orchestration](../deep-dives/orchestration.md) - How routing works
[+] [Write SOPs](sops.md) - Standard procedures
[+] [Example: Multi-Agent Team](../examples/05-multi-agent-team/) - Working example
