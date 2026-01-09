---
title: Multi-Identity System
description: How MUXI links multiple identifiers to a single user
---
# Multi-Identity System

## Link email, Slack ID, GitHub username to one user profile

MUXI's multi-identity system solves a common problem: users interact through multiple platforms (email, Slack, GitHub), each with its own ID. MUXI automatically links these identities to maintain consistent memory, preferences, and context.


## The Problem

Users have different IDs across platforms:

```
Same person, multiple identifiers:
- Email: alice@example.com
- Slack: U123ABC456
- GitHub: alice-dev
- Phone: +1-555-0123
```

**Without multi-identity:**
- Each ID = separate user profile
- No shared memory or preferences
- Agent doesn't recognize same person
- Fragmented experience

**With multi-identity:**
- All IDs → one user profile
- Shared memory across platforms
- Consistent preferences
- Unified experience

---

## How It Works

### Identity Resolution

```
User messages: "Check my GitHub repos"
From: alice@example.com (email)
         ↓
System checks: Is alice@example.com linked to a user?
         ↓
Found: Internal user_id = 123, MUXI ID = usr_abc123
         ↓
Agent uses user 123's memory, credentials, preferences
         ↓
Response is contextualized for that user
```

### Automatic Linking

```
First interaction:
  alice@example.com → Creates new user (id=123)

Later interaction:
  alice@example.com → Found! Uses user 123

Add another identifier:
  U123ABC456 (Slack) → Link to user 123

Now both identifiers resolve to same user:
  alice@example.com → user 123
  U123ABC456 → user 123
```

---

## Database Schema

### Two Tables

**`users` table** - Core user entity:
```sql
CREATE TABLE users (
    id INTEGER PRIMARY KEY,           -- Internal ID (fast FK)
    public_id VARCHAR(255),           -- External MUXI ID (usr_abc123)
    formation_id VARCHAR(255),        -- Formation isolation
    created_at TIMESTAMP,
    updated_at TIMESTAMP
);
```

**`user_identifiers` table** - Maps identifiers to users:
```sql
CREATE TABLE user_identifiers (
    id INTEGER PRIMARY KEY,
    user_id INTEGER,                  -- FK to users.id
    identifier VARCHAR(255),          -- External ID (email, Slack, etc.)
    identifier_type VARCHAR(50),      -- Optional type hint
    formation_id VARCHAR(255),        -- Formation isolation
    created_at TIMESTAMP,
    
    FOREIGN KEY(user_id) REFERENCES users(id),
    UNIQUE(identifier, formation_id)  -- One identifier per formation
);
```

### Key Design

**Three ID types:**
1. **Internal ID** (`users.id`) - Integer, fast database FK
2. **MUXI ID** (`users.public_id`) - String like `usr_abc123`, API-visible
3. **External ID** (`user_identifiers.identifier`) - Developer-provided (email, Slack, etc.)

**Why three IDs?**
- Internal ID: Fast database joins
- MUXI ID: Stable external identifier
- External IDs: Multiple per user, platform-specific

---

## Resolution Flow

### Fast Path (Cached)

```
Request: user_id="alice@example.com"
     ↓
Check cache: "user_id:formation_abc:alice@example.com"
     ↓
Cache hit: "123:usr_abc123"
     ↓
Return: (internal_id=123, muxi_id="usr_abc123")
     ↓
Total time: <1ms
```

### Slow Path (New User)

```
Request: user_id="alice@example.com"
     ↓
Check cache: Miss
     ↓
Query database: Not found
     ↓
Create new user:
  - Generate public_id: usr_abc123
  - Insert into users: id=123
     ↓
Create identifier:
  - Insert into user_identifiers
  - Link to user_id=123
     ↓
Cache: "user_id:formation_abc:alice@example.com" = "123:usr_abc123"
     ↓
Return: (123, "usr_abc123")
     ↓
Total time: ~50ms (database write)
```

### Slow Path (Existing User)

