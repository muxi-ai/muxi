---
title: Human-in-the-Loop
description: Review complex workflow plans before MUXI executes them
---
# Human-in-the-Loop

MUXI can pause a decomposed workflow, present its execution plan, and wait for
the user to approve, reject, or revise it. Approval happens in the same
conversation, so the next chat message continues the pending workflow.

## Configuration

Set the approval threshold under `overlord.workflow`:

```yaml
overlord:
  workflow:
    auto_decomposition: true
    complexity_threshold: 7
    plan_approval_threshold: 7
```

`plan_approval_threshold` accepts a number from 1 through 10. A decomposed
request requires approval when its complexity score is greater than or equal
to this threshold. Lower values request approval more often. A value of `10`
limits automatic approval requests to workflows scored at the maximum
complexity.

The workflow complexity threshold and approval threshold are separate:

- `complexity_threshold` determines when MUXI decomposes a request into a
  workflow.
- `plan_approval_threshold` determines whether the resulting workflow pauses
  for approval.
- A user can explicitly ask to see a plan before execution, which also enters
  the approval flow.

## Approval Flow

1. MUXI analyzes the request and creates a workflow when decomposition applies.
2. If approval is required, MUXI returns the proposed plan with metadata that
   includes `approval_required: true` and `requires_user_response: true`.
3. The user replies in the same session:
   - `yes` or `proceed` approves and starts execution.
   - `no` or `reject` cancels the workflow.
   - A requested change keeps the workflow paused while MUXI gathers the
     modification details.
4. MUXI executes, cancels, or revises the pending workflow based on that reply.

There are no separate `approve_request` or `reject_request` client methods.
The follow-up chat message is the approval decision.

## SDK Example

```python
from muxi import FormationClient

formation = FormationClient(
    server_url="http://localhost:7890",
    formation_id="my-assistant",
    client_key="fmc_...",
)

session_id = "deployment-review"
user_id = "user_123"

plan = formation.chat(
    {
        "message": "Prepare and execute the production deployment",
        "session_id": session_id,
    },
    user_id=user_id,
)

if plan.get("metadata", {}).get("approval_required"):
    result = formation.chat(
        {
            "message": "Proceed",
            "session_id": session_id,
        },
        user_id=user_id,
    )
    print(result)
```

The exact response envelope can vary with the formation response format. Keep
the same `user_id` and `session_id` so MUXI can associate the reply with the
pending approval.

## API Example

```bash
curl -X POST http://localhost:7890/api/my-assistant/v1/chat \
  -H "Content-Type: application/json" \
  -H "X-Muxi-Client-Key: fmc_..." \
  -H "X-Muxi-User-ID: user_123" \
  -d '{
    "message": "Prepare and execute the production deployment",
    "session_id": "deployment-review"
  }'
```

After reviewing the returned plan, approve it with another chat request:

```bash
curl -X POST http://localhost:7890/api/my-assistant/v1/chat \
  -H "Content-Type: application/json" \
  -H "X-Muxi-Client-Key: fmc_..." \
  -H "X-Muxi-User-ID: user_123" \
  -d '{
    "message": "Proceed",
    "session_id": "deployment-review"
  }'
```

## Operational Guidance

- Preserve the user and session identifiers across the plan and decision.
- Display the full plan before asking the user to approve it.
- Treat plan metadata as a signal to collect a conversational response, not as
  a separate request-state endpoint.
- Use triggers only for workflows that are safe to run without interactive
  approval. Trigger execution bypasses workflow approval because triggers are
  automated entry points.
- Capture workflow and conversation events through the configured
  observability transports for audit trails.
