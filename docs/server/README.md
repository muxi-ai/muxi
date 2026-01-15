---
title: Server
description: The orchestration platform for running MUXI formations
---

# MUXI Server

## The orchestration platform for AI agents

MUXI Server manages your formations - deploying, routing, monitoring, and auto-restarting them. Think of it as **Docker + nginx + PM2** for AI agents.


## What It Does

```
┌─────────────────────────────────────────┐
│ MUXI Server (Port 7890)                 │
│                                         │
│   /rpc/*  → Management API (HMAC auth)  │
│   /api/*  → Formation Proxy             │
│   /health → Health check                │
└────────────────────┬────────────────────┘
                     │
        ┌────────────┼────────────┐
        ↓            ↓            ↓
   ┌─────────┐  ┌─────────┐  ┌─────────┐
   │ Form. A │  │ Form. B │  │ Form. C │
   │  :8001  │  │  :8002  │  │  :8003  │
   └─────────┘  └─────────┘  └─────────┘
```

- **Deploy** formations with one command
- **Route** requests to the right formation
- **Authenticate** using HMAC signatures
- **Monitor** health and auto-restart on failure
- **Scale** across multiple formations

---

## Quick Start

[[steps]]

[[step Install]]

```bash
brew install muxi-ai/tap/muxi
```
[[/step]]

[[step Initialize]]

```bash
muxi-server init
```

Save the generated credentials!
[[/step]]

[[step Start]]

```bash
muxi-server start
```
[[/step]]

[[step Verify]]

```bash
curl http://localhost:7890/health
```
[[/step]]

[[/steps]]

---

## Server Commands

| Command | Description |
|---------|-------------|
| `muxi-server init` | Generate config and credentials |
| `muxi-server start` | Start server (foreground) |
| `muxi-server serve` | Start server (alias) |
| `muxi-server status` | Check server status |
| `muxi-server version` | Show version |

---

## Managing Formations

Use the CLI to manage deployed formations:

```bash
muxi deploy                    # Deploy a formation
muxi formation list            # List all formations
muxi formation stop <id>       # Stop a formation
muxi formation restart <id>    # Restart a formation
muxi logs --follow             # Stream logs
```

---

## Key Features

:::: cols=2

[[card]]
#### HMAC Authentication
Cryptographically signed requests prevent unauthorized access. Time-limited, replay-resistant.
[[/card]]

[[card]]
#### Auto-Restart
Crashed formations restart automatically. Configure retry limits and delays.
[[/card]]

[[card]]
#### Health Monitoring
Continuous health checks detect issues. Automatic recovery when problems occur.
[[/card]]

[[card]]
#### Request Routing
Intelligent routing to formations. Proxy handles load and failover.
[[/card]]

::::

---

## Configuration Preview

```yaml
# ~/.muxi/server/config.yaml
server:
  port: 7890
  host: 0.0.0.0

auth:
  enabled: true
  keys:
    - id: MUXI_production
      secret: sk_...

formations:
  port_range: [8000, 9000]
  auto_restart: true
```

---

## Learn More

:::: cols=2

(configuration.md)[[card]]
#### Configuration
All server settings and options.
[[/card]]

(authentication.md)[[card]]
#### Authentication
HMAC setup and API keys.
[[/card]]

(formations.md)[[card]]
#### Managing Formations
Deploy, stop, restart, rollback.
[[/card]]

(monitoring.md)[[card]]
#### Monitoring
Health checks, logs, metrics.
[[/card]]

(production.md)[[card]]
#### Production
TLS, systemd, best practices.
[[/card]]

::::
