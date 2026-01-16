---
title: Registry
description: Discover, share, and distribute AI agent formations
---

# MUXI Registry

## The package manager for AI agents

Find pre-built formations, share your own, and manage versions - all from the command line.

<!-- TODO: Add registry screenshot -->
<!-- [Image placeholder: Registry website homepage] -->


## Quick Start

### Pull a Formation

```bash
muxi pull @muxi/starter-assistant
cd starter-assistant
muxi secrets setup
muxi dev
```

### Publish Your Formation

```bash
cd my-formation
muxi auth login
muxi push
```

---

## Browse Formations

Visit [registry.muxi.org](https://registry.muxi.org) or search from CLI:

```bash
muxi search "customer support"
```

Output:

```
NAME                      DESCRIPTION                 DOWNLOADS
@muxi/support-bot         Customer support agent      1,234
@acme/helpdesk           Helpdesk assistant          567
@company/zendesk-agent   Zendesk integration         89
```

---

## Namespaces

| Namespace | Who |
|-----------|-----|
| `@muxi/*` | Official MUXI formations |
| `@username/*` | Individual developers |
| `@org/*` | Organizations |

---

## Official Formations

:::: cols=2

[[card]]
#### @muxi/starter
Minimal assistant - perfect starting point.
```bash
muxi pull @muxi/starter
```
[[/card]]

[[card]]
#### @muxi/research
Web research agent with search tools.
```bash
muxi pull @acme/research
```
[[/card]]

[[card]]
#### @muxi/support
Customer support with knowledge base.
```bash
muxi pull @acme/support
```
[[/card]]

[[card]]
#### @muxi/devops
DevOps assistant with GitHub integration.
```bash
muxi pull @acme/devops
```
[[/card]]

::::

---

## Version Management

### Pull Specific Version

```bash
muxi pull @muxi/starter@1.0.0
```

### Version Ranges

```bash
muxi pull @muxi/starter@^1.0.0   # Latest 1.x.x
muxi pull @muxi/starter@~1.0.0   # Latest 1.0.x
```

---

## Learn More

:::: cols=2

(searching.md)[[card]]
#### Searching
Find formations by keyword, tag, or category.
[[/card]]

(publishing.md)[[card]]
#### Publishing
Share your formations with the world.
[[/card]]

::::
