---
title: muxi logs
description: Stream real-time logs from your formations
---
# muxi logs

## Debug and monitor your formations in real-time

Stream logs from a running formation with powerful filtering options. Great for debugging, monitoring, and understanding what your agents are doing.

## Usage

```bash
muxi logs [options]
```

Requires at least one filter. Press `Ctrl+C` to stop streaming.

## Quick Examples

```bash
muxi logs -u alice                    # All logs for a user
muxi logs -s sess_abc123              # Logs for a session
muxi logs --agent weather-bot         # Logs from specific agent
muxi logs --level error               # Errors only
muxi logs -u alice --level error      # Errors for a user
```

## Filters

### By User

```bash
muxi logs -u alice
muxi logs --user usr_abc123
```

### By Session

```bash
muxi logs -s sess_abc123
muxi logs --session sess_abc123
```

### By Request

```bash
muxi logs --request req_xyz789
```

### By Agent

```bash
muxi logs --agent weather-bot
muxi logs --agent muxi-generalist
```

### By Log Level

```bash
muxi logs --level debug      # All levels
muxi logs --level info       # Info and above
muxi logs --level warn       # Warnings and above
muxi logs --level error      # Errors only
```

### By Event Type

Supports wildcards:

```bash
muxi logs --event "chat.*"           # All chat events
muxi logs --event "agent.selected"   # Specific event
muxi logs --event "error.*"          # All errors
```

## Combining Filters

```bash
# Errors for a specific user
muxi logs -u alice --level error

# Agent activity in a session
muxi logs -s sess_abc123 --agent weather-bot

# Chat events for debugging
muxi logs -u alice --event "chat.*"
```

## Options

| Flag | Description |
|------|-------------|
| `-f, --formation` | Formation ID |
| `-p, --profile` | Server profile |
| `-u, --user` | Filter by user ID |
| `-s, --session` | Filter by session ID |
| `--request` | Filter by request ID |
| `--agent` | Filter by agent ID |
| `--level` | Log level (debug/info/warn/error) |
| `--event` | Event type filter (wildcards supported) |

## Log Output

Logs stream with timestamps in JSON format:

```
[24-Dec-2024 10:30:45 UTC] {"event": "chat.message", "user_id": "alice", ...}
[24-Dec-2024 10:30:46 UTC] {"event": "agent.selected", "agent": "researcher", ...}
```

## Tips

- Start broad (`-u user`) then narrow down with additional filters
- Use `--level error` to quickly find problems
- Use `--event "chat.*"` to follow conversation flow
- Combine `--agent` with `--level debug` for detailed agent debugging
