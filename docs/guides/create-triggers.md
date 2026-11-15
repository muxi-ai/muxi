---
title: Create Triggers
description: Set up webhook triggers for external systems
---

# Create Triggers

## Connect external systems to your agents via webhooks

Triggers let GitHub, Slack, monitoring tools, and other systems invoke your agents automatically.

## Why Triggers?

MUXI needs to be valuable **outside of chat sessions**. Without triggers, MUXI only responds when users type messages. With triggers, external systems can invoke MUXI with information - making it useful in automated workflows.

**Examples:**
- GitHub issue opened → Agent triages and assigns
- Monitoring alert fired → Agent investigates and notifies
- Stripe payment received → Agent sends receipt and updates records
- Slack mention → Agent responds in thread

Triggers are always async (there's no one waiting) - the result is delivered via webhook callback.


## Quick Start

### 1. Create Template

Create a markdown file in `triggers/`:

```
<!-- triggers/github-issue.md -->
New issue from ${{ data.repository }}:

**#${{ data.issue.number }}**: ${{ data.issue.title }}
**Author**: ${{ data.issue.author }}

${{ data.issue.body }}

Please analyze this issue and suggest next steps.
```

### 2. Test Locally

```bash
curl -X POST http://localhost:7890/draft/my-formation/v1/triggers/github-issue \
  -H "X-Muxi-Client-Key: fmc_..." \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "repository": "muxi/runtime",
      "issue": {
        "number": 123,
        "title": "Bug in login",
        "author": "alice",
        "body": "Login fails when..."
      }
    },
    "use_async": false
  }'
```

### 3. Configure External Webhook

Point your external system to:
```
https://your-server/api/{formation_id}/v1/triggers/github-issue
```


## Template Syntax

Use `${{ data.* }}` to reference webhook payload data:

```
# Simple
Hello ${{ data.name }}!

# Nested
Issue #${{ data.issue.number }}

# Deep nesting
User: ${{ data.user.profile.name }}
```


## Integration Examples

### GitHub

**Template:** `triggers/github-issue.md`
```
New issue in ${{ data.repository.full_name }}:

**#${{ data.issue.number }}**: ${{ data.issue.title }}
**Author**: ${{ data.issue.user.login }}
**Labels**: ${{ data.issue.labels }}

${{ data.issue.body }}

Please triage this issue.
```

**GitHub Webhook Settings:**
- Payload URL: `https://your-server/api/my-formation/v1/triggers/github-issue`
- Content type: `application/json`
- Events: Issues

### Slack

**Template:** `triggers/slack-message.md`
```
Message from ${{ data.event.user }} in #${{ data.event.channel }}:

"${{ data.event.text }}"

Please respond helpfully.
```

**Slack App Settings:**
- Event Subscriptions → Request URL: `https://your-server/api/my-formation/v1/triggers/slack-message`
- Subscribe to: `message.channels`

> [!NOTE]
> Slack cannot send the client key, and a trigger's `auth` check cannot answer Slack's `url_verification` handshake yet, so put a small relay in front of the trigger for now.

### Stripe

Stripe cannot send the client key. It signs each request with a `Stripe-Signature` header, so the trigger checks that itself with an [`auth`](../reference/triggers.md#per-trigger-auth) script.

**Template:** `triggers/stripe-payment.md`
```
---
auth: ./auth/stripe.py
---
Payment received:

**Amount**: ${{ data.data.object.amount }}
**Customer**: ${{ data.data.object.customer }}
**Status**: ${{ data.data.object.status }}

Please send receipt and update records.
```

**Auth script:** `auth/stripe.py`, executable (`chmod +x`), with the endpoint's signing secret in `STRIPE_WEBHOOK_SECRET` in the runtime's environment. It reads the request on stdin and exits `0` only for a valid signature less than five minutes old:

```python
#!/usr/bin/env python3
import base64, hashlib, hmac, json, os, sys, time

request = json.load(sys.stdin)
body = base64.b64decode(request["body"])
header = request["headers"].get("stripe-signature", "")
pairs = [item.split("=", 1) for item in header.split(",") if "=" in item]
timestamp = next((value for key, value in pairs if key == "t"), "")
signatures = [value for key, value in pairs if key == "v1"]
if not timestamp.isdigit() or abs(time.time() - int(timestamp)) > 300:
    sys.exit(1)
secret = os.environ["STRIPE_WEBHOOK_SECRET"].encode()
expected = hmac.new(secret, timestamp.encode() + b"." + body, hashlib.sha256).hexdigest()
sys.exit(0 if any(hmac.compare_digest(expected, s) for s in signatures) else 1)
```

**Stripe Webhook Settings:**
- Endpoint URL: `https://your-server/api/my-formation/v1/triggers/stripe-payment`
- Events: `payment_intent.succeeded`

Stripe posts its event as-is, and the event becomes `data`, so `${{ data.data.object.amount }}` reads the amount. If the script or the secret is wrong, Stripe gets a `401` and the attempt is logged as `error.authentication.failed`.


## Async vs Sync

### Async (Default, Best for Webhooks)

```json
{"data": {...}, "use_async": true}
```

- Returns immediately with `request_id`
- Webhook caller doesn't wait
- Best for production webhooks

### Sync (For Testing)

```json
{"data": {...}, "use_async": false}
```

- Waits for agent response
- Returns full response text
- Use for testing templates


## Debugging

### Test Template Rendering

Use sync mode to see the response:

```bash
curl -X POST .../triggers/my-trigger \
  -d '{"data": {...}, "use_async": false}'
```

### Check Available Triggers

```bash
curl http://localhost:7890/draft/my-formation/v1/triggers
```

### Common Errors

**"Key not found"**: Template references data that wasn't provided
```
Template: ${{ data.issue.number }}
Payload missing: issue.number
Fix: Check webhook payload structure
```

**401 on a trigger with `auth`**: the check failed (bad signature, expired timestamp, missing secret, a timeout)
```
Fix: Look for error.authentication.failed in the logs; it names the trigger and the failure kind
```

**"Trigger not found"**: No template file exists
```
Fix: Create triggers/trigger-name.md
```


## Best Practices

1. **Use async mode** - Webhooks expect fast acknowledgment
2. **Match payload structure** - Design templates around actual webhook payloads
3. **Include context** - Give the agent enough information to act
4. **Test with real payloads** - Use actual webhook data for testing
5. **Monitor executions** - Track via observability


## Learn More

- [Triggers & Webhooks](../concepts/triggers-and-webhooks.md) - Full concept
- [Triggers Reference](../reference/triggers.md) - API reference
- [Async Processing](../concepts/async.md) - Async mode details
