---
title: Local Development
description: Fast iteration with muxi up/down
---
# Local Development

## Fast iteration with muxi up/down

Think of `muxi up` / `muxi down` like `docker compose up` / `docker compose down` - quick start/stop for local development without the full deploy cycle.

## Prerequisites

- `muxi-server` running locally
- A valid formation directory with `formation.afs`

## Quick Start

```bash
# Terminal 1: Start server (one-time)
muxi-server start

# Terminal 2: Start formation
cd my-formation
muxi up
```

**Output:**

```
✓ Started my-formation
✓ Formation running on port 8001

  Draft URL: http://localhost:7890/draft/my-formation

  To stop:   muxi down
```

## Commands

### muxi up

Start formation from current directory.

```bash
cd my-formation
muxi up
muxi up --port 9000    # Use different server port
```

Must be run from inside a formation directory (where `formation.afs` lives).

### muxi down

Stop a running formation.

```bash
muxi down              # Stop formation (reads ID from current dir)
muxi down my-bot       # Stop by ID from anywhere
muxi down --port 9000  # Use different server port
```

## URL Structure

Local dev formations use the `/draft/` prefix to run alongside live formations:

| Mode | URL Pattern | Use Case |
|------|-------------|----------|
| `muxi up` | `http://localhost:7890/draft/{id}/*` | Development |
| `muxi deploy` | `http://server:7890/api/{id}/*` | Production |

This means you can test a draft version while the live version keeps running.

## muxi up vs muxi deploy

| Aspect | `muxi up` | `muxi deploy` |
|--------|-----------|---------------|
| Speed | Instant | Bundles & uploads |
| Source | Runs from local directory | Copies to server storage |
| Persistence | In-memory (gone on restart) | Persisted & restored |
| URL prefix | `/draft/` | `/api/` |
| Rollback | No | Yes |
| Use case | Development | Production |

## Draft Mode

After `muxi up` starts your formation, it will prompt:

```
  Enable draft mode for this formation? (Y/n):
```

When enabled, **all CLI commands** (chat, logs, info, sessions, etc.) automatically route to the draft formation at `/draft/{id}` instead of `/api/{id}`. This is saved to the `.muxi` file in your formation directory.

`muxi down` automatically disables draft mode.

You can also use `--draft` on any command explicitly:

```bash
muxi chat --draft "Hello!"
muxi logs --draft
```

### User ID in Draft Mode

In draft mode, the user ID defaults to `"tester"` if not explicitly set. No need to pass `-u` every time.

For non-PostgreSQL formations (single-user), the user ID defaults to `"default"` in any mode.

## Typical Workflow

```bash
# 1. Start server (once)
muxi-server start

# 2. Develop with fast iteration
cd my-formation
muxi up
# Make changes to formation files...
muxi down
muxi up    # Restart to pick up changes

# 3. Test
muxi chat "Hello!"
curl http://localhost:7890/draft/my-formation/v1/chat

# 4. Deploy when ready
muxi deploy --profile production
```

## Troubleshooting

### Server not running

```
✗ Server not running

  muxi-server is not running on localhost:7890

  muxi-server start        # in another terminal
  muxi-server start &      # run in background
```

Start the server first, then retry `muxi up`.

### Not in formation directory

```
✗ Not in a formation directory

  Run this command from inside a formation directory.

  cd my-formation && muxi up
```

Navigate to a directory containing `formation.afs`.

### Formation already running

If a formation with the same ID is already running as a draft:

```bash
muxi down
muxi up
```
