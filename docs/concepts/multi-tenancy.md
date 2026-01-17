---
title: Multi-Tenancy
description: How MUXI isolates users, data, and credentials
---
# Multi-Tenancy

## Isolation by default for users, data, and tools

MUXI formations are multi-tenant: every user gets isolated sessions, memory, and credentials. One deployment can safely serve many customers or end-users.

---

## What's Isolated

| Layer | Isolation Method | Description |
|-------|-----------------|-------------|
| **Sessions** | User ID binding | Each session belongs to one user; cross-user access returns 404 |
| **Buffer memory** | Namespace partitioning | Conversations stored in `user_id`-prefixed namespaces |
| **Persistent memory** | Row-level security | All queries filtered by `user_id` column |
| **Credentials** | Per-user encryption | Each user's API keys encrypted separately |
| **Tools** | Runtime injection | MCP servers receive only the calling user's secrets |

---

## How It Works

### 1. User Identification

Every request includes a user identifier:

```http
POST /v1/chat HTTP/1.1
X-Muxi-Client-Key: fmc_...
X-Muxi-User-Id: tenant_acme:user_123
```

For multi-tenant SaaS, use compound identifiers (`tenant:user`) or separate headers:

```http
X-Muxi-Tenant-Id: acme
X-Muxi-User-Id: user_123
```

### 2. Buffer Memory Namespacing

Buffer memory (recent conversation context) is automatically namespaced per user:

```
User A sends: "My API key is abc123"
  → Stored in buffer namespace: user_a

User B asks: "What's my API key?"
  → Searches buffer namespace: user_b
  → Finds nothing (isolated)
```

This happens automatically - no configuration needed. Each user's conversation history is completely separate.

### 3. Persistent Memory Isolation

> [!IMPORTANT]
> **PostgreSQL is required for multi-tenant persistent memory.** SQLite does not support row-level security needed for proper user isolation.

Configure PostgreSQL for persistent storage:

```yaml
memory:
  persistent:
    connection_string: "postgres://user:pass@localhost:5432/muxi"
```

MUXI uses PostgreSQL's row-level security (RLS) to enforce isolation:

```sql
-- Every memory row includes user_id
CREATE TABLE memories (
    id UUID PRIMARY KEY,
    user_id VARCHAR(255) NOT NULL,
    content TEXT,
    embedding VECTOR(1536)
);

-- RLS policy ensures users only see their own data
CREATE POLICY user_isolation ON memories
    USING (user_id = current_setting('app.current_user_id'));
```

All queries are automatically filtered - no way for User A to access User B's memories.

### 4. Per-User Credentials

Users bring their own API keys for external services. See [User Credentials](./user-credentials.md) for the full guide.

```yaml
# mcp/github.afs
schema: "1.0.0"
id: github
type: command
command: npx
args: ["-y", "@modelcontextprotocol/server-github"]
auth:
  type: env
  GITHUB_TOKEN: "${{ user.credentials.GITHUB }}"
```

At runtime:
1. Request arrives with `X-Muxi-User-Id: alice`
2. MUXI looks up `alice`'s `GITHUB_TOKEN`
3. Decrypts and injects into the MCP environment
4. Tool accesses Alice's GitHub, never Bob's

---

## Configuration

### Minimal Multi-Tenant Setup

```yaml
# formation.afs
schema: "1.0.0"
id: multi-tenant-bot

memory:
  persistent:
    connection_string: "${{ secrets.POSTGRES_URL }}"
```

That's it. User isolation is automatic once you:
1. Use PostgreSQL for persistent memory
2. Pass `X-Muxi-User-Id` header on requests

### Working Memory for Scale

For high-traffic multi-tenant deployments, use remote FAISSx with tenant partitioning:

```yaml
memory:
  working:
    mode: "remote"
    remote:
      url: "tcp://faissx.internal:8000"
      api_key: "${{ secrets.FAISSX_API_KEY }}"
      tenant: "${{ secrets.FAISSX_TENANT_ID }}"
```

Each tenant gets separate vector indices for optimal performance.

---

## Best Practices

**Do:**
- Always use PostgreSQL for production multi-tenant deployments
- Use compound user IDs (`tenant:user`) for SaaS applications
- Require user-provided credentials for external services
- Enable audit logging to trace per-user actions

**Don't:**
- Use SQLite for multi-tenant persistent memory (no RLS support)
- Share API keys across users
- Trust client-provided user IDs without authentication

> [!TIP]
> **Start simple.** Buffer memory isolation works automatically. Add PostgreSQL for persistent memory when you need cross-session recall. Add FAISSx when you need to scale beyond a single instance.

---

## Learn More

- [User Credentials](./user-credentials.md) - Per-user API keys and secrets
- [Memory System](./memory.md) - How the four memory layers work
- [Deep dive: Multi-Tenancy](../deep-dives/multi-tenancy.md) - Technical implementation details
- [Authentication](../server/authentication.md) - API key and header requirements
