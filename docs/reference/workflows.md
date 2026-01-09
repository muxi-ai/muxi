---
title: Workflow Configuration Reference
description: Complete reference for overlord workflow settings
---
# Workflow Configuration Reference

## Complete reference for overlord workflow settings

Configure how MUXI decomposes complex tasks, routes work to agents, handles errors, and executes workflows. All workflow settings are under `overlord.workflow` in your formation.

**Official Schema:** https://github.com/agent-formation/afs-spec

## Configuration Location

```yaml
# In formation.yaml
overlord:
  workflow:
    # Workflow settings here
```

## Core Workflow Settings

### auto_decomposition

**Type:** `boolean`  
**Default:** `true`  

Enable automatic task decomposition for complex requests.

```yaml
overlord:
  workflow:
    auto_decomposition: true  # Enable (default)
```

When enabled, MUXI automatically breaks complex tasks into subtasks and coordinates their execution across multiple agents.

### plan_approval_threshold

**Type:** `integer` (1-10)  
**Default:** `7`  

Complexity threshold for requiring plan approval before execution.

```yaml
overlord:
  workflow:
    plan_approval_threshold: 7  # Require approval for complexity ≥ 7
```

Requests with complexity scores at or above this threshold will present an execution plan and wait for user approval before proceeding.

See [Human-in-the-Loop](../concepts/approvals.md) for details.

## Complexity Calculation

### complexity_method

**Type:** `string`  
**Options:** `heuristic`, `llm`, `custom`, `hybrid`  
**Default:** `heuristic`  

Method for calculating request complexity.

```yaml
overlord:
  workflow:
    complexity_method: "heuristic"  # Default: rule-based scoring
```

**Methods:**
- **heuristic** - Rule-based scoring (fast, deterministic)
- **llm** - LLM evaluates complexity (accurate, slower)
- **custom** - Custom complexity function (advanced)
- **hybrid** - Combines multiple methods with weights

### complexity_threshold

**Type:** `number` (1.0-10.0)  
**Default:** `7.0`  

Complexity threshold for triggering workflow decomposition.

```yaml
overlord:
  workflow:
    complexity_threshold: 7.0  # Trigger workflows at 7+
```

Requests scoring at or above this threshold trigger automatic workflow creation.

### complexity_weights

**Type:** `object`  
**Default:** See example  

Weights for hybrid complexity calculation (only used when `complexity_method: "hybrid"`).

```yaml
overlord:
  workflow:
    complexity_method: "hybrid"
    complexity_weights:
      heuristic: 0.4
      llm: 0.4
      custom: 0.2
```

Weights must sum to 1.0.

## Task Routing

### routing_strategy

**Type:** `string`  
**Options:** `capability_based`, `load_balanced`, `priority_based`, `custom`, `round_robin`, `specialized`  
**Default:** `capability_based`  

Strategy for routing tasks to agents.

```yaml
overlord:
  workflow:
    routing_strategy: "capability_based"  # Default: match capabilities
```

**Strategies:**
- **capability_based** - Match task to agent capabilities (default)
- **load_balanced** - Distribute evenly across agents
- **priority_based** - Route to highest priority agents
- **round_robin** - Cycle through agents
- **specialized** - Prefer specialist agents
- **custom** - Custom routing logic

### enable_agent_affinity

**Type:** `boolean`  
**Default:** `true`  

Prefer agents that successfully completed similar tasks.

```yaml
overlord:
  workflow:
    enable_agent_affinity: true  # Learn from past successes
```

When enabled, MUXI tracks which agents succeeded at specific task types and prefers them for similar future tasks.

## Error Handling

### error_recovery

**Type:** `string`  
**Options:** `fail_fast`, `retry_with_backoff`, `retry_with_alternate`, `skip_and_continue`, `compensate`, `manual_intervention`  
**Default:** `retry_with_backoff`  

Strategy for recovering from task failures.

```yaml
overlord:
  workflow:
    error_recovery: "retry_with_backoff"  # Default: retry with delays
```

**Strategies:**
- **fail_fast** - Stop immediately on first error
- **retry_with_backoff** - Retry with exponential backoff
- **retry_with_alternate** - Try alternate agents/approaches
- **skip_and_continue** - Skip failed task, continue workflow
- **compensate** - Execute compensating actions
- **manual_intervention** - Request user input

### retry

**Type:** `object`  

Retry configuration for failed tasks.

```yaml
overlord:
  workflow:
    retry:
      max_attempts: 3                     # Max retry attempts (1-10, default: 3)
      initial_delay: 1.0                  # Initial delay in seconds (default: 1.0)
      max_delay: 60.0                     # Max delay in seconds (default: 60.0)
      backoff_factor: 2.0                 # Exponential backoff multiplier (default: 2.0)
      retry_on_errors: ["timeout", "rate_limit", "temporary_failure"]
```

