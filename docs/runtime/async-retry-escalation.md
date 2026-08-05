---
title: Async Retry Escalation
description: Failed turns escalate to bounded background retries with an honest give-up report
---
# Async Retry Escalation

## When a turn fails, the formation keeps working - and tells you how it went

A synchronous chat turn that exhausts its in-request retries used to end in a bare
error. The caller got a failure and nothing else: no second attempt, no account of
what was tried, no way to know whether a different approach would have worked.

Async retry escalation changes the ending. When a turn fails terminally, the runtime
returns a short, honest statement instead of the error, then keeps working in the
background with a *different plan* - bounded by an explicit budget. When the chain
ends, you get the answer or a report explaining why there isn't one.

The contract the caller sees is deliberately plain:

> This has failed. I'm going to retry with a different approach and let you know
> asynchronously. Your request ID is `req_abc123`. Poll GET /v1/requests/req_abc123
> for the outcome.

The last sentence changes with how the result will reach you - a notification
channel, a webhook, or polling. The rest is fixed text, never restyled by the
overlord's persona. A failure report that has been through a personality is not a
failure report.


## What triggers escalation

Escalation fires when a chat turn reaches a *terminal* failure - the point at which
the resilient workflow executor has already spent its own task retries, alternate
agents, and fallbacks:

- A workflow finished with `failed` status.
- An exception escaped workflow execution.
- An exception escaped chat processing (streaming or non-streaming).

Escalation is a last resort layered *on top of* the existing in-request retry
machinery under `overlord.workflow.retry`, not a replacement for it.


## What never escalates

Some failures cannot be fixed by trying again with a different plan, and some
"failures" are not failures at all. The gate refuses these:

| Situation | Why |
|-----------|-----|
| The failure matches a non-replannable error pattern | A different plan hits the same wall. Patterns come from `overlord.workflow.replanning.non_replannable_error_patterns` - by default `auth`, `unauthorized`, `forbidden`, `permission`, `credential`, `configuration error`, `invalid api key`, `data corruption` |
| The turn ended awaiting a clarification, an approval, or a credential prompt | The turn is waiting on a human, not broken |
| The request was cancelled | You already said stop |
| The request is itself an escalated retry | Chains do not nest |
| The formation is shutting down | No budget to spend |
| `retry_async.enabled` is `false`, or there is no request ID to escalate under | Nothing to hang a chain on |

Because a permission or credential failure names a blocker rather than a flaky step,
it is returned to the caller directly - escalating it would only burn budget on a
predictable second failure.


## Configuration

The block lives under `overlord.response`:

```yaml
overlord:
  response:
    retry_async:
      enabled: true                  # default: true
      max_attempts: 2                # default: 2 (min 1, max 10)
      attempt_idle_timeout: "15m"    # default: "15m"
      deadline: null                 # default: null (no deadline)
```

| Key | Type | Default | Meaning |
|-----|------|---------|---------|
| `enabled` | boolean | `true` | Turn escalation on or off |
| `max_attempts` | integer 1-10 | `2` | Background attempts allowed **after** the failed sync attempt |
| `attempt_idle_timeout` | duration | `"15m"` | Per-attempt liveness bound - an attempt that emits no observability activity for this long is declared hung and counted as failed |
| `deadline` | duration or `null` | `null` | Optional hard ceiling on the tail of the chain |

Durations are strings like `"500ms"`, `"90s"`, `"15m"`, or `"2h"`; a bare number means
seconds. Unknown keys, non-boolean `enabled`, and unparseable or non-positive
durations are rejected when the formation loads, not silently ignored.

> [!IMPORTANT]
> **`attempt_idle_timeout` bounds idleness, not duration.** An attempt that is
> genuinely working - emitting observability activity - can run as long as it needs
> to. The bound exists to catch attempts that have stopped making progress, not to
> cap legitimately long work. The same value also bounds the replanning step between
> attempts.

### How the budget adds up

`max_attempts` counts *background* attempts. The failed synchronous turn is attempt 1
and is not charged against the budget, so the default `max_attempts: 2` allows three
attempts in total and produces a three-entry report.

