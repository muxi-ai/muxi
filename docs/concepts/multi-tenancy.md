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

### Typical setup

```yaml
security:
  users:
    auth: hmac  # or your auth plugin

memory:
  persistence: postgres
  namespace: "{{ user.id }}"  # per-user partitioning

mcps:
  - id: github
    server: "@anthropic/github"
    env:
      GITHUB_TOKEN: "${{ user.secrets.GITHUB_TOKEN }}"
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
