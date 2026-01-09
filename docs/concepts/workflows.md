---
title: Workflows & Task Decomposition
description: How MUXI breaks complex requests into multi-agent workflows
---
# Workflows & Task Decomposition

## How MUXI breaks complex requests into multi-agent workflows

Complex tasks need multiple agents with different skills. MUXI automatically decomposes requests into workflows, coordinates execution, and synthesizes results - no manual orchestration needed.

## The Problem

**Traditional approach:**
```
User: "Research AI trends, write blog post, create social media posts"

You manually:
1. Call researcher agent
2. Wait for results
3. Pass to writer agent
4. Wait for blog post
5. Pass to social media agent
6. Combine everything
```

Manual orchestration, error-prone, slow.

**MUXI approach:**
```
User: "Research AI trends, write blog post, create social media posts"

MUXI automatically:
1. Analyzes complexity
2. Creates workflow
3. Executes in parallel
4. Returns combined results
```

Automatic orchestration, reliable, fast.

## How It Works

```mermaid
graph TD
    U[User Request] --> O[Overlord]
    O --> C{Complexity Check}
    C -->|Simple ﹤7| A1[Single Agent]
    C -->|Complex ≥7| W[Create Workflow]
    W --> D[Decompose Tasks]
    D --> T1[Task 1]
    D --> T2[Task 2]
    D --> T3[Task 3]
    T1 --> E[Execute]
    T2 --> E
    T3 --> E
    E --> S[Synthesize Results]
    A1 --> R[Response]
    S --> R
```

## Complexity Scoring

The Overlord scores requests from 0-10:

| Score | Example | Action |
|-------|---------|--------|
| 0-3 | "What's the weather?" | Simple, single agent |
| 4-6 | "Explain quantum computing" | Moderate, may use tools |
| 7-10 | "Research, analyze, create report" | Complex, trigger workflow |

**Configure threshold:**
```yaml
overlord:
  auto_decomposition: true
  complexity_threshold: 7.0  # Workflows trigger at 7+
```

## Workflow Creation

**When complexity ≥ threshold:**

```
User: "Analyze our codebase, create security audit, file GitHub issues"
         ↓
Overlord analyzes:
  - Multiple steps mentioned
  - Different skills needed (analysis, security, GitHub)
  - Multiple output artifacts
         ↓
Complexity score: 9/10
         ↓
Create workflow:
  Task 1: Analyze codebase → code-reviewer agent
  Task 2: Security audit → security-agent (depends on Task 1)
  Task 3: Create issues → github-agent (depends on Task 2)
```

## Workflow Components

### 1. Task Decomposition

Break request into subtasks:

```
Original: "Research competitors and create comparison report"

Decomposed:
  - Task 1: Research company A (researcher)
  - Task 2: Research company B (researcher)
  - Task 3: Research company C (researcher)
  - Task 4: Compare features (analyst)
  - Task 5: Create report (writer)
```

### 2. Dependency Management

Determine task order:

```
Task 1, 2, 3: Can run in parallel (no dependencies)
Task 4: Depends on 1, 2, 3 (needs research results)
Task 5: Depends on 4 (needs comparison)
```

### 3. Agent Assignment

Match agents to tasks:

```
Tasks 1-3: researcher (has web-search tool)
Task 4: analyst (has data-analysis tool)
Task 5: writer (has document-creation tool)
```

### 4. Parallel Execution

Run independent tasks simultaneously:

```
Start: Tasks 1, 2, 3 execute in parallel
         ↓
    All complete
         ↓
Start: Task 4 executes (uses results from 1, 2, 3)
         ↓
    Complete
         ↓
Start: Task 5 executes (uses result from 4)
         ↓
    Complete
         ↓
Return combined result to user
```

## Workflow Example

### Simple Request (Score: 4)
```
User: "What's the capital of France?"

Overlord:
  - Score: 4 (factual question)
  - Route to: assistant agent
  - No workflow needed

Response: "The capital of France is Paris."
```

### Complex Request (Score: 9)
```
User: "Research top 5 project management tools, compare features,
       create a recommendation report"

Overlord:
  - Score: 9 (multiple steps, research, analysis, creation)
  - Create workflow:

    Task 1: Research Asana
      Agent: researcher
      Tools: web-search
      Output: feature_list_asana

    Task 2: Research Monday
      Agent: researcher
      Tools: web-search
      Output: feature_list_monday

    Task 3: Research ClickUp
      Agent: researcher
      Tools: web-search
      Output: feature_list_clickup

    Task 4: Research Jira
      Agent: researcher
      Tools: web-search
      Output: feature_list_jira

    Task 5: Research Trello
      Agent: researcher
      Tools: web-search
      Output: feature_list_trello

    [Tasks 1-5 run in parallel]

    Task 6: Compare features
      Agent: analyst
      Tools: data-analysis
      Input: feature_lists from Tasks 1-5
      Output: comparison_matrix

    Task 7: Create recommendation report
      Agent: writer
      Tools: document-creation
      Input: comparison_matrix from Task 6
      Output: recommendation_report.pdf

Response: "Here's your report comparing the top 5 PM tools..."
         [Attached: recommendation_report.pdf]
```

## Execution Flow

```mermaid
sequenceDiagram
    participant U as User
    participant O as Overlord
    participant R as Researcher
    participant A as Analyst
    participant W as Writer

    U->>O: Complex request
    O->>O: Score complexity: 9
    O->>O: Create workflow
    O->>R: Tasks 1-5 (parallel)
    R-->>O: All research complete
    O->>A: Task 6: Compare
    A-->>O: Comparison complete
    O->>W: Task 7: Create report
    W-->>O: Report complete
    O->>O: Synthesize results
    O->>U: Final response + PDF
```

