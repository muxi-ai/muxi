---
title: Access Control (Groups & RBAC)
description: Control who can reach a formation and which agents, tools, and SOPs each group can use
---

# Access Control (Groups & RBAC)

## Who can do what, resolved on every request

MUXI controls access with three pieces that work together:

- **`groups/`** - YAML files defining permission sets (which agents, triggers,
  SOPs, and MCP tools a group can reach).
- **`middleware:`** - the single source of group membership: an MCP server you
  supply that tags each inbound request with the caller's groups.
- **`rbac:`** - the switch that decides whether membership is enforced and what
  happens to a request with no groups.

Permissions resolve once per request (behind a membership + resolution cache)
and filter agent routing, direct agent addressing, workflow planning/execution,
trigger firing (403), SOP matching, and each agent's per-server MCP tool surface.
A denied agent behaves exactly like an unknown one - no information leak. A
formation with no `groups/` directory is unaffected.

> [!IMPORTANT]
> The pre-1.0 `server.auth` key and the `user_groups` membership table have been
> **removed**. Membership now comes only from the `middleware:` server; user-level
> gating is `rbac.fallback: false` plus a middleware that returns no groups for
> unknown users. A formation still carrying `server.auth` fails to load.


## Group files

A `groups/` directory activates permission filtering. Each file is one group;
its id is the filename stem:

```yaml
# groups/support.yaml
inherits: [base]          # optional, cycle-detected

agents:                   # a plain list is an allow-list
  - support-*             # fnmatch globs supported
  - faq

triggers:
  - zendesk-*

sops:
  - customer-onboarding

native_apps:
  allow: ["support-console"]
  deny: ["admin-*"]

memory:
  write:                  # grants for writing shared memory scopes
    - "formation"
```

Across a user's groups, allows are unioned and any deny wins.

### Cascading tool overrides

One `tools: {allow, deny}` structure applies at four levels; the most specific
wins, and `deny` is applied after `allow`:

1. MCP registry catalog (`mcp.servers[].tools`)
2. Agent attachment (`{id, tools}` on an agent's `mcp_servers`)
3. Group per-server
4. Group per-agent-per-server

`tools: {deny: "*"}` hides a server from a group entirely.

```yaml
# groups/support.yaml
mcp_servers:
  web-search:
    tools:
      allow: ["search"]
      deny: ["admin_*"]
```

Per-agent overrides live inside that agent's allow-list entry:

```yaml
agents:
  - support:
      web-search:
        tools:
          allow: ["search", "read-page"]
```

The separate `mcp:` group key is reserved for the `watch_job` quota:

```yaml
mcp:
  watch:
    max_concurrent: 4
```

`allow` / `deny` is the canonical vocabulary at every level; `whitelist` /
`blacklist` remain accepted aliases.


## Membership middleware

Membership comes from a `middleware:` MCP server you run. It exposes exactly one
tool named `middleware`, receives the full request payload (`user_id`,
`message`, `attachments`, `metadata`, `route_class` - never `groups` inbound),
and returns the same-shaped payload, optionally attaching `groups`, rewriting
identity, or applying payload policy.

```yaml
middleware:
  command: python           # stdio server
  args: ["middleware.py"]
  timeout: 5
  # or an http server:
  # url: https://rbac.internal/mcp
  # headers: { Authorization: "Bearer ${{ secrets.RBAC_TOKEN }}" }
```

The pipeline runs after client-key auth, before any processing, on **all**
authenticated inbound traffic (chat, audiochat, triggers, memory routes) and on
internally-originated requests (heartbeat and scheduler jobs synthesize the same
payload with `route_class: "heartbeat"` / `"scheduler"`). It is **fail-closed**:
an error, timeout, or malformed response rejects the request with 403. There is
no runtime-side caching of the middleware result - `timeout` is the only knob.

A shipped one-file stdio template (`contributing/templates/middleware.py` in the
runtime repo) resolves groups from a static map and doubles as a starting point.


## The `rbac:` switch

```yaml
rbac:
  active: auto              # auto = on iff groups/ has files; true = force on; false = kill switch
  fallback: false           # reject no-group requests, or name a group whose permissions apply
```

- `active: true` without any `groups/` files fails the load.
- `active: false` is a loud kill switch.
- `fallback: <group_name>` must reference an existing group.
- Dead config (`active` on + `fallback: false` + no `middleware`) fails the load.


## Shared memory grants

Writing formation- or group-scoped [memory](memory-system.md) requires a
`memory.write` grant in the caller's group YAML (403 without one; globs
supported). Conversation-derived extraction is always user-scoped.


## Learn More

- [Multi-Tenancy](multi-tenancy.md) - per-user isolation this builds on
- [Memory System](memory-system.md) - memory scopes and shared-write grants
- [Tools & MCP](tools-and-mcp.md) - the tool cascade in context
