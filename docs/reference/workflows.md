---
title: Workflow Configuration
description: Complete reference for workflow and task decomposition settings
---
# Workflow Configuration

## Complete reference for workflow and task decomposition settings

Configure how MUXI decomposes complex requests into multi-agent workflows. Control parallel execution, timeouts, retries, and approval requirements.

## Configuration Location

```yaml
# In formation.yaml
overlord:
  auto_decomposition: true
  complexity_threshold: 7.0

workflow:
  # Workflow settings here
```

## Overlord Settings

### auto_decomposition
```yaml
overlord:
  auto_decomposition: true
```

**Type:** `boolean`  
**Default:** `true`  
**Description:** Enable automatic workflow creation for complex requests

**When disabled:**
- All requests route to single agent
- No workflow decomposition
- Useful for testing/debugging

### complexity_threshold
```yaml
overlord:
  complexity_threshold: 7.0
```

**Type:** `float`  
**Range:** `0.0` to `10.0`  
**Default:** `7.0`  
**Description:** Complexity score threshold to trigger workflows

**Scoring:**
- `0-3`: Simple (single agent)
- `4-6`: Moderate (single agent, may use tools)
- `7-10`: Complex (workflow mode)

**Example:**
```yaml
complexity_threshold: 8.0  # More selective (only very complex)
complexity_threshold: 6.0  # More aggressive (more workflows)
```

## Workflow Settings

### max_parallel_tasks
```yaml
workflow:
  max_parallel_tasks: 10
```

**Type:** `integer`  
**Default:** `10`  
**Range:** `1` to `50`  
**Description:** Maximum concurrent tasks

**Considerations:**
- Higher = faster execution (if tasks are independent)
- Lower = less resource usage, fewer API rate limits

**Example:**
```yaml
# Conservative (avoid rate limits)
max_parallel_tasks: 3

# Aggressive (fast execution)
max_parallel_tasks: 20
```

### min_tasks_for_parallel
```yaml
workflow:
  min_tasks_for_parallel: 3
```

**Type:** `integer`  
**Default:** `3`  
**Description:** Minimum tasks required to enable parallel execution

**Rationale:**
- Overhead of parallel execution not worth it for 1-2 tasks
- Only parallelize if ≥ 3 tasks

### task_timeout
```yaml
workflow:
  task_timeout: 300
```

**Type:** `integer` (seconds)  
**Default:** `300` (5 minutes)  
**Range:** `30` to `3600`  
**Description:** Maximum time per individual task

**Recommendations:**
```yaml
# Quick operations
task_timeout: 60     # 1 minute

# Standard operations
task_timeout: 300    # 5 minutes

# Long-running operations
task_timeout: 900    # 15 minutes
```

### workflow_timeout
```yaml
workflow:
  workflow_timeout: 1800
```

**Type:** `integer` (seconds)  
**Default:** `1800` (30 minutes)  
**Range:** `60` to `7200`  
**Description:** Maximum time for entire workflow

**Note:** Should be > task_timeout × expected_max_tasks

### retry_failed_tasks
```yaml
workflow:
  retry_failed_tasks: true
```

**Type:** `boolean`  
**Default:** `true`  
**Description:** Automatically retry failed tasks

### max_retries
```yaml
workflow:
  max_retries: 3
```

**Type:** `integer`  
**Default:** `3`  
**Range:** `0` to `10`  
**Description:** Maximum retry attempts per task

**Requires:** `retry_failed_tasks: true`

### retry_delay
```yaml
workflow:
  retry_delay: 5
```

**Type:** `integer` (seconds)  
**Default:** `5`  
**Range:** `1` to `60`  
**Description:** Delay between retry attempts

**Strategies:**
```yaml
# Quick retry (transient errors)
retry_delay: 2

# Standard backoff
retry_delay: 5

# Long backoff (rate limits)
retry_delay: 30
```

### retry_exponential_backoff
```yaml
workflow:
  retry_exponential_backoff: true
```

**Type:** `boolean`  
**Default:** `false`  
**Description:** Use exponential backoff for retries

**Behavior:**
```
Attempt 1: Wait retry_delay (5s)
Attempt 2: Wait retry_delay × 2 (10s)
Attempt 3: Wait retry_delay × 4 (20s)
```

