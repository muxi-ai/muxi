---
title: Triggers
description: API reference for webhook triggers
---

# Triggers

## API reference for webhook triggers

Triggers provide webhook endpoints for external systems to invoke your agents.

> [!TIP]
> **New to triggers?** Read [Triggers & Webhooks →](concepts/triggers-and-webhooks.md) first.

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

```markdown
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

```markdown
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
curl -X POST http://localhost:8001/v1/formations/my-formation/triggers/github-issue \
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

## Related

- [Triggers & Webhooks](concepts/triggers-and-webhooks.md) - Concept overview
- [Create Triggers](guides/create-triggers.md) - Step-by-step guide
- [Async Processing](concepts/async.md) - Async mode details
