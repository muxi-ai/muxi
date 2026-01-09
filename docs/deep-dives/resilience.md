---
title: Resilience
description: Reliability patterns built into MUXI
---
# Resilience

## Reliability patterns for agents and tools

MUXI bakes in failure handling so formations stay healthy under load, outages, and flaky dependencies.

### Core patterns
- **Circuit breakers:** Prevent cascading failures when tools or LLMs misbehave.
- **Retries with backoff:** Exponential backoff and jitter for transient errors.
- **Fallbacks:** Graceful degradation to cached or reduced responses when primaries fail.
- **Timeouts and budgets:** Bound execution time per call and per workflow.
- **Idempotency:** Safe retries for tool calls and webhooks.
- **Backpressure:** Shift long work to async paths; stream progress updates.

### Where it applies
- **LLM calls:** Retry + fallback models or providers; respect rate limits.
- **MCP tools:** Per-tool timeouts, circuit breakers, and scoped credentials.
- **Webhooks & triggers:** Idempotent handlers, replay-safe processing, signed requests.
- **Streaming:** Downgrade to buffered responses if streams fail; resume with checkpoints.

### Observability hooks
- Emit structured events for retries, breaker opens/closes, and fallbacks.
- Ship to Datadog, Elastic, Splunk, OTLP, or webhooks for alerting and audits.

### Best practices
- Set per-tool and per-LLM timeouts and retry budgets.
- Use caches for critical read paths; invalidate on write.
- Keep fallback behaviors narrow and explicit.
- Combine with [Observability](./observability.md) to detect regressions fast.
