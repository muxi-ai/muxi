---
title: Standard Operating Procedures
description: How SOPs give agents repeatable workflows for common tasks
---

# Standard Operating Procedures (SOPs)

SOPs are step-by-step instructions that agents follow for specific, repeatable tasks - like onboarding customers, processing refunds, or handling incidents.

---

## Why SOPs?

```
Without SOPs:
  User: "Onboard new customer"
  Agent: Improvises each time, inconsistent results

With SOPs:
  User: "Onboard new customer"
  Overlord: Matches "customer-onboarding" SOP
  Agent: Follows exact 8-step process every time
```

SOPs ensure:
- **Consistency** - Same process every time
- **Quality** - Best practices baked in
- **Speed** - No figuring out what to do
- **Compliance** - Required steps never skipped

---

## How SOPs Work

### Request Matching

When a request arrives, the Overlord checks for SOP matches **first**, before routing to agents:

```
User request
     ↓
Does it match an SOP?
     ↓ YES
Execute SOP (bypass normal routing)
     ↓ NO
Route to agent normally
```

SOPs have the **highest routing priority** - they override everything else.

---

## SOP Structure

SOPs are Markdown files with structured steps:

```markdown
# Customer Onboarding

**Trigger phrases:**
- "onboard new customer"
- "add customer"
- "new client setup"

## Steps

### 1. Gather Information
Ask for:
- Company name
- Primary contact name and email
- Billing address

### 2. Create Account
Use the `create-customer` tool:
- Set status: "trial"
- Trial period: 30 days

### 3. Send Welcome Email
Use the `send-email` tool with template "welcome-trial"

### 4. Schedule Follow-up
Create reminder for 7 days: "Check in with {customer_name}"
```

Each step tells the agent exactly what to do.

---

## Matching Requests

### Trigger Phrases

SOPs define phrases that activate them:

```markdown
**Trigger phrases:**
- "refund customer"
- "process refund"
- "issue refund"
```

If a user message contains any of these, the SOP activates.

### Semantic Matching

MUXI also uses semantic similarity - related phrases match even if not exact:

```
SOP trigger: "onboard new customer"
Matches:
  ✓ "set up a new customer"
  ✓ "add customer to system"
  ✓ "start onboarding for Acme Corp"
```

---

## Multi-Agent SOPs

SOPs can coordinate multiple agents:

```markdown
# Content Production

### 1. Research Phase
**Agent:** researcher
Gather information on {topic}

### 2. Writing Phase
**Agent:** writer
Create 1000-word article based on research

### 3. Review Phase
**Agent:** editor
Check for accuracy and style
```

The Overlord routes each step to the appropriate agent.

---

## Variables & Context

SOPs can reference information from the request:

```markdown
### 1. Greet Customer
Say: "Hi {customer_name}, I'll help you with {issue_type}"

### 2. Check Account Status
Look up account for {customer_email}
```

Variables are extracted from the conversation automatically.

---

## Conditional Steps

SOPs can branch based on conditions:

```markdown
### 3. Check Eligibility
If customer is on "enterprise" plan:
  → Approve immediately
Otherwise:
  → Require manager approval
```

Agents evaluate conditions and follow the appropriate path.

---

## Required vs Optional Steps

Mark critical steps:

```markdown
### 3. Record Transaction [REQUIRED]
Log all details to audit system
**Must complete before proceeding**

### 4. Send Survey [OPTIONAL]
Email customer satisfaction survey if time allows
```

Required steps block progress until completed.

---

## When to Use SOPs

| Use SOPs For | Don't Use SOPs For |
|-------------|-------------------|
| Repeatable processes | One-time tasks |
| Multi-step workflows | Simple questions |
| Critical procedures | Creative work |
| Compliance requirements | Exploratory conversations |
| Customer service flows | General chat |

Examples of good SOP candidates:
- Customer onboarding/offboarding
- Incident response procedures
- Refund/return processing
- Data export requests
- Account verification
- Bug report triage

---

## SOP Library

Build a library of SOPs for your organization:

```
sops/
├── customer/
│   ├── onboarding.md
│   ├── offboarding.md
│   └── upgrade.md
├── support/
│   ├── refund.md
│   ├── bug-report.md
│   └── escalation.md
└── operations/
    ├── incident-response.md
    └── data-export.md
```

Agents can execute any SOP in the library.

---

## Why This Matters

| Without SOPs | With SOPs |
|-------------|-----------|
| Agents improvise | Agents follow best practices |
| Inconsistent outcomes | Consistent quality |
| Steps forgotten | All steps completed |
| Training needed | Instructions built in |
| Hard to improve | Update SOP once |

The result: **reliable, repeatable workflows** without hardcoding logic.

---

## Quick Setup

```yaml
agents:
  - id: support
    role: Customer support specialist
```

Add SOP file to `sops/customer-onboarding.md`, restart - it works automatically.

---

## Learn More

- [Create SOPs Guide](../guides/sops.md) - Write your first SOP
- [SOPs Reference](../reference/sops.md) - Syntax and options
- [The Overlord](overlord.md) - How routing priority works
