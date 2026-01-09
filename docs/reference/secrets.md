---
title: Secrets
description: Secure credential management for formations
---

# Secrets

## Secure credentials without environment variables

MUXI uses encrypted files instead of environment variables. Your API keys stay secure, portable, and never leak in logs.


## Why Not Environment Variables?

| Problem | Environment Variables | MUXI Secrets |
|---------|----------------------|--------------|
| Visible in process lists | ✗ Yes | ✓ No |
| Appear in crash dumps | ✗ Yes | ✓ No |
| Leak in CI/CD logs | ✗ Often | ✓ Never |
| Portable with code | ✗ No | ✓ Yes |
| Safe to commit | ✗ Never | ✓ Encrypted file |

> [!TIP]
> Learn more about the design decision in [Why Encrypted Secrets](../concepts/secrets.md).
>
> **API Reference:**
> - [GET /v1/secrets](api/formation#tag/Secrets/GET/secrets) - List secrets
> - [POST /v1/secrets](api/formation#tag/Secrets/POST/secrets) - Create secret
> - [PUT /v1/secrets/{key}](api/formation#tag/Secrets/PUT/secrets/{key}) - Update secret
> - [DELETE /v1/secrets/{key}](api/formation#tag/Secrets/DELETE/secrets/{key}) - Delete secret

---

## How It Works

```
formation/
├── formation.afs     # References secrets like ${{ secrets.KEY }}
├── secrets.enc       # Encrypted secrets (safe to commit)
├── secrets   # Template showing required secrets
└── .key              # Encryption key (NEVER commit!)
```

Reference secrets in YAML:

```yaml
llm:
  api_keys:
    openai: ${{ secrets.OPENAI_API_KEY }}

mcps:
  - id: github
    env:
      GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

---

## Set Up Secrets

[[steps]]

[[step Run the setup wizard]]

```bash
muxi secrets setup
```

The wizard scans your formation and prompts for each required secret:

```
Setting up secrets for my-assistant...

Required secrets:
  OPENAI_API_KEY (from llm.api_keys)
  BRAVE_API_KEY (from mcps.web-search)

Enter OPENAI_API_KEY: ****
Enter BRAVE_API_KEY: ****

✓ Secrets encrypted and saved
```
[[/step]]

[[step Verify secrets are saved]]

```bash
muxi secrets list
```

Output (keys only, not values):

```
OPENAI_API_KEY
BRAVE_API_KEY
```
[[/step]]

[[/steps]]

---

## Manage Individual Secrets

### Set a Secret

```bash
muxi secrets set GITHUB_TOKEN
# Enter value when prompted
```

Or from a file/pipe:

```bash
echo $GITHUB_TOKEN | muxi secrets set GITHUB_TOKEN --stdin
```

### Get a Secret

```bash
muxi secrets get OPENAI_API_KEY
# Displays the value (use carefully!)
```

### Delete a Secret

```bash
muxi secrets delete OLD_API_KEY
```

---

## File Structure

```
my-formation/
├── secrets.enc        # Encrypted - SAFE to commit
├── secrets    # Template - SAFE to commit
└── .key               # Encryption key - NEVER commit!
```

### .gitignore

Always include:

```gitignore
# MUXI secrets
.key
```

> [!CAUTION]
> Never commit `.key` to version control. Without the key, `secrets.enc` is unreadable - this is a security feature.

### secrets

Document required secrets for your team:

```
# Required secrets for this formation
OPENAI_API_KEY=
BRAVE_API_KEY=
DATABASE_URL=
```

---

## Using Secrets in Formations

### In LLM Config

```yaml
llm:
  api_keys:
    openai: ${{ secrets.OPENAI_API_KEY }}
    anthropic: ${{ secrets.ANTHROPIC_API_KEY }}
```

### In MCP Config

```yaml
mcps:
  - id: database
    server: "@anthropic/postgres"
    config:
      connection_string: ${{ secrets.DATABASE_URL }}
```

### In Environment Variables

```yaml
mcps:
  - id: github
    server: "@anthropic/github"
    env:
      GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

---

## Team Sharing

To share a formation with teammates:

1. **Commit** `secrets.enc` and `secrets` to git
2. **Share `.key` securely** (password manager, secure channel)
3. **Or** have teammates run `muxi secrets setup` with their own keys

---

## Backup and Recovery

### Backup

Store `.key` in a secure location:

- Password manager (1Password, Bitwarden)
- Secret vault (HashiCorp Vault, AWS Secrets Manager)
- Secure backup separate from the repository

### Recovery

If you lose `.key`:

```bash
# No recovery possible - must recreate
rm secrets.enc
muxi secrets setup
# Re-enter all secrets
```

> [!IMPORTANT]
> There's no backdoor. Lost key = re-enter all secrets. This is intentional security.

---

## Troubleshooting

[[toggle "Missing secret" error]]
The secret isn't set. Add it:

```bash
muxi secrets set MISSING_KEY
```
[[/toggle]]

[[toggle "Cannot decrypt" error]]
Wrong `.key` file or corrupted `secrets.enc`:

```bash
rm secrets.enc
muxi secrets setup
```
[[/toggle]]

[[toggle Secrets not loading]]
Check files exist and permissions:

```bash
ls -la secrets.enc .key
# Both should exist and be readable
```
[[/toggle]]

---

## Best Practices

1. **Never commit `.key`** - Add to `.gitignore`
2. **Keep `secrets` updated** - Document all required secrets
3. **Backup `.key` securely** - Can't recover without it
4. **Use descriptive names** - `OPENAI_API_KEY` not `KEY1`
5. **Rotate periodically** - Update secrets regularly
6. **Least privilege** - Each tool gets only the secrets it needs

---

## Next Steps

[+] [Why Encrypted Secrets](../concepts/secrets.md) - Design rationale
[+] [Tools](tools.md) - Use secrets with MCP servers
[+] [Security](../deep-dives/security.md) - Full security model
