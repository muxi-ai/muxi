---
title: Multi-Server Deployments
description: How to run FAISSx as a shared vector store for formations across multiple servers
---

# Multi-Server Deployments

## When running multiple servers, each formation instance needs access to shared state for memory. This guide covers how to set up centralized memory for multi-server deployments.

> Load balancers only distribute traffic; every formation instance must read/write the same sources of truth for memory. That means **PostgreSQL is required for persistent (long-term) memory** in multi-server setups (SQLite is single-node only), and your vector/buffer memory must point to a shared service (e.g., FAISSx).

## Shared state requirements

- **Persistent memory (long-term):** PostgreSQL only. Configure all servers to the same DB so conversations, credentials, and identifiers stay consistent.
- **Vector/buffer memory (short-term + embeddings):** Centralize via FAISSx (ZeroMQ/TCP) so embeddings and buffer context are shared across servers.
- **Load balancer:** L4/L7 balancer fronts MUXI servers for ingress; it does **not** provide shared state. Ensure both DB (Postgres) and FAISSx endpoints are reachable from all servers.
Use [FAISSx](https://github.com/muxi-ai/faissx) as a remote vector service when formations run on multiple servers. FAISSx extends FAISS over ZeroMQ (TCP) with API-key/tenant isolation.

## When to use
- You need one shared vector index across multiple MUXI servers.
- You want multi-tenant isolation via API keys/tenant IDs.
- You prefer centralizing memory instead of per-node vector stores.

## Run FAISSx

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

## Configure persistent memory (PostgreSQL)

All servers must point to the **same** Postgres database for long-term memory. SQLite is not supported for multi-server deployments.

```yaml
memory:
  persistent:
    provider: postgres
    connection_string: ${{ secrets.POSTGRES_URI }}

memory:
  vector:
    provider: faissx
    endpoint: tcp://faissx-lb.internal:45678
    api_key: ${{ secrets.FAISSX_API_KEY }}
```

With Postgres + FAISSx, every server instance shares both long-term (DB) and short-term/vector memory, while the load balancer simply distributes incoming traffic.
