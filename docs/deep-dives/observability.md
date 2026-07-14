---
title: Observability
description: Events, logging, and monitoring for MUXI formations
---
# Observability

## Track every request from ingress to response

MUXI emits structured events across the full request lifecycle - from initial routing through agent execution to final delivery. Stream these events to your logging infrastructure for debugging, auditing, and performance monitoring.

**Why this matters:** When a user reports "the agent gave me a wrong answer," you need to trace exactly what happened - which agent was selected, what tools were called, what context was retrieved from memory, and how the response was generated. Without observability, you're debugging blind.

**Example use cases:**
- **Debugging:** Trace why an agent selected the wrong tool or returned unexpected results
- **Auditing:** Log all tool calls and data access for compliance requirements
- **Performance:** Identify slow LLM calls, memory lookups, or tool executions
- **Alerting:** Get notified when error rates spike or latency exceeds thresholds


## Event System

MUXI emits hundreds of typed events across the system lifecycle.

### Event Categories

| Category | Examples |
|----------|----------|
| System | Startup, shutdown, configuration, services |
| Conversation | Requests, responses, routing, agents, tools, memory |
| Server | HTTP and runtime connections |
| API | API call lifecycle |
| Error | Typed failures across subsystems |

## Event Format

```json
{
  "event_type": "request.received",
  "timestamp": "2025-01-08T10:00:00.123Z",
  "formation_id": "my-assistant",
  "session_id": "sess_abc123",
  "user_id": "user_123",
  "data": {
    "message_length": 42,
    "agent": "assistant"
  }
}
```

## Event Stream

Subscribe to events:

```bash
curl http://localhost:7890/draft/my-assistant/v1/events \
  -H "X-Muxi-Admin-Key: fma_..." \
  -H "Accept: text/event-stream"
```

Events stream via SSE:

```
event: conversation.request.received
data: {"session_id": "sess_123", ...}

event: conversation.llm.call.started
data: {"model": "GPT-5", ...}

event: conversation.response.completed
data: {"tokens": 150, ...}
```

## Key Events

### Request Lifecycle

| Event | Description |
|-------|-------------|
| `request.received` | Request arrived |
| `auth.validated` | Authentication passed |
| `orchestration.started` | Routing began |
| `agent.selected` | Agent chosen |
| `llm.call.started` | LLM request sent |
| `llm.call.completed` | LLM response received |
| `response.completed` | Response sent |

### Tool Execution

| Event | Description |
|-------|-------------|
| `tool.invoked` | Tool call started |
| `tool.completed` | Tool returned result |
| `tool.failed` | Tool error |

### Memory

| Event | Description |
|-------|-------------|
| `memory.loaded` | Context loaded |
| `memory.updated` | Memory saved |
| `knowledge.searched` | RAG search |

## Logging configuration (schema-aligned)

Two-tier config matching the formation schema:

```yaml
logging:
  system:
    level: info
    destination: stdout        # or file path
  conversation:
    enabled: true
    streams:
      - transport: stdout      # stdout | file | stream | trail
        level: info
        format: jsonl          # jsonl | text | msgpack | datadog_json | splunk_hec | ...
        events: ["request.*", "agent.*", "tool.*"]
      - transport: stream
        protocol: http         # http/https, kafka, or zmq
        destination: https://logs.example.com/ingest
        format: jsonl
        auth:
          type: bearer
          token: "${{ secrets.LOG_TOKEN }}"
```

- **System tier:** infrastructure events (startup/shutdown, MCP, A2A, errors) → single destination.
- **Conversation tier:** multi-stream with per-stream level/format/event filters; use `events` to scope (e.g., `request.*`, `memory.*`).

## PII & secret redaction

Every event is redacted **before** it reaches any sink (stdout, file, stream, or
external integration). This applies to all event tiers, including system, MCP,
and workflow events - not just user-facing ones - so secrets carried in any
payload are scrubbed by default.

Redaction runs in two layers:

| Layer | Always on? | What it masks |
|-------|-----------|----------------|
| **Regex** | Yes | API keys/tokens, passwords, AWS credentials, database URLs, JWTs, emails, phone numbers, SSNs, and credit cards (Luhn-validated) |
| **Entity** | Default on (toggle) | Names, addresses, organizations, dates of birth, and financial identifiers |

- **Luhn validation:** a 16-digit run is masked only when it passes the Luhn checksum, so order IDs, timestamps, and other long digit runs are preserved. Placeholders are length-accurate (one `****` group per four digits).
- **Consistent tokens:** entity matches are replaced with indexed tokens such as `[PERSON_1]`, `[ORG_1]`, `[ADDRESS_1]`, `[DOB_1]`. Repeated mentions of the same value reuse the same token within an event.
- **Dates of birth:** generic timestamps are left intact; a date is only masked as `[DOB_1]` when birth context (e.g. "born", "date of birth") precedes it.

### Configuration

```yaml
logging:
  redaction:
    entities: true   # default; set false to disable the entity layer only
```

- `logging.redaction.entities` toggles the **entity** layer. The regex layer is always on and cannot be disabled.
- Entity redaction is a built-in capability (Microsoft Presidio + spaCy `en_core_web_sm`) baked into the default images - no extra install step.
- If the NLP model is unavailable at startup, the entity layer **degrades gracefully to regex-only** and logs a one-time warning; secret/structured-PII redaction is unaffected.

> [!NOTE]
> The same detector and confidence threshold gate the memory extractor, so detected PII is also kept out of long-term memory, not just logs.

## Metrics

### Available Metrics

| Metric | Description |
|--------|-------------|
| `requests_total` | Total requests |
| `request_duration` | Request latency |
| `llm_calls_total` | LLM API calls |
| `llm_tokens` | Token usage |
| `tool_calls` | Tool invocations |
| `errors_total` | Error count |

### Prometheus Export

```bash
curl http://localhost:7890/metrics
```

## Integration

### Datadog

Forward logs:

```yaml
logging:
  format: json
  output: stdout  # Datadog agent picks up
```

### Elastic

Use Filebeat to ship logs:

```yaml
filebeat.inputs:
  - type: log
    paths:
      - /var/log/muxi/*.log
    json.keys_under_root: true
```

### Custom Webhook

Forward events:

```yaml
observability:
  webhook:
    url: https://your-service.com/events
    events:
      - "error.*"
      - "conversation.completed"
```

## Debugging

### Enable Debug Logs

```yaml
logging:
  level: debug
```

### Trace Requests

Add trace ID:

```bash
curl -H "X-Trace-Id: trace_123" ...
```

Trace ID appears in all related events.
