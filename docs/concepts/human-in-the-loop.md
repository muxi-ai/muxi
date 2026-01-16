---
title: Human-in-the-Loop
description: Require approval before executing complex or sensitive workflows
---
# Human-in-the-Loop

## Require approval before executing complex or sensitive workflows

Give users control over complex operations. MUXI can present execution plans and wait for approval before proceeding - preventing unwanted actions and building trust.

## Why Approvals Matter

**Without approvals:**
```
User:  "Deploy to production"
         ↓
MUXI immediately deploys
         ↓
User:  "Wait, I didn't mean NOW!"
```

Too late - deployment already happened.

**With approvals:**
```
User:  "Deploy to production"
         ↓
MUXI: "I'll deploy version 2.1.0 to production.
       This affects 10,000 users. Proceed? [y/N]"
         ↓
User:  "Actually, let me check something first..."
         ↓
Deployment prevented
```

Safe, controlled execution.

## When to Use Approvals

**Sensitive operations:**
- ✅ Production deployments
- ✅ Data deletion
- ✅ Financial transactions
- ✅ External communications (emails, posts)
- ✅ User account modifications

**Complex workflows:**
- ✅ Multi-step operations
- ✅ Long-running tasks
- ✅ Resource-intensive operations
- ✅ Operations affecting multiple systems

**Learning scenarios:**
- ✅ New agent testing
- ✅ Unfamiliar operations
- ✅ Compliance requirements
- ✅ Training/demo purposes

## Configuration

The `plan_approval_threshold` controls when approvals are required based on complexity scoring (1-10).

### Default Configuration
```yaml
overlord:
  workflow:
    plan_approval_threshold: 7   # Default: require approval for complexity ≥ 7
```

### Require Approval for Complex Tasks
```yaml
overlord:
  workflow:
    plan_approval_threshold: 8   # Only very complex tasks require approval
```

> [!TIP]
> **Start with a lower threshold in production.** Better to approve too often than to let something slip through. Raise the threshold as you build confidence in your agents.

### Require Approval for Most Tasks
```yaml
overlord:
  workflow:
    plan_approval_threshold: 5   # Most tasks require approval
```

### Require Approval for Everything
```yaml
overlord:
  workflow:
    plan_approval_threshold: 1   # All tasks require approval (even simple ones)
```

### Disable Approvals
```yaml
overlord:
  workflow:
    plan_approval_threshold: 10  # Never require approval (max score is 10)
```

## How It Works

```
1. User makes request
         ↓
2. Overlord calculates complexity score (1-10)
         ↓
3. Score ≥ plan_approval_threshold?
         ↓
4. YES → Create execution plan
         ↓
5. Present plan to user
         ↓
6. Wait for approval
         ↓
7. User approves? → Execute
   User rejects? → Cancel
```

## Approval Flow Example

### Simple Request (No Approval)
```
User:  "What's the weather in SF?"

Complexity: 2/10 (below threshold)
         ↓
Execute immediately (no approval needed)
         ↓
Response: "It's 68°F and sunny in San Francisco."
```

### Complex Request (Approval Required)
```
User:  "Research competitors, create comparison report, post to Slack"

Overlord analyzes:
  - Multiple steps required
  - Different skills needed
  - External communication
  - Complexity score: 9/10
         ↓
Score ≥ plan_approval_threshold (7 by default)
         ↓
MUXI presents plan:

"I've created a plan for this task:

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

Proceed? [y/N]"
         ↓
User types: y
         ↓
Execute workflow
         ↓
Complete and return results
```

## Approval Messages

### Basic Approval
```
MUXI: I'll execute this workflow:
      1. Research data
      2. Create report
      3. Send email

      Proceed? [y/N]
```

### Detailed Approval
```
MUXI: I've created a plan for this task:

**What I'll do:**
1. Research competitors (researcher)
   - Tools: web-search, company-db
   - Output: competitor_analysis.json

2. Compare features (analyst)
   - Input: competitor_analysis.json
   - Tools: data-analysis
   - Output: comparison_matrix.csv

3. Create presentation (writer)
   - Input: comparison_matrix.csv
   - Tools: document-creation
   - Output: presentation.pdf

**Impact:**
- External API calls: ~15 requests
- Estimated cost: $0.12
- Estimated time: 5-7 minutes

**Proceed? [y/N]**
```

### Sensitive Operation Approval
```
MUXI: ⚠️  PRODUCTION DEPLOYMENT

I'll deploy version 2.1.0 to production:

**Changes:**
- 12 files modified
- 3 new migrations
- API endpoint /v2/users updated

**Impact:**
- Affects: 10,234 active users
- Downtime: ~30 seconds (rolling update)
- Rollback available: Yes

**Last deployed:** 2 hours ago (v2.0.9)

This is a PRODUCTION change. Are you sure? [y/N]
```

## Approval Responses

### Approve
```
User: y
User: yes
User: proceed
User: go ahead
```

All trigger execution.

### Reject
```
User: n
User: no
User: cancel
User: abort
```

All cancel execution.

### Modify
```
User:  "Actually, skip the Slack post"
         ↓
MUXI: "Updated plan:
       1. Research competitors
       2. Create comparison report
       (Removed: Post to Slack)

       Proceed? [y/N]"
```

## Adjusting the Threshold

The threshold determines which requests require approval. Lower threshold = more approvals.

| Threshold | When Approvals Trigger |
|-----------|------------------------|
| 1 | All requests (even "What's the weather?") |
| 3 | Most requests with any complexity |
| 5 | Moderate and complex requests |
| **7** | **Complex requests only (default)** |
| 9 | Only very complex multi-step workflows |
| 10 | Never (effectively disables approvals) |

**Example: Strict control for production**
```yaml
overlord:
  workflow:
    plan_approval_threshold: 5   # Require approval for most tasks
```

