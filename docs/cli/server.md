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
```