## Error Handling

**What happens when a task fails?**

```
Task 1: ✅ Success
Task 2: ❌ Failed (API timeout)
Task 3: ✅ Success
         ↓
Overlord:
  - Retry Task 2 (up to 3 times)
  - If still fails, mark as partial failure
  - Continue with available results
         ↓
Task 4: Executes with results from 1 and 3
         ↓
Response: "I completed the analysis, but encountered an issue
           accessing one data source. Here are the results..."
```

## Configuration

### Enable Workflows
```yaml
overlord:
  auto_decomposition: true        # Enable automatic workflows
  complexity_threshold: 7.0       # Score to trigger workflow
```

### Workflow Settings
```yaml
workflow:
  max_parallel_tasks: 10          # Max concurrent tasks
  task_timeout: 300               # Seconds per task
  retry_failed_tasks: true        # Retry on failure
  max_retries: 3                  # Max retry attempts
```

### Disable for Testing
```yaml
overlord:
  auto_decomposition: false       # Force single-agent mode
```

## Task Types

### Sequential Tasks
Tasks that must run in order:

```
Task 1: Fetch data
         ↓
Task 2: Process data (needs Task 1 output)
         ↓
Task 3: Generate report (needs Task 2 output)
```

### Parallel Tasks
Tasks that can run simultaneously:

```
    ┌─ Task 1: Research A
    ├─ Task 2: Research B
    └─ Task 3: Research C
         ↓
    All complete → Task 4: Compare
```

### Conditional Tasks
Tasks that depend on previous results:

```
Task 1: Check if user has credentials
         ↓
    ├─ Yes → Task 2A: Use existing
    └─ No  → Task 2B: Prompt for credentials
```

## When Workflows Trigger

**Automatic triggers:**

- ✅ Multiple distinct steps mentioned
- ✅ Different domains/skills required
- ✅ Research + creation/analysis
- ✅ Multiple output artifacts
- ✅ Time-consuming operations

**Examples:**
```
✅ "Research and write blog post" → Workflow
✅ "Analyze codebase and create report" → Workflow
✅ "Search docs, find answer, create ticket" → Workflow
✅ "Compare 3 products and recommend best" → Workflow

❌ "What's the weather?" → Single agent
❌ "Explain this code" → Single agent
❌ "Search for information" → Single agent
```

## Benefits

### 1. Automatic Orchestration
No manual coordination needed - MUXI handles everything.

### 2. Parallel Execution
Independent tasks run simultaneously, reducing total time:

```
Sequential: 5 tasks × 10s = 50s total
Parallel:   5 tasks → 10s total (all at once)
```

### 3. Intelligent Routing
Each task goes to the most capable agent.

### 4. Error Recovery
Failed tasks retry automatically, partial results still useful.

### 5. Consistent Results
Same workflow structure for similar requests.

## Comparison with SOPs

| Feature | Workflows | SOPs |
|---------|-----------|------|
| **Trigger** | Automatic (complexity score) | Manual (keyword match) |
| **Definition** | AI-generated on-the-fly | Pre-defined template |
| **Flexibility** | Adapts to request | Fixed structure |
| **Use case** | One-off complex tasks | Repeatable processes |

**When to use workflows:**
- New, complex requests
- Ad-hoc analysis
- Creative projects

**When to use SOPs:**
- Repeatable processes
- Standardized workflows
- Compliance requirements

## Advanced: Custom Decomposition

Override automatic decomposition:

```yaml
overlord:
  custom_decomposition:
    patterns:
      - match: "research and write"
        tasks:
          - type: "research"
            agent: "researcher"
          - type: "write"
            agent: "writer"
            depends_on: ["research"]
```

## Performance Optimization

### Reduce Overhead
```yaml
workflow:
  min_tasks_for_parallel: 3  # Only parallelize if 3+ tasks
```

### Adjust Timeouts
```yaml
workflow:
  task_timeout: 600          # Longer timeout for complex tasks
```

### Limit Concurrency
```yaml
workflow:
  max_parallel_tasks: 5      # Reduce if hitting rate limits
```

## Debugging Workflows

**Enable workflow logging:**
```yaml
overlord:
  log_workflows: true        # Log workflow creation
```

**Output:**
```
[WORKFLOW] Created workflow for request req_abc123
[WORKFLOW]   Task 1: research (researcher) - no dependencies
[WORKFLOW]   Task 2: research (researcher) - no dependencies
[WORKFLOW]   Task 3: analyze (analyst) - depends on [1, 2]
[WORKFLOW] Executing Task 1 and Task 2 in parallel
[WORKFLOW] Task 1 completed in 3.2s
[WORKFLOW] Task 2 completed in 4.1s
[WORKFLOW] Executing Task 3 (dependencies met)
[WORKFLOW] Task 3 completed in 2.8s
[WORKFLOW] Workflow completed in 7.3s total
```

## Best Practices

**DO:**
- ✅ Let MUXI handle decomposition automatically
- ✅ Set reasonable timeouts (balance speed vs completion)
- ✅ Use SOPs for repeatable workflows
- ✅ Monitor workflow performance
- ✅ Adjust complexity threshold based on your use case

**DON'T:**
- ❌ Disable auto_decomposition (unless testing)
- ❌ Set timeout too low (causes failures)
- ❌ Set max_parallel_tasks too high (rate limits)
- ❌ Manually orchestrate (let MUXI handle it)

## Learn More

- [The Overlord](overlord.md) - How orchestration works
- [Agents & Orchestration](agents.md) - Multi-agent coordination
- [Standard Operating Procedures](sops.md) - Pre-defined workflows
- [Human-in-the-Loop](approvals.md) - Workflow confirmations
- [How Orchestration Works](../deep-dives/orchestration.md) - Technical details
