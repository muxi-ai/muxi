---
title: Multi-Identity Users
description: One user, many identifiers - unified experience across platforms
---
# Multi-Identity Users

## One user, many identifiers - unified experience across platforms

Users interact through multiple channels: Slack, email, web chat, mobile app. Multi-identity lets you link all these identifiers to a single user, so they get the same memory and context everywhere.

## The Problem

Without multi-identity:

```
Web chat (user_123):
  User:  "My budget is $5,000"
  MUXI: Stores preference for user_123

Slack (U98765ABC):
  User:  "What's my budget?"
  MUXI: "I don't have that information"
  ↑ Different identifier = different user = lost context
```

## The Solution

Link identifiers to the same user:

```
alice@email.com ─┐
U98765ABC ───────┼──► Same user (usr_alice)
user_123 ────────┘

Web chat:
  User:  "My budget is $5,000"
  MUXI: Stores for usr_alice

Slack:
  User:  "What's my budget?"
  MUXI: "$5,000" ← Found it!
```

---

## How It Works

### Identifier Resolution

When a request comes in with any identifier:

```
Request with "U98765ABC"
         ↓
Check cache: "U98765ABC" → usr_alice
         ↓
Load user context for usr_alice
         ↓
Process request with full user context
```

First request with a new identifier? MUXI creates a new user:

```
Request with "new@email.com"
         ↓
Not found in cache/DB
         ↓
Create new user: usr_xyz789
Link identifier: "new@email.com" → usr_xyz789
         ↓
Continue with new user
```

### What Gets Unified

When identifiers are linked, users share:

| Shared | Not Shared |
|--------|------------|
| Working memory | Session buffer |
| Persistent memory | Session context |
| User synopsis | Session ID |
| Credentials | |
| Preferences | |
| Scheduled tasks | |

Each session is still separate, but the **user** is the same.

---

## Managing Identities (SDK)

Developers manage identities using the SDK:

### Link Multiple Identifiers

```python
from muxi import FormationClient

formation = FormationClient(...)

# Link email, Slack ID, and internal ID to one user
result = formation.associate_user_identifiers(
    identifiers=[
        "alice@email.com",                    # Email
        {"identifier": "U98765ABC", "type": "slack"},  # Slack
        ("user_123", "internal"),             # Internal ID
    ],
    user_id=None,  # Creates new user, or specify existing
)

print(f"MUXI User ID: {result['muxi_user_id']}")
# Output: usr_abc123
```

### Check User Identifiers

```python
# Get all identifiers for a user
identifiers = formation.get_user_identifiers(user_id="usr_abc123")
# Returns: ["alice@email.com", "U98765ABC", "user_123"]
```

### Add Identifier to Existing User

```python
# User signs up with email, later connects Slack
formation.associate_user_identifiers(
    identifiers=["alice@email.com", ("U98765ABC", "slack")],
    user_id="usr_abc123",  # Link to existing user
)
```

---

## Common Patterns

### Pattern 1: Web + Slack Integration

```python
# User signs up on web with email
response = formation.chat(
    "Hello!",
    user_id="alice@acme.com"
)
# Creates usr_alice linked to email

# Later, admin links Slack workspace
for slack_user in slack_workspace.users:
    formation.associate_user_identifiers(
        identifiers=[slack_user.email, (slack_user.id, "slack")],
        user_id=None,  # Auto-matches by email or creates new
    )

# Now Slack messages have full context
@slack_bot.on_message
def handle_slack(event):
    response = formation.chat(
        event.text,
        user_id=event.user_id  # Slack ID resolves to same user
    )
```

### Pattern 2: OAuth Connections

