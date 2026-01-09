---
title: Your Account
description: Set up your MUXI Registry account and authentication
---
# Your Account

## Set up your MUXI Registry account and authentication


Your GitHub handle is your registry namespace. Authenticate with GitHub - no separate account needed.


## Authentication

```bash
muxi auth login
```

This opens your browser to authenticate with GitHub. Once complete:

```
✓ Authenticated as @alice
✓ Registry access granted
```

Your formations publish under `@alice/*`.

---

## Your Namespace

Your GitHub username becomes your registry namespace:

```bash
# Publish under your username
muxi push
# → @alice/my-formation
```

> [!NOTE]
> Organization namespaces (`@org/*`) are planned for future releases.

---

## Profile Information

Your registry profile is pulled from GitHub:


- **Username** - Your handle
- **Avatar** - Your GitHub avatar
- **Bio** - Your GitHub bio (optional)
- **URL** - Your GitHub profile or website


Update these on GitHub; they sync automatically.

---

## Check Authentication

```bash
muxi auth status
```

```
Authenticated as: @alice
Email: alice@example.com
```

---

## Logout

```bash
muxi auth logout
```

Revokes the stored token. You'll need to re-authenticate to push.

---

## Token Storage

Authentication tokens are stored locally:

| Platform | Location |
|----------|----------|
| macOS | Keychain |
| Linux | `~/.muxi/cli/auth.json` (encrypted) |
| Windows | Credential Manager |

Tokens are scoped to registry access only - MUXI cannot access your GitHub repos.

---

## Publishing Formations

Publish formations to the registry:

```bash
muxi push              # Publish to your namespace
```

> [!NOTE]
> Visibility controls (public/private), organization access, team permissions, and CI/CD tokens are planned for future releases.

---

## Next Steps

- [Publishing](publishing.md) - Publish your first formation
- [Versioning](versioning.md) - Version management
