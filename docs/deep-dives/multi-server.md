---
title: Multi-Server Deployments
description: Share memory across multiple MUXI server instances
---

# Multi-Server Deployments

## Scale horizontally without losing user context

When you run multiple MUXI servers behind a load balancer, each instance needs access to the same memory. Otherwise, a user might chat with Server A, get load-balanced to Server B on the next request, and lose all context.

**The problem:** Load balancers distribute traffic, but they don't share state. If Alice's conversation history lives only on Server A, she gets amnesia when Server B handles her next message.

**The solution:** Centralize both persistent memory (PostgreSQL) and vector memory (FAISSx) so every server instance reads and writes to the same sources of truth. Your load balancer handles traffic distribution; shared storage handles state.

> [!IMPORTANT]
> SQLite is single-node only. For multi-server deployments, you **must** use PostgreSQL for persistent memory.

---

## What You Need

| Component | Purpose | Required? |
|-----------|---------|-----------|
| **PostgreSQL** | Persistent memory (conversations, credentials, user data) | Yes |
| **FAISSx** | Vector memory (embeddings, semantic search) | Yes, if using vector search |
| **L4 Load Balancer** | Distribute traffic to MUXI servers | Yes |

---

## Set Up FAISSx (Vector Memory)

[FAISSx](https://github.com/muxi-ai/faissx) provides shared vector storage over ZeroMQ/TCP with multi-tenant isolation. Run it on your internal network:

```bash
# CLI
faissx.server run --port 45678 --data-dir /data --enable-auth --auth-keys "key1:tenant1,key2:tenant2"

# Docker
docker run -p 45678:45678 \
  -v /path/to/data:/data \
  -e FAISSX_DATA_DIR=/data \
  -e FAISSX_ENABLE_AUTH=true \
  -e FAISSX_AUTH_KEYS="key1:tenant1,key2:tenant2" \
  ghcr.io/muxi-ai/faissx:latest-slim
```

FAISSx listens on ZeroMQ (TCP) — keep it on a private network and require auth keys.

## Load balance FAISSx (L4/TCP)

Use an L4 balancer (e.g., nginx stream, HAProxy) to front multiple FAISSx nodes. HTTP reverse proxies won’t work because FAISSx is not HTTP.

```nginx
stream {
    upstream faissx_pool {
        server faissx-1.internal:45678;
        server faissx-2.internal:45678;
    }

    server {
        listen 45678;
        proxy_pass faissx_pool;
    }
}
```

## Wire MUXI to FAISSx

Point your vector (and optional buffer) memory configuration to the FAISSx ZeroMQ endpoint (e.g., `tcp://faissx-lb.internal:45678`) and provide the API key/tenant ID used above so every formation shares the same index across servers.

Keep FAISSx traffic on internal networks, rotate auth keys periodically, and monitor the service like any other stateful dependency.

## Configure Your Formation

Point all server instances to the same PostgreSQL database and FAISSx endpoint:

```yaml
memory:
  persistent:
    provider: postgres
    connection_string: ${{ secrets.POSTGRES_URI }}
  
  vector:
    provider: faissx
    endpoint: tcp://faissx-lb.internal:45678
    api_key: ${{ secrets.FAISSX_API_KEY }}
```

With this configuration, every server instance shares both long-term (Postgres) and vector (FAISSx) memory. Your load balancer distributes traffic; shared storage maintains state consistency.

---

## Architecture Overview

```
                    ┌─────────────────┐
                    │  Load Balancer  │
                    │   (L4 or L7)    │
                    └────────┬────────┘
                             │
            ┌────────────────┼────────────────┐
            ▼                ▼                ▼
     ┌────────────┐   ┌────────────┐   ┌────────────┐
     │  MUXI #1   │   │  MUXI #2   │   │  MUXI #3   │
     │  (:8001)   │   │  (:8001)   │   │  (:8001)   │
     └─────┬──────┘   └─────┬──────┘   └─────┬──────┘
           │                │                │
           └────────────────┼────────────────┘
                            │
           ┌────────────────┴────────────────┐
           ▼                                 ▼
    ┌────────────┐                   ┌────────────┐
    │ PostgreSQL │                   │  FAISSx    │
    │ (memory)   │                   │ (vectors)  │
    └────────────┘                   └────────────┘
```