```python
# User starts with email
formation.chat("Hi", user_id="alice@email.com")

# User connects GitHub OAuth
@app.route("/oauth/github/callback")
def github_callback(github_user_id):
    formation.associate_user_identifiers(
        identifiers=[
            session["email"],
            (github_user_id, "github")
        ],
        user_id=None,  # Links to existing user by email
    )

# Now GitHub webhooks can route to user
@webhook.on("github.issue.created")
def handle_github(event):
    response = formation.chat(
        f"New issue: {event.title}",
        user_id=event.user_id  # GitHub ID → same user
    )
```

### Pattern 3: API Key to User Mapping

```python
# Generate API key for user
api_key = generate_api_key()

formation.associate_user_identifiers(
    identifiers=[
        "alice@email.com",
        (api_key, "api_key")
    ],
    user_id="usr_alice"
)

# API requests authenticated by key
@app.route("/api/chat")
def api_chat():
    api_key = request.headers["Authorization"]
    # API key resolves to alice
    response = formation.chat(request.json["message"], user_id=api_key)
```

---

## Identifier Types

Optional type hints help with observability and organization:

| Type | Example | Use Case |
|------|---------|----------|
| `email` | alice@acme.com | Primary identifier |
| `slack` | U12345ABC | Slack integration |
| `github` | github_456 | GitHub OAuth |
| `telegram` | tg_789 | Telegram bot |
| `discord` | discord_012 | Discord bot |
| `api_key` | key_abc123 | API authentication |
| `internal` | user_456 | Your internal user ID |

```python
formation.associate_user_identifiers(
    identifiers=[
        {"identifier": "alice@acme.com", "type": "email"},
        {"identifier": "U12345ABC", "type": "slack"},
    ]
)
```

---

## Database Requirements

Multi-identity requires persistent storage:

```yaml
memory:
  persistent:
    provider: postgres  # Required for multi-identity
    connection_string: ${{ secrets.POSTGRES_URL }}
```

**Why Postgres?** Multi-identity needs:
- User table with internal IDs
- Identifier mapping table
- Formation-level isolation
- Transaction support for linking

SQLite works for single-user/testing but doesn't support full multi-identity.

---

## Conflict Handling

### Identifier Already Linked

If you try to link an identifier that belongs to another user:

```python
# alice@email.com already linked to usr_alice
formation.associate_user_identifiers(
    identifiers=["alice@email.com"],
    user_id="usr_bob"  # Different user
)
# Raises: IdentifierConflictError
```

**Resolution:** Check who owns the identifier first, or use a different identifier.

### Merging Users (Not Yet Supported)

Currently, you cannot merge two users into one. If needed:
1. Choose the primary user
2. Manually migrate data
3. Delete the secondary user

User merging is planned for a future release.

---

## Performance

### Caching

Identifier resolution is cached for fast lookups:

```
First request: DB lookup (~10ms) → Cache
Subsequent: Cache hit (~1ms)
Cache TTL: 1 hour (configurable)
```

### Best Practices

1. **Link identifiers early** - During signup/OAuth connection
2. **Use consistent identifier types** - Helps with debugging
3. **Don't link temporary identifiers** - API keys that rotate, session IDs

---

## Security

### Formation Isolation

Identifiers are scoped to formations:

```
Formation A: alice@email.com → usr_123
Formation B: alice@email.com → usr_456
```

Same email, different formations = different users. This is intentional.

### Never Expose Internal IDs

Use public MUXI IDs (`usr_xxx`) for external systems, not database IDs.

---

## Summary

Multi-identity solves the "same user, different channels" problem:

| Without Multi-Identity | With Multi-Identity |
|-----------------------|---------------------|
| Each channel = new user | All channels = same user |
| Memory fragmented | Memory unified |
| Context lost across platforms | Context preserved |
| User repeats preferences | User sets once, works everywhere |

**The result:** Users get a consistent, personalized experience whether they're chatting on Slack, email, or your web app.

---

## Learn More

- [Memory System](memory-system.md) - How memory works per-user
- [Sessions](sessions.md) - Sessions vs users
- [User Credentials](user-credentials.md) - Per-user API keys
- [SDK Reference](../sdks/README.md) - Identity management API
