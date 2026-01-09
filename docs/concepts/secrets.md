---
title: Secrets & Security
description: Why MUXI uses encrypted secrets instead of environment variables
---
# Secrets & Security

## Why MUXI uses encrypted secrets instead of environment variables


MUXI uses encrypted files instead of environment variables. Here's why - and how it keeps your credentials safer.


## The Problem with Environment Variables

Environment variables are the default for secrets, but they have serious issues:

### They're Visible Everywhere

```bash
# Anyone on the system can see them
ps auxe | grep my-app
cat /proc/1234/environ
```

Environment variables are visible to:

- All child processes
- Process monitors
- System administrators
- Crash dumps
- Container inspection tools

### They Leak in Logs

```bash
# Accidentally logged
logger "Starting with API_KEY=$API_KEY"

# In error messages
Error: connection failed (DATABASE_URL=postgres://user:pass@host/db)

# In CI/CD output
+ export OPENAI_API_KEY=sk-abc123...
```

### They're Not Portable

```bash
# Works locally
export OPENAI_API_KEY=sk-...
python app.py

# Now deploy to 5 servers...
# Each needs manual env var setup
# Easy to misconfigure
# No versioning
```

---

## MUXI's Solution

### Encrypted Files

```
formation/
├── formation.afs     # References ${{ secrets.KEY }}
├── secrets.enc       # Encrypted (safe to commit)
├── secrets   # Template (safe to commit)
└── .key              # Encryption key (NEVER commit)
```

### Separation of Concerns

| File | Contains | Commit? | Share? |
|------|----------|---------|--------|
| `secrets.enc` | Encrypted secrets | ✓ Yes | ✓ Yes |
| `secrets` | Required keys (no values) | ✓ Yes | ✓ Yes |
| `.key` | Encryption key | ✗ Never | Securely |

---

## How It's Better

### 1. Safe to Share

```bash
# The formation repo contains secrets.enc
git clone https://github.com/team/formation.git

# Team member gets .key securely (vault, password manager)
# Then just works
muxi dev
```

Compare to environment variables:

- Document all required vars somewhere
- Each person sets them up manually
- Easy to miss one, typo another

### 2. Portable

Secrets travel with the formation:

```bash
# Deploy anywhere
muxi deploy --profile production
muxi deploy --profile staging
muxi deploy --profile local
```

Same formation, same secrets file, different servers.

### 3. Not in Memory

```bash
# Environment variables
$ env | grep API
OPENAI_API_KEY=sk-abc123...

# MUXI secrets
$ env | grep API
# Nothing!
```

Secrets are:

- Decrypted only when needed
- Passed directly to LLM/tool calls
- Never stored in process environment
- Not visible to `ps`, `top`, or debuggers

### 4. Self-Documenting

```yaml
# formation.afs shows what secrets are used
llm:
  api_keys:
    openai: ${{ secrets.OPENAI_API_KEY }}

mcps:
  - id: search
    config:
      api_key: ${{ secrets.BRAVE_API_KEY }}
```

```bash
# secrets shows what's required
OPENAI_API_KEY=
BRAVE_API_KEY=
```

---

## Security Model

```mermaid
graph LR
    A[.key] -->|decrypts| B[secrets.enc]
    B -->|produces| C[Plaintext secrets]
    C -->|injected into| D[LLM/Tool calls]
```

- **AES-256 encryption** - Industry standard
- **Key never stored with secrets** - Physical separation
- **No recovery without key** - Security feature, not bug

---

## Common Questions

[[toggle Why not just use a secrets manager?]]
You can! For enterprise deployments, store `.key` in:

- HashiCorp Vault
- AWS Secrets Manager
- Azure Key Vault
- Google Secret Manager

MUXI's approach works standalone AND with secret managers.
[[/toggle]]

[[toggle What if I lose the .key file?]]
You must re-enter all secrets:

```bash
rm secrets.enc
muxi secrets setup
```

This is intentional. There's no backdoor.
[[/toggle]]

[[toggle Can I use environment variables anyway?]]
Not recommended, but the runtime can read them as fallback for specific deployment scenarios. The encrypted approach is strongly preferred.
[[/toggle]]

---

## Best Practices

> [!CAUTION]
> Never commit `.key` to version control. Ever.

1. **Store `.key` in a password manager** - 1Password, Bitwarden, etc.
2. **Backup `.key` separately** - Not in the same place as the repo
3. **Rotate periodically** - Regenerate secrets on a schedule
4. **Use descriptive names** - `OPENAI_API_KEY` not `KEY1`
5. **Keep `secrets` updated** - Document all required secrets

---

## Migration from Environment Variables

[[tabs]]

[[tab Before (env vars)]]
```bash
# .env file (risky to commit)
OPENAI_API_KEY=sk-abc123
BRAVE_API_KEY=BSA-xyz789

# Load and run
source .env
python app.py
```
[[/tab]]

[[tab After (MUXI)]]
```bash
# One-time setup
muxi secrets setup
# Enter: sk-abc123
# Enter: BSA-xyz789

# Run (secrets auto-loaded)
muxi dev
```
[[/tab]]

[[/tabs]]

---

## Next Steps

[+] [Secrets Reference](../reference/secrets.md) - How to manage secrets
[+] [Security Model](../deep-dives/security.md) - Full security architecture
