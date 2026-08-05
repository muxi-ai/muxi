---
title: Request Status
description: Poll a request for its state, its result, or its give-up report
---
# Request Status

## Ask the runtime what happened to a request

`GET /v1/requests/{request_id}` reports the state of any request the runtime is
tracking - a background async turn, an escalated retry chain, or an ordinary chat
turn that has already finished.

```bash
curl http://localhost:7890/api/my-assistant/v1/requests/req_abc123 \
  -H "X-Muxi-Client-Key: fmc_..." \
  -H "X-Muxi-User-Id: user-123"
```

The endpoint accepts a client key (with `X-Muxi-User-Id`) or an admin key. A client
key can only read its own user's requests.


## Response

The response uses the standard API envelope:

```json
{
  "object": "request_status",
  "timestamp": 1785843861099,
  "type": "request.status.retrieved",
  "request": { "id": "req_KmmLwVssTNetTpTm8IqnS", "idempotency_key": null },
  "success": true,
  "error": null,
  "data": {
    "request_id": "req_abc123",
    "user_id": "user-123",
    "status": "completed",
    "progress": null,
    "created_at": 1785843812.44,
    "completed_at": 1785843861.09,
    "result": "Q3 revenue came in at $4.2M, up 11% on Q2."
  }
}
```

| Field | Present | Description |
|-------|---------|-------------|
| `request_id` | Always | The request's ID |
| `user_id` | Always | The user the request belongs to |
| `status` | Always | See the state table below |
| `progress` | Always | Progress value, or `null` when the request does not report one |
| `created_at` | Always | Epoch seconds when the request was tracked |
| `completed_at` | When finished | Epoch seconds when the request reached a terminal state |
| `error` | When set | Error text for a failed request |
| `result` | `completed` only | The answer. A string for ordinary text responses; a structured object where the request produced one |
| `escalated` | When true | The request escalated to [async retry](async-retry-escalation.md) |
| `report` | `failed` + escalated | The give-up report explaining what was tried and what would unblock it |


## Request states

| State | Terminal | Description |
|-------|----------|-------------|
| `pending` | | Queued, not started |
| `processing` | | Accepted and being worked on |
| `running` | | Executing |
| `completed` | Yes | Finished successfully |
| `failed` | Yes | Finished unsuccessfully |
| `cancelled` | Yes | Cancelled |
| `awaiting_clarification` | | Waiting on a human answer, neither running nor finished |

A chat turn that completes now reaches `completed` rather than being left in
`processing`, so polling a finished turn reports the truth. Turns that end awaiting a
clarification or an approval stay in `awaiting_clarification` instead of being marked
terminal, and turns that escalate leave the terminal transition to the retry chain
that owns them.

> [!NOTE]
> **Retention:** terminal requests remain pollable for 5 minutes, after which they
> are purged and `GET /v1/requests/{id}` returns 404. If you need results to outlive
> that window, use webhook delivery and persist them on your side.


## Escalated requests

A request whose synchronous turn failed and escalated to background retries carries
`escalated: true` for the life of the chain. Its final status depends on how the
chain ended:

| Chain outcome | `status` | Carries |
|---------------|----------|---------|
| `achieved` | `completed` | `result` - the answer a later attempt produced |
| `impossible`, `stuck`, `budget_exhausted` | `failed` | `report` - the give-up report |
| `abandoned` | `cancelled` | - |

```json
{
  "data": {
    "request_id": "req_abc123",
    "status": "failed",
    "escalated": true,
    "report": {
      "state": "budget_exhausted",
      "detail": "all 2 async attempt(s) failed",
      "attempts": [ ],
      "what_would_unblock": "Address the underlying failure(s) and resubmit: ...",
      "wall_time_seconds": 412.508
    }
  }
}
```

See [Async Retry Escalation](async-retry-escalation.md) for what each state means and
how the report is built.


## Errors

| Situation | Status | `error.code` |
|-----------|--------|--------------|
| Unknown or purged `request_id` | 404 | `REQUEST_NOT_FOUND` |
| Request belongs to another user | 403 | `FORBIDDEN` |
| Formation not ready | 503 | `SERVICE_UNAVAILABLE` |


## Learn More

- [Async Retry Escalation](async-retry-escalation.md) - escalated requests and give-up reports
- [Request Cancellation](request-cancellation.md) - stopping an in-flight request
- [Async Processing](../concepts/async.md) - webhook delivery as an alternative to polling
