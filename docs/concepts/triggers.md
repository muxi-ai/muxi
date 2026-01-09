---
title: Triggers & Webhooks
description: Webhooks with intelligence - external systems that invoke agents
---
# Triggers & Webhooks

## Webhooks with intelligence - external systems that invoke agents


Triggers let external systems invoke your agents via webhooks. Instead of executing hardcoded functions, triggers render templates with event data and let agents process them intelligently.


## What Are Triggers?

Triggers are **webhook-friendly requests** - external systems send JSON data to a trigger endpoint, which renders a message template and processes it like any other agent request.

```
External System → Webhook POST → Trigger Template → Agent Message → Agent Processes
```

**Examples:**
- **GitHub issue opened** → Webhook calls trigger → Agent triages issue
- **Stripe payment received** → Webhook calls trigger → Agent sends receipt
- **Slack message posted** → Webhook calls trigger → Agent responds
- **Monitoring alert fired** → Webhook calls trigger → Agent investigates

---

## Webhooks + Brains

> **Key insight:** Traditional webhooks just pass data. MUXI triggers combine webhooks with intelligence.

**Traditional webhooks:**
- Receive event → Parse JSON → Execute hardcoded function
- Fixed logic: `if event.type == "issue_opened" then add_label("triage")`
- Can't adapt to context or make decisions

**MUXI triggers:**
- Receive event → Give full context to agent → Agent decides what to do
- Intelligent response: Agent reads issue, understands problem, applies judgment
- Can handle unexpected situations

```
Example: GitHub Issue Opened

Traditional Webhook:
  1. Receive issue event
  2. IF title contains "bug" THEN add label "bug"
  3. IF title contains "feature" THEN add label "enhancement"
  4. Done
  → Brittle, misses nuance

MUXI Trigger:
  1. Receive issue event with full payload
  2. Agent reads issue title and body
  3. Agent determines: Is this a bug? Feature request? Question? Duplicate?
  4. Agent adds appropriate labels, assigns to team, adds helpful comment
  5. Agent can escalate if uncertain
  → Adaptive, context-aware
```

**The difference:**
- **Traditional webhooks** are dumb pipes that execute code
- **MUXI triggers** give events to agents who apply intelligence

The result: **smart automation** that adapts to reality, not brittle scripts that break on edge cases.

---

## How Triggers Work

### 1. Define a Trigger Template

Create a Markdown template in `triggers/` directory:

```markdown
<!-- triggers/github-issue.md -->
New GitHub issue opened in ${{ data.repository }}:

**Issue #${{ data.issue.number }}**: ${{ data.issue.title }}
**Author**: ${{ data.issue.author }}
**Labels**: ${{ data.issue.labels | join(', ') }}

${{ data.issue.body }}

Please analyze this issue and take appropriate action.
```

Templates use Jinja2 syntax to render webhook data.

### 2. External System Calls Trigger

```bash
# GitHub webhook sends POST to your formation
curl -X POST https://your-server.com/v1/formations/support-bot/triggers/github-issue \
  -H "X-Muxi-Client-Key: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "repository": "acme/app",
      "issue": {
        "number": 123,
        "title": "Login button not working",
        "author": "alice",
        "labels": ["bug", "frontend"],
        "body": "When I click login, nothing happens..."
      }
    }
  }'
```

### 3. Template Renders into Message

```
New GitHub issue opened in acme/app:

**Issue #123**: Login button not working
**Author**: alice
**Labels**: bug, frontend

When I click login, nothing happens...

Please analyze this issue and take appropriate action.
```

### 4. Agent Processes Message

The agent receives this rendered message and processes it like any other request:

```
Agent reads the issue
Agent understands: Frontend bug affecting login
Agent checks for duplicates
Agent adds label: "critical"
Agent assigns to: @frontend-team
Agent posts comment: "Thanks for reporting! This looks like a critical 
                      issue affecting user login. I've assigned it to 
                      the frontend team for urgent investigation."
```

All happening automatically when the webhook fires.

---

## Trigger Configuration

### Formation Setup

```yaml
# formation.afs
triggers:
  enabled: true
  
# Optionally configure specific triggers
# (or just create template files in triggers/ directory)
```

### Create Trigger Templates

```
your-formation/
├── formation.afs
├── triggers/
│   ├── github-issue.md
│   ├── github-pr.md
│   ├── slack-mention.md
│   └── stripe-payment.md
```

Each `.md` file becomes a trigger endpoint.

### Template Syntax

Templates use Jinja2:

