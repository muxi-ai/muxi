---
title: Monitoring & Logs
description: Monitor your formations and debug issues in production
---
# Monitoring & Logs

## Know what your agents are doing

Monitor formation health, track request latency, and debug issues with structured logs and metrics. This guide covers the built-in monitoring and integration with external tools.

## Quick Health Check

```bash
# Server health
curl http://localhost:7890/health

# Formation health
curl http://localhost:8001/health
```

## View Logs

### CLI

```bash
# Recent logs
muxi logs my-assistant

# Follow logs
muxi logs my-assistant --follow

# Last 100 lines
muxi logs my-assistant --lines 100
```

### Direct

```bash
# Server logs
journalctl -u muxi-server -f

# Formation logs
tail -f ~/.muxi/server/logs/my-assistant.log
```

## Formation Status

```bash
muxi server list
```

Output:

```
ID              STATUS    PORT   UPTIME    MEMORY
my-assistant    running   8001   2h 15m    128MB
support-bot     running   8002   1d 3h     256MB
```

Detailed status:

```bash
muxi server get my-assistant
```

## Log Levels

Configure in server config:

```yaml
logging:
  level: info    # debug, info, warn, error
  format: json   # json or text
```

## Key Metrics

Watch for:

| Metric | What to Monitor |
|--------|-----------------|
| Health status | Should be "healthy" |
| Memory usage | Growth over time |
| Response time | Latency trends |
| Error rate | Spikes |

## Alerting

### Simple Script

```bash
#!/bin/bash
if ! curl -sf http://localhost:8001/health > /dev/null; then
    echo "Formation unhealthy!" | mail -s "MUXI Alert" admin@example.com
fi
```

Run via cron:

```bash
* * * * * /path/to/health-check.sh
```

### External Monitoring

Use services like:

- Pingdom
- UptimeRobot
- Datadog

## Log Aggregation

Forward logs to centralized system:

### Datadog

```yaml
logging:
  format: json
  output: stdout
```

Datadog agent collects stdout.

### Elastic

Use Filebeat:

```yaml
filebeat.inputs:
  - type: log
    paths:
      - /var/log/muxi/*.log
    json.keys_under_root: true
```

## Troubleshooting

### Formation Not Responding

1. Check health endpoint
2. View recent logs
3. Check memory usage
4. Restart if needed

```bash
muxi server restart my-assistant
```

### High Memory

```bash
# Check memory
muxi server get my-assistant

# Restart to clear
muxi server restart my-assistant
```

### Slow Responses

Check logs for slow operations:

```bash
muxi logs my-assistant | grep -i slow
```

## Next Steps

- [Observability Deep Dive](deep-dives/observability.md) - Events system
- [Troubleshooting](troubleshooting.md) - Common issues
