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

Create agent files in `agents/` directory:

```yaml
# agents/researcher.afs
schema: "1.0.0"
id: researcher
name: Research Specialist
description: Gathers information from sources

system_message: |
  You research topics thoroughly.
  Gather accurate, well-sourced information.
```

```yaml
# agents/writer.afs
schema: "1.0.0"
id: writer
name: Content Writer
description: Creates content

system_message: |
  You write clear, engaging content.
  Follow style guidelines.
```

```yaml
# agents/reviewer.afs
schema: "1.0.0"
id: reviewer
name: Content Reviewer
description: Reviews content

system_message: |
  You review content for accuracy.
  Check clarity and tone.
```

## Step 2: Add Tools

Create MCP files in `mcp/` directory:

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

## Step 3: Configure Orchestration

```yaml
# formation.afs
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

### By System Message

Each agent has a specialized `system_message` in `agents/*.afs`:

```yaml
# agents/support.afs - Customer support focus
# agents/sales.afs - Sales advisor focus  
# agents/technical.afs - Technical expert focus
```

### By Tools

Give agents specific MCP servers via `mcp_servers` in agent files:

```yaml
# agents/researcher.afs - Has web-search mcp_servers
# agents/data-analyst.afs - Has database mcp_servers
# agents/developer.afs - Has github, filesystem mcp_servers
```

### By Knowledge

Configure knowledge sources per agent:

```yaml
# agents/product-expert.afs
schema: "1.0.0"
id: product-expert
name: Product Expert
description: Product knowledge expert

system_message: "You are a product expert. Your job is to answer questions about our products..."

knowledge:
  enabled: true
  sources:
    - path: knowledge/product/
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
[+] [Agents Concept](../concepts/agents-and-orchestration.md) - Architecture
[+] [Workflows Reference](../reference/workflows.md) - Routing configuration
[+] [Deep Dive: Orchestration](../deep-dives/how-orchestration-works.md) - How routing works
[+] [Write SOPs](sops.md) - Standard procedures
[+] [Example: Multi-Agent Team](../examples/05-multi-agent-team/) - Working example
