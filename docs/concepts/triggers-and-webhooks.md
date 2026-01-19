---
title: Triggers & Webhooks
description: Let external systems invoke your agents via webhooks
---
# Triggers & Webhooks

## Let external systems invoke your agents via webhooks

Triggers let external systems (GitHub, Slack, monitoring tools, etc.) send webhook events to your formation. MUXI renders a template with the event data and processes it like any other request.

## Why Triggers?

MUXI needs to be valuable outside of chat sessions. Triggers let external applications invoke formations with information, making MUXI useful in automated workflows.

## How Triggers Work

```
External System → Webhook POST → Template Rendering → Agent Processes
```

1. Developer creates a trigger template (MD file with placeholders)
2. External system sends webhook with payload
3. MUXI fills placeholders with payload data
4. Request is processed like any normal request

**Triggers are always async** - there's no one waiting on the other end. Results are delivered via webhook callback.

**Examples:**
- GitHub issue opened → Agent triages and labels
- Slack message → Agent responds
- Monitoring alert → Agent investigates
- Stripe payment → Agent sends receipt


## Triggers vs Regular Chat

| Feature | `/chat` | `/triggers/{name}` |
|---------|---------|-------------------|
| Message source | User provides directly | Template + webhook data |
| Response | Streaming or async | Complete response (no streaming) |
| Use case | Interactive chat | Webhook integration |
| Authentication | Same | Same |

Triggers are just webhook-optimized requests.


## Create a Trigger

### 1. Create Template File

Create a markdown file in `triggers/`:

```
<!-- triggers/github-issue.md -->
New GitHub issue from ${{ data.repository }}:

**Issue #${{ data.issue.number }}**: ${{ data.issue.title }}
**Author**: ${{ data.issue.author }}

${{ data.issue.body }}

Please analyze this issue and suggest next steps.
```

### 2. Configure Webhook

Point your external system to:

```
POST https://your-server.com/v1/formations/{formation_id}/triggers/github-issue
```

### 3. Send Webhook

```bash
curl -X POST http://localhost:8001/v1/formations/my-formation/triggers/github-issue \
  -H "X-Muxi-Client-Key: YOUR_CLIENT_KEY" \
  -H "X-Muxi-User-Id: webhook-user" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "repository": "muxi/runtime",
      "issue": {
        "number": 123,
        "title": "Bug in login flow",
        "author": "alice",
        "body": "Login fails when..."
      }
    }
  }'
```


## Template Syntax

Templates use `${{ data.* }}` for variable substitution:

### Simple Access
```
Hello ${{ data.name }}!
```

### Nested Access
```
Issue #${{ data.issue.number }}: ${{ data.issue.title }}
```

### Multi-Level Nesting
```
User: ${{ data.user.profile.name }}
```


## Processing Modes

### Async Mode (Default)

Best for webhooks - returns immediately:

```json
{
  "data": {...},
  "use_async": true
}
```

Response:
```json
{
  "request": {"id": "req_abc123"},
  "status": "processing"
}
```

### Sync Mode

Waits for completion (for testing):

```json
{
  "data": {...},
  "use_async": false
}
```

### Handling Trigger Callbacks

When a trigger completes, MUXI sends a webhook to your configured callback URL. Use the SDK webhook helpers to verify and parse the response:

[[tabs]]
[[tab Python]]
```python
from muxi import webhook

@app.post("/trigger-callback")
async def handle_trigger_result(request: Request):
    payload = await request.body()
    signature = request.headers.get("X-Muxi-Signature")

    # Verify signature (security)
    if not webhook.verify_signature(payload, signature, WEBHOOK_SECRET):
        raise HTTPException(401, "Invalid signature")

    # Parse into typed object
    event = webhook.parse(payload)

    if event.status == "completed":
        for item in event.content:
            if item.type == "text":
                print(f"Trigger result: {item.text}")
    elif event.status == "failed":
        print(f"Trigger failed: {event.error.message}")

    return {"received": True}
```
[[/tab]]
[[tab TypeScript]]
```typescript
import { webhook } from "@muxi-ai/muxi-typescript";

app.post("/trigger-callback", (req, res) => {
    const signature = req.headers["x-muxi-signature"] as string;

    // Verify signature
    if (!webhook.verifySignature(req.rawBody, signature, WEBHOOK_SECRET)) {
        return res.status(401).send("Invalid signature");
    }

    // Parse into typed object
    const event = webhook.parse(req.rawBody);

    if (event.status === "completed") {
        for (const item of event.content) {
            if (item.type === "text") {
                console.log(`Trigger result: ${item.text}`);
            }
        }
    } else if (event.status === "failed") {
        console.error(`Trigger failed: ${event.error?.message}`);
    }

    res.json({ received: true });
});
```
[[/tab]]
[[tab Go]]
```go
import "github.com/muxi-ai/muxi-go/webhook"

func handleTriggerCallback(w http.ResponseWriter, r *http.Request) {
    payload, _ := io.ReadAll(r.Body)
    sig := r.Header.Get("X-Muxi-Signature")

    // Verify signature
    if err := webhook.VerifySignature(payload, sig, secret); err != nil {
        http.Error(w, "Invalid signature", http.StatusUnauthorized)
        return
    }

    // Parse into typed object
    event, err := webhook.Parse(payload)
    if err != nil {
        http.Error(w, "Invalid payload", http.StatusBadRequest)
        return
    }

    switch event.Status {
    case "completed":
        for _, item := range event.Content {
            if item.Type == "text" {
                fmt.Printf("Trigger result: %s\n", item.Text)
            }
        }
    case "failed":
        fmt.Printf("Trigger failed: %s\n", event.Error.Message)
    }

    w.WriteHeader(http.StatusOK)
}
```
[[/tab]]
[[/tabs]]

