---
title: Managing Formations
description: Deploy, update, and control formations on MUXI Server
---
# Managing Formations

## Control the lifecycle of your agents

Deploy, update, rollback, and monitor formations with zero-downtime operations. Deploy new formations, update existing ones, rollback changes, and monitor health - all through the CLI or API.

## Deploy

Deploy a formation to the server:

```bash
cd my-formation
muxi deploy
```

Or specify profile:

```bash
muxi deploy --profile production
```

**CLI:** [`muxi deploy`](../cli/deploy.md) | **API:** [`POST /rpc/formations`](/api/server#tag/Formations/POST/rpc/formations)

### Zero-Downtime Updates

**All formation updates and rollbacks happen with zero downtime.** The server uses a blue-green deployment strategy:

1. **New version starts** on a temporary port
2. **Health check passes** before switching traffic
3. **Traffic switches** to new version instantly
4. **Old version stops** after graceful shutdown

During the update:
- ✅ No dropped requests
- ✅ No connection interruptions
- ✅ No service downtime
- ✅ Instant rollback capability

**Example update process:**
```bash
muxi deploy  # New version starts, old version still serving
# Server validates new version...
# Health check passes...
# Traffic instantly switches to new version
# Old version gracefully shuts down
```

If the new version fails health checks, the old version keeps running and the update is automatically aborted.

### Preserved runtime state

An update deploys a new bundle, but some files are **runtime-owned** and belong
to the running formation, not the bundle. The server preserves these from the
live version so an update (or rollback) never wipes them:

- `memory.db` - persistent conversation memory
- `MUXI.md` and `PENDING-MUXI.md` - the formation's self-improvement (tuning)
  state

The live files always win over any copies carried in the uploaded bundle, and
they are carried across a [rollback](#rollback) the same way. See
[Self-Tuning](../concepts/self-tuning.md) for what `MUXI.md` holds.

## List Formations

```bash
muxi remote list
```

**CLI:** [`muxi remote list`](../cli/remote.md#list) | **API:** [`GET /rpc/formations`](/api/server#tag/Formations/GET/rpc/formations)

Output:

```
ID              STATUS    PORT   VERSION
my-assistant    running   8001   1.0.0
support-bot     running   8002   1.2.0
```

## Formation Status

```bash
muxi remote get my-assistant
```

Output:

```
Formation: my-assistant
Status:    running
Port:      8001
Version:   1.0.0
Uptime:    2h 15m
Memory:    128MB
```

## Stop Formation

```bash
muxi remote stop my-assistant
```

**CLI:** [`muxi remote stop`](../cli/remote.md#stop) | **API:** [`POST /rpc/formations/{id}/stop`](/api/server#tag/Formations/POST/rpc/formations/{formation_id}/stop)

## Restart Formation

```bash
muxi remote restart my-assistant
```

**CLI:** [`muxi remote restart`](../cli/remote.md#restart) | **API:** [`POST /rpc/formations/{id}/restart`](/api/server#tag/Formations/POST/rpc/formations/{formation_id}/restart)

## Delete Formation

```bash
muxi remote delete my-assistant
```

**CLI:** [`muxi remote delete`](../cli/remote.md#delete) | **API:** [`DELETE /rpc/formations/{id}`](/api/server#tag/Formations/DELETE/rpc/formations/{formation_id})

## Rollback

Rollback to previous version with **zero downtime**:

**API:** [`POST /rpc/formations/{id}/rollback`](/api/server#tag/Formations/POST/rpc/formations/{formation_id}/rollback)

```bash
muxi remote rollback my-assistant
```

The rollback process is instantaneous:
1. Previous version starts on temporary port
2. Health check passes
3. Traffic switches back to previous version
4. Current version shuts down gracefully

**No requests are dropped during rollback.** The entire process completes in seconds while maintaining full availability.

Rollback preserves the same [runtime-owned state](#preserved-runtime-state) as
an update: `memory.db` and the tuning files (`MUXI.md`, `PENDING-MUXI.md`) are
carried from the current version into the restored one, so rolling back the code
never discards live memory or self-improvement state.

## View Logs

```bash
# Recent logs
muxi logs my-assistant

# Follow logs
muxi logs my-assistant --follow

# Last 100 lines
muxi logs my-assistant --lines 100
```

## Health Check

```bash
curl http://localhost:8001/health
```

Response:

```json
{
  "status": "healthy",
  "agents": ["assistant"],
  "uptime": 8100
}
```

## API Operations

### Deploy via API

```bash
curl -X POST http://localhost:7890/rpc/formations/deploy \
  -H "X-MUXI-Key-ID: ..." \
  -H "X-MUXI-Timestamp: ..." \
  -H "X-MUXI-Signature: ..." \
  -H "Content-Type: application/json" \
  -d '{
    "id": "my-assistant",
    "bundle_path": "/path/to/bundle.tar.gz"
  }'
```

### List via API

```bash
curl http://localhost:7890/rpc/formations \
  -H "X-MUXI-Key-ID: ..." \
  -H "X-MUXI-Timestamp: ..." \
  -H "X-MUXI-Signature: ..."
```

### Stop via API

```bash
curl -X POST http://localhost:7890/rpc/formations/my-assistant/stop \
  -H "X-MUXI-Key-ID: ..." \
  -H "X-MUXI-Timestamp: ..." \
  -H "X-MUXI-Signature: ..."
```

## Multi-Server Deployment

Deploy to multiple servers:

```bash
muxi deploy --profile production
```

With multi-server profile:

```yaml
# ~/.muxi/cli/servers.yaml
profiles:
  production:
    servers:
      - id: us-east
        url: https://east.example.com:7890
      - id: us-west
        url: https://west.example.com:7890
```

## Auto-Restart

Formations auto-restart on crash:

```yaml
# Server config
formations:
  auto_restart: true
  max_restarts: 5
  restart_delay: 5s
```

## Resource Limits

Configure resource limits:

```yaml
# Server config
formations:
  default_memory: 512MB
  max_memory: 2GB
```

## Port Assignment

Formations get assigned ports from configured range:

```yaml
# Server config
formations:
  port_range: [8000, 9000]
```

## Troubleshooting

### Formation Won't Start

Check logs:

```bash
muxi logs my-assistant
```

Common issues:

- Missing secrets
- Invalid configuration
- Port conflict

### Formation Keeps Restarting

Check crash logs:

```bash
muxi logs my-assistant --lines 200
```

Disable auto-restart temporarily:

```yaml
formations:
  auto_restart: false
```

### Port Already in Use

Check what's using the port:

```bash
lsof -i :8001
```

## Next Steps

- [Monitoring](../guides/setup-monitoring.md) - Health and metrics
- [Production](../server/production-checklist.md) - Production deployment