```
Request: user_id="U123ABC456" (Slack)
     ↓
Check cache: Miss
     ↓
Query:
  SELECT u.id, u.public_id
  FROM user_identifiers ui
  JOIN users u ON ui.user_id = u.id
  WHERE ui.identifier = 'U123ABC456'
  AND ui.formation_id = 'formation_abc'
     ↓
Found: (123, "usr_abc123")
     ↓
Cache result
     ↓
Return: (123, "usr_abc123")
     ↓
Total time: ~10ms (database read)
```

---

## Linking Identifiers

### Automatic Linking

When a user provides a new identifier:

```yaml
# User interacts via email
User: alice@example.com
Agent: "What's your Slack username so I can notify you?"
User: "U123ABC456"
         ↓
System automatically links:
  - alice@example.com → user 123
  - U123ABC456 → user 123
```

### Manual Linking

```python
from muxi.runtime.formation import Formation

formation = Formation()

# Link new identifier to existing user
await formation.link_identifier(
    user_id="alice@example.com",  # Existing identifier
    new_identifier="alice-github",  # New identifier to link
    identifier_type="github"  # Optional type hint
)

# Now both resolve to same user
```

### Identifier Types

Optional type hints for clarity:

```python
identifier_types = [
    "email",
    "slack",
    "github",
    "phone",
    "discord",
    "custom"
]
```

**Example:**
```python
await formation.link_identifier(
    user_id="alice@example.com",
    new_identifier="U123ABC456",
    identifier_type="slack"
)
```

---

## Formation Isolation

### Same Identifier, Different Formations

```
Formation A:
  alice@example.com → user 123 (Alice Smith)

Formation B:
  alice@example.com → user 456 (Different Alice)
```

Each formation maintains separate user namespaces.

**Why?**
- Privacy: Formation A can't see Formation B's data
- Flexibility: Same identifier can represent different people
- Security: Complete isolation between formations

---

## Use Cases

### Multi-Platform Support

```yaml
# User interacts via multiple platforms
Email: alice@example.com
Slack: U123ABC456
GitHub: alice-dev
Phone: +1-555-0123

# All linked to one profile
# Consistent memory and preferences across all platforms
```

### Context Preservation

```
User via email: "I prefer Python over JavaScript"
         ↓
Stored in user 123's memory
         ↓
Later, user via Slack: "Generate code for me"
         ↓
Agent: [Generates Python, remembers preference]
```

### Credential Management

```
User via email: Adds GitHub credentials
         ↓
Later, user via Slack: "Show my GitHub repos"
         ↓
Agent: [Uses same GitHub credentials, recognizes same user]
```

---

## API Usage

### Chat with Identifier

```bash
# Via email identifier
curl -X POST http://localhost:8001/v1/chat \
  -H "X-Muxi-User-Id: alice@example.com" \
  -d '{"message": "Hello"}'

# Via Slack identifier
curl -X POST http://localhost:8001/v1/chat \
  -H "X-Muxi-User-Id: U123ABC456" \
  -d '{"message": "Hello"}'

# Both use same user profile automatically
```

### List User Identifiers

```python
# Get all identifiers for a user
identifiers = await formation.get_user_identifiers("alice@example.com")

for identifier in identifiers:
    print(f"{identifier.type}: {identifier.identifier}")

# Output:
# email: alice@example.com
# slack: U123ABC456
# github: alice-dev
```

### Remove Identifier

```python
# Unlink an identifier
await formation.remove_identifier(
    identifier="alice-old-email@example.com"
)

# User still accessible via other identifiers
```

---

## Performance

### Caching

**Cache key:** `user_id:formation_id:identifier`

**Cache TTL:** 1 hour (configurable)

**Hit rate:** >95% after warmup

### Database Indexes

```sql
-- Fast identifier lookup
CREATE INDEX idx_user_identifiers_lookup 
ON user_identifiers(identifier, formation_id);

-- Fast user reverse lookup
CREATE INDEX idx_user_identifiers_user 
ON user_identifiers(user_id);
```

### Benchmarks

| Operation | Cold (no cache) | Warm (cached) |
|-----------|-----------------|---------------|
| Resolve existing user | ~10ms | <1ms |
| Create new user | ~50ms | N/A |
| Link identifier | ~30ms | N/A |

---

## Configuration

### Enable Multi-Identity

