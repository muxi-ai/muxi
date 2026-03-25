---
title: SOPs
description: Reference for Standard Operating Procedures
---

# SOPs

## Reference for Standard Operating Procedures

SOPs are markdown files in `sops/` that define procedures agents follow for specific tasks.

> [!TIP]
> **New to SOPs?** Read [SOPs Concept →](../concepts/standard-operating-procedures.md) first.

## File Structure

```
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
| `synthesis` | No | boolean | `true` | Pass output through synthesis LLM before returning |

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

## Synthesis Control

By default, the Overlord passes all SOP outputs through a synthesis LLM that unifies the response into a single voice. This is useful for most procedures, but can be counterproductive when the SOP specifies a strict output format (e.g., raw JSON, CSV, or a particular template).

Set `synthesis: false` in the frontmatter to return the last task's raw output without synthesis:

```yaml
---
type: sop
name: Daily Report
description: Generate daily metrics report
mode: template
synthesis: false
tags: report, daily, metrics
---
```

When `synthesis: false`:
- The last completed task's output is returned directly
- No synthesis LLM call is made
- Format instructions in the SOP body are preserved exactly

Use `synthesis: false` when:
- The SOP requests output in a specific format (JSON, CSV, tables)
- The SOP already produces a final, user-ready response
- You want to avoid the overhead of an extra LLM call

## Parallel Execution

In **guide mode**, the Overlord automatically parallelizes independent steps. Steps that don't depend on each other run concurrently, which can significantly reduce execution time.

### How It Works

The workflow decomposer analyzes step dependencies and builds an execution graph:

- **Independent steps** (no shared data dependencies) run in parallel
- **Dependent steps** (need output from a prior step) wait for their dependencies
- **Fan-in patterns** (multiple steps feeding one final step) are supported

### Writing Parallelizable SOPs

Structure your steps so that independent work is clearly separated from synthesis:

```
---
type: sop
name: Morning Briefing
description: Compile morning briefing from multiple sources
mode: template
synthesis: false
tags: briefing, morning, summary
---

# Morning Briefing

## Steps

1. **Fetch Calendar** [agent:assistant]
   Retrieve today's calendar events.

2. **Fetch Emails** [agent:assistant]
   Get unread email summaries.

3. **Fetch Tasks** [agent:assistant]
   List pending tasks.

4. **Compile Briefing**
   Combine all results into a structured report.
```

Steps 1-3 have no dependencies on each other and will execute in parallel. Step 4 depends on all three and runs after they complete.

### Tips

- **Avoid unnecessary ordering.** If steps don't need each other's output, don't write them as a linear sequence of dependent actions. Use clear, independent descriptions.
- **Use template mode for parallel fetch patterns.** Template mode with independent steps gives you both strict execution and parallelism.
- **Fan-in is supported.** Multiple parallel steps can feed into a single synthesis step.
- **Linear chains run sequentially.** If every step depends on the previous one (A→B→C), they execute in order regardless of mode.

## Directives

Embed in step descriptions:

| Directive | Syntax | Description |
|-----------|--------|-------------|
| Agent routing | `[agent:agent-id]` | Route step to specific agent |
| MCP tool | `[mcp:server/tool]` | Use specific MCP tool |
| Critical | `[critical]` | Step cannot be optimized away |
| File reference | `[file:path/to/file.md]` | Include file content |

### Examples

```
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

```
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

- [SOPs Concept](../concepts/standard-operating-procedures.md) - Overview and examples
- [Create SOPs](../guides/create-sops.md) - Step-by-step guide
- [The Overlord](../concepts/overlord.md) - How SOP matching works
