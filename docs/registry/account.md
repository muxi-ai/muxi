---
title: Your Account
description: Set up your MUXI Registry account and authentication
---

# Your Account

## Your GitHub handle is your registry handle

No separate account needed. Authenticate with GitHub and your username becomes your registry namespace.

---

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

| You are... | Your namespace |
|------------|----------------|
| GitHub user `alice` | `@alice/*` |
| GitHub org `acme` | `@acme/*` |

```bash
# Publish as yourself
muxi push
# → @alice/my-formation

# Publish as org (if you're a member)
muxi push --org acme
# → @acme/my-formation
```

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
Organizations: acme, startup
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

## Organization Access

If you're a member of GitHub organizations:

```bash
muxi auth orgs
```

```
Organizations you can publish to:
  @acme (admin)
  @startup (member)
```

Publish to an org:

```bash
muxi push --org acme
```

Requires appropriate org permissions.

---

## API Tokens

For CI/CD, generate a registry token:

```bash
muxi auth token create --name "GitHub Actions"
```

```
Token created: mrt_abc123...
(This will only be shown once)
```

Use in CI:

```bash
export MUXI_REGISTRY_TOKEN=mrt_abc123...
muxi push
```

### Token Management

```bash
muxi auth token list
muxi auth token revoke mrt_abc123
```

---

## Private Formations

By default, formations are private:

```bash
muxi push              # Private (only you)
muxi push --public     # Anyone can pull
muxi push --org acme   # Org members can pull
```

### Sharing Private Formations

Grant access to specific users:

```bash
muxi access grant @bob --formation my-private-bot
muxi access revoke @bob --formation my-private-bot
muxi access list --formation my-private-bot
```

---

## Next Steps

- [Publishing](publishing.md) - Publish your first formation
- [Versioning](versioning.md) - Version management
