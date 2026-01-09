---
title: Approval Configuration
description: Complete reference for human-in-the-loop approval settings
---
# Approval Configuration

## Complete reference for human-in-the-loop approval settings

Configure when and how users approve workflows before execution. Control approval requirements, timeouts, and custom rules.

## Configuration Location

```yaml
# In formation.yaml
workflow:
  # Approval settings here
```

## Basic Settings

### requires_approval
```yaml
workflow:
  requires_approval: true
```

**Type:** `boolean`  
**Default:** `false`  
**Description:** Require approval for all workflows

**Behavior:**
```yaml
requires_approval: false  # No approval needed (default)
requires_approval: true   # All workflows need approval
```

### approval_threshold
```yaml
workflow:
  approval_threshold: 10
```

**Type:** `float`  
**Range:** `0.0` to `10.0`  
**Default:** `10` (effectively disabled)  
**Description:** Complexity score threshold for approval

**How it works:**
```
Complexity score < approval_threshold → No approval needed
Complexity score ≥ approval_threshold → Approval required
```

**Examples:**
```yaml
# Very selective (only most complex tasks)
approval_threshold: 9.5

# Moderately selective
approval_threshold: 8.0

# More aggressive
approval_threshold: 7.0

# Approve everything (same as requires_approval: true)
approval_threshold: 0.0
```

## Timeout Settings

### approval_timeout
```yaml
workflow:
  approval_timeout: 300
```

**Type:** `integer` (seconds)  
**Default:** `300` (5 minutes)  
**Range:** `30` to `3600`  
**Description:** Time to wait for user approval

**Behavior:**
```
Present plan → Wait for approval
         ↓
approval_timeout seconds pass
         ↓
No response → Cancel workflow
```

**Recommendations:**
```yaml
# Quick decisions
approval_timeout: 60    # 1 minute

# Standard
approval_timeout: 300   # 5 minutes

# Complex reviews
approval_timeout: 900   # 15 minutes
```

### approval_timeout_action
```yaml
workflow:
  approval_timeout_action: "cancel"
```

**Type:** `string`  
**Options:**
- `cancel` - Cancel workflow (default)
- `proceed` - Execute anyway (dangerous!)
- `retry` - Ask again

**Default:** `"cancel"`

## Display Options

### show_time_estimate
```yaml
workflow:
  show_time_estimate: true
```

**Type:** `boolean`  
**Default:** `true`  
**Description:** Show estimated execution time in approval message

**Example:**
```
With show_time_estimate: true
"Estimated time: 5-7 minutes"

With show_time_estimate: false
(No time estimate shown)
```

### show_cost_estimate
```yaml
workflow:
  show_cost_estimate: true
```

**Type:** `boolean`  
**Default:** `false`  
**Description:** Show estimated cost in approval message

**Requires:** Cost tracking enabled

**Example:**
```
With show_cost_estimate: true
"Estimated cost: $0.12 (15 API calls)"
```

### show_impact
```yaml
workflow:
  show_impact: true
```

**Type:** `boolean`  
**Default:** `false`  
**Description:** Show impact analysis (affected users, systems, etc.)

**Example:**
```
With show_impact: true
"Impact:
 - Affects: 10,234 active users
 - Systems: production-api, production-db
 - Downtime: ~30 seconds"
```

### show_task_details
```yaml
workflow:
  show_task_details: true
```

**Type:** `boolean`  
**Default:** `true`  
**Description:** Show individual task details in plan

**Example:**
```
With show_task_details: true
"Tasks:
 1. Research competitors (researcher)
    Tools: web-search, company-db
    Estimated time: 2-3 minutes
 2. Create report (writer)
    Tools: document-creation
    Estimated time: 1-2 minutes"

With show_task_details: false
"I'll complete this task in 2 steps. Proceed?"
```

## Response Requirements

### require_explicit_yes
```yaml
workflow:
  require_explicit_yes: true
```

**Type:** `boolean`  
**Default:** `false`  
**Description:** Only accept "y" or "yes" as approval

**When true:**
```
✓ Accepted: "y", "yes"
✗ Rejected: "ok", "sure", "proceed", "go ahead"
```

**When false:**
```
✓ Accepted: "y", "yes", "ok", "sure", "proceed", "go ahead"
```

### require_confirmation
```yaml
workflow:
  require_confirmation: true
```

