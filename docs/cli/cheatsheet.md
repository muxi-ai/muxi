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

# Upgrade to latest
muxi upgrade

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


## Local Development

Think of `muxi up` / `muxi down` like `docker compose up` / `docker compose down`:

```bash
# Start server (first terminal)
muxi-server start

# Start formation (second terminal)
cd my-formation
muxi up                      # Start from formation directory

# Formation runs at http://localhost:7890/draft/my-formation

# Stop formation
muxi down                    # Stop (ID from current dir)
muxi down my-bot             # Stop by ID from anywhere

# Server keeps running - stop with: muxi-server stop
```

**Key differences from `muxi deploy`:**
- Runs directly from source (no bundle/upload)
- Uses `/draft/{id}` URL prefix (live uses `/api/{id}`)
- Fast iteration - instant start


## Develop

```bash
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


## Configure Formation

```bash
# LLM providers and models
muxi config llm              # Interactive wizard
muxi config llm --remote     # View remote config

# Overlord soul and behavior
muxi config overlord         # Configure soul, response format
muxi config overlord --remote

# Other configuration
muxi config memory           # Memory settings
muxi config logging          # Logging streams
muxi config async            # Async response settings
muxi config a2a              # Agent-to-Agent communication
muxi config security         # User credential handling
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

# Bump version before deploying updates
muxi bump              # Patch: 1.0.0 -> 1.0.1
muxi bump minor        # Minor: 1.0.0 -> 1.1.0
muxi bump major        # Major: 1.0.0 -> 2.0.0
muxi bump --set 2.0.0  # Set specific version
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

# Download from server to local
muxi download                    # In formation dir: replace with server version
muxi download my-bot             # Outside: create ./my-bot/ with server version
muxi download --profile prod     # Use specific profile
muxi download --include-db       # Include memory.db from server
```


## Console

```bash
# Open MUXI Console in browser
muxi console                     # Opens console, auto-detects formation context
muxi console --profile prod      # Use specific profile
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
muxi pull @muxi/hello-world
muxi pull @muxi/hello-world@1.0.0
muxi pull @muxi/hello-world --output ~/formations/

# Show details
muxi show @muxi/hello-world

# Push
muxi login
muxi push
muxi push --tag v1.0.0
muxi push --dry-run
muxi push --org my-org          # Publish under organization (admin only)

# Organizations
muxi registry orgs              # List orgs and publish permissions
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


## Formation Info

```bash
# View formation status (requires deployed formation)
muxi info
muxi info --full       # Include detailed config
muxi info -f my-bot    # Specify formation
```


## Sessions

```bash
# Manage user sessions
muxi sessions list -u user123
muxi sessions show <session_id> -u user123
muxi sessions messages <session_id> -u user123
```


## Requests

```bash
# Track and manage chat requests
muxi requests list -u user123
muxi requests show <request_id> -u user123
muxi requests cancel <request_id> -u user123   # Cancel in-progress request
```


## Memory

```bash
# View memory config
muxi memory status

# Manage user memories
muxi memory list -u user123
muxi memory add -u user123 --type preference --detail "Prefers formal tone"
muxi memory delete <memory_id> -u user123

# Buffer management
muxi memory buffer -u user123           # View user buffer
muxi memory buffer --clear -u user123   # Clear user buffer
muxi memory buffer --stats              # View buffer stats
```


## Agents (Remote)

```bash
# View agents in running formation
muxi agents list
muxi agents show researcher
```


## MCP Servers (Remote)

```bash
# View MCP servers in running formation
muxi mcp list
muxi mcp show web-search
muxi mcp tools              # List all available tools
```


## SOPs

```bash
# View Standard Operating Procedures
muxi sops list
muxi sops show customer-onboarding
```


## Triggers

```bash
# Manage and run triggers
muxi triggers list
muxi triggers show github-issue
muxi triggers run github-issue --data '{"repo": "muxi-ai/cli"}'
muxi triggers run daily-report --async    # Run asynchronously
```


## Scheduler

```bash
# View scheduler config and jobs
muxi scheduler status
muxi scheduler list
muxi scheduler show <job_id>

# Manage scheduled jobs
muxi scheduler add --schedule "0 9 * * *" --message "Daily summary"
muxi scheduler remove <job_id>
```


## Saved Formations

```bash
# Save formation configs for quick access
muxi formations add my-bot --profile production --user user123
muxi formations list
muxi formations show my-bot
muxi formations remove my-bot

# Then use from anywhere
muxi info -f my-bot
muxi chat -f my-bot
```


## Upgrade

```bash
# Check for updates and upgrade
muxi upgrade
```

The CLI automatically checks for updates and shows a notification:

```
[*] MUXI CLI 0.20260210.0 available (current: 0.20260203.0)
    Run: muxi upgrade
```

`muxi upgrade` detects your install method:
- **Homebrew**: Shows `brew upgrade muxi`
- **Go install**: Shows `go install github.com/muxi-ai/cli@latest`
- **Binary**: Downloads and self-updates from GitHub releases

Disable update checks: `export MUXI_NO_UPDATE_CHECK=1`


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
| `bump` | `--set <ver>` | Set specific version |
| `server delete` | `--force` | Skip confirmation |
| `logs` | `--follow` | Stream logs |
| `logs` | `--lines <n>` | Number of lines |
| `pull` | `--output <dir>` | Output directory |
| `push` | `--dry-run` | Preview without publishing |
| `push` | `--org <name>` | Publish under organization |
| `dev` | `--port <n>` | Override port |
| `dev` | `--reindex` | Force reindex |
| `info` | `--full` | Show full configuration |
| `sessions` | `-u <user>` | User ID (required) |
| `memory` | `-u <user>` | User ID for user-scoped ops |
| `memory buffer` | `--clear` | Clear buffer |
| `memory buffer` | `--stats` | Show buffer statistics |
| `triggers run` | `--data <json>` | Trigger input data |
| `triggers run` | `--async` | Run asynchronously |
| `scheduler add` | `--schedule` | Cron schedule |
| `download` | `-f, --force` | Skip confirmation |
| `download` | `-p, --profile` | Server profile |
| `download` | `--include-db` | Include memory.db |
| `deploy` | `--include-db` | Include memory.db |


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
