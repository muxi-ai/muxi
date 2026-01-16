---
title: User Credentials
description: How each user brings their own API keys for personalized tool access
---
# User Credentials

## How each user brings their own API keys for personalized tool access


User credentials let each person use their own API keys with shared agents. Same formation, personalized access - your GitHub, your Slack, your data.


## Why Per-User Credentials?

**Traditional approach** (shared credentials):
```
Formation uses one GitHub token
  → Alice sees Bob's repos
  → Bob sees Alice's repos
  → No privacy
  → Compliance nightmare
```

**MUXI approach** (per-user credentials):
```
Alice's GitHub token → Alice's repos only
Bob's GitHub token → Bob's repos only
  → Complete isolation
  → Privacy by default
  → Audit trail per user
```

Each user brings their own credentials. Agents automatically use the right ones.

---

## How It Works

### Automatic Credential Detection

Agents know when they need credentials:

```
User:  "Show my GitHub repos"
Agent: Detects GitHub tool needs credentials
Agent: Checks if user has GitHub token stored
  → Yes: Uses it automatically
  → No: Asks user to add it
```

No manual configuration. The agent figures it out.

**Operational details:**
- Uses the MCP registry from your formation to know which services need credentials and whether inline collection is allowed (`accept_inline`).
- Validates tokens before storing; rejects invalid/duplicate tokens.
- If multiple accounts exist, agents (or clarification) will ask which one to use.

### Encrypted Storage

Your credentials are encrypted before storage:

```
User provides: ghp_abc123xyz
         ↓
Encryption (per-user key)
         ↓
Stored: 2j8f9a0s8df7a0s8df...
         ↓
Used by agent: ghp_abc123xyz (decrypted)
```

**Security features:**
- Per-user encryption keys
- Credentials never in logs
- No cross-user access
- Industry-standard AES-256

---

## Multiple Accounts

You can have multiple accounts for the same service:

```
GitHub:
  - ranaroussi (personal)
  - muxi-ai (work)

Slack:
  - acme-corp
  - freelance-clients
```

The agent asks which one to use:

```
User:  "Check my GitHub notifications"
Agent: You have two GitHub accounts: ranaroussi and muxi-ai. Which one?
User:  "ranaroussi"
Agent: [Uses ranaroussi's credentials]
```

---

## Two Modes

### Redirect Mode (Enterprise)

For organizations with existing credential management:

```
User:  I need to add my GitHub token
Agent: Please configure GitHub credentials in the company portal at
       https://credentials.company.com
```

**Best for:**
- Enterprise environments
- Compliance requirements
- Centralized credential management
- IT-controlled access

### Dynamic Mode (Developer)

For individual use and development:

```
User:  "Add my GitHub token"
Agent: Please provide your GitHub personal access token:
User: [provides token]
Agent: [Validates, discovers identity]
Agent: Successfully added GitHub account 'ranaroussi'
```

**Best for:**
- Developer environments
- Personal use
- Rapid setup
- Self-service

Configure the mode in your formation:

```yaml
user_credentials:
  mode: "dynamic"  # or "redirect"
  redirect_message: "Add credentials at https://credentials.example.com"

# Optional hardening (see formation schema):
# user_credentials:
#   encryption:
#     key: "${{ secrets.CREDENTIAL_KEY }}"  # otherwise derives from formation_id
#     salt: "muxi-user-credentials-salt-v1"
#   require_https: true          # default true for inline collection
#   credential_ttl_minutes: 60   # cache lifetime for inline tokens
#   max_attempts: 3              # lockout after failed attempts
```

- **Redirect:** Never collect secrets in chat; send users to your portal/CLI. Best for enterprise compliance.
- **Dynamic:** Collect inline only if the MCP server marks `accept_inline: true`; tokens are validated, deduped, and named by discovered identity (e.g., GitHub handle).

---

## Identity Discovery

When you add credentials, MUXI discovers your actual account identity:

```
User provides: GitHub token ghp_abc123
         ↓
MUXI validates token
         ↓
Discovers: Account is "ranaroussi"
         ↓
Stored as: "ranaroussi" (not "GitHub")
```

**Benefits:**
- Meaningful names instead of "GitHub #1", "GitHub #2"
- Easy to identify which account is which
- Clear selection when you have multiple accounts

---

## Using Credentials in Tools

Reference user credentials in MCP server configuration (`mcp/github.afs`):

```yaml
schema: "1.0.0"
id: github
type: command
command: npx
args: ["-y", "@modelcontextprotocol/server-github"]
auth:
  type: env
  GITHUB_TOKEN: "${{ user.credentials.GITHUB }}"
```

