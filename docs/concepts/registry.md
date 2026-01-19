---
title: Registry
description: Share and distribute formations like Docker images
---
# Registry

## Share and distribute formations like Docker images


The MUXI Registry is a distribution hub for formations. Pull community formations with one command. Publish your own. Version control, rollbacks, and namespaces built in.

When to use the registry:
- Start from a proven template (`muxi pull @muxi/starter`)
- Share formations with the community
- Version and roll back formations safely


## The Docker Analogy

If you know Docker, you know MUXI:

| Docker | MUXI |
|--------|------|
| `docker pull nginx` | `muxi pull @muxi/starter` |
| `docker push myapp` | `muxi push` |
| `Dockerfile` | `formation.afs` |
| Docker Hub | MUXI Registry |
| Images | Formations |

Formations are **portable, versioned, shareable** - just like container images.


## How It Works

### Pull a Formation

```bash
muxi pull @acme/research-agent
cd research-agent
muxi secrets setup
muxi dev
```

That's it. Someone else's formation, running locally in seconds.

### Push Your Formation

```bash
cd my-formation
muxi push
```

Now anyone can `muxi pull @yourusername/my-formation`.


## Namespaces

| Namespace | Who | Example |
|-----------|-----|---------|
| `@muxi/*` | Official formations | `@muxi/starter` |
| `@username/*` | Individual developers | `@alice/support-bot` |

Your GitHub handle is your registry handle - no separate account needed.

> [!NOTE]
> Private registries are planned for future release.


## Version Control

### Automatic Versioning

Every push increments the version:

```bash
muxi push
# Published @alice/my-bot v1.0.0

# Make changes...
muxi push
# Published @alice/my-bot v1.0.1
```

### Pull Specific Versions

```bash
muxi pull @muxi/starter@1.0.0      # Exact version
muxi pull @muxi/starter@^1.0.0     # Latest 1.x.x
muxi pull @muxi/starter@latest     # Latest (default)
```

### Instant Rollback

```bash
muxi server rollback my-assistant
# Shows available versions, reverts instantly
```

---

> [!NOTE]
> Visibility controls (public/private formations) and private registries are planned for future releases.


## What Gets Published

```
my-formation/
├── formation.afs      ✓ Published
├── agents/            ✓ Published
├── mcp/               ✓ Published
├── sops/              ✓ Published
├── triggers/          ✓ Published
├── knowledge/         ✓ Published (optional)
├── secrets.enc        ❌ Never (encrypted secrets)
├── .key               ❌ Never (encryption key)
└── secrets            ✓ Published (shows required secrets)
```

Secrets never leave your machine. Users who pull must provide their own credentials.


## Discovery

### Browse Online

- Browse by category (support, research, devops)
- View documentation and examples
- Check download stats and ratings

[>] [Visit registry.muxi.org](https://registry.muxi.org)

### Search from CLI

```bash
muxi search "customer support"
```

```
NAME                     DESCRIPTION                 DOWNLOADS
@muxi/support-bot        Customer support agent      1,234
@alice/helpdesk          Helpdesk assistant          567
```


## Why This Matters

| Without Registry | With Registry |
|-----------------|---------------|
| Copy files manually | `muxi pull` |
| No versioning | Semantic versions, rollbacks |
| Scattered documentation | Centralized, searchable |
| Reinvent solutions | Pull and customize |
| Manual distribution | One command sharing |

The result: **formations that spread**, not code that rots in private repos.


## Quick Start

```bash
# Try an official formation
muxi pull @muxi/starter
cd starter
muxi secrets setup
muxi dev

# Publish your own
cd my-formation
muxi login
muxi push
```


## Learn More

- [Registry: Your Account](../registry/account.md) - Setup and authentication
- [Registry: Publishing](../registry/publish-formations.md) - Publish your formations
- [Registry: Versioning](../registry/versioning.md) - Version management
