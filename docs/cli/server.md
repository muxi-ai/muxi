---
title: muxi server
description: Manage deployed formations on MUXI Server
---
# muxi server

## Control your running formations

List, inspect, restart, and remove formations running on MUXI Server. Monitor health and manage the lifecycle of your deployed agents.

## Usage

```bash
muxi server <command> [options]
```

## Commands

| Command | Description |
|---------|-------------|
| `list` | List all formations |
| `get <id>` | Get formation details |
| `stop <id>` | Stop a formation |
| `start <id>` | Start a stopped formation |
| `restart <id>` | Restart a formation |
| `delete <id>` | Delete a formation |
| `rollback <id>` | Rollback to previous version |
| `download <id>` | Download formation to local |
| `console` | Open MUXI Console in browser |

## List Formations

```bash
muxi server list
```

Output:

```
ID              STATUS    PORT   VERSION   UPTIME
my-assistant    running   8001   1.0.0     2h 15m
support-bot     running   8002   1.2.0     1d 3h
research-bot    stopped   -      1.0.0     -
```

### Filter by Status

```bash
muxi server list --status running
muxi server list --status stopped
```

## Get Formation

```bash
muxi server get my-assistant
```

Output:

```
Formation: my-assistant
=======================
Status:    running
Port:      8001
Version:   1.0.0
Uptime:    2h 15m
Memory:    128MB

Agents:
  - assistant
  - researcher

MCPs:
  - web-search

Endpoints:
  Health:  http://localhost:8001/health
  Chat:    http://localhost:8001/v1/chat
```

## Stop Formation

```bash
muxi server stop my-assistant
```

Output:

```
Stopping my-assistant...
✓ Formation stopped
```

## Start Formation

```bash
muxi server start my-assistant
```

Output:

```
Starting my-assistant...
✓ Formation started on port 8001
✓ Health check passed
```

## Restart Formation

```bash
muxi server restart my-assistant
```

Output:

```
Restarting my-assistant...
✓ Formation restarted on port 8001
```

## Delete Formation

```bash
muxi server delete my-assistant
```

Output:

```
Delete formation my-assistant? [y/N] y
✓ Formation deleted
```

### Force Delete

```bash
muxi server delete my-assistant --force
```

## Rollback

Rollback to previous version:

```bash
muxi server rollback my-assistant
```

Output:

```
Current version: 1.2.0
Previous version: 1.1.0

Rollback to 1.1.0? [y/N] y
✓ Rolled back to 1.1.0
```

## Download Formation

Download a formation from the server to your local machine:

```bash
muxi download my-assistant
```

Output:

```
This will download 'my-assistant' from server 'production' to ./my-assistant/

Continue? [Y/n] y

✓ Downloading from server...
✓ Extracting...
✓ Downloaded 'my-assistant' to ./my-assistant/
  cd my-assistant && muxi dev
```

### In Formation Directory

When inside an existing formation directory, download replaces the local files with the server version:

```bash
cd my-assistant
muxi download
```

Output:

```
⚠ This will replace the entire 'my-assistant' directory with the server version.

Download and replace? [y/N] y

✓ Downloading from server...
✓ Extracting...
✓ Downloaded 'my-assistant' from server
```

### Download Options

| Flag | Description |
|------|-------------|
| `-p, --profile <name>` | Server profile to use |
| `-f, --force` | Skip confirmation prompt |
| `--include-db` | Include SQLite database files (excluded by default) |

### Use Cases

- **Sync to another machine**: Download production formation to dev laptop
- **Restore local changes**: Replace local edits with deployed version  
- **Team collaboration**: Get the current server state

## Console

Open the MUXI Console web interface in your browser:

```bash
muxi console
```

When run from a formation directory with a configured server profile, Console opens pre-configured with your formation context:

```bash
cd my-assistant
muxi console
# Opens: https://muxi.dev/launch?server=http://localhost:7890&formation=my-assistant
```

When run outside a formation directory or without a profile:

```bash
muxi console
# Opens: https://muxi.dev
```

## Options

| Flag | Description |
|------|-------------|
| `--profile <name>` | Server profile |
| `--force` | Skip confirmation |
| `--output json` | JSON output |

## Examples

```bash
# List all formations
muxi server list

# Get details
muxi server get my-bot

# Restart after config change
muxi server restart my-bot

# Delete with confirmation
muxi server delete old-bot

# Force delete
muxi server delete old-bot --force

# Download formation to local
muxi download my-bot
muxi download my-bot --profile production

# Download and replace current directory
cd my-bot && muxi download

# Open Console
muxi console
```
