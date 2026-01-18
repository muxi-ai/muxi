---
title: SOPs
description: Reference for Standard Operating Procedures
---

# SOPs

## Reference for Standard Operating Procedures

SOPs are markdown files in `sops/` that define procedures agents follow for specific tasks.

> [!TIP]
> **New to SOPs?** Read [SOPs Concept →](concepts/standard-operating-procedures.md) first.

## File Structure

```markdown
---
type: sop
name: Procedure Name
description: Brief description for search matching
mode: guide
tags: keyword1, keyword2
bypass_approval: true
---

# Procedure Title

Description of what this procedure does.

## Steps

1. **Step Name** [agent:optional-agent]
   Step description.
   [mcp:server/tool] Optional tool directive.

2. **Another Step** [critical]
   Critical steps cannot be optimized away.

## Expected Outcome

What happens when this SOP completes.
```

## Frontmatter Fields

| Field | Required | Type | Default | Description |
|-------|----------|------|---------|-------------|
| `type` | Yes | string | - | Must be `"sop"` |
| `name` | Yes | string | - | Human-readable name |
| `description` | Yes | string | - | Description for semantic search |
| `mode` | No | string | `"guide"` | `"template"` (strict) or `"guide"` (flexible) |
| `tags` | No | string | - | Comma-separated keywords |
| `bypass_approval` | No | boolean | `true` | Skip workflow approval |

## Execution Modes

### Template Mode

```yaml
mode: template
```

- Execute every step exactly as written
- Maintain exact order
- All directives are mandatory
- For compliance and regulated processes

### Guide Mode

```yaml
mode: guide
```

- Use SOP as guidance
- Optimize for efficiency
- Combine trivial operations
- Parallelize independent steps
- For standard operations

## Directives

Embed in step descriptions:

| Directive | Syntax | Description |
|-----------|--------|-------------|
| Agent routing | `[agent:agent-id]` | Route step to specific agent |
| MCP tool | `[mcp:server/tool]` | Use specific MCP tool |
| Critical | `[critical]` | Step cannot be optimized away |
| File reference | `[file:path/to/file.md]` | Include file content |

### Examples

```markdown
1. **Code Review** [agent:senior-dev]
   Route to senior developer agent.

2. **Create Ticket** [mcp:jira/create-issue]
   Use Jira MCP tool.

3. **Audit Log** [critical]
   This step is always executed.

4. **Apply Template** [file:templates/report.md]
   Include template file.
```

## Directory Structure

```
sops/
├── customer-onboarding.md
├── incident-response.md
├── code-review.md
└── compliance/
    ├── security-audit.md
    └── data-export.md
```

SOPs are auto-discovered from `sops/` directory.

## Matching Behavior

SOPs match via semantic search:

1. User request is embedded
2. Search against all SOP descriptions and tags
3. Match if relevance score ≥ 0.7
4. Matched SOP executes (bypasses normal routing)

## Example: Complete SOP

```markdown
---
type: sop
name: Customer Refund Processing
description: Process customer refund requests
mode: template
tags: refund, customer, payment, return
bypass_approval: false
---

# Customer Refund Processing

Handle refund requests according to company policy.

## Steps

1. **Verify Purchase** [agent:support] [critical]
   Look up order in system.
   Confirm purchase exists and is refund-eligible.

2. **Check Refund Policy** [critical]
   Verify request meets refund criteria:
   - Within 30-day window
   - Item condition acceptable
   - No previous refund on this order

3. **Process Refund** [mcp:stripe/create-refund]
   Issue refund through payment processor.
   Amount must match original purchase.

4. **Send Confirmation** [agent:support]
   Email customer with refund confirmation.
   Include expected timeline (3-5 business days).

5. **Update Records** [critical]
   Log refund in CRM system.
   Update customer account status.

## Expected Outcome

Customer receives refund confirmation email.
Refund processed within 24 hours.
All records updated for audit trail.
```

## Related

- [SOPs Concept](concepts/standard-operating-procedures.md) - Overview and examples
- [Create SOPs](guides/create-sops.md) - Step-by-step guide
- [The Overlord](concepts/overlord.md) - How SOP matching works