**Fields:**

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `max_attempts` | integer | 3 | Maximum retry attempts (1-10) |
| `initial_delay` | number | 1.0 | Initial retry delay in seconds |
| `max_delay` | number | 60.0 | Maximum retry delay in seconds |
| `backoff_factor` | number | 2.0 | Exponential backoff multiplier |
| `retry_on_errors` | array | - | Error types to retry on |

**Backoff calculation:**  
`delay = min(initial_delay * (backoff_factor ^ attempt), max_delay)`

## Timeouts

### timeouts

**Type:** `object`  

Timeout configuration for tasks and workflows.

```yaml
overlord:
  workflow:
    timeouts:
      task_timeout: 300                   # Default per task (seconds, default: 300)
      workflow_timeout: 3600              # Overall workflow (seconds, default: 3600)
      enable_adaptive_timeout: true       # Adjust based on complexity (default: true)
```

**Fields:**

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `task_timeout` | integer | 300 | Default timeout per task in seconds |
| `workflow_timeout` | integer | 3600 | Overall workflow timeout in seconds |
| `enable_adaptive_timeout` | boolean | true | Adjust timeouts based on task complexity |

### max_timeout_seconds

**Type:** `integer`  
**Default:** `7200`  

Hard ceiling for entire workflow execution (2 hours).

```yaml
overlord:
  workflow:
    max_timeout_seconds: 7200  # Absolute maximum: 2 hours
```

Workflows exceeding this limit will fail with a timeout error. This prevents runaway workflows from consuming resources indefinitely.

## Execution Configuration

### parallel_execution

**Type:** `boolean`  
**Default:** `true`  

Execute independent tasks in parallel.

```yaml
overlord:
  workflow:
    parallel_execution: true  # Enable parallel execution (default)
```

When enabled, tasks without dependencies run simultaneously, reducing total workflow time.

### max_parallel_tasks

**Type:** `integer` (1-20)  
**Default:** `5`  

Maximum number of tasks to execute in parallel.

```yaml
overlord:
  workflow:
    max_parallel_tasks: 5  # Default: 5 concurrent tasks
```

Limits concurrent task execution to prevent overwhelming the system or hitting rate limits.

### partial_results

**Type:** `boolean`  
**Default:** `true`  

Return partial results if some tasks fail.

```yaml
overlord:
  workflow:
    partial_results: true  # Return what succeeded (default)
```

When enabled, workflows return completed task results even if some tasks fail. When disabled, any task failure causes the entire workflow to fail.

## Complete Example

```yaml
overlord:
  workflow:
    # Core settings
    auto_decomposition: true
    plan_approval_threshold: 7
    
    # Complexity calculation
    complexity_method: "hybrid"
    complexity_threshold: 7.0
    complexity_weights:
      heuristic: 0.5
      llm: 0.5
    
    # Routing
    routing_strategy: "capability_based"
    enable_agent_affinity: true
    
    # Error handling
    error_recovery: "retry_with_backoff"
    retry:
      max_attempts: 3
      initial_delay: 1.0
      max_delay: 60.0
      backoff_factor: 2.0
      retry_on_errors: ["timeout", "rate_limit"]
    
    # Timeouts
    timeouts:
      task_timeout: 300
      workflow_timeout: 3600
      enable_adaptive_timeout: true
    max_timeout_seconds: 7200
    
    # Execution
    parallel_execution: true
    max_parallel_tasks: 5
    partial_results: true
```

## Common Configurations

### Conservative (High Reliability)

```yaml
overlord:
  workflow:
    error_recovery: "retry_with_alternate"
    retry:
      max_attempts: 5
      initial_delay: 2.0
      max_delay: 120.0
    max_parallel_tasks: 3
    partial_results: false  # All or nothing
```

### Aggressive (Fast Execution)

```yaml
overlord:
  workflow:
    error_recovery: "skip_and_continue"
    retry:
      max_attempts: 1
    timeouts:
      task_timeout: 60
      workflow_timeout: 300
    max_parallel_tasks: 10
    partial_results: true
```

### Development (Quick Feedback)

```yaml
overlord:
  workflow:
    auto_decomposition: false  # Single agent only
    plan_approval_threshold: 10  # No approvals
    timeouts:
      task_timeout: 30
      workflow_timeout: 120
```

## Learn More

- **[Agent Formation Schema](https://github.com/agent-formation/afs-spec)** - Official schema specification
- [Workflows & Task Decomposition](../concepts/workflows.md) - Concept guide
- [Human-in-the-Loop](../concepts/approvals.md) - Plan approvals
- [The Overlord](../concepts/overlord.md) - How orchestration works
