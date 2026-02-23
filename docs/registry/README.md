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
muxi pull @muxi/hello-muxi
cd starter-assistant
muxi secrets setup
muxi dev
```

### Publish Your Formation

```bash
cd my-formation
muxi login
muxi push
```


## Browse Formations

Visit [registry.muxi.org](https://registry.muxi.org) or search from CLI:

```bash
muxi search "customer support"
```

Output:

```
NAME                      DESCRIPTION                 DOWNLOADS
@muxi/support-bot         Customer support agent      1,234
@acme/helpdesk            Helpdesk assistant          567
@company/zendesk-agent    Zendesk integration         89
```


## Namespaces

| Namespace | Who |
|-----------|-----|
| `@muxi/*` | Official MUXI formations |
| `@username/*` | Individual developers |
| `@org/*` | Organizations |


## Official Formations

:::: cols=2

[[card]]
#### @muxi/hello-muxi
Minimal assistant - perfect starting point.
```
muxi pull @muxi/hello-muxi
```
[[/card]]

[[card]]
#### @muxi/research
Web research agent with search tools.
```
muxi pull @muxi/research-assistant
```
[[/card]]

[[card]]
#### @muxi/support
Customer support with knowledge base.
```
muxi pull @muxi/customer-support
```
[[/card]]

[[card]]
#### @muxi/bookkeeping
Bookkeeping assistant with Xero integration.
```
muxi pull @muxi/bookkeeping
```
[[/card]]

::::


## Version Management

### Pull Specific Version

```bash
muxi pull @muxi/hello-muxi@1.0.0
```

### Version Ranges

```bash
muxi pull @muxi/hello-muxi@^1.0.0   # Latest 1.x.x
muxi pull @muxi/hello-muxi@~1.0.0   # Latest 1.0.x
```


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
