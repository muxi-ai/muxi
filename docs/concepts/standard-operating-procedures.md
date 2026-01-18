---
title: Standard Operating Procedures
description: Predefined workflows that agents follow for consistent execution
---
# Standard Operating Procedures (SOPs)

## Predefined workflows that agents follow for consistent execution

SOPs are markdown documents in your `sops/` directory that define step-by-step procedures for common tasks. When a user request matches an SOP (via semantic search), the agent follows that procedure instead of improvising.

## Why SOPs?

```
Without SOPs:
  User:  "Onboard new customer"
  Agent: Improvises each time, inconsistent results

With SOPs:
  User:  "Onboard new customer"
  Overlord: Finds matching SOP via semantic search
  Agent: Follows defined procedure every time
```

SOPs ensure:
- **Consistency** - Same process every time
- **Quality** - Best practices baked in
- **Compliance** - Required steps never skipped
- **Efficiency** - No figuring out what to do

---

## SOP vs Workflows

> **Key insight:** SOPs are intelligent guidelines, not rigid automation.

**Traditional workflow automation:**
- Fixed branching logic (`if X then Y`)
- Hard-coded sequences
- Breaks when reality doesn't match the script

**MUXI SOPs:**
- Semantic matching to find relevant procedures
- Two execution modes (strict or flexible)
- Agents adapt to context while following guidelines

---

## How SOPs Work

### Matching Flow

```
User request
     ↓
Semantic search against SOPs
     ↓
Match found (relevance ≥ 0.7)?
     ↓ YES
Execute SOP (bypasses normal routing)
     ↓ NO
Route to agent normally
```

SOPs have **highest routing priority** - when matched, they override normal agent routing.

---

## SOP Structure

SOPs are Markdown files with YAML frontmatter in your `sops/` directory:

```markdown
---
type: sop
name: Customer Onboarding
description: Standard process for new customer setup
mode: guide
tags: customer, onboarding, new user
bypass_approval: true
---

# Customer Onboarding

Set up new customers with all required accounts and configurations.

## Steps

1. **Gather Information** [agent:support]
   Collect company name, contact email, and billing details.

2. **Create Account** [mcp:crm/create-customer]
   Set up customer record in CRM system.

3. **Send Welcome Email** [critical]
   Email welcome package with login credentials.
   This step cannot be skipped.

4. **Schedule Follow-up**
   Create reminder for 7-day check-in.

## Expected Outcome

Customer has active account with welcome email sent.
```

### Frontmatter Fields

| Field | Required | Description |
|-------|----------|-------------|
| `type` | Yes | Must be `"sop"` |
| `name` | Yes | Human-readable name |
| `description` | Yes | Brief description for search matching |
| `mode` | No | `"template"` (strict) or `"guide"` (flexible) |
| `tags` | No | Keywords for search matching |
| `bypass_approval` | No | Skip workflow approval (default: true) |

---

## Execution Modes

### Template Mode (Strict)

```yaml
mode: template
```

- Follow SOP exactly as written
- Execute every step without skipping
- Maintain exact order
- All directives are mandatory

**Use for:** Compliance procedures, safety protocols, regulated processes

### Guide Mode (Flexible)

```yaml
mode: guide
```

- Use SOP as structured guidance
- Optimize for efficiency
- Combine trivial operations
- Execute independent steps in parallel

**Use for:** Development workflows, best practices, standard operations

---

## Directives

Embed instructions in step descriptions:

### Agent Routing
```markdown
1. **Review Code** [agent:senior-dev]
   Route this step to a specific agent.
```

### MCP Tools
```markdown
2. **Create Ticket** [mcp:jira/create-issue]
   Use specific MCP tool.
```

### Critical Steps
```markdown
3. **Audit Log** [critical]
   This step cannot be optimized away.
```

### File References
```markdown
4. **Use Template** [file:templates/report.md]
   Include external file content.
```

---

## Directory Structure

```
my-formation/
├── formation.afs
├── agents/
├── sops/
│   ├── customer-onboarding.md
│   ├── incident-response.md
│   ├── code-review.md
│   └── refund-processing.md
└── ...
```

SOPs are auto-discovered from the `sops/` directory.

---

## Examples

### Compliance Audit (Template Mode)

```markdown
---
type: sop
name: Security Compliance Audit
description: Mandatory security audit procedure
mode: template
tags: security, audit, compliance
bypass_approval: false
---

# Security Compliance Audit

## Steps

1. **Verify Credentials** [agent:security] [critical]
   Authenticate user and verify audit permissions.

2. **System Inventory** [mcp:inventory/scan]
   Document all system components and versions.

3. **Vulnerability Scan** [agent:security] [critical]
   Run comprehensive vulnerability assessment.

4. **Generate Report** [critical]
   Create signed audit report with findings.
```

### Code Review (Guide Mode)

```markdown
---
type: sop
name: Pull Request Review
description: Standard code review workflow
mode: guide
tags: code, review, pr, github
bypass_approval: true
---

# Pull Request Review

## Steps

1. **Fetch PR Details** [mcp:github/pr]
   Get pull request information and changed files.

2. **Analyze Code Quality**
   Review code style, patterns, best practices.

3. **Security Review**
   Check for vulnerabilities.

4. **Post Review** [agent:senior-dev]
   Provide constructive feedback.
```

---

## When to Use SOPs

| Use SOPs For | Don't Use SOPs For |
|--------------|-------------------|
| Repeatable procedures | One-time tasks |
| Compliance requirements | Simple questions |
| Multi-step workflows | Creative/exploratory work |
| Standard operations | Novel situations |

Good SOP candidates:
- Customer onboarding/offboarding
- Incident response
- Code review procedures
- Refund processing
- Data export requests
- Compliance audits

---

## Learn More

- [Create SOPs Guide](guides/create-sops.md) - Write your first SOP
- [SOPs Reference](reference/sops.md) - Full syntax reference
- [The Overlord](overlord.md) - How SOP matching works