The optional `deadline` clock **starts when the second background attempt begins** -
the first background retry always gets an unhurried run. One consequence worth
knowing: with `max_attempts: 1` a `deadline` never takes effect, because there is no
second background attempt to arm it.


## What the caller sees

The failed synchronous turn returns normally - HTTP 200, an assistant message
carrying the escalation text, and `escalated: true` in the response metadata
alongside the `request_id` and the chosen `delivery` mode. It is not an error
response; the work is still in progress.

Streaming callers get the same text on the terminal `completed` event, with
`status: "escalated"` and `escalated: true`.

Delivery is chosen in this order:

1. **Notification channel** - if the formation has [proactive](../concepts/proactiveness.md) notification routing configured and the request carries a user ID.
2. **Webhook** - if the request has a webhook URL (per-request `webhook_url`, or the formation's `async.webhook_url`).
3. **Polling** - the floor. Always available.


## Terminal states

Every chain ends in exactly one of five states:

| State | Meaning | Request status |
|-------|---------|----------------|
| `achieved` | A background attempt succeeded. The answer is delivered. | `completed` |
| `impossible` | A mid-chain failure named a blocker that no plan can route around. | `failed` |
| `stuck` | Replanning could not produce a meaningfully different plan and the failure signature has not changed. Ends early, budget deliberately unspent. | `failed` |
| `budget_exhausted` | Every background attempt failed, or the deadline expired, or the chain itself broke. | `failed` |
| `abandoned` | The request was cancelled mid-chain. | `cancelled` |

`abandoned` is the one terminal state that sends no push notification - the
`DELETE /v1/requests/{id}` response was already the acknowledgement.

> [!NOTE]
> `stuck` is a deliberate early exit, not a bug. When the formation keeps hitting the
> same wall with plans it cannot meaningfully vary, spending the rest of the budget
> would produce a longer report and no better answer.


## The give-up report

Every non-`achieved` terminal produces a report:

```json
{
  "state": "budget_exhausted",
  "detail": "all 2 async attempt(s) failed",
  "request_id": "req_abc123",
  "original_message": "Pull the Q3 numbers and chart them",
  "attempts": [
    {
      "attempt": 1,
      "kind": "sync",
      "plan_summary": "Fetch Q3 revenue; build chart",
      "failure_reason": "reporting_api timed out after 15s"
    },
    {
      "attempt": 2,
      "kind": "async",
      "plan_summary": "Read Q3 revenue from the warehouse; build chart",
      "failure_reason": "warehouse query returned no rows"
    }
  ],
  "what_would_unblock": "Address the underlying failure(s) and resubmit: reporting_api timed out after 15s; warehouse query returned no rows",
  "wall_time_seconds": 412.508
}
```

| Field | Description |
|-------|-------------|
| `state` | One of the five terminal states |
| `detail` | Human-readable cause of the terminal state |
| `request_id` | The original request's ID |
| `original_message` | The message that started the turn |
| `attempts` | One entry per attempt, in order. `attempt` is 1-based; attempt 1 is always the failed synchronous turn (`kind: "sync"`), later ones are `kind: "async"` |
| `what_would_unblock` | What a human would have to change. Deterministic, derived from the failures - never LLM-generated prose |
| `wall_time_seconds` | Total elapsed time across the whole chain |

Reports are assembled from recorded facts. `plan_summary` is a deterministic summary
of the plan that attempt ran, and `what_would_unblock` is derived from the distinct
failure reasons. Nothing in the report is a model's guess about what went wrong.

When a chain gives up, the same account is also appended as a decision line on the
day's Captain's Log entry for that user, so the formation's own record reflects what
it could not do.


## Getting the outcome

### Polling

```bash
curl http://localhost:7890/api/my-assistant/v1/requests/req_abc123 \
  -H "X-Muxi-Client-Key: fmc_..." \
  -H "X-Muxi-User-Id: user-123"
```

An escalated entry carries `escalated: true` from the moment the chain starts. On a
`failed` terminal it also carries the `report`:

```json
{
  "object": "request_status",
  "type": "request.status.retrieved",
  "success": true,
  "data": {
    "request_id": "req_abc123",
    "user_id": "user-123",
    "status": "failed",
    "escalated": true,
    "report": { }
  }
}
```

On an `achieved` terminal the status is `completed` and the answer arrives in
`data.result` - there is no report, because there is nothing to explain.

See [Request Status](request-status.md) for the full response shape.

### Webhook

If the request carries a webhook URL, the runtime POSTs the terminal outcome to it:

```http
POST https://your-app.com/muxi-callback
Content-Type: application/json
X-Muxi-Signature: t=1785843861,v1=6f1c...
X-Muxi-Timestamp: 1785843861

{"attempts":2,"report":{ },"request_id":"req_abc123","state":"budget_exhausted","timestamp":1785843861.42}
```

| Field | Description |
|-------|-------------|
| `request_id` | The original request's ID |
| `state` | The terminal state - this is the discriminator; there is no separate event-type field |
| `attempts` | Total number of attempts, as a **count** (the per-attempt array lives inside `report.attempts`) |
| `timestamp` | When the outcome was delivered |
| `result` | The answer. Present only when `state` is `achieved` |
| `report` | The give-up report. Present on every other state |

Verify the signature exactly as you would any other MUXI webhook: `X-Muxi-Signature`
is `t=<unix seconds>,v1=<hex>`, where the signature is HMAC-SHA256 of
`{timestamp}.{body}` and the body is the exact bytes received. Compare in constant
time.

> [!WARNING]
> **Escalation webhooks are signed with the formation's client key**, not the admin
> key used for ordinary async-completion webhooks. A receiver that only knows the
> admin key will fail to verify them.

Delivery failures never change the chain's state - if your endpoint is down, the
outcome is still recorded and still retrievable by polling.


## Correlating a result with your application

The webhook carries a `request_id` and nothing about your application's world. That
is deliberate: MUXI does not model your domain, so it cannot know that
`req_abc123` was a Slack thread, a support ticket, or a checkbox on someone's todo
list.

**Keep your own `request_id` → source mapping.** Record it when you make the call,
look it up when the outcome arrives, and decide what delivery means on your side:

```python
# When you send the request: the turn escalated, so remember what it was for.
# The request ID is on the response envelope under `request.id`, and the
# response metadata carries `escalated: true` and the same `request_id`.
pending[request_id] = {"channel": "slack", "thread_ts": message.thread_ts}

# When the webhook arrives
source = pending.pop(payload["request_id"], None)
if source is None:
    return  # not ours, or already handled

if payload["state"] == "achieved":
    slack.post(source["thread_ts"], payload["result"])
else:
    slack.post(source["thread_ts"], render_giveup(payload["report"]))
```

Delivery does not have to mean "send a message". A background job might tick a task
as done and stay silent; a dashboard might update a row. The runtime's job is to be
honest about the outcome and hand it to you with a stable identifier - what that
means to your users is yours to decide.


## Observability

Three events trace a chain end to end:

| Event | Level | Emitted when |
|-------|-------|--------------|
| `response.retry.escalated` | INFO | The gate decided to escalate |
| `response.retry.attempt` | INFO | A background attempt starts |
| `response.retry.terminal` | INFO / WARN | The chain reaches a terminal state (INFO for `achieved`, WARNING otherwise) |

See [Event Types](../deep-dives/observability-events.md).


## Limits

- **Chains do not survive a runtime restart.** Escalation state is in-process. A
  formation restarted mid-chain loses the chain; the request's last recorded status
  stands.
- **Chains do not nest.** An escalated retry that fails does not escalate again - it
  ends in a terminal state and reports.
- **Cancellation still works.** `DELETE /v1/requests/{id}` on an escalated request
  ends the chain as `abandoned`. See [Request Cancellation](request-cancellation.md).


## Learn More

- [Request Status](request-status.md) - polling for outcomes and reading the report
- [Request Cancellation](request-cancellation.md) - stopping an in-flight chain
- [Async Processing](../concepts/async.md) - webhook configuration and signature verification
- [Resilience](../deep-dives/resilience.md) - the in-request retry layer beneath escalation
