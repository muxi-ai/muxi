---
title: Security
description: MUXI's security model and best practices
---

# Security

## Defense in depth for AI agents

MUXI implements multiple security layers: transport encryption, authentication, secrets encryption, and user isolation.


## Security Layers

```
┌─────────────────────────────────────────┐
│             Internet                     │
└────────────────┬────────────────────────┘
                 ↓
┌─────────────────────────────────────────┐
│   TLS/HTTPS (Transport Encryption)      │
└────────────────┬────────────────────────┘
                 ↓
┌─────────────────────────────────────────┐
│   HMAC Authentication (Server API)      │
└────────────────┬────────────────────────┘
                 ↓
┌─────────────────────────────────────────┐
│   API Keys (Formation Access)           │
└────────────────┬────────────────────────┘
                 ↓
┌─────────────────────────────────────────┐
│   User Isolation (Data Separation)      │
└────────────────┬────────────────────────┘
                 ↓
┌─────────────────────────────────────────┐
│   Encrypted Secrets (Credential Store)  │
└─────────────────────────────────────────┘
```

---

## Authentication

### Server API (HMAC)

The management API uses HMAC-SHA256 signatures:

```
Signature = HMAC-SHA256(secret, timestamp + method + path + body_hash)
```

Headers:
```
X-MUXI-Key-ID: MUXI_abc123
X-MUXI-Timestamp: 1705484123
X-MUXI-Signature: base64(signature)
```

Properties:

- **Time-limited** - 5 minute window prevents replay
- **Request-bound** - Signature includes request body
- **Tamper-proof** - Any modification invalidates signature

### Formation API (Keys)

Formations use simpler API keys:

| Key Type | Header | Access |
|----------|--------|--------|
| Admin | `X-Muxi-Admin-Key` | Full access |
| Client | `X-Muxi-Client-Key` | Chat only |

---

## Secrets Encryption

### Why Not Environment Variables

| Risk | Env Vars | MUXI |
|------|----------|------|
| Visible in `ps` output | ✗ Yes | ✓ No |
| Logged in crash dumps | ✗ Yes | ✓ No |
| Leaked in CI/CD logs | ✗ Often | ✓ Never |

### Encryption Details

- **Algorithm**: AES-256-GCM
- **Key derivation**: PBKDF2
- **Separation**: `.key` stored separately from `secrets.enc`

```
formation/
├── secrets.enc    # Safe to commit
└── .key           # NEVER commit
```

> [!CAUTION]
> There's no recovery without the `.key` file. This is intentional security.

---

## User Isolation

### Memory Isolation

Each user's data is completely separated:

```
User A: "My API key is xyz"
        ↓ Stored under user_a

User B: "What's my API key?"
        ↓ Searches user_b namespace
        → "I don't have that information"
```

### Multi-Tenant Support

Use compound user IDs for tenant isolation:

```
X-Muxi-User-Id: tenant_a:user_123
X-Muxi-User-Id: tenant_b:user_456
```

---

## Tool Security

### Filesystem Restrictions

```yaml
mcps:
  - id: filesystem
    server: "@anthropic/filesystem"
    config:
      allowed_directories:
        - /home/user/documents    # ✓ Allowed
        - /home/user/projects     # ✓ Allowed
        # /etc                    # ✗ Not listed
        # /root                   # ✗ Not listed
```

> [!WARNING]
> Never allow root (`/`) or sensitive directories in `allowed_directories`.

### Credential Isolation

Each tool gets only its required secrets:

```yaml
mcps:
  - id: github
    env:
      GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      # Cannot access OPENAI_API_KEY

  - id: database
    config:
      connection_string: ${{ secrets.DATABASE_URL }}
      # Cannot access GITHUB_TOKEN
```

---

## Network Security

### TLS Configuration

Always use TLS in production:

```nginx
ssl_protocols TLSv1.2 TLSv1.3;
ssl_ciphers ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256;
ssl_prefer_server_ciphers on;
```

### Firewall Rules

```bash
# Allow HTTPS
sudo ufw allow 443/tcp

# Block direct server access (proxy handles it)
sudo ufw deny 7890/tcp

# Block formation ports from outside
sudo ufw deny 8000:9000/tcp
```

---

## Production Checklist

- [ ] **Auth enabled**: `auth.enabled: true`
- [ ] **TLS configured**: HTTPS on all endpoints
- [ ] **Secrets encrypted**: Using `.key` + `secrets.enc`
- [ ] **.key secured**: Not in git, backed up safely
- [ ] **Firewall set**: Only 443 exposed
- [ ] **Tool paths restricted**: Minimal `allowed_directories`
- [ ] **Keys rotated**: Regular rotation schedule
- [ ] **Logs reviewed**: Not exposing secrets

---

## Vulnerability Reporting

Report security issues to: **security@muxi.org**

We follow responsible disclosure and will:
1. Acknowledge within 24 hours
2. Investigate and provide timeline
3. Credit reporters (unless anonymity requested)

---

## Security Defaults

MUXI ships secure by default:

| Setting | Default | Why |
|---------|---------|-----|
| Auth | Enabled | Prevent unauthorized access |
| Secrets | Encrypted | No env var leakage |
| Filesystem | Restricted | No unrestricted access |
| Logging | No secrets | Secrets never logged |

---

## Next Steps

[+] [Authentication](../server/authentication.md) - HMAC setup details
[+] [Secrets](../reference/secrets.md) - Managing credentials
[+] [Production](../server/production.md) - Deployment hardening