> [!TIP]
> See [Async Processing - Handling Webhooks](../concepts/async.md#4-handling-webhooks-in-your-application) for complete webhook handling documentation including signature verification details.


## Directory Structure

```
my-formation/
├── formation.afs
├── agents/
├── triggers/
│   ├── github-issue.md
│   ├── slack-message.md
│   ├── stripe-payment.md
│   └── monitoring-alert.md
└── ...
```

Triggers are auto-discovered from the `triggers/` directory.


## Example Templates

### GitHub Issue

```
<!-- triggers/github-issue.md -->
New GitHub issue from ${{ data.repository }}:

**Issue #${{ data.issue.number }}**: ${{ data.issue.title }}
**Author**: ${{ data.issue.author }}
**Labels**: ${{ data.issue.labels }}

**Description**:
${{ data.issue.body }}

Please analyze and provide:
1. Summary of the problem
2. Impact assessment
3. Suggested priority
4. Relevant code areas
```

### Slack Message

```
<!-- triggers/slack-message.md -->
Slack message from ${{ data.user.name }} in #${{ data.channel.name }}:

"${{ data.text }}"

Please respond appropriately.
```

### Monitoring Alert

```
<!-- triggers/monitoring-alert.md -->
Alert: ${{ data.alert.name }}
Severity: ${{ data.alert.severity }}
Service: ${{ data.alert.service }}

Details: ${{ data.alert.description }}

Please investigate and suggest remediation.
```


## Webhook Integration

### GitHub Setup

1. Go to repository Settings → Webhooks
2. Add webhook:
   - URL: `https://your-server/v1/formations/{id}/triggers/github-issue`
   - Content type: `application/json`
   - Events: Select relevant events

### Slack Setup

1. Create Slack App
2. Enable Event Subscriptions
3. Set Request URL: `https://your-server/v1/formations/{id}/triggers/slack-message`

### Stripe Setup

1. Go to Developers → Webhooks
2. Add endpoint: `https://your-server/v1/formations/{id}/triggers/stripe-payment`
3. Select events to listen for


## Authentication

All trigger endpoints require:
- `X-Muxi-Client-Key`: Client API key (required)
- `X-Muxi-User-Id`: User ID for isolation (optional, defaults to "0")


## Workflow Approvals

Triggers automatically **bypass workflow approvals**. This is intentional because:
- Webhooks are already automated decisions
- No human available to approve
- External system made the decision to call


## Error Handling

### Missing Template Data

```json
{
  "error": {
    "code": "INVALID_REQUEST",
    "message": "Template rendering failed: Key 'data.issue.number' not found"
  }
}
```

### Trigger Not Found

```json
{
  "error": {
    "code": "RESOURCE_NOT_FOUND",
    "message": "Trigger template 'unknown' not found"
  }
}
```


## Best Practices

1. **Use async mode** for webhooks (they expect fast acknowledgment)
2. **Keep templates focused** - clear context for the LLM
3. **Include actionable instructions** - tell the agent what to do
4. **Test with sync mode** before deploying webhooks
5. **Monitor via observability** - track trigger executions


## Learn More

- [Create Triggers Guide](../guides/create-triggers.md) - Step-by-step setup
- [Triggers Reference](../reference/triggers.md) - Full API reference
- [Async Processing](../concepts/async.md) - Async mode details
