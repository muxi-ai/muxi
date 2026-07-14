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
POST /v1/formations/{formation_id}/triggers/{trigger_name}
```

**Headers:**
| Header | Required | Description |
|--------|----------|-------------|
| `X-Muxi-Client-Key` | Yes | Client API key |
| `X-Muxi-User-Id` | No | User ID (defaults to "0") |
| `Content-Type` | Yes | `application/json` |

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
GET /v1/formations/{formation_id}/triggers
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

- URL: `https://your-server/v1/formations/my-formation/triggers/github-issue`
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