```markdown
<!-- Basic variable -->
Hello ${{ data.user.name }}!

<!-- Conditionals -->
{% if data.priority == "high" %}
🚨 **HIGH PRIORITY**
{% endif %}

<!-- Loops -->
{% for item in data.items %}
- ${{ item.name }}: ${{ item.value }}
{% endfor %}

<!-- Filters -->
Date: ${{ data.timestamp | datetime }}
Tags: ${{ data.tags | join(', ') }}
```

---

## Real-World Examples

### GitHub Issue Triage

```markdown
<!-- triggers/github-issue.md -->
New GitHub issue from **${{ data.repository }}**:

**#${{ data.issue.number }}**: ${{ data.issue.title }}
**Author**: @${{ data.issue.author }}
**Created**: ${{ data.issue.created_at }}

${{ data.issue.body }}

{% if data.issue.labels %}
**Current labels**: ${{ data.issue.labels | join(', ') }}
{% endif %}

Please:
1. Analyze the issue type (bug, feature, question, etc.)
2. Add appropriate labels
3. Determine priority
4. Assign to the right team
5. Add a helpful comment for the author
```

**Agent response:**
- Reads full issue context
- Determines it's a critical bug
- Adds labels: `bug`, `critical`, `frontend`
- Assigns to `@frontend-team`
- Posts: "Thanks for reporting! This is a critical bug affecting login. The frontend team will investigate immediately."

### Stripe Payment Webhook

```markdown
<!-- triggers/stripe-payment.md -->
Payment received via Stripe:

**Amount**: ${{ data.amount / 100 }} USD
**Customer**: ${{ data.customer.email }}
**Description**: ${{ data.description }}
**Invoice**: ${{ data.invoice_id }}

Please:
1. Send a receipt email to the customer
2. Update their account status to "active"
3. Log this transaction
4. Notify the customer success team
```

**Agent response:**
- Sends professional receipt email
- Updates customer record
- Logs transaction in database
- Notifies team via Slack

### Slack Mention

```markdown
<!-- triggers/slack-mention.md -->
You were mentioned in Slack:

**Channel**: #${{ data.channel }}
**User**: @${{ data.user }}
**Message**: ${{ data.text }}
**Thread**: ${{ data.thread_ts }}

Please respond helpfully in the thread.
```

**Agent response:**
- Understands the question
- Provides relevant answer
- Posts response in Slack thread
- All happening automatically

---

## Webhook Security

### HMAC Signatures

Verify webhooks are authentic:

```yaml
triggers:
  enabled: true
  secrets:
    github: ${{ secrets.GITHUB_WEBHOOK_SECRET }}
    stripe: ${{ secrets.STRIPE_WEBHOOK_SECRET }}
```

MUXI automatically verifies webhook signatures before processing.

### IP Allowlists

Restrict to known sources:

```yaml
triggers:
  enabled: true
  allowed_ips:
    - 192.30.252.0/22  # GitHub IPs
    - 10.0.0.0/8       # Internal network
```

### API Key Authentication

All trigger requests require authentication:

```bash
curl -X POST .../triggers/my-trigger \
  -H "X-Muxi-Client-Key: YOUR_API_KEY" \  # Required
  ...
```

---

## Async vs Sync Execution

### Async (Default - Recommended)

```json
{
  "data": { ... },
  "use_async": true
}
```

**Response:** Immediate acknowledgment with `request_id`

```json
{
  "request": {
    "id": "req_abc123",
    "status": "processing"
  }
}
```