**Example: Testing/development**
```yaml
overlord:
  workflow:
    plan_approval_threshold: 10  # No approvals during development
```

## Approval in Different Modes

### Synchronous (Default)
```
User:  "Complex task"
         ↓
MUXI: "Plan... Proceed? [y/N]"
         ↓
User:  "y"
         ↓
MUXI executes and returns result
```

Connection stays open, user waits.

### Asynchronous
```
User:  "Complex task" (async: true)
         ↓
MUXI: "Plan... Proceed? [y/N]"
         ↓
MUXI: Returns request_id immediately
         ↓
User:  "y" (to /requests/{id}/approve)
         ↓
MUXI executes in background
         ↓
User polls or receives webhook when complete
```

Non-blocking, user can do other things.

## SDK Examples

### Python
```python
from muxi import FormationClient

formation = FormationClient(
    server_url="http://localhost:7890",
    formation_id="my-assistant",
    client_key="...",
)

# Streaming chat - approvals are handled via events
for event in formation.chat_stream(
    {"message": "Deploy to production"},
    user_id="user_123"
):
    if event.get("type") == "approval_required":
        print("Plan:", event.get("plan"))
        # User approval handled via separate endpoint
    elif event.get("type") == "text":
        print(event.get("text"), end="")
```

### Async Request Status
```python
# Check async request status
status = formation.get_request_status(request_id="req_abc123")

# Later, approve
formation.approve_request(request.id)

# Or reject
formation.reject_request(request.id)
```

### TypeScript
```typescript
// Sync with approval
const response = await formation.chat({
  message: "Deploy to production",
  waitForApproval: true
});

// Async with manual approval
const request = await formation.chatAsync({
  message: "Deploy to production"
});

// Later
await formation.approveRequest(request.id);
```

## API Endpoints

### Get Approval Status
```bash
GET /v1/requests/{request_id}
```

Response:
```json
{
  "request_id": "req_abc123",
  "status": "awaiting_approval",
  "plan": {
    "tasks": [...],
    "estimated_time": "5-7 minutes",
    "estimated_cost": "$0.12"
  }
}
```

### Approve Request
```bash
POST /v1/requests/{request_id}/approve
```

### Reject Request
```bash
POST /v1/requests/{request_id}/reject
```

## Use Cases

### 1. Production Deployments
```yaml
overlord:
  workflow:
    approval_rules:
      - match: "deploy.*production"
        requires_approval: true
        show_impact: true
```

**Flow:**
```
User:  "Deploy version 2.1.0 to production"
MUXI: Shows deployment plan with impact analysis
User: Reviews and approves
MUXI: Executes deployment
```

### 2. Data Deletion
```yaml
overlord:
  workflow:
    approval_rules:
      - match: "delete|remove|drop"
        requires_approval: true
        require_confirmation: true
```

**Flow:**
```
User:  "Delete all test users"
MUXI: "This will delete 1,234 users. Type 'CONFIRM' to proceed:"
User:  "CONFIRM"
MUXI: Executes deletion
```

### 3. External Communications
```yaml
overlord:
  workflow:
    approval_rules:
      - match: "send email|post.*slack|tweet"
        requires_approval: true
        show_preview: true
```

**Flow:**
```
User:  "Send weekly update email to customers"
MUXI: Shows email preview and recipient count
User: Approves
MUXI: Sends emails
```

### 4. Financial Transactions
```yaml
overlord:
  workflow:
    approval_rules:
      - match: "transfer|payment|refund"
        requires_approval: true
        require_explicit_amount: true
```

**Flow:**
```
User:  "Process refund for order #12345"
MUXI: "This will refund $249.99 to customer. Proceed? [y/N]"
User:  "y"
MUXI: Processes refund
```

## Best Practices

**DO:**
- ✅ Use approvals for sensitive operations
- ✅ Show clear impact in approval messages
- ✅ Include estimated time and cost
- ✅ Set reasonable timeouts
- ✅ Test approval flows before production

**DON'T:**
- ❌ Require approval for everything (annoying)
- ❌ Use vague approval messages
- ❌ Set timeout too short (rushed decisions)
- ❌ Skip approval for destructive operations
- ❌ Approve without reading (defeats purpose)

## Monitoring Approvals

Use logging to track approval patterns:

```yaml
logging:
  enabled: true
  streams:
    - transport: file
      level: info
      destination: /var/log/muxi/approvals.log
```

This captures when approvals are requested, approved, or rejected for audit purposes.

## Troubleshooting

### Approval Not Showing
```yaml
# Configuration
overlord:
  workflow:
    plan_approval_threshold: 8   # Requires approval for complexity ≥ 8

# Request complexity calculated as 7 (below threshold)
# Solution: Lower threshold to 7 or 6
overlord:
  workflow:
    plan_approval_threshold: 6   # Now complexity 7 will require approval
```

### Too Many Approvals
```yaml
# Was: Approving everything
overlord:
  workflow:
    plan_approval_threshold: 3   # Almost all requests need approval

# Solution: Raise threshold
overlord:
  workflow:
    plan_approval_threshold: 8   # Only very complex requests need approval
```

### Not Enough Control
```yaml
# Was: Too few approvals
overlord:
  workflow:
    plan_approval_threshold: 9   # Only extremely complex requests

# Solution: Lower threshold for production environments
overlord:
  workflow:
    plan_approval_threshold: 6   # More requests require approval
```

## Learn More

- **[Agent Formation Schema](https://github.com/agent-formation/afs-spec)** - Official formation schema specification
- [Workflows & Task Decomposition](workflows.md) - How workflows are created
- [The Overlord](overlord.md) - Orchestration engine
- [Async Processing](async.md) - Background task execution
