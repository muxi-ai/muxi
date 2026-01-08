---
title: CLI Cheatsheet
description: Quick reference for all MUXI CLI commands
---

# CLI Cheatsheet

---

## Getting Started

```bash
# Check version
muxi --version

# Get help
muxi --help
muxi <command> --help
```

---

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

---

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

---

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

---

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

---

## Formations (Remote)

```bash
# List formations
muxi formation list
muxi formation list --profile production

# Get details
muxi formation get my-assistant

# Lifecycle
muxi formation stop my-assistant
muxi formation start my-assistant
muxi formation restart my-assistant

# Delete
muxi formation delete my-assistant
muxi formation delete my-assistant --force

# Rollback
muxi formation rollback my-assistant
```

---

## Logs

```bash
# View logs
muxi logs my-assistant
muxi logs my-assistant --lines 100
muxi logs my-assistant --follow
muxi logs --mcp                    # MCP tool logs
```

---

## Registry

```bash
# Search
muxi search "customer support"
muxi search --tag research

# Pull
muxi pull @muxi/starter
muxi pull @muxi/starter@1.0.0
muxi pull @muxi/starter --output ~/formations/

# Info
muxi info @muxi/starter

# Push
muxi auth login
muxi push
muxi push --public
muxi push --private
muxi push --tag v1.0.0
```

---

## Profiles

```bash
# Manage profiles
muxi profile add production
muxi profile list
muxi profile use production
muxi profile remove old-server
```

---

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
| `formation delete` | `--force` | Skip confirmation |
| `logs` | `--follow` | Stream logs |
| `logs` | `--lines <n>` | Number of lines |
| `pull` | `--output <dir>` | Output directory |
| `push` | `--public` | Public visibility |
| `dev` | `--port <n>` | Override port |
| `dev` | `--reindex` | Force reindex |

---

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
muxi formation get my-bot
```

### Registry Workflow

```bash
# Contribute a formation
muxi new formation awesome-agent
# ... build it ...
muxi auth login
muxi push --public

# Use someone else's
muxi pull @alice/awesome-agent
muxi secrets setup
muxi dev
```
