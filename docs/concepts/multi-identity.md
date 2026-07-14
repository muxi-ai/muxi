---
title: Multi-Identity Users
description: Link external identifiers to one MUXI user
---
# Multi-Identity Users

Multi-identity links email addresses, channel IDs, application IDs, and other
external identifiers to one MUXI user. Requests made with any linked identifier
resolve to the same user record, allowing user-scoped memory, credentials, and
scheduled work to follow the person across channels.

Sessions remain separate. Linking identities unifies the user, not every
conversation buffer.

## Resolve an Identifier

Chat resolves the supplied user identifier and creates a user mapping when
persistent memory is available. Applications can also resolve explicitly:

```python
user = formation.resolve_user("alice@example.com", create_user=True)
print(user["muxi_user_id"])
```

`create_user=False` performs lookup only and returns a not-found response when
the identifier is unknown.

## Link Identifiers

Identifier management is an admin operation. Link one or more identifiers to
an existing MUXI user:

```python
result = formation.link_user_identifier(
    "usr_abc123",
    [
        {"identifier": "alice@example.com", "type": "email"},
        {"identifier": "U98765ABC", "type": "slack"},
        "customer_123",
    ],
)
```

The identifier list accepts strings, `[identifier, type]` pairs, or objects
with `identifier` and `type` fields.

Retrieve the mappings for a MUXI user:

```python
identifiers = formation.get_user_identifiers_for_user("usr_abc123")
```

Remove a mapping by its identifier value:

```python
formation.unlink_user_identifier("U98765ABC")
```

Linking an identifier that already belongs to a different user returns a
conflict rather than silently moving it.

## API

```bash
# Resolve or create a user
curl -X POST http://localhost:7890/api/my-assistant/v1/users/resolve \
  -H "Content-Type: application/json" \
  -H "X-Muxi-Client-Key: fmc_..." \
  -d '{"identifier":"alice@example.com","create_user":true}'

# Link identifiers (admin key required)
curl -X POST http://localhost:7890/api/my-assistant/v1/users/identifiers \
  -H "Content-Type: application/json" \
  -H "X-Muxi-Admin-Key: fma_..." \
  -d '{
    "muxi_user_id": "usr_abc123",
    "identifiers": [
      {"identifier":"alice@example.com","type":"email"},
      {"identifier":"U98765ABC","type":"slack"}
    ]
  }'

# List mappings for a MUXI user (admin key required)
curl http://localhost:7890/api/my-assistant/v1/users/identifiers/usr_abc123 \
  -H "X-Muxi-Admin-Key: fma_..."
```

Omit `muxi_user_id` from the link request to create a new MUXI user for the
provided identifiers.

## Using Linked Identities

After linking, pass the channel-specific identifier as the request user ID:

```python
response = formation.chat(
    {"message": "What preferences have I saved?"},
    user_id="U98765ABC",
)
```

The runtime resolves `U98765ABC` to the linked MUXI user before loading
user-scoped state.

## Requirements and Security

- Configure persistent memory so identifier mappings survive restarts.
- Identifier mappings are scoped to a formation.
- Treat identity linking as privileged account-management work. The linking,
  listing, and unlinking routes require the formation admin key.
- Verify ownership in your application before linking an external identity.
- Use public `usr_...` IDs at integration boundaries, not database row IDs.

## Learn More

- [Memory System](memory-system.md)
- [Sessions](sessions.md)
- [User Credentials](user-credentials.md)
- [Multi-Identity Deep Dive](../deep-dives/multi-identity.md)
