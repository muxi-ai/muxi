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

Create agent files in `agents/` directory.

> [!IMPORTANT]
> **Be specific about roles and specialties.** The Overlord uses `description`, `role`, and `specialization` fields to route requests to the right agent. Vague descriptions lead to poor routing.

```yaml
# agents/researcher.afs
schema: "1.0.0"
id: researcher
name: Research Specialist
description: Gathers accurate information from web searches and documents

role: researcher
specialization:
  domain: research
  keywords: [search, investigate, find, gather, sources]

system_message: |
  You research topics thoroughly.
  Gather accurate, well-sourced information.
```

```yaml
# agents/writer.afs
schema: "1.0.0"
id: writer
name: Content Writer
description: Creates blog posts, articles, and marketing copy

role: executor
specialization:
  domain: content-writing
  keywords: [write, draft, compose, article, blog, copy]

system_message: |
  You write clear, engaging content.
  Follow style guidelines.
```

```yaml
# agents/reviewer.afs
schema: "1.0.0"
id: reviewer
name: Content Reviewer
description: Reviews and edits content for accuracy, clarity, and tone

role: specialist
specialization:
  domain: editing
  keywords: [review, edit, proofread, check, quality]

system_message: |
  You review content for accuracy.
  Check clarity and tone.
```

**Why this matters:**
- `description` - The Overlord reads this to understand what the agent does
- `role` - Categorizes the agent (specialist, generalist, coordinator, researcher, executor)
- `specialization.keywords` - Helps match user requests to the right agent

## Step 2: Add Tools

> [!IMPORTANT]
> **Prefer per-agent tools over global tools.** Define MCP servers in agent files (`mcp_servers:`) rather than formation-level (`mcp/*.afs`) for better routing accuracy.
>
> **Why:** The Overlord uses tool capabilities to select the right agent. Per-agent tools mean:
> - Better agent routing (Overlord knows which agent has which tools)
> - Better tool selection (agents only see relevant tools)
>
> ---
>
> **When to use global tools:** Only for tools that genuinely apply to ALL agents. For example, a web fetch tool useful for both a research agent AND a shopping agent.

### Per-Agent Tools (Recommended)

Reference formation-level MCP servers by ID in agent files:

```yaml
# agents/researcher.afs
schema: "1.0.0"
id: researcher
name: Research Specialist
description: Gathers accurate information from web searches

mcp_servers:
  - web-search              # Reference formation-level MCP by ID
```

### Global Tools (When Needed)

For tools used by ALL agents, create MCP files in `mcp/` directory:

```yaml
# mcp/web-fetch.afs - Useful for research, shopping, support agents
schema: "1.0.0"
id: web-fetch
type: command
command: npx
args: ["-y", "@modelcontextprotocol/server-fetch"]
```

### Per-Agent MCP Scoping (for large catalogs)

When you're integrating an MCP server with a very large tool surface — Microsoft 365, Google Workspace, ms365-assistant, or any catalog over ~20 tools — the right pattern is **one MCP `.afs` file per domain**, each filtered to the relevant tools and assigned to a corresponding domain-specialist agent. Don't give a single "ms365-assistant" agent the entire catalog.

**Why:**
- The Overlord routes by tool capability. If every Excel question and every email question lands on the same agent, the routing signal is gone.
- The planning prompt for an agent grows with its tool surface. A whitelisted slice keeps the prompt small and improves tool-selection accuracy.
- Per-domain agents can have per-domain system messages tailored to the domain's idioms.

**Worked example** — splitting Microsoft 365 across four specialist agents:

```yaml
# mcp/ms365-excel.afs
schema: "1.0.0"
id: ms365-excel
type: command
command: npx
args: ["-y", "@softeria/ms-365-mcp-server"]
auth:
  type: env
  ACCESS_TOKEN: "${{ user.credentials.MS365 }}"
tools:
  whitelist:
    - "list-excel-files"
    - "read-excel-*"
    - "update-excel-*"
    - "create-excel-*"
```

```yaml
# mcp/ms365-email.afs
schema: "1.0.0"
id: ms365-email
type: command
command: npx
args: ["-y", "@softeria/ms-365-mcp-server"]
auth:
  type: env
  ACCESS_TOKEN: "${{ user.credentials.MS365 }}"
tools:
  whitelist:
    - "list-mail-*"
    - "read-mail-*"
    - "send-mail"
    - "draft-mail"
```

```yaml
# agents/ms365-excel-agent.afs
schema: "1.0.0"
id: ms365-excel-agent
name: Excel Specialist
description: Reads, writes, and analyzes Microsoft Excel workbooks in OneDrive

role: specialist
specialization:
  domain: spreadsheets
  keywords: [excel, spreadsheet, workbook, xlsx, cell, sheet, formula]

system_message: |
  You are an Excel specialist. You operate on workbooks in the user's OneDrive.
  Always confirm the workbook by name before mutating it.

mcp_servers:
  - ms365-excel
```

Repeat the same shape for `ms365-email-agent.afs`, `ms365-calendar-agent.afs`, `ms365-onedrive-agent.afs`. The Overlord now has four well-bounded specialists with crisp routing signals, instead of one over-broad agent that has to disambiguate every M365 verb itself.

> [!TIP]
> The same pattern applies to any large MCP — Google Workspace, Atlassian, Salesforce, internal company MCPs. The rule of thumb: if a single MCP server exposes more tools than a single agent's role naturally needs, split it.

See [Tools & MCP → Tool Filtering](../concepts/tools-and-mcp.md#tool-filtering-whitelist--blacklist) for the underlying mechanism.


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

**MUXI:**

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

system_message: |
  You are a product expert.
  Your job is to answer questions about our products...

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
[+] [Write SOPs](../guides/create-sops.md) - Standard procedures
[+] [Example: Multi-Agent Team](examples/05-multi-agent-team/) - Working example
