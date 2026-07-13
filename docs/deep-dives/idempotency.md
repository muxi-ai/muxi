---
title: Idempotency
description: Safe retries for mutating Formation API requests
---

# Idempotency

## Retry a mutating request without doing the work twice

Network calls fail halfway. A client that retries a "send chat", "fire trigger",
or "create scheduled job" request has no way to know whether the first attempt
took effect. Idempotency keys close that gap: a retry carrying the same key
replays the original response instead of running the request again.


## The header

Clients send an `X-Muxi-Idempotency-Key` header on mutating requests. All MUXI
SDKs generate one automatically (set once per logical request, reused across the
SDK's own retries), so idempotency is on by default when you use an SDK. When
calling the API directly, supply your own unique value per logical operation:

```bash
curl -X POST http://localhost:7890/api/my-assistant/v1/chat \
  -H "X-Muxi-Client-Key: $CLIENT_KEY" \
  -H "X-Muxi-User-Id: user-123" \
  -H "X-Muxi-Idempotency-Key: 7b2f9c1e-3a44-4c8d-9f21-1e6b0d8a55aa" \
  -H "Content-Type: application/json" \
  -d '{"message": "Charge my account and summarize the invoice"}'
```


## Where it applies

The runtime honours idempotency keys on these mutating Formation API endpoints:

| Endpoint | Method |
|----------|--------|
| Chat (`/v1/chat`) | POST |
| Execute trigger (`/v1/triggers/{trigger_name}`) | POST |
| Create scheduled job (`/v1/scheduler/jobs`) | POST |

Requests without the header run unchanged - the feature is entirely opt-in per
request.


## Semantics

- **Only successful (2xx) JSON responses are cached.** Errors are always
  retryable, so a failed attempt never poisons the key.
- **Streaming (SSE) responses pass through untouched.** A token stream can't be
  replayed from a response cache. A repeated streaming request executes again,
  so use `stream: false` when you need idempotent chat retries.
- **Concurrent requests with the same key are single-flighted.** The second
  caller waits for the first to finish, then receives the same cached response
  rather than racing it.
- **Keys are scoped per method, path, and user.** Different callers, endpoints,
  or path parameters (e.g. two different trigger names) can reuse the same key
  value safely.
- **Storage is in-process with a 24-hour TTL.** Like async request state, cached
  responses do not survive a runtime restart.


## The echoed key

When a request carries an idempotency key, the runtime echoes it back on the
response envelope under `request.idempotency_key`:

```json
{
  "object": "chat_response",
  "request": {
    "id": "req_abc123",
    "idempotency_key": "7b2f9c1e-3a44-4c8d-9f21-1e6b0d8a55aa"
  },
  "success": true,
  "data": { }
}
```

All SDKs surface this echoed value on the unwrapped response (Go exposes it via
`RequestInfo.IdempotencyKey`; the others carry `idempotency_key` on the
unwrapped result), so a client can confirm which key a response belongs to.


## Learn More

- [API Reference](../reference/api-reference.md) - request headers and envelope
- [Resilience](resilience.md) - retries, timeouts, and graceful degradation
- [Request Lifecycle](request-lifecycle.md) - how a request flows through the runtime