The runtime automatically:
1. Resolves the requesting user's GitHub credential
2. Injects it when calling the MCP server
3. Ensures isolation (User A never gets User B's token)

---

## Security & Isolation

### Complete User Isolation

```
User A requests GitHub data
         ↓
Runtime fetches User A's GitHub token
         ↓
MCP server sees only User A's token
         ↓
Returns only User A's data
```

User B's credentials are never accessible to User A, even if they use the same formation.

### Encryption Details

- **Algorithm**: AES-256-GCM
- **Key derivation**: PBKDF2 with 100,000 iterations
- **Per-user keys**: Each user has unique encryption key
- **Salt**: Formation-specific salt for additional security

### What's Protected

✅ **Encrypted:**
- API tokens
- Access keys
- OAuth tokens
- Any credential values

❌ **Not encrypted** (not sensitive):
- Service names (e.g., "github")
- Account names (e.g., "ranaroussi")
- Timestamps

---

## Duplicate Detection

MUXI prevents storing the same token twice:

```
User:  Add my GitHub token ghp_abc123
Agent: [Checks if token already exists]
        → Found duplicate
Agent: That GitHub token is already stored in your account. You're all set!
```

**Benefits:**
- Avoids unnecessary validation calls
- Clear feedback to users
- Keeps credential list clean

---

## Example Flows

### First-Time Setup (Dynamic Mode)

```
User:  Show my GitHub repositories
Agent: I need access to GitHub. Please provide your personal access token:
User:  [provides token: ghp_xxxxxx]
Agent: Validating...
Agent: Successfully added GitHub account 'ranaroussi'
Agent: [Shows repositories from ranaroussi's account]
```

### Using Multiple Accounts

```
User:  Show my GitHub PRs
Agent: You have two GitHub accounts: ranaroussi (personal) and muxi-ai (work). Which one?
User:  Show PRs from both
Agent: [Fetches PRs from both accounts, labeled by account]
```

### Adding Second Account

```
User:  Add my work GitHub account
Agent: Please provide your GitHub personal access token:
User:  [provides second token]
Agent: Successfully added GitHub account 'muxi-ai'
Agent: You now have 2 GitHub accounts: ranaroussi, muxi-ai
```

---

## Supported Services

Any service that uses:
- **API keys**: Bearer tokens, API keys
- **OAuth**: OAuth 2.0 tokens (with refresh support)
- **Basic auth**: Username/password pairs

Common examples:
- GitHub (Personal Access Tokens)
- Slack (OAuth tokens)
- Google (OAuth 2.0)
- AWS (Access keys)
- OpenAI (API keys)
- Custom APIs

The system is extensible - new services work automatically if they're configured as MCP servers.

---

## Configuration Reference

### Formation Configuration

`formation.afs`:
```yaml
# User credential settings
user_credentials:
  mode: "dynamic"  # "redirect" or "dynamic"
  redirect_message: |
    Configure credentials at: https://credentials.example.com
```

`mcp/github.afs` - MCP server that uses user credentials:
```yaml
schema: "1.0.0"
id: github
type: command
command: npx
args: ["-y", "@modelcontextprotocol/server-github"]
auth:
  type: bearer
  accept_inline: true   # Allow dynamic credential collection
  token: "${{ user.credentials.GITHUB }}"
```

### Mode Comparison

| Feature | Redirect Mode | Dynamic Mode |
|---------|--------------|--------------|
| Credential input | External portal | In-chat |
| Validation | External | Automatic |
| Identity discovery | Manual | Automatic |
| Best for | Enterprise | Developers |
| Compliance | High | Medium |
| Setup speed | Slower | Instant |

---

## When to Use User Credentials

| Use User Credentials For | Use Formation Secrets For |
|-------------------------|--------------------------|
| User-specific data access | Shared service access |
| GitHub, Gmail, Slack | LLM API keys |
| Personalized tools | Infrastructure APIs |
| Multi-tenant formations | Background jobs |
| User privacy required | System-level operations |

**Rule of thumb:** If the data is personal or user-specific, use user credentials. If it's shared across all users, use formation secrets.

---

## Why This Matters

| Shared Credentials | User Credentials |
|-------------------|------------------|
| Everyone sees everything | Users see only their data |
| No audit trail | Per-user audit log |
| Compliance issues | Privacy by default |
| Single point of failure | Distributed security |
| Can't revoke per-user | Individual revocation |

The result: **privacy, security, and compliance** without extra work.

---

## Quick Setup

**Dynamic mode** (fastest):

`formation.afs`:
```yaml
user_credentials:
  mode: "dynamic"
```

`mcp/github.afs`:
```yaml
schema: "1.0.0"
id: github
type: command
command: npx
args: ["-y", "@modelcontextprotocol/server-github"]
auth:
  accept_inline: true
  token: "${{ user.credentials.GITHUB }}"
```

That's it. The agent handles the rest.

---

## Learn More

- [Tools & MCP](tools.md) - How tools use credentials
- [Secrets & Security](secrets.md) - Formation-level secrets
- [Multi-Tenancy Deep Dive](../deep-dives/multi-tenancy.md) - Technical implementation
- [CLI Secrets Management](../cli/secrets.md) - Managing credentials via CLI
