---
title: muxi deploy
description: Deploy formations to MUXI Server
---
# muxi deploy

## Push your formation to production

Deploy a formation to MUXI Server with zero downtime. The server handles rolling updates, health checks, and automatic restarts.

## Usage

```bash
muxi deploy [options]
```

## Basic Deploy

```bash
cd my-formation
muxi deploy
```

Output:

```
Deploying my-assistant...
✓ Bundled formation
✓ Uploaded to server
✓ Formation started on port 8001
✓ Health check passed

Formation deployed successfully!
API: http://localhost:8001
```

## Options

| Flag | Description |
|------|-------------|
| `--profile <name>` | Server profile to use |
| `--path <dir>` | Formation directory |
| `--validate` | Validate only, don't deploy |
| `--dry-run` | Show what would be deployed |

## Deploy to Profile

```bash
muxi deploy --profile production
```

Uses server from profile:

```yaml
# ~/.muxi/cli/servers.yaml
profiles:
  production:
    url: https://prod.example.com:7890
    key_id: MUXI_production
    secret_key: sk_...
```

## Deploy from Path

```bash
muxi deploy --path /path/to/formation
```

## Validate Only

Check formation without deploying:

```bash
muxi deploy --validate
```

Output:

```
Validating my-assistant...
✓ Schema valid
✓ Agents valid
✓ MCPs valid
✓ Secrets configured

Formation is valid.
```

## Dry Run

```bash
muxi deploy --dry-run
```

Shows what would be deployed without making changes.

## Deploy Process

1. **Validate** - Check formation configuration
2. **Bundle** - Package formation files
3. **Upload** - Send to server
4. **Deploy** - Server starts formation
5. **Health check** - Verify formation is running

## Errors

### Missing Secrets

```
Error: Missing required secrets: OPENAI_API_KEY

Run: muxi secrets setup
```

### Validation Failed

```
Error: Invalid formation

- agents[0].role is required
- mcps[0].server is required
```

Fix the errors and retry.

### Server Unreachable

```
Error: Cannot connect to server

Check:

- Server is running
- Network connectivity
- Profile configuration
```

## Examples

```bash
# Deploy current directory
muxi deploy

# Deploy to production
muxi deploy --profile production

# Deploy specific formation
muxi deploy --path ~/formations/my-bot

# Validate before deploy
muxi deploy --validate
```