```yaml
# formation.afs
multi_identity:
  enabled: true
  cache_ttl: 3600  # 1 hour
```

### Database Configuration

**PostgreSQL (recommended for production):**
```yaml
memory:
  persistent:
    connection_string: ${{ secrets.POSTGRES_URI }}
```

**SQLite (development):**
```yaml
memory:
  persistent:
    connection_string: "sqlite:///./formation.db"
```

---

## Migration

### From Single User to Multi-Identity

```python
# Existing single-user formation
# user_id was always "0"

# Enable multi-identity
# Old "0" identifier automatically migrated

# Add new identifiers
await formation.link_identifier(
    user_id="0",  # Old identifier
    new_identifier="alice@example.com",
    identifier_type="email"
)

# Now both work:
# - "0" (backward compatible)
# - "alice@example.com" (new)
```

### Bulk Import

```python
# Import existing user mappings
mappings = [
    ("alice@example.com", "U123ABC", "slack"),
    ("bob@example.com", "U456DEF", "slack"),
    # ...
]

for email, slack_id, id_type in mappings:
    await formation.link_identifier(
        user_id=email,
        new_identifier=slack_id,
        identifier_type=id_type
    )
```

---

## Debugging

### List All Users

```python
# Get all users in formation
users = await formation.list_users()

for user in users:
    print(f"User: {user.public_id}")
    print(f"Identifiers: {user.identifiers}")
    print(f"Created: {user.created_at}")
```

### Trace Resolution

```python
import logging
logging.getLogger('muxi.identity').setLevel(logging.DEBUG)

# Shows resolution flow:
# "Resolving alice@example.com"
# "Cache miss, querying database"
# "Found user 123 (usr_abc123)"
# "Cached for 1 hour"
```

---

## Best Practices

### 1. Use Stable Identifiers

```
✅ Good:
- Email addresses
- OAuth IDs (GitHub, Google)
- Phone numbers

❌ Bad:
- Session IDs (temporary)
- Request IDs (per-request)
- Temporary tokens
```

### 2. Link Early

```python
# Ask for additional identifiers early
User: "Hello" (via email)
Agent: "To help you better across platforms, what's your Slack username?"
User: "U123ABC"
Agent: [Links identifiers automatically]
```

### 3. Type Hints

```python
# Always provide type hints
await formation.link_identifier(
    user_id="alice@example.com",
    new_identifier="U123ABC",
    identifier_type="slack"  # ✓ Clear what this is
)
```

### 4. Monitor Linkages

```python
# Periodically audit user identifiers
users_with_multiple = await formation.get_users_with_multiple_identifiers()

for user in users_with_multiple:
    if len(user.identifiers) > 5:
        logger.warning(f"User {user.public_id} has {len(user.identifiers)} identifiers")
```

---

## Security

### Verification

Before linking identifiers, verify ownership:

```python
# Send verification code
code = await formation.send_verification_code(
    identifier="alice-new@example.com",
    method="email"
)

# User provides code
if await formation.verify_code(code, user_input):
    # Link identifier
    await formation.link_identifier(...)
```

### Audit Trail

```sql
-- Track identifier changes
CREATE TABLE user_identifier_audit (
    id INTEGER PRIMARY KEY,
    user_id INTEGER,
    action VARCHAR(50),  -- 'linked', 'unlinked'
    identifier VARCHAR(255),
    timestamp TIMESTAMP,
    ip_address VARCHAR(45)
);
```

---

## Limitations

### No Automatic Merging

If two identifiers accidentally create separate users:

```
alice@example.com → user 123
alice-slack → user 456

# These are separate users, not automatically merged
```

**Solution:** Manually link or merge:
```python
await formation.merge_users(
    primary_user="alice@example.com",
    merge_user="alice-slack"
)
```

### Formation Scoped

Identifiers don't transfer between formations:

```
Formation A: alice@example.com → user 123
Formation B: alice@example.com → user 456 (different)
```

Each formation is isolated by design.

---

## Learn More

- [User Credentials](../concepts/user-credentials.md) - Per-user credential storage
- [Memory System](../concepts/memory.md) - User-specific memory
- [Multi-Tenancy](multi-tenancy.md) - User isolation architecture
