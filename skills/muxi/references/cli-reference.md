# CLI Reference

Complete command reference for the `muxi` CLI.

## Local Development (muxi up/down)

Think of `muxi up` / `muxi down` like `docker compose up` / `docker compose down` -- quick start/stop for local development without the full deploy cycle.

```bash
# Terminal 1: Start server (one-time)
muxi-server start

# Terminal 2: Start formation from its directory
cd my-formation
muxi up                      # Start formation
muxi up --port 9000          # Use different server port

# Stop formation
muxi down                    # Stop (ID from current dir)
muxi down my-bot             # Stop by ID from anywhere
muxi down --port 9000        # Use different server port
```

**Key differences from `muxi deploy`:**
- Runs directly from source (no bundle/upload)
- Uses `/draft/{id}` URL prefix (live uses `/api/{id}`)
- Fast iteration -- instant start
- In-memory only (gone on server restart)

## Create

```bash
muxi new formation my-assistant          # New formation project
muxi new formation my-bot --template research  # From template (default, research, support)
muxi new agent researcher               # Add agent to formation
muxi new mcp web-search                 # Add MCP server config
muxi new mcp github --template github   # MCP from template
muxi new trigger github-issue           # Add trigger template
muxi new sop customer-onboarding        # Add SOP document
```

## Develop

```bash
muxi dev                     # Run locally (hot reload at http://localhost:8001)
muxi dev --port 8002         # Custom port
muxi dev --reindex           # Force knowledge reindex
muxi validate                # Validate formation config
muxi chat                    # Interactive chat
muxi chat "Hello!"           # Single message
muxi chat --agent researcher "Find info on X"  # Target specific agent
```

## Secrets

```bash
muxi secrets setup           # Interactive wizard (scans formation, prompts for all)
muxi secrets set OPENAI_API_KEY  # Set individual secret (prompts for value)
muxi secrets get OPENAI_API_KEY  # Get secret value
muxi secrets list            # List all secret keys
muxi secrets delete OLD_KEY  # Delete a secret
```

Options: `--path <dir>` (formation directory), `--stdin` (read from stdin), `--force` (overwrite without confirm).

## Configure

```bash
muxi config llm              # LLM providers and models wizard
muxi config llm --remote     # View remote LLM config
muxi config overlord         # Persona, response format
muxi config overlord --remote
muxi config memory           # Memory settings
muxi config logging          # Logging streams
muxi config async            # Async response settings
muxi config a2a              # Agent-to-Agent communication
muxi config security         # User credential handling
```

## Deploy

```bash
muxi deploy                  # Deploy to default profile
muxi deploy --profile production
muxi deploy --validate       # Validate only, don't deploy
muxi deploy --dry-run        # Show what would deploy

muxi bump                    # Patch: 1.0.0 -> 1.0.1
muxi bump minor              # Minor: 1.0.0 -> 1.1.0
muxi bump major              # Major: 1.0.0 -> 2.0.0
muxi bump --set 2.0.0        # Set specific version
```

## Manage Remote Formations

```bash
muxi server list             # List all formations
muxi server list --profile production

muxi server get my-assistant # Get formation details
muxi server stop my-assistant
muxi server start my-assistant
muxi server restart my-assistant
muxi server delete my-assistant
muxi server delete my-assistant --force
muxi server rollback my-assistant

# From inside a formation directory:
muxi get
muxi stop
muxi start
muxi restart
muxi delete
muxi rollback
```

## Logs

```bash
muxi logs my-assistant
muxi logs my-assistant --lines 100
muxi logs my-assistant --follow     # Stream logs (like tail -f)
muxi logs --mcp                     # MCP tool logs
```

## Registry

```bash
muxi search "customer support"
muxi search --tag research
muxi pull @muxi/hello-muxi
muxi pull @muxi/hello-muxi@1.0.0
muxi pull @muxi/hello-muxi --output ~/formations/
muxi show @muxi/hello-muxi            # Show details
muxi login
muxi push
muxi push --tag v1.0.0
muxi push --dry-run
```

## Profiles

```bash
muxi profiles add production
muxi profiles list
muxi profiles remove old-server
muxi set default profile production
```

## Formation Info

```bash
muxi info                    # Formation status (requires deployed formation)
muxi info --full             # Include detailed config
muxi info -f my-bot          # Specify formation
```

## Sessions

```bash
muxi sessions list -u user123
muxi sessions show <session_id> -u user123
muxi sessions messages <session_id> -u user123
```

## Requests

```bash
muxi requests list -u user123
muxi requests show <request_id> -u user123
muxi requests cancel <request_id> -u user123
```

## Memory

```bash
muxi memory status
muxi memory list -u user123
muxi memory add -u user123 --type preference --detail "Prefers formal tone"
muxi memory delete <memory_id> -u user123
muxi memory buffer -u user123           # View buffer
muxi memory buffer --clear -u user123   # Clear buffer
muxi memory buffer --stats              # Buffer stats
```

## Agents (Remote)

```bash
muxi agents list
muxi agents show researcher
```

## MCP Servers (Remote)

```bash
muxi mcp list
muxi mcp show web-search
muxi mcp tools               # List all available tools
```

## SOPs

```bash
muxi sops list
muxi sops show customer-onboarding
```

## Triggers

```bash
muxi triggers list
muxi triggers show github-issue
muxi triggers run github-issue --data '{"repo": "muxi-ai/cli"}'
muxi triggers run daily-report --async
```

## Scheduler

```bash
muxi scheduler status
muxi scheduler list
muxi scheduler show <job_id>
muxi scheduler add --schedule "0 9 * * *" --message "Daily summary"
muxi scheduler remove <job_id>
```

## Saved Formations

```bash
muxi formations add my-bot --profile production --user user123
muxi formations list
muxi formations show my-bot
muxi formations remove my-bot
muxi chat -f my-bot           # Use from anywhere
```

## Global Flags

| Flag | Description |
|------|-------------|
| `--profile <name>` | Use specific server profile |
| `--output json` | JSON output format |
| `--quiet` | Suppress non-essential output |
| `--verbose` | Verbose output |
| `--help` | Show help |

## Environment Variable Overrides

| Variable | Description |
|----------|-------------|
| `MUXI_PROFILE` | Override default profile |
| `MUXI_SERVER_URL` | Override profile URL |
| `MUXI_KEY_ID` | Override profile key_id |
| `MUXI_SECRET_KEY` | Override profile secret_key |
| `MUXI_OUTPUT_FORMAT` | Override output format |

## CLI Configuration

**Config file:** `~/.muxi/cli/config.afs`

```yaml
default_profile: local
editor: vim
output:
  format: table      # table, json, yaml
  color: true
dev:
  port: 8001
  auto_reload: true
```

**Server profiles:** `~/.muxi/cli/servers.afs`

```yaml
profiles:
  local:
    url: http://localhost:7890
    key_id: MUXI_local
    secret_key: sk_...
  production:
    url: https://muxi.example.com:7890
    key_id: MUXI_production
    secret_key: sk_...
```
