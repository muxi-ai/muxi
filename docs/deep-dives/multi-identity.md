---
title: Multi-Identity Architecture
description: Identifier resolution and linking APIs
---
# Multi-Identity Architecture

MUXI separates the identifier supplied by an application from the public MUXI
user ID used to scope persistent user state. An identifier record maps a value
such as an email address, Slack user ID, or application account ID to one
formation-local user.

## Resolution Path

For a chat request, the runtime:

1. Reads the request's user identifier.
2. Looks up that identifier within the current formation.
3. Uses the linked user's internal record for persistent memory and other
   user-scoped services.
4. Creates a new user mapping when the identifier is unknown and persistent
   storage is available.

This lets two linked identifiers share user-scoped state while retaining
separate sessions and session buffers.

## Identifier Shapes

The linking API accepts three input forms:

```json
{
  "muxi_user_id": "usr_abc123",
  "identifiers": [
    "customer_123",
    ["U98765ABC", "slack"],
    {"identifier": "alice@example.com", "type": "email"}
  ]
}
```

`muxi_user_id` is optional. When omitted, the runtime creates a new public MUXI
user ID and links the supplied identifiers to it.

## Routes

All paths below are relative to `/api/{formation-id}/v1` for a deployed
formation or `/draft/{formation-id}/v1` for a local draft.

| Method | Path | Purpose |
|--------|------|---------|
| `POST` | `/users/resolve` | Resolve an identifier, optionally creating a user |
| `GET` | `/users/{identifier}` | Look up an identifier without creating a user |
| `POST` | `/users/identifiers` | Link identifiers to a new or existing MUXI user |
| `GET` | `/users/identifiers/{user_id}` | List identifiers linked to a public MUXI user ID |
| `DELETE` | `/users/identifiers/{identifier}` | Remove one identifier mapping |

Identifier-management operations use the formation admin key. Resolution is a
client operation.

## SDK Operations

```python
from muxi import FormationClient

formation = FormationClient(
    server_url="http://localhost:7890",
    formation_id="my-assistant",
    client_key="fmc_...",
    admin_key="fma_...",
)

resolved = formation.resolve_user(
    "alice@example.com",
    create_user=True,
)

formation.link_user_identifier(
    resolved["muxi_user_id"],
    [
        {"identifier": "alice@example.com", "type": "email"},
        {"identifier": "U98765ABC", "type": "slack"},
    ],
)

linked = formation.get_user_identifiers_for_user(
    resolved["muxi_user_id"]
)

formation.unlink_user_identifier("U98765ABC")
```

The runtime rejects an attempt to link an identifier that is already assigned
to another user. It does not provide user-merging, verification-code, bulk
analytics, or identity-search SDK methods.

## Formation Isolation

Identifier uniqueness is scoped to a formation. The same email address can map
to different MUXI users in different formations. Public `usr_...` IDs are the
supported user references for identifier-management routes; internal database
IDs are implementation details.

## Persistence and Failure Behavior

Identity APIs require the formation's database service. They return service
unavailable when persistent storage is not initialized, not found for unknown
users or lookup-only resolution, and conflict when an identifier is already
linked elsewhere.

The runtime performs writes transactionally so a failed association does not
partially move an existing identifier. Applications should still verify that a
person controls an external identity before linking it.

## Security Checklist

- Keep the admin key on trusted backends.
- Never expose identity-linking operations directly to an untrusted client.
- Verify OAuth callbacks, signed channel events, or equivalent ownership proof
  before linking identifiers.
- Avoid identifiers that contain reusable credentials, such as raw API keys.
- Remove obsolete channel mappings when an account is disconnected.