**Type:** `boolean`  
**Default:** `false`  
**Description:** Require typing "CONFIRM" for sensitive operations

**Behavior:**
```
MUXI: "This will delete 1,234 records. Type 'CONFIRM' to proceed:"
User: "CONFIRM"
MUXI: [Executes]
```

### confirmation_text
```yaml
workflow:
  confirmation_text: "CONFIRM"
```

**Type:** `string`  
**Default:** `"CONFIRM"`  
**Description:** Text user must type for confirmation

**Custom text:**
```yaml
confirmation_text: "DELETE DATA"
```

## Approval Rules

### approval_rules
```yaml
workflow:
  approval_rules:
    - match: "pattern"
      requires_approval: true
      message: "Custom message"
```

**Type:** `array<object>`  
**Description:** Pattern-based approval rules

**Fields:**

#### match
```yaml
match: "deploy.*production"
```

**Type:** `string` (regex)  
**Description:** Pattern to match in user request

#### requires_approval
```yaml
requires_approval: true
```

**Type:** `boolean`  
**Description:** Force approval for matching requests

#### message
```yaml
message: "⚠️ PRODUCTION DEPLOYMENT"
```

**Type:** `string`  
**Description:** Custom message in approval prompt

#### require_confirmation
```yaml
require_confirmation: true
```

**Type:** `boolean`  
**Description:** Require typing confirmation text

#### approval_timeout
```yaml
approval_timeout: 600
```

**Type:** `integer` (seconds)  
**Description:** Custom timeout for this rule

#### show_impact
```yaml
show_impact: true
```

**Type:** `boolean`  
**Description:** Always show impact for this rule

### Example Rules

```yaml
workflow:
  approval_rules:
    # Production deployments
    - match: "deploy.*production"
      requires_approval: true
      message: "⚠️ PRODUCTION DEPLOYMENT"
      show_impact: true
      approval_timeout: 600

    # Data deletion
    - match: "delete|remove|drop"
      requires_approval: true
      require_confirmation: true
      message: "⚠️ DESTRUCTIVE OPERATION"
      confirmation_text: "DELETE DATA"

    # External communications
    - match: "send email|post.*slack|tweet"
      requires_approval: true
      message: "External communication"
      show_preview: true

    # Financial transactions
    - match: "transfer|payment|refund"
      requires_approval: true
      message: "⚠️ FINANCIAL TRANSACTION"
      require_explicit_yes: true
      show_cost_estimate: true
```

## Role-Based Approvals

### approval_requires_auth
```yaml
workflow:
  approval_requires_auth: true
```

**Type:** `boolean`  
**Default:** `false`  
**Description:** Require authenticated user to approve

### approval_roles
```yaml
workflow:
  approval_roles:
    - admin
    - ops
    - manager
```

**Type:** `array<string>`  
**Description:** Roles allowed to approve

**Requires:** User authentication enabled

**Behavior:**
```
User role: "admin" → ✓ Can approve
User role: "developer" → ✗ Cannot approve (not in list)
```

### approval_users
```yaml
workflow:
  approval_users:
    - alice@company.com
    - bob@company.com
```

**Type:** `array<string>`  
**Description:** Specific users allowed to approve

## Multi-Approval

### require_multiple_approvals
```yaml
workflow:
  require_multiple_approvals: true
  minimum_approvals: 2
```

**Type:** `boolean` + `integer`  
**Default:** `false`, `1`  
**Description:** Require multiple users to approve

**Behavior:**
```
User 1 approves: "1/2 approvals"
User 2 approves: "2/2 approvals - Executing..."
```

### approval_timeout_per_user
```yaml
workflow:
  approval_timeout_per_user: 300
```

**Type:** `integer` (seconds)  
**Description:** Timeout for each approver

## Audit & Logging

### log_approvals
```yaml
workflow:
  log_approvals: true
```

**Type:** `boolean`  
**Default:** `false`  
**Description:** Log all approval decisions

**Output:**
```
2025-01-09 14:32:15 | req_abc123 | APPROVED | user@company.com | Deploy v2.1.0
2025-01-09 14:35:22 | req_def456 | REJECTED | admin@company.com | Delete users
2025-01-09 14:40:11 | req_ghi789 | TIMEOUT  | system           | Backup database
```

### emit_approval_events
```yaml
workflow:
  emit_approval_events: true
```

**Type:** `boolean`  
**Default:** `true`  
**Description:** Emit events for approval lifecycle

