---
title: muxi new
description: Scaffold new formations and components
---
# muxi new

## Create formations, agents, and more with one command

Scaffold new formations, agents, MCP servers, triggers, and SOPs from templates. Skip the boilerplate and start with working code.

## Usage

```bash
muxi new <type> <name> [options]
```

## Types

| Type | Description |
|------|-------------|
| `formation` | Create new formation project |
| `agent` | Add agent to formation |
| `mcp` | Add MCP server config |
| `trigger` | Add trigger template |
| `sop` | Add SOP document |

## Create Formation

```bash
muxi new formation my-assistant
```

Creates:

```
my-assistant/
├── formation.afs
├── agents/
├── mcp/
├── triggers/
├── sops/
├── knowledge/
├── secrets
└── .gitignore
```

### From Template

```bash
muxi new formation my-bot --template research
```

Templates:

- `default` - Basic assistant
- `research` - Research with web search
- `support` - Customer support with knowledge

## Create Agent

```bash
cd my-formation
muxi new agent researcher
```

Creates `agents/researcher.afs`:

```yaml
id: researcher
name: Research Specialist
role: |
  You are a research specialist who...
```

## Create MCP

```bash
muxi new mcp web-search
```

Creates `mcp/web-search.afs`:

```yaml
schema: "1.0.0"
id: web-search
type: command
command: npx
args: ["-y", "@modelcontextprotocol/server-brave-search"]
auth:
  type: env
  BRAVE_API_KEY: "${{ secrets.BRAVE_API_KEY }}"
```

### From Template

```bash
muxi new mcp github --template github
```

Templates match common MCP servers.

## Create Trigger

```bash
muxi new trigger github-issue
```

Creates `triggers/github-issue.md`:

```
---
name: GitHub Issue Handler
tags: [github, issue]
---

New issue from ${{ data.repository }}:

**Issue #${{ data.issue.number }}**: ${{ data.issue.title }}
```

## Create SOP

```bash
muxi new sop customer-onboarding
```

Creates `sops/customer-onboarding.md`:

```
---
name: Customer Onboarding
tags: [customer, onboarding]
triggers:
  keywords: [new customer, onboard]
---

# Onboarding Procedure

1. Collect customer information
2. Create account
3. Send welcome email
```

## Options

| Flag | Description |
|------|-------------|
| `--template <name>` | Use specific template |
| `--output <dir>` | Output directory |
| `--force` | Overwrite existing |

## Examples

```bash
# Create formation from template
muxi new formation support-bot --template support

# Add agent to current formation
muxi new agent writer

# Add MCP from template
muxi new mcp database --template postgres
```
