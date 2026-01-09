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

---

## Event System

MUXI emits 349 typed events across the system lifecycle.

### Event Categories

| Category | Count | Examples |
|----------|-------|----------|
| System | 120 | Startup, shutdown, config |
| Conversation | 157 | Request, response, routing |
| Server | 9 | HTTP, connections |
| API | 2 | Call start/end |
| Error | 61 | All error types |

## Event Format

```json
{
  "event_type": "conversation.request.received",
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
curl http://localhost:8001/v1/events \
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
        protocol: http         # http/https/zmq/websocket
        destination: https://logs.example.com/ingest
        format: jsonl
        auth:
          type: bearer
          token: "${{ secrets.LOG_TOKEN }}"
```

- **System tier:** infrastructure events (startup/shutdown, MCP, A2A, errors) → single destination.
- **Conversation tier:** multi-stream with per-stream level/format/event filters; use `events` to scope (e.g., `request.*`, `memory.*`).

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