**Events:**
- `approval.requested`
- `approval.approved`
- `approval.rejected`
- `approval.timeout`

## Complete Example

```yaml
workflow:
  # Basic settings
  requires_approval: false
  approval_threshold: 8
  approval_timeout: 300
  approval_timeout_action: "cancel"

  # Display
  show_time_estimate: true
  show_cost_estimate: true
  show_impact: false
  show_task_details: true

  # Response requirements
  require_explicit_yes: false
  require_confirmation: false

  # Rules
  approval_rules:
    - match: "deploy.*production"
      requires_approval: true
      message: "⚠️ PRODUCTION DEPLOYMENT"
      show_impact: true
      approval_timeout: 600

    - match: "delete|remove|drop"
      requires_approval: true
      require_confirmation: true
      message: "⚠️ DESTRUCTIVE OPERATION"
      confirmation_text: "DELETE DATA"

    - match: "transfer|payment|refund"
      requires_approval: true
      message: "⚠️ FINANCIAL TRANSACTION"
      require_explicit_yes: true
      show_cost_estimate: true

  # Role-based
  approval_requires_auth: true
  approval_roles: [admin, ops]

  # Audit
  log_approvals: true
  emit_approval_events: true
```

## Presets

### Strict (Maximum Safety)
```yaml
workflow:
  requires_approval: true
  approval_threshold: 5
  approval_timeout: 600
  require_explicit_yes: true
  approval_requires_auth: true
  log_approvals: true
```

### Moderate (Balanced)
```yaml
workflow:
  requires_approval: false
  approval_threshold: 8
  approval_timeout: 300
  show_time_estimate: true
  show_cost_estimate: true
```

### Lenient (Speed)
```yaml
workflow:
  requires_approval: false
  approval_threshold: 9.5
  approval_timeout: 60
```

### Production
```yaml
workflow:
  requires_approval: false
  approval_threshold: 9
  approval_rules:
    - match: "production"
      requires_approval: true
      show_impact: true
  log_approvals: true
```

## Environment Variables

```bash
# Override approval settings
MUXI_WORKFLOW_REQUIRES_APPROVAL=true
MUXI_WORKFLOW_APPROVAL_THRESHOLD=8.0
MUXI_WORKFLOW_APPROVAL_TIMEOUT=300
```

## API Endpoints

### Get Approval Status
```http
GET /v1/requests/{request_id}
```

**Response:**
```json
{
  "request_id": "req_abc123",
  "status": "awaiting_approval",
  "plan": {
    "tasks": [...],
    "estimated_time": "5-7 minutes"
  }
}
```

### Approve Request
```http
POST /v1/requests/{request_id}/approve
```

**Request:**
```json
{
  "user_id": "alice@company.com",
  "confirmation": "CONFIRM"  // If required
}
```

### Reject Request
```http
POST /v1/requests/{request_id}/reject
```

**Request:**
```json
{
  "user_id": "alice@company.com",
  "reason": "Not ready for production"
}
```

## Validation

**Valid configuration:**
```yaml
workflow:
  requires_approval: true     # ✓ Boolean
  approval_threshold: 8.0     # ✓ Float in range
  approval_timeout: 300       # ✓ Positive integer
```

**Invalid configuration:**
```yaml
workflow:
  requires_approval: "yes"    # ✗ Must be boolean
  approval_threshold: 15      # ✗ Max is 10
  approval_timeout: -1        # ✗ Must be positive
```

## Troubleshooting

### Approvals Not Triggering
```yaml
# Check settings
workflow:
  requires_approval: false
  approval_threshold: 10      # Complexity must be ≥ 10

# If complexity is 8, no approval triggered
# Solution: Lower threshold
  approval_threshold: 8
```

### Timeout Too Short
```yaml
# Users complaining they don't have enough time
workflow:
  approval_timeout: 60        # Too short!

# Solution: Increase
  approval_timeout: 300       # 5 minutes
```

### Wrong User Approving
```yaml
# Only admins should approve
workflow:
  approval_roles: [admin]     # Restrict to admins
  approval_requires_auth: true  # Must be authenticated
```

## Learn More

- [Human-in-the-Loop Concept](../concepts/approvals.md) - User-facing explanation
- [Workflows & Task Decomposition](../concepts/workflows.md) - Workflow system
- [How Orchestration Works](../deep-dives/orchestration.md) - Technical details
