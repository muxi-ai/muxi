---
title: Standard Operating Procedures
description: Guidelines that let agents adapt while maintaining consistency
---
# Standard Operating Procedures (SOPs)

## Guidelines that let agents adapt while maintaining consistency


SOPs are step-by-step guidelines that agents interpret for specific, repeatable tasks - like onboarding customers, processing refunds, or handling incidents. Unlike rigid workflows, SOPs allow agents to adapt to context while ensuring consistent outcomes.


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

## Guidelines, Not Workflows

> **Key insight:** Workflows ≠ Agentic

Traditional workflow automation (Zapier, n8n, etc.) is **rigid**:
- Fixed branching logic (`if X then Y`)
- Hard-coded sequences
- No adaptation to context
- Breaks when reality doesn't match the script

SOPs are **guidelines** that agents interpret:

```
Traditional Workflow:
  1. IF premium customer THEN approve refund
  2. ELSE IF < $50 THEN approve refund
  3. ELSE reject
  → Rigid, can't handle edge cases

MUXI SOP:
  1. Check customer tier and refund amount
  2. Evaluate refund request based on policy
  3. Use judgment for edge cases
  → Agent adapts to context
```

**The difference:**
- **Workflows** tell the system exactly what to do
- **SOPs** tell the agent what outcome to achieve

Agents can:
- Skip irrelevant steps
- Ask clarifying questions
- Adapt to unexpected situations
- Apply domain knowledge
- Escalate when uncertain

The result: **consistent outcomes** through adaptive behavior, not brittle automation.

> [!TIP]
> **Start with one SOP for your most common task.** Get it working well before adding more. SOPs compound - a few good ones beat many mediocre ones.

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
| Processes with known best practices | One-time tasks |
| Tasks with clear outcomes | Simple questions |
| Critical procedures that need consistency | Creative/exploratory work |
| Compliance requirements | General conversation |
| Common customer flows | Novel situations |

Examples of good SOP candidates:
- Customer onboarding/offboarding
- Incident response procedures
- Refund/return processing
- Data export requests
- Account verification
- Bug report triage

> [!TIP]
> SOPs work best when there's a **repeatable pattern** but **context still matters**. The agent follows the guidelines while adapting to specifics.

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

| Rigid Workflows | SOPs + Agents |
|----------------|---------------|
| Brittle automation | Adaptive guidance |
| Breaks on edge cases | Handles unexpected situations |
| IF/THEN logic trees | Intelligent interpretation |
| Can't ask questions | Seeks clarification when needed |
| One-size-fits-all | Context-aware decisions |

The result: **consistent outcomes through intelligent adaptation**, not brittle automation.

---

## Quick Setup

Create an agent file:

```yaml
# agents/support.afs
schema: "1.0.0"
id: support
name: Support Agent
description: Customer support specialist

system_message: Customer support specialist.
```

Add SOP file to `sops/customer-onboarding.md`, restart - it works automatically.

---

## Learn More

- [Create SOPs Guide](../guides/create-sops.md) - Write your first SOP
- [SOPs Reference](../reference/sops.md) - Syntax and options
- [The Overlord](overlord.md) - How routing priority works
