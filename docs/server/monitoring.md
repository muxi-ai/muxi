---
title: Monitoring
description: Monitor MUXI Server health and formation performance
---
# Monitoring

## Keep your agents healthy

Monitor server health, formation status, request latency, and resource usage. MUXI exposes health endpoints and Prometheus metrics for integration with your monitoring stack.

## Health Endpoints

### Server Health

```bash
curl http://localhost:7890/health
```

```json
{
  "status": "healthy",
  "formations": 3,
  "uptime": 86400
}
```

### Formation Health

```bash
curl http://localhost:8001/health
```

```json
{
  "status": "healthy",
  "agents": ["assistant", "researcher"],
  "uptime": 3600
}
```

## Logs

### Server Logs

```bash
# If running in foreground
# Logs output to stdout

# If running as service
journalctl -u muxi-server -f
```

### Formation Logs

```bash
# Via CLI
muxi logs my-assistant --follow

# Direct file access
tail -f ~/.muxi/server/logs/my-assistant.log
```

### Log Levels

```yaml
# Server config
logging:
  level: debug    # debug, info, warn, error
```

## Metrics

### Server Status

```bash
muxi-server status
```

```
MUXI Server Status
==================
Status:     running
Port:       7890
Uptime:     24h 15m
Formations: 3 running, 0 stopped
Memory:     256MB
```

### Formation Status

```bash
muxi formation list
```

```
ID              STATUS    PORT   UPTIME    MEMORY
my-assistant    running   8001   2h 15m    128MB
support-bot     running   8002   1d 3h     256MB
research-bot    stopped   -      -         -
```

## Log Format

### JSON Format (Production)

```yaml
logging:
  format: json
```

```json
{"level":"info","time":"2025-01-08T10:00:00Z","message":"Formation started","formation":"my-assistant","port":8001}
```

### Text Format (Development)

```yaml
logging:
  format: text
```

```
2025-01-08 10:00:00 INFO Formation started formation=my-assistant port=8001
```

## Observability Events

MUXI emits 349 typed events for observability.

### Event Categories

| Category | Events | Description |
|----------|--------|-------------|
| System | 120 | Server lifecycle |
| Conversation | 157 | Request processing |
| Server | 9 | HTTP operations |
| API | 2 | API events |
| Error | 61 | Error conditions |

### Streaming Events

```bash
curl http://localhost:8001/v1/events \
  -H "X-MUXI-Admin-Key: ..." \
  -H "Accept: text/event-stream"
```

### Event Integration

Forward events to observability platforms:

- Datadog
- Splunk
- Elastic
- Custom webhook

## Alerting

### Health Check Alerts

Monitor health endpoint:

```bash
# Simple check
curl -f http://localhost:7890/health || alert

# With timeout
curl -f --max-time 5 http://localhost:7890/health || alert
```

### Log-Based Alerts

Watch for error patterns:

```bash
tail -f /var/log/muxi/server.log | grep -i error
```

## Dashboard Integration

### Prometheus Metrics

Export metrics for Prometheus (if enabled):

```bash
curl http://localhost:7890/metrics
```

### Grafana

Import MUXI dashboard for visualization.

## Troubleshooting

### Formation Not Responding

1. Check health endpoint
2. View logs for errors
3. Check resource usage
4. Restart if needed

```bash
muxi formation restart my-assistant
```

### High Memory Usage

Check formation memory:

```bash
muxi formation get my-assistant
```

Restart to clear memory:

```bash
muxi formation restart my-assistant
```

### Slow Responses

Check logs for slow operations:

```bash
muxi logs my-assistant | grep -i slow
```

## Next Steps

- [Production](production.md) - Production deployment
- [Deep Dives: Observability](../deep-dives/observability.md) - Event details
