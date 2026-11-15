---
title: Triggers
description: API reference for webhook triggers
---

# Triggers

## API reference for webhook triggers

Triggers provide webhook endpoints for external systems to invoke your agents.

> [!TIP]
> **New to triggers?** Read [Triggers & Webhooks →](../concepts/triggers-and-webhooks.md) first.

## API Endpoints

### Execute Trigger

```http
POST /api/{formation_id}/v1/triggers/{trigger_name}
```

**Headers:**
| Header | Required | Description |
|--------|----------|-------------|
| `X-Muxi-Client-Key` | Yes, unless the trigger declares [`auth`](#per-trigger-auth) | Client API key |
| `X-Muxi-User-Id` | No | User ID (defaults to "0"; ignored after an `auth` pass) |
| `Content-Type` | Yes | `application/json` (an `auth` trigger takes the provider's own body) |

**Request Body:**
```json
{
  "data": {
    "key": "value",
    "nested": {
      "property": "value"
    }
  },
  "session_id": "optional-session-id",
  "use_async": true
}
```

**Response (Async):**
```json
{
  "object": "request",
  "type": "request.processing",
  "request": {"id": "req_abc123"},
  "success": true,
  "data": {"status": "processing"}
}
```

**Response (Sync):**
```json
{
  "object": "request",
  "type": "request.completed",
  "request": {"id": "req_abc123"},
  "success": true,
  "data": {
    "status": "completed",
    "response": "Agent's response text..."
  }
}
```

### List Triggers

```http
GET /api/{formation_id}/v1/triggers
```

**Response:**
```json
{
  "object": "list",
  "data": {
    "formation_id": "my-formation",
    "triggers": ["github-issue", "slack-message"],
    "count": 2
  }
}
```

## Template Syntax

Templates use `${{ data.* }}` for variable substitution:

```
${{ data.name }}                    # Simple access
${{ data.issue.number }}            # Nested access
${{ data.user.profile.name }}       # Multi-level nesting
```

## Directory Structure

```
triggers/
├── github-issue.md
├── slack-message.md
├── stripe-payment.md
└── monitoring-alert.md
```

Templates are auto-discovered from `triggers/` directory.

## Request Options

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `data` | object | Required | Event data for template rendering |
| `session_id` | string | Auto-generated | Session ID for conversation context |
| `use_async` | boolean | `true` | Return immediately or wait for completion |

## Error Responses

### Template Rendering Error

```json
{
  "object": "error",
  "type": "error.validation",
  "error": {
    "code": "INVALID_REQUEST",
    "message": "Template rendering failed: Key 'data.issue.number' not found"
  }
}
```

### Failed Auth Check

A failed `auth` check returns the same `401` as a missing client key, so callers cannot tell the two apart.

### Trigger Not Found

```json
{
  "object": "error",
  "type": "error.not_found",
  "error": {
    "code": "RESOURCE_NOT_FOUND",
    "message": "Trigger template 'unknown' not found"
  }
}
```

## Example: Complete Workflow

### 1. Create Template

```
<!-- triggers/github-issue.md -->
New issue from ${{ data.repository }}:

**#${{ data.issue.number }}**: ${{ data.issue.title }}

${{ data.issue.body }}

Please triage this issue.
```

### 2. Configure GitHub Webhook

- URL: `https://your-server/api/my-formation/v1/triggers/github-issue`
- Content type: `application/json`
- Events: Issues

### 3. Test with curl

```bash
curl -X POST http://localhost:7890/draft/my-formation/v1/triggers/github-issue \
  -H "X-Muxi-Client-Key: fmc_..." \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "repository": "muxi/runtime",
      "issue": {
        "number": 123,
        "title": "Bug report",
        "body": "Description..."
      }
    },
    "use_async": false
  }'
```

## Per-trigger auth

A webhook provider such as Stripe or HubSpot cannot send `X-Muxi-Client-Key`. It proves who it is with its own signature header. A trigger can name its own check for that in its frontmatter, with a script path or an http(s) URL:

```markdown
<!-- triggers/stripe-payment.md -->
---
auth: ./auth/stripe.py
---
A Stripe event arrived: ${{ data.type }}
```

```yaml
---
auth: https://auth.internal/stripe
---
```

A relative path is resolved from the formation directory. Formations with no `auth` line behave exactly as before.

### Load-time checks

The runtime reads `auth` from every trigger once, at load. The formation fails to load, with an error naming the trigger and the target, when:

- the script does not exist or is not executable;
- the URL gives no HTTP response at all (DNS failure, connection refused, timeout); any HTTP status counts as reachable;
- the value is neither a path nor an http(s) URL.

Only `auth` is checked at load. Any other frontmatter problem stays a call-time error, as before.

### The check

On `POST /v1/triggers/{name}` for a trigger with `auth`, the runtime sends the check this JSON:

```json
{
  "trigger": "stripe-payment",
  "method": "POST",
  "url": "http://127.0.0.1:8001/v1/triggers/stripe-payment",
  "headers": {"stripe-signature": "...", "content-type": "application/json"},
  "body": "<base64 of the exact request bytes>"
}
```

`url` is the URL the runtime received, which behind the MUXI server is the local address. Header names are lowercase, and a repeated header arrives as one comma-joined value. The body is passed byte for byte, because provider signatures are computed over the exact bytes.

| Target | How it gets the JSON | Pass |
|--------|----------------------|------|
| Script | On stdin, with the runtime's environment (which is how it reads its signing secret). Its output is discarded. | Exit code `0` |
| URL | As a POST | Any `2xx` response |

The check fails closed: an error, a timeout (2 seconds, fixed) or an unreachable target is a fail.

### Pass and fail

- **Pass:** the request runs as a normal key-authenticated call. The provider's body becomes `data` (see below), the run is async in its own session, and a caller-sent `X-Muxi-User-Id` is dropped, so the user is `"0"`. A request middleware, if configured, may still set the identity.
- **Fail:** nothing is added to the request, so the caller gets the existing `401` for a missing client key. The fail is logged as `error.authentication.failed` with the trigger name and the failure kind (`exit_code`, `http_status`, `timeout` or `error`), never headers or body.

| Provider body | `data` |
|---------------|--------|
| A JSON object | The object as-is |
| Anything else (form-encoded, plain text, a JSON array, empty) | `{"body": "<the body as text>"}` |

A caller holding the client key can still call an `auth` trigger the normal way, with a `{"data": ...}` body. A request that already carries the real key skips the check and runs exactly as today, with its own `session_id`, user and `use_async`. Replays and scheduled catch-ups use this path, since a stored provider event cannot pass a signature check twice. `GET` on a trigger always requires the key.

A body sent as `Content-Type: application/json` that is not valid JSON gets a `422` rather than the `401`, as on every route.

### Not supported

- Responses beyond pass or fail. Slack's one-time `url_verification` handshake, which must echo a challenge, cannot be answered, so Slack Events cannot be pointed at a trigger yet.
- A configurable timeout, auth headers on the auth URL, or retries.

See [Create Triggers → Stripe](../guides/create-triggers.md#stripe) for a complete example.

## Transformers: outbound routing

A trigger's result can be delivered to an external platform (Slack, Telegram,
Twilio, any webhook consumer) with no custom glue. Add `transformer:` to use a
named payload template. `webhook:` may be used alone for the standard payload or
together with `transformer:` to override its delivery URL. Use `parse:` to
extract inbound fields through simple JSON-style paths into the request and
template context.

```markdown
<!-- triggers/slack-message.md -->
---
transformer: slack
webhook: https://hooks.example.com/slack-bridge
parse:
  message: "$.event.text"
  user_id: "$.event.user"
  context:
    channel: "$.event.channel"
    thread_ts: "$.event.thread_ts"
---
Handle the inbound Slack message.
```

Transformer YAML files live in `transformers/<name>.yaml` (fail-fast validation)
and define the outbound payload template, HTTP delivery with
bearer/basic/header auth, and optional content transformation:

```yaml
# transformers/slack.yaml
name: slack
endpoint:
  url: "${{ secrets.SLACK_WEBHOOK }}"   # optional; trigger/channel URL wins
  method: POST
auth:
  type: bearer
  token: "${{ secrets.SLACK_TOKEN }}"
headers:
  Content-Type: application/json
body:
  channel: "${{ context.channel }}"
  text: "${{ response.content }}"
content_transform:
  format: markdown
  max_length: 4000
  truncation_suffix: "..."
```

URL resolution is trigger/channel URL first, then the transformer's own, with a
load-time error when neither exists. Delivery failures use the webhook
manager's retry policy, then fall back to
the formation's default async webhook with `transformer_error` metadata - a
broken transformer never loses the trigger result. Markdown-to-HTML link
substitution only emits anchors for `http(s)` URLs.

Bundled dormant channel transformers (`slack`, `telegram`, `discord`, `email`)
ship for use with [proactiveness](../concepts/proactiveness.md); a
formation-local `transformers/` file shadows them.

## Response UI widgets over channels

Triggers can render [response UI widgets](response-ui-widgets.md) natively on
supporting channels, and inbound payloads carrying a `ui_response` are parsed
back into the conversation as the user's widget selection.

## Related

- [Triggers & Webhooks](../concepts/triggers-and-webhooks.md) - Concept overview
- [Create Triggers](../guides/create-triggers.md) - Step-by-step guide
- [Async Processing](../concepts/async.md) - Async mode details