## Execution Settings

### async_threshold
```yaml
workflow:
  async_threshold: 10
```

**Type:** `integer` (seconds)  
**Default:** `10`  
**Description:** Estimated time threshold to switch to async mode

**Behavior:**
```
Estimated time < 10s → Sync (blocking)
Estimated time ≥ 10s → Async (non-blocking)
```

### enable_async
```yaml
workflow:
  enable_async: true
```

**Type:** `boolean`  
**Default:** `true`  
**Description:** Allow async execution mode

**When disabled:**
- All workflows run synchronously
- Long tasks may timeout
- Not recommended for production

### force_async
```yaml
workflow:
  force_async:
    - pattern: "research.*"
    - pattern: "analyze.*codebase"
```

**Type:** `array<object>`  
**Description:** Always use async for matching patterns

**Example:**
```yaml
force_async:
  - pattern: "research"
  - pattern: "analyze"
  - pattern: "generate.*report"
```

## Approval Settings

### requires_approval
```yaml
workflow:
  requires_approval: true
```

**Type:** `boolean`  
**Default:** `false`  
**Description:** Require user approval before executing workflows

### approval_threshold
```yaml
workflow:
  approval_threshold: 10
```

**Type:** `float`  
**Range:** `0.0` to `10.0`  
**Default:** `10` (disabled)  
**Description:** Complexity score to trigger approval

**Example:**
```yaml
# Require approval for very complex tasks only
requires_approval: false
approval_threshold: 9

# Require approval for moderately complex tasks
requires_approval: false
approval_threshold: 7

# Require approval for all workflows
requires_approval: true
```

### approval_timeout
```yaml
workflow:
  approval_timeout: 300
```

**Type:** `integer` (seconds)  
**Default:** `300` (5 minutes)  
**Description:** Time to wait for user approval

**Behavior:**
```
Present plan → Wait for approval
         ↓
300 seconds pass, no response
         ↓
Cancel workflow, return timeout error
```

### show_cost_estimate
```yaml
workflow:
  show_cost_estimate: true
```

**Type:** `boolean`  
**Default:** `false`  
**Description:** Include cost estimate in approval message

**Requires:** Cost tracking enabled

### show_time_estimate
```yaml
workflow:
  show_time_estimate: true
```

**Type:** `boolean`  
**Default:** `true`  
**Description:** Include time estimate in approval message

### require_explicit_yes
```yaml
workflow:
  require_explicit_yes: true
```

**Type:** `boolean`  
**Default:** `false`  
**Description:** Only accept "y" or "yes" (reject "ok", "sure", etc.)

### approval_rules
```yaml
workflow:
  approval_rules:
    - match: "deploy.*production"
      requires_approval: true
      message: "⚠️ PRODUCTION DEPLOYMENT"
    
    - match: "delete|remove"
      requires_approval: true
      require_confirmation: true
```

**Type:** `array<object>`  
**Description:** Pattern-based approval rules

**Fields:**
- `match`: Regex pattern
- `requires_approval`: Force approval
- `message`: Custom approval message
- `require_confirmation`: Require typing "CONFIRM"

## Logging & Observability

### log_workflows
```yaml
workflow:
  log_workflows: true
```

**Type:** `boolean`  
**Default:** `false`  
**Description:** Log workflow creation and execution

**Output:**
```
[WORKFLOW] Created workflow for request req_abc123
[WORKFLOW]   Task 1: research (researcher)
[WORKFLOW]   Task 2: analyze (analyst) - depends on [1]
[WORKFLOW] Executing Task 1
[WORKFLOW] Task 1 completed in 3.2s
[WORKFLOW] Executing Task 2
[WORKFLOW] Task 2 completed in 2.1s
[WORKFLOW] Workflow completed in 5.5s
```

### log_task_results
```yaml
workflow:
  log_task_results: true
```

**Type:** `boolean`  
**Default:** `false`  
**Description:** Log individual task results

**Warning:** May log sensitive data

### emit_events
```yaml
workflow:
  emit_events: true
```

**Type:** `boolean`  
**Default:** `true`  
**Description:** Emit workflow events for observability

