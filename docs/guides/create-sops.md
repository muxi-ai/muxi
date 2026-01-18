---
title: Create SOPs
description: Define standard procedures for consistent agent behavior
---

# Create SOPs

## Define standard procedures for your agents to follow

SOPs (Standard Operating Procedures) ensure agents handle specific tasks consistently. When a user request matches an SOP, the agent follows that procedure.

## Why SOPs?

> **TL;DR:** We don't believe traditional workflows are agentic.

Traditional workflow automation (if X then Y) is rigid and breaks when reality doesn't match the script. Instead, MUXI's Overlord creates workflows **dynamically** based on each user request.

**But sometimes you need consistency.** SOPs are predefined templates that tell agents "this is how we do things" - ensuring compliance, quality, and repeatability for specific procedures.

| Dynamic Workflows | SOPs |
|-------------------|------|
| Created per-request by Overlord | Predefined by developers |
| Adapts to context | Consistent execution |
| For novel situations | For repeatable procedures |

**SOPs can be strict or flexible:**
- **Template mode**: Follow exactly as written (for compliance)
- **Guide mode**: Use as guidelines, optimize as needed (default)


## Quick Start

### 1. Create SOP File

Create a markdown file in `sops/`:

```markdown
<!-- sops/customer-onboarding.md -->
---
type: sop
name: Customer Onboarding
description: Standard process for setting up new customers
mode: guide
tags: customer, onboarding, new user, setup
---

# Customer Onboarding

Set up new customers with all required accounts.

## Steps

1. **Gather Information**
   Ask for company name, contact email, and billing address.

2. **Create Account**
   Set up customer record in the system.

3. **Send Welcome Email**
   Email welcome package with login credentials.

4. **Schedule Follow-up**
   Create reminder for 7-day check-in call.

## Expected Outcome

Customer has active account and received welcome email.
```

### 2. Test It

```
User:  "I need to onboard a new customer"
Agent: [Follows the onboarding SOP]
```

The agent will follow your defined steps automatically.


## SOP Structure

### Frontmatter (Required)

```yaml
---
type: sop                    # Must be "sop"
name: Procedure Name         # Human-readable name
description: Brief desc      # Used for search matching
mode: guide                  # "guide" (flexible) or "template" (strict)
tags: key1, key2             # Keywords for matching
bypass_approval: true        # Skip workflow approval
---
```

### Body

```markdown
# Title

Description of what this procedure does.

## Steps

1. **Step Name**
   What to do in this step.

2. **Another Step**
   Next action.

## Expected Outcome

What happens when complete.
```


## Execution Modes

### Guide Mode (Default)

```yaml
mode: guide
```

- Agent uses SOP as guidance
- Can optimize and parallelize steps
- Best for most procedures

### Template Mode

```yaml
mode: template
```

- Agent follows SOP exactly
- Every step executed in order
- For compliance and audits


## Directives

Add directives in square brackets to control execution:

### Route to Specific Agent

```markdown
1. **Code Review** [agent:senior-dev]
   Have senior developer review the code.
```

### Use Specific Tool

```markdown
2. **Create Ticket** [mcp:jira/create-issue]
   Create a Jira ticket for tracking.
```

### Mark Critical Steps

```markdown
3. **Audit Log** [critical]
   This step cannot be skipped.
```


## Examples

### Refund Processing

```markdown
---
type: sop
name: Refund Processing
description: Process customer refund requests
mode: template
tags: refund, customer, payment
---

# Refund Processing

## Steps

1. **Verify Purchase** [critical]
   Look up order and confirm it exists.

2. **Check Policy**
   Verify request meets refund criteria.

3. **Process Refund** [mcp:stripe/refund]
   Issue refund through payment system.

4. **Send Confirmation**
   Email customer with confirmation.
```

### Incident Response

```markdown
---
type: sop
name: Incident Response
description: Handle production incidents
mode: guide
tags: incident, outage, alert, emergency
---

# Incident Response

## Steps

1. **Acknowledge** [critical]
   Confirm receipt of incident alert.

2. **Assess Severity** [agent:ops]
   Determine impact and priority level.

3. **Notify Stakeholders**
   Alert relevant teams based on severity.

4. **Investigate**
   Identify root cause.

5. **Remediate**
   Apply fix or workaround.

6. **Document** [critical]
   Record incident details and resolution.
```

### Code Review

```markdown
---
type: sop
name: Code Review
description: Standard PR review process
mode: guide
tags: code, review, pr, github
---

# Code Review

## Steps

1. **Fetch PR** [mcp:github/get-pr]
   Get pull request details.

2. **Review Code**
   Check for bugs, style issues, best practices.

3. **Security Check**
   Look for vulnerabilities.

4. **Provide Feedback** [agent:senior-dev]
   Post constructive review comments.
```


## Organization

Structure your SOPs logically:

```
sops/
├── customer/
│   ├── onboarding.md
│   ├── offboarding.md
│   └── refund.md
├── operations/
│   ├── incident-response.md
│   └── deployment.md
└── development/
    ├── code-review.md
    └── bug-triage.md
```


## Best Practices

1. **Clear descriptions** - Help semantic search find the right SOP
2. **Relevant tags** - Include keywords users might say
3. **Focused scope** - One SOP per procedure
4. **Use guide mode** - Unless strict compliance is required
5. **Mark critical steps** - Ensure important steps aren't skipped


## Troubleshooting

### SOP Not Matching

- Check description matches user intent
- Add more relevant tags
- Verify `type: sop` in frontmatter

### Wrong Mode Behavior

- Template mode: exact execution
- Guide mode: optimized execution
- Check `mode:` field in frontmatter

### Steps Being Skipped

- Use `[critical]` directive for required steps
- Consider template mode for strict procedures


## Learn More

- [SOPs Concept](concepts/standard-operating-procedures.md) - Full overview
- [SOPs Reference](reference/sops.md) - Complete syntax
- [The Overlord](concepts/overlord.md) - How matching works
