---
title: Approval Configuration Reference
description: Complete reference for plan approval settings
---
# Approval Configuration Reference

## Complete reference for plan approval settings

Configure when MUXI requires user approval before executing complex workflows. Approval configuration is part of the workflow settings under `overlord.workflow`.

**Official Schema:** https://github.com/agent-formation/afs-spec

## Configuration Location

```yaml
# In formation.yaml
overlord:
  workflow:
    plan_approval_threshold: 7  # Approval threshold (1-10)
```

## Field Reference

### plan_approval_threshold

**Type:** `integer` (1-10)  
**Required:** No  
**Default:** `7`  

Complexity threshold for requiring plan approval before execution.

**Description:**  
When MUXI calculates a request's complexity score, it compares the score to this threshold. If the score is at or above the threshold, MUXI presents an execution plan and waits for user approval before proceeding.

```yaml
overlord:
  workflow:
    plan_approval_threshold: 7  # Default: require approval for complexity ≥ 7
```

## How It Works

1. **Request arrives** - User makes a request
2. **Complexity calculated** - MUXI scores complexity (1-10)
3. **Threshold check** - Compare score to `plan_approval_threshold`
4. **If score ≥ threshold:**
   - Generate execution plan
   - Present plan to user
   - Wait for approval (yes/no)
   - Execute if approved, cancel if rejected
5. **If score < threshold:**
   - Execute immediately without approval

## Threshold Values

| Threshold | When Approvals Trigger | Use Case |
|-----------|------------------------|----------|
| **1** | All requests | Maximum control (even simple queries) |
| **3** | Most requests | Very conservative |
| **5** | Moderate complexity+ | Balanced control |
| **7** | Complex workflows only | **Default** - practical for production |
| **9** | Very complex only | Minimal interruptions |
| **10** | Never | Effectively disables approvals |

## Examples

### Default (Balanced)

```yaml
overlord:
  workflow:
    plan_approval_threshold: 7  # Complex workflows only
```

**Requires approval for:**
- Multi-step research and analysis
- Workflows with 5+ subtasks
- Operations affecting multiple systems

**No approval for:**
- Simple questions
- Single-agent tasks
- Quick lookups

### Strict (Maximum Control)

```yaml
overlord:
  workflow:
    plan_approval_threshold: 5  # Most tasks require approval
```

**Requires approval for:**
- Any task requiring multiple steps
- Most agent interactions
- Operations with dependencies

Good for: Production environments, sensitive operations, compliance requirements.

### Lenient (Speed Priority)

```yaml
overlord:
  workflow:
    plan_approval_threshold: 9  # Only very complex workflows
```

**Requires approval for:**
- Extremely complex multi-agent workflows
- Tasks with 10+ subtasks
- Long-running operations

Good for: Development, testing, trusted environments.

### Disabled

```yaml
overlord:
  workflow:
    plan_approval_threshold: 10  # Never require approval
```

All requests execute immediately without approval, regardless of complexity.

Good for: Automated systems, background jobs, development.

## Complexity Scoring

MUXI calculates complexity based on:

- **Number of steps** - More steps = higher complexity
- **Agent requirements** - Multiple agents = higher complexity
- **Tool usage** - External tools = higher complexity
- **Dependencies** - Task dependencies = higher complexity
- **Estimated time** - Longer operations = higher complexity

See [Workflows & Task Decomposition](../concepts/workflows.md) for details on complexity calculation.

## Configuration with Other Settings

### With Complexity Method

```yaml
overlord:
  workflow:
    complexity_method: "llm"           # Use LLM for scoring
    complexity_threshold: 7.0          # Trigger workflows at 7+
    plan_approval_threshold: 7         # Require approval at 7+
```

**Note:** `complexity_threshold` determines when workflows are created. `plan_approval_threshold` determines when approval is required. They can be different values.

### With Async Processing

```yaml
overlord:
  workflow:
    plan_approval_threshold: 7

async:
  threshold_seconds: 30
```

If a request requires approval AND is async:
1. Present plan immediately
2. Wait for approval
3. If approved, execute in background
4. Notify via webhook when complete

