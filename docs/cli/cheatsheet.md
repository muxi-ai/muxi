---
title: CLI Cheatsheet
description: Quick reference for all MUXI CLI commands
---
# CLI Cheatsheet

## Quick reference for all MUXI CLI commands

The CLI is your primary tool for creating, developing, deploying, and managing MUXI formations.

## Getting Started

```bash
# Check version
muxi --version

# Get help
muxi --help
muxi <command> --help
```


## Create

```bash
# Create new formation
muxi new formation my-assistant
muxi new formation my-bot --template research

# Add components to formation
muxi new agent researcher
muxi new mcp web-search
muxi new trigger github-issue
muxi new sop customer-onboarding
```


## Develop

```bash
# Run locally
muxi dev
muxi dev --port 8002
muxi dev --reindex           # Force knowledge reindex

# Validate formation
muxi validate

# Interactive chat
muxi chat
muxi chat "Hello!"
muxi chat --agent researcher "Find info on X"
```


## Secrets

```bash
# Setup wizard (scans formation, prompts for all)
muxi secrets setup

# Individual secrets
muxi secrets set OPENAI_API_KEY
muxi secrets get OPENAI_API_KEY
muxi secrets list
muxi secrets delete OLD_KEY
```


## Deploy

```bash
# Deploy to default profile
muxi deploy

# Deploy to specific profile
muxi deploy --profile production

# Validate without deploying
muxi deploy --validate

# Dry run
muxi deploy --dry-run
```


## Formations (Remote)

```bash
# List formations
muxi server list
muxi server list --profile production

# Get details
muxi server get my-assistant
# Or from formation directory:
muxi get

# Lifecycle
muxi server stop my-assistant
muxi server start my-assistant
muxi server restart my-assistant
# Or from formation directory:
muxi stop
muxi start
muxi restart

# Delete
muxi server delete my-assistant
muxi server delete my-assistant --force
# Or from formation directory:
muxi delete

# Rollback
muxi server rollback my-assistant
# Or from formation directory:
muxi rollback
```


## Logs

```bash
# View logs
muxi logs my-assistant
muxi logs my-assistant --lines 100
muxi logs my-assistant --follow
muxi logs --mcp                    # MCP tool logs
```


## Registry

```bash
# Search
muxi search "customer support"
muxi search --tag research

# Pull
muxi pull @muxi/starter
muxi pull @muxi/starter@1.0.0
muxi pull @muxi/starter --output ~/formations/

# Show details
muxi show @muxi/starter

# Push
muxi login
muxi push
muxi push --tag v1.0.0
muxi push --dry-run
```


## Profiles

```bash
# Manage profiles
muxi profiles add production
muxi profiles list
muxi profiles remove old-server

# Set default profile
muxi set default profile production
```


## Flags Reference

### Global Flags

| Flag | Description |
|------|-------------|
| `--profile <name>` | Use specific server profile |
| `--output json` | JSON output format |
| `--quiet` | Suppress non-essential output |
| `--verbose` | Verbose output |
| `--help` | Show help |

### Common Flags

| Command | Flag | Description |
|---------|------|-------------|
| `deploy` | `--validate` | Validate only |
| `deploy` | `--dry-run` | Show what would deploy |
| `server delete` | `--force` | Skip confirmation |
| `logs` | `--follow` | Stream logs |
| `logs` | `--lines <n>` | Number of lines |
| `pull` | `--output <dir>` | Output directory |
| `push` | `--dry-run` | Preview without publishing |
| `dev` | `--port <n>` | Override port |
| `dev` | `--reindex` | Force reindex |


## Examples

### Full Workflow

```bash
# Create and develop
muxi new formation my-bot
cd my-bot
muxi secrets setup
muxi dev

# Test
muxi chat "Hello!"

# Deploy
muxi deploy --profile production

# Monitor
muxi logs my-bot --follow
```

### CI/CD

```bash
# In CI pipeline
muxi deploy --validate
muxi deploy --profile production
muxi server get my-bot
```

### Registry Workflow

```bash
# Contribute a formation
muxi new formation awesome-agent
# ... build it ...
muxi login
muxi push

# Use someone else's
muxi pull @alice/awesome-agent
muxi secrets setup
muxi dev
```