**Events:**
- `workflow.created`
- `workflow.started`
- `task.started`
- `task.completed`
- `task.failed`
- `workflow.completed`
- `workflow.failed`

## Resource Limits

### max_workflow_memory
```yaml
workflow:
  max_workflow_memory: "2GB"
```

**Type:** `string`  
**Default:** `"2GB"`  
**Description:** Maximum memory per workflow

### max_workflow_cpu
```yaml
workflow:
  max_workflow_cpu: 2.0
```

**Type:** `float` (CPU cores)  
**Default:** `2.0`  
**Description:** Maximum CPU per workflow

## Complete Example

```yaml
overlord:
  auto_decomposition: true
  complexity_threshold: 7.0

workflow:
  # Execution
  max_parallel_tasks: 10
  min_tasks_for_parallel: 3
  task_timeout: 300
  workflow_timeout: 1800
  
  # Async
  async_threshold: 10
  enable_async: true
  force_async:
    - pattern: "research.*"
    - pattern: "analyze.*codebase"
  
  # Retries
  retry_failed_tasks: true
  max_retries: 3
  retry_delay: 5
  retry_exponential_backoff: true
  
  # Approvals
  requires_approval: false
  approval_threshold: 9
  approval_timeout: 300
  show_time_estimate: true
  show_cost_estimate: true
  approval_rules:
    - match: "deploy.*production"
      requires_approval: true
      message: "⚠️ PRODUCTION DEPLOYMENT"
  
  # Observability
  log_workflows: true
  log_task_results: false
  emit_events: true
  
  # Resources
  max_workflow_memory: "2GB"
  max_workflow_cpu: 2.0
```

## Presets

### Conservative (Safe)
```yaml
workflow:
  max_parallel_tasks: 3
  task_timeout: 600
  retry_failed_tasks: true
  max_retries: 5
  requires_approval: true
  approval_threshold: 7
```

### Aggressive (Fast)
```yaml
workflow:
  max_parallel_tasks: 20
  task_timeout: 300
  retry_failed_tasks: true
  max_retries: 2
  requires_approval: false
```

### Development
```yaml
workflow:
  max_parallel_tasks: 5
  task_timeout: 60
  retry_failed_tasks: false
  log_workflows: true
  log_task_results: true
```

### Production
```yaml
workflow:
  max_parallel_tasks: 10
  task_timeout: 300
  retry_failed_tasks: true
  max_retries: 3
  requires_approval: false
  approval_threshold: 9
  log_workflows: false
  emit_events: true
```

## Environment Variables

```bash
# Override workflow settings
MUXI_WORKFLOW_MAX_PARALLEL=5
MUXI_WORKFLOW_TASK_TIMEOUT=600
MUXI_WORKFLOW_RETRY_ENABLED=true
```

## Validation

**Valid configuration:**
```yaml
workflow:
  max_parallel_tasks: 10      # ✓ Integer in range
  task_timeout: 300           # ✓ Positive integer
  retry_failed_tasks: true    # ✓ Boolean
```

**Invalid configuration:**
```yaml
workflow:
  max_parallel_tasks: 100     # ✗ Too high (max 50)
  task_timeout: -1            # ✗ Must be positive
  retry_failed_tasks: "yes"   # ✗ Must be boolean
```

## Troubleshooting

### Workflows Not Creating
```yaml
# Check decomposition is enabled
overlord:
  auto_decomposition: true   # Must be true
  complexity_threshold: 7.0  # Check threshold
```

### Tasks Timing Out
```yaml
# Increase timeout
workflow:
  task_timeout: 600          # Was 300, now 600
```

### Too Many Parallel Tasks
```yaml
# Hitting rate limits
workflow:
  max_parallel_tasks: 5      # Reduce from 10
```

### Retries Not Working
```yaml
# Check retry settings
workflow:
  retry_failed_tasks: true   # Must be true
  max_retries: 3             # Must be > 0
```

## Learn More

- [Workflows & Task Decomposition](../concepts/workflows.md) - User-facing explanation
- [Human-in-the-Loop](../concepts/approvals.md) - Approval system
- [How Orchestration Works](../deep-dives/orchestration.md) - Technical details
