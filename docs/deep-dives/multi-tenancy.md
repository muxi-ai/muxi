---
title: Multi-Tenancy
description: Technical deep dive into user isolation and multi-tenant architectures
---
# Multi-Tenancy

## Technical deep dive into user isolation and multi-tenant architectures


MUXI provides complete isolation between users: separate memory, per-user credentials, and tenant-aware data partitioning.

---

## Isolation Layers

```
┌─────────────────────────────────────────────────────────┐
│                    Request Layer                         │
│         X-Muxi-User-Id: tenant_a:user_123               │
└─────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────┐
│                   Session Layer                          │
│         Sessions bound to user_id                        │
└─────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────┐
│                   Memory Layer                           │
│         All queries filtered by user_id                  │
└─────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────┐
│                  Credential Layer                        │
│         Per-user encrypted credential storage            │
└─────────────────────────────────────────────────────────┘
```

---

## User Identification

### Request Headers

```http
POST /v1/chat HTTP/1.1
X-Muxi-Client-Key: fmc_...
X-Muxi-User-Id: user_123
```

| Header | Required | Default | Description |
|--------|----------|---------|-------------|
| `X-Muxi-User-Id` | No | `"0"` | User identifier |
| `X-Muxi-Tenant-Id` | No | - | Tenant (for multi-tenant) |

### Compound Identifiers

For multi-tenant SaaS:

```
X-Muxi-User-Id: tenant_acme:user_123
```

Or separate headers:

```
X-Muxi-Tenant-Id: acme
X-Muxi-User-Id: user_123
```

Internally normalized to: `acme:user_123`

---

## Session Isolation

### Session Binding

```python
class Session:
    id: str              # sess_abc123
    user_id: str         # tenant:user
    formation_id: str
    created_at: datetime
    messages: List[Message]
```

Sessions are **always** bound to a user. Attempting to access another user's session returns 404.

### Session Validation

```python
def get_session(session_id: str, user_id: str) -> Session:
    session = sessions.get(session_id)
    
    if session is None:
        raise NotFoundError("Session not found")
    
    if session.user_id != user_id:
        # Don't reveal session exists
        raise NotFoundError("Session not found")
    
    return session
```

---

## Memory Isolation

### Database Schema

```sql
-- PostgreSQL with row-level security
CREATE TABLE memories (
    id UUID PRIMARY KEY,
    user_id VARCHAR(255) NOT NULL,
    content TEXT,
    embedding VECTOR(1536)
);

-- Row-level security policy
ALTER TABLE memories ENABLE ROW LEVEL SECURITY;

CREATE POLICY user_isolation ON memories
    USING (user_id = current_setting('app.current_user_id'));
```

### Query Enforcement

Every query includes user context:

```python
def search_memories(query: str, user_id: str):
    # Set session context for RLS
    db.execute(f"SET app.current_user_id = '{user_id}'")
    
    # Query automatically filtered by RLS
    return db.query(
        "SELECT * FROM memories WHERE embedding <-> %s < 0.5",
        embed(query)
    )
```

### Vector Index Partitioning

For large deployments, separate FAISS indices per tenant:

```python
class TenantMemoryManager:
    def __init__(self):
        self.indices = {}  # tenant_id → FAISS index
    
    def get_index(self, tenant_id: str):
        if tenant_id not in self.indices:
            self.indices[tenant_id] = faiss.IndexFlatIP(1536)
        return self.indices[tenant_id]
```

---

## Credential Isolation

### Per-User Secrets

Users can store their own API keys:

```
┌─────────────────────────────────────────────┐
│  User: alice                                 │
│  ├── GITHUB_TOKEN: ghp_alice...             │
│  └── SLACK_TOKEN: xoxb_alice...             │
├─────────────────────────────────────────────┤
│  User: bob                                   │
│  ├── GITHUB_TOKEN: ghp_bob...               │
│  └── NOTION_TOKEN: secret_bob...            │
└─────────────────────────────────────────────┘
```

### Storage

```sql
CREATE TABLE user_secrets (
    user_id VARCHAR(255) NOT NULL,
    key_name VARCHAR(255) NOT NULL,
    encrypted_value BYTEA NOT NULL,
    created_at TIMESTAMP DEFAULT NOW(),
    PRIMARY KEY (user_id, key_name)
);
```

Encryption: AES-256-GCM with per-user derived keys.

### Access Pattern

```yaml
# formation.afs - references user secrets
mcps:
  - id: github
    env:
      GITHUB_TOKEN: ${{ user.secrets.GITHUB_TOKEN }}
```

At runtime:
1. Request includes `X-Muxi-User-Id: alice`
2. MUXI looks up `alice.GITHUB_TOKEN`
3. Decrypts and injects into MCP environment
4. Tool accesses Alice's GitHub, not Bob's

---

## RBAC (Role-Based Access Control)

### Formation-Level Roles

```yaml
# formation.afs
access:
  roles:
    admin:
      - deploy
      - manage
      - chat
      - configure
    developer:
      - chat
      - view_logs
    user:
      - chat

  assignments:
    admin:
      - user:alice
      - user:bob
    developer:
      - group:engineering
    user:
      - "*"  # Everyone
```

### Permission Check

```python
def check_permission(user_id: str, action: str) -> bool:
    roles = get_user_roles(user_id)
    
    for role in roles:
        if action in role.permissions:
            return True
    
    return False
```

---

## Tenant Architecture Patterns

### Shared Formation

One formation, many users:

```
Formation: support-bot
├── User: acme:alice → isolated memory, credentials
├── User: acme:bob   → isolated memory, credentials
├── User: corp:carol → isolated memory, credentials
└── User: corp:dave  → isolated memory, credentials
```

Pros: Simple, efficient
Cons: No tenant-level customization

### Formation per Tenant

Each tenant gets their own formation:

```
Formation: acme-support   → Acme's configuration
Formation: corp-support   → Corp's configuration
```

Pros: Full isolation, customization
Cons: More resources, management overhead

### Hybrid

Shared formation with tenant overrides:

```yaml
# Base formation
id: support-bot

# Tenant overrides
tenants:
  acme:
    agents:
      - id: support
        role: "Support for Acme products..."
  corp:
    agents:
      - id: support
        role: "Support for Corp services..."
```

---

## Performance Considerations

### Index Sizing

| Users | Memory per Index | Recommendation |
|-------|-----------------|----------------|
| <1K | Shared index | Single FAISS |
| 1K-100K | Partitioned | Per-tenant indices |
| >100K | Distributed | Milvus/Pinecone |

### Connection Pooling

```yaml
memory:
  persistent:
    provider: postgresql
    pool:
      min_connections: 5
      max_connections: 20
      per_tenant_limit: 5
```

### Caching

Per-tenant cache partitioning:

```python
cache_key = f"{tenant_id}:{user_id}:{key}"
```

---

## Security Checklist

- [ ] User ID validated on every request
- [ ] Sessions bound to user ID
- [ ] Memory queries filtered by user ID
- [ ] Credentials encrypted per-user
- [ ] RLS enabled on database tables
- [ ] Audit logging includes user context
- [ ] Cross-tenant access tested and blocked

---

## Next Steps

- [Multi-User Concepts](../concepts/memory.md#multi-user-isolation) - High-level overview
- [Security Deep Dive](security.md) - Full security model
- [Authentication](../server/authentication.md) - API authentication
