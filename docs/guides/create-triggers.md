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

```markdown
<!-- triggers/github-issue.md -->
New issue from ${{ data.repository }}:

**#${{ data.issue.number }}**: ${{ data.issue.title }}
**Author**: ${{ data.issue.author }}

${{ data.issue.body }}

Please analyze this issue and suggest next steps.
```

### 2. Test Locally

```bash
curl -X POST http://localhost:8001/v1/formations/my-formation/triggers/github-issue \
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
https://your-server/v1/formations/{formation_id}/triggers/github-issue
```


## Template Syntax

Use `${{ data.* }}` to reference webhook payload data:

```markdown
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
```markdown
New issue in ${{ data.repository.full_name }}:

**#${{ data.issue.number }}**: ${{ data.issue.title }}
**Author**: ${{ data.issue.user.login }}
**Labels**: ${{ data.issue.labels }}

${{ data.issue.body }}

Please triage this issue.
```

**GitHub Webhook Settings:**
- Payload URL: `https://your-server/v1/formations/my-formation/triggers/github-issue`
- Content type: `application/json`
- Events: Issues

### Slack

**Template:** `triggers/slack-message.md`
```markdown
Message from ${{ data.event.user }} in #${{ data.event.channel }}:

"${{ data.event.text }}"

Please respond helpfully.
```

**Slack App Settings:**
- Event Subscriptions → Request URL: `https://your-server/v1/formations/my-formation/triggers/slack-message`
- Subscribe to: `message.channels`

### Stripe

**Template:** `triggers/stripe-payment.md`
```markdown
Payment received:

**Amount**: ${{ data.data.object.amount }}
**Customer**: ${{ data.data.object.customer }}
**Status**: ${{ data.data.object.status }}

Please send receipt and update records.
```

**Stripe Webhook Settings:**
- Endpoint URL: `https://your-server/v1/formations/my-formation/triggers/stripe-payment`
- Events: `payment_intent.succeeded`


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
curl http://localhost:8001/v1/formations/my-formation/triggers
```

### Common Errors

**"Key not found"**: Template references data that wasn't provided
```
Template: ${{ data.issue.number }}
Payload missing: issue.number
Fix: Check webhook payload structure
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

- [Triggers & Webhooks](concepts/triggers-and-webhooks.md) - Full concept
- [Triggers Reference](reference/triggers.md) - API reference
- [Async Processing](concepts/async.md) - Async mode details