**Best for:**
- Long-running agent tasks
- External webhooks (don't timeout)
- Fire-and-forget scenarios

### Sync (Blocks Until Complete)

```json
{
  "data": { ... },
  "use_async": false
}
```

**Response:** Full agent response after completion

```json
{
  "request": {
    "id": "req_abc123",
    "status": "completed"
  },
  "response": {
    "content": "I've analyzed the issue and added labels...",
    ...
  }
}
```

**Best for:**
- Quick operations
- When you need immediate response
- Testing/debugging

---

## Setting Up External Webhooks

### GitHub

1. Go to repository settings → Webhooks
2. Add webhook URL: `https://your-server.com/v1/formations/your-formation/triggers/github-issue`
3. Set content type: `application/json`
4. Set secret: Your webhook secret
5. Select events: Issues, Pull requests, etc.

### Stripe

1. Go to Stripe Dashboard → Webhooks
2. Add endpoint: `https://your-server.com/v1/formations/billing-bot/triggers/stripe-payment`
3. Set signing secret in formation config
4. Select events: `payment_intent.succeeded`, etc.

### Slack

1. Create Slack app → Enable Events API
2. Set request URL: `https://your-server.com/v1/formations/slack-bot/triggers/slack-mention`
3. Subscribe to bot events: `app_mention`, `message.channels`, etc.
4. Install app to workspace

---

## Monitoring Triggers

### View Trigger Executions

```bash
# Check recent trigger activity
muxi logs formation-name --filter="trigger"

# See specific trigger executions
muxi logs formation-name --filter="trigger:github-issue"
```

### Audit Trail

Every trigger execution is logged:

```json
{
  "request_id": "req_abc123",
  "trigger_name": "github-issue",
  "timestamp": "2025-01-09T10:30:00Z",
  "user_id": "webhook-user",
  "data": {
    "repository": "acme/app",
    "issue": { ... }
  },
  "status": "completed",
  "agent_response": "Analyzed issue #123..."
}
```

---

## Testing Triggers

### Test Locally

```bash
# Send test webhook
curl -X POST http://localhost:8001/v1/formations/test/triggers/github-issue \
  -H "X-Muxi-Client-Key: test_key" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "repository": "test/repo",
      "issue": {
        "number": 1,
        "title": "Test issue",
        "author": "tester"
      }
    }
  }'
```

### Dry Run

Preview what the agent would see:

```bash
curl .../triggers/github-issue?dry_run=true \
  -d '{ "data": { ... } }'
```

Returns the rendered template without executing.

---

## Common Patterns

### Conditional Logic in Templates

```markdown
{% if data.priority == "critical" %}
🚨 **CRITICAL ALERT** 🚨
{% elif data.priority == "high" %}
⚠️ **HIGH PRIORITY**
{% endif %}

${{ data.message }}

{% if data.priority == "critical" %}
**Action required within 1 hour.**
{% endif %}
```

### Data Transformation

```markdown
<!-- Format currency -->
Amount: ${{ (data.amount_cents / 100) | round(2) }} USD

<!-- Format dates -->
Date: ${{ data.timestamp | datetime("%Y-%m-%d %H:%M") }}

<!-- Join lists -->
Tags: ${{ data.tags | join(', ') }}

<!-- Default values -->
Status: ${{ data.status | default("pending") }}
```

### Multi-Source Triggers

One template can handle multiple webhook sources:

```markdown
<!-- triggers/alert.md -->
{% if data.source == "datadog" %}
Datadog Alert: ${{ data.alert.title }}
Severity: ${{ data.alert.severity }}
{% elif data.source == "pagerduty" %}
PagerDuty Incident: ${{ data.incident.title }}
Urgency: ${{ data.incident.urgency }}
{% elif data.source == "newrelic" %}
New Relic Alert: ${{ data.violation.condition_name }}
{% endif %}

Please investigate and respond.
```

---

## Limitations

### What Triggers Are NOT

❌ **Not scheduled tasks** - Use [Scheduled Tasks](scheduled-tasks.md) for recurring jobs
❌ **Not internal events** - Triggers are for external webhooks only
❌ **Not bidirectional** - Agents can't "call back" through trigger (use tools/MCP for that)

### What Triggers ARE

✅ **Webhook endpoints** for external systems
✅ **Template-based** message generation
✅ **Agent-powered** intelligent responses
✅ **Real-time** processing

---

## Why This Matters

| Traditional Webhooks | MUXI Triggers |
|-------------------|---------------|
| Execute hardcoded functions | Agents make decisions |
| IF/THEN logic trees | Intelligent interpretation |
| Brittle, breaks on edge cases | Adapts to unexpected situations |
| Same response every time | Context-aware responses |
| Can't ask questions | Seeks clarification when needed |

The result: **smart automation** that handles real-world complexity, not brittle scripts.

---

## Quick Setup

```yaml
# formation.afs
triggers:
  enabled: true
```

Create trigger template:

```bash
mkdir -p triggers
cat > triggers/my-trigger.md <<EOF
Event received: ${{ data.event_type }}
Data: ${{ data | tojson }}

Please process this event.
EOF
```

Configure external webhook to call:
```
POST https://your-server.com/v1/formations/your-formation/triggers/my-trigger
```

---

## Learn More

- [Scheduled Tasks](scheduled-tasks.md) - Recurring tasks with natural language
- [Tools & MCP](tools.md) - How agents use tools to respond to triggers
- [Standard Operating Procedures](sops.md) - Templates for agent workflows
