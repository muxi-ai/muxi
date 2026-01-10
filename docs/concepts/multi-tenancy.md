---
title: Multi-Tenancy
description: How MUXI isolates users, data, and credentials
---
# Multi-Tenancy

## Isolation by default for users, data, and tools

MUXI formations are multi-tenant: every user gets isolated sessions, memory, and credentials. One deployment can safely serve many customers or end-users.

### What’s isolated
- **Sessions & memory:** Conversations, vectors, and transcripts are scoped per user.
- **Credentials:** OAuth tokens/API keys are stored per user; agents use only the caller’s credentials.
- **Tools:** MCP servers run with user-scoped secrets; path and capability restrictions apply per tool.

### How it works

**Authentication:** Requests include `X-Muxi-User-Id` header to identify the caller:

```http
POST /v1/chat HTTP/1.1
X-Muxi-Client-Key: fmc_...
X-Muxi-User-Id: tenant_acme:user_123
```

**Memory isolation:** Configure persistent storage - all queries are automatically filtered by user ID:

```yaml
memory:
  persistent:
    connection_string: "postgres://user:pass@localhost:5432/muxi"
```

**Per-user credentials:** MCP servers can access user-specific secrets:

```yaml
# In your MCP config (mcp/github.yaml)
schema: "1.0.0"
id: github
type: http
endpoint: "https://api.github.com"
auth:
  type: bearer
  token: "${{ user.secrets.GITHUB_TOKEN }}"
```

### Best practices
- Use user-scoped namespaces for memory/storage.
- Require user-provided secrets for external systems.
- Keep tool allowlists tight (paths, databases, APIs).
- Audit with observability events to trace per-user actions.

### Learn more
- [Deep dive: Multi-Tenancy](../deep-dives/multi-tenancy.md)
- [Triggers](./triggers.md)
- [Tools & MCP](./tools.md)
- [Memory](./memory.md)