### With Custom Persona

```yaml
overlord:
  persona: |
    When presenting execution plans for approval, clearly explain the impact
    of each step. Highlight any irreversible actions or operations that affect
    production systems. Use clear language and provide time estimates.
  
  workflow:
    plan_approval_threshold: 6  # Slightly lower for sensitive operations
```

The persona influences how approval messages are presented.

## Approval Flow Example

**Request:** "Research competitors, create comparison report, post to Slack"

**Complexity score:** 9/10

**If `plan_approval_threshold: 7`:**

```
MUXI: I've created a plan for this task:

1. Research competitors (researcher agent)
   - Search for top 5 competitors
   - Analyze their offerings
   - Estimated time: 2-3 minutes

2. Create comparison report (analyst + writer agents)
   - Compare features and pricing
   - Generate PDF report
   - Estimated time: 1-2 minutes

3. Post to Slack (slack-agent)
   - Post to #marketing channel
   - Include report as attachment
   - Estimated time: <10 seconds

Total estimated time: 3-6 minutes

Proceed? [y/N]

User: y

MUXI: Starting execution...
```

**If `plan_approval_threshold: 10`:**

```
MUXI: [Immediately starts execution without approval]
```

## Best Practices

### DO ✅

**Match environment to threshold:**
```yaml
# Development
overlord:
  workflow:
    plan_approval_threshold: 10  # No interruptions

# Staging
overlord:
  workflow:
    plan_approval_threshold: 8   # Review complex operations

# Production
overlord:
  workflow:
    plan_approval_threshold: 6   # Review most operations
```

**Consider your use case:**
- Customer-facing chatbot → Higher threshold (8-9)
- Internal operations tool → Lower threshold (5-6)
- Automated background jobs → Disabled (10)

**Provide context in persona:**
```yaml
overlord:
  persona: |
    For operations requiring approval, explain what will be modified,
    estimate the time required, and highlight any risks.
```

### DON'T ❌

**Don't set too low in production:**
```yaml
# ❌ Bad: Users will be frustrated
overlord:
  workflow:
    plan_approval_threshold: 2  # Approving everything!
```

**Don't disable without considering risks:**
```yaml
# ❌ Dangerous for sensitive operations
overlord:
  workflow:
    plan_approval_threshold: 10  # No approval for deployments!
```

**Don't forget to test:**
- Test with various request complexities
- Verify approval messages are clear
- Ensure timeout handling works

## Troubleshooting

### Approvals Too Frequent

**Problem:** Approving too many simple requests.

**Solution:** Increase threshold.

```yaml
# Was: 5 (too many approvals)
# Fix: 7 (only complex workflows)
overlord:
  workflow:
    plan_approval_threshold: 7
```

### Approvals Not Showing

**Problem:** Expected approval didn't trigger.

**Solution:** Request complexity is below threshold.

```yaml
# Check complexity scoring
overlord:
  workflow:
    complexity_method: "llm"  # More accurate scoring
    plan_approval_threshold: 6  # Lower threshold
```

### Approval Messages Unclear

**Problem:** Users don't understand what they're approving.

**Solution:** Improve persona guidance.

```yaml
overlord:
  persona: |
    When presenting plans, use clear language. Explain:
    - What will be modified
    - Estimated time
    - Any irreversible actions
    - Potential risks
```

## Related Configuration

### Async Threshold

```yaml
async:
  threshold_seconds: 30  # Switch to async after 30s
```

Approvals work with both sync and async modes.

### Workflow Timeout

```yaml
overlord:
  workflow:
    timeouts:
      workflow_timeout: 3600  # Total workflow timeout
```

If user doesn't approve within the timeout, the request is cancelled.

## Learn More

- **[Agent Formation Schema](https://github.com/agent-formation/afs-spec)** - Official schema specification
- [Human-in-the-Loop](../concepts/approvals.md) - Concept guide
- [Workflows & Task Decomposition](../concepts/workflows.md) - Workflow configuration
- [Workflow Configuration Reference](workflows.md) - All workflow settings
- [The Overlord](../concepts/overlord.md) - How orchestration works
