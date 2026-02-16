---
title: Observability Event Types
description: Complete reference for 350+ MUXI observability events
---
# Observability Event Types

## Complete reference for system and conversation events

MUXI emits 350+ typed events across the request lifecycle. Route them to your logging infrastructure for debugging, auditing, and performance monitoring. Events are emitted as JSONL and can be filtered by pattern.


## Event Envelope

Every event follows this structure:

```json
{
  "id": "evt_abc123",
  "timestamp": 1706616000000,
  "level": "info",
  "event": "request.completed",
  "request": {
    "id": "req_xyz789",
    "status": "completed",
    "duration_ms": 842,
    "formation_id": "my-formation",
    "user_id": "usr_123",
    "session_id": "sess_456"
  },
  "data": {
    "agent": "researcher",
    "tokens_prompt": 1200,
    "tokens_completion": 450
  }
}
```


## Request Ingestion & Validation

Events related to initial request processing, authentication, rate limiting, and validation.

| Event | Level | Description |
|-------|-------|-------------|
| `request.received` | INFO | Request initially received by MUXI |
| `request.denied.auth` | WARN | Request denied due to authentication failure |
| `request.denied.rate_limit` | WARN | Request denied due to rate limiting |
| `request.denied.validation` | WARN | Request denied due to validation failure |
| `request.validated` | DEBUG | Request format and structure validation passed |


## Multi-Modal Content Processing

Events for processing documents, images, audio, and other media content types.

| Event | Level | Description |
|-------|-------|-------------|
| `content.document.parsed` | INFO | Document attachment processing |
| `content.image.analyzed` | INFO | Image analysis and vision processing |
| `content.audio.transcribed` | INFO | Audio transcription |
| `content.extraction.failed` | ERROR | Content processing failure |


## Overlord Orchestration

Events tracking the overlord's routing decisions, task decomposition, and orchestration activities.

| Event | Level | Description |
|-------|-------|-------------|
| `overlord.initialized` | DEBUG | Overlord component startup |
| `overlord.routing.started` | DEBUG | Begin routing decision process |
| `overlord.routing.completed` | INFO | Routing decision made |
| `overlord.task.decomposed` | INFO | Request broken into subtasks |


## Memory & Context Operations

Events for memory storage, retrieval, and context management across short-term and long-term memory systems.

| Event | Level | Description |
|-------|-------|-------------|
| `memory.retrieval.started` | DEBUG | Memory search initiated |
| `memory.retrieval.short_term` | DEBUG | Short-term buffer search |
| `memory.retrieval.long_term` | DEBUG | Long-term memory search |
| `memory.retrieval.context` | DEBUG | User context memory lookup |
| `memory.storage.short_term` | DEBUG | Buffer memory storage |
| `memory.storage.long_term` | DEBUG | Persistent memory storage |
| `memory.extraction.started` | INFO | Automatic user info extraction |


## Agent Processing

Events tracking individual agent activities including reasoning, planning, and context application.

| Event | Level | Description |
|-------|-------|-------------|
| `agent.selected` | INFO | Specific agent chosen for processing |
| `agent.thinking.started` | DEBUG | Agent reasoning process begins |
| `agent.thinking.completed` | INFO | Agent reasoning finished |
| `agent.planning.created` | INFO | Agent generates execution plan |
| `agent.context.applied` | DEBUG | System message/soul applied |


## Model Operations

Events for LLM API calls, inference operations, and streaming response handling.

| Event | Level | Description |
|-------|-------|-------------|
| `model.inference.started` | DEBUG | Model API call initiated |
| `model.inference.completed` | INFO | Model response received |
| `model.streaming.started` | DEBUG | Streaming response initiated |


## Tool & MCP Operations

Events for MCP server connections, tool discovery, execution, and error handling.

| Event | Level | Description |
|-------|-------|-------------|
| `mcp.connection.established` | DEBUG | MCP server connection |
| `mcp.tool.discovered` | DEBUG | Available tools identified |
| `mcp.tool.called` | INFO | Tool execution initiated |
| `mcp.tool.completed` | INFO | Tool execution finished |
| `mcp.tool.failed` | ERROR | Tool execution error |


## A2A & Collaboration

Events for agent-to-agent communication, external agent discovery, and multi-agent collaboration.

| Event | Level | Description |
|-------|-------|-------------|
| `a2a.discovery.started` | DEBUG | External agent discovery |
| `a2a.request.sent` | INFO | A2A request to external agent |
| `a2a.response.received` | INFO | A2A response received |
| `collaboration.internal.started` | INFO | Internal multi-agent collaboration |


## Response Generation

Events for response composition, multi-modal content creation, validation, and formatting.

| Event | Level | Description |
|-------|-------|-------------|
| `response.generation.started` | DEBUG | Begin response composition |
| `response.multimodal.created` | INFO | Multi-modal response generated |
| `response.validation.completed` | DEBUG | Response safety/validation check |
| `response.formatted` | DEBUG | Final response formatting |


## Async & Delivery

Events for asynchronous processing, webhook delivery, and response completion tracking.

| Event | Level | Description |
|-------|-------|-------------|
| `async.threshold.detected` | INFO | Request exceeds sync threshold |
| `async.processing.started` | INFO | Background processing initiated |
| `webhook.sent` | INFO | Webhook delivery attempted |
| `response.delivered` | INFO | Final response sent to user |


## Error Handling & Recovery

Events for error detection, retry mechanisms, fallback activation, and recovery completion.

| Event | Level | Description |
|-------|-------|-------------|
| `error.timeout.detected` | WARN | Operation timeout |
| `error.retry.attempted` | WARN | Retry mechanism triggered |
| `error.fallback.activated` | WARN | Fallback mechanism used |
| `error.recovery.completed` | INFO | Error recovery successful |


## Performance & Monitoring

Events for performance metrics, resource usage tracking, and session management.

| Event | Level | Description |
|-------|-------|-------------|
| `performance.duration.recorded` | DEBUG | Component performance metric |
| `resource.usage.measured` | DEBUG | Resource consumption tracking |
| `session.created` | INFO | User session established |
| `session.context.updated` | DEBUG | Session context modified |


## Clarification & Parameter Collection

Events for intelligent parameter collection, clarifying questions, and missing parameter detection.

| Event | Level | Description |
|-------|-------|-------------|
| `clarification.analysis.started` | DEBUG | Information requirement analysis initiated |
| `clarification.analysis.completed` | INFO | Missing parameters/information identified |
| `clarification.question.generated` | INFO | Clarifying question created for user |
| `clarification.response.parsed` | DEBUG | User clarification response processed |
| `clarification.parameter.enriched` | DEBUG | Parameter filled from user context |
| `clarification.parameter.validated` | DEBUG | Parameter validation completed |
| `clarification.failed` | WARN | Clarification process failed, falling back |


## Proactive Clarification & Modes

Events for proactive clarification sessions, user-requested questioning, and mode management.

| Event | Level | Description |
|-------|-------|-------------|
| `clarification.proactive.detected` | INFO | Explicit turn-taking request detected |
| `clarification.mode.started` | INFO | Proactive clarification session initiated |
| `clarification.mode.questioning` | DEBUG | Proactive questioning mode active |
| `clarification.mode.planning` | DEBUG | Plan analysis mode active |
| `clarification.mode.context_building` | DEBUG | Context building mode active |
| `clarification.mode.goal_achievement` | DEBUG | Goal achievement mode active |
| `clarification.session.completed` | INFO | Proactive session reached completion criteria |
| `clarification.session.cancelled` | INFO | User cancelled proactive session |


## Planning Workflow Detection

Events for implicit planning workflow identification, data synthesis, and continuation.

| Event | Level | Description |
|-------|-------|-------------|
| `planning.workflow.detected` | INFO | Implicit planning workflow identified |
| `planning.data.synthesized` | INFO | Tool results synthesized for decision-making |
| `planning.continuation.started` | INFO | Planning continuation after data gathering |
| `planning.step.analyzed` | DEBUG | Individual plan step feasibility assessed |
| `planning.dependencies.identified` | DEBUG | Plan step dependencies mapped |
| `planning.options.presented` | INFO | Decision options generated for user |


## Enhanced Tool Processing

Events for tool parameter collection, validation, and context-enhanced execution.

| Event | Level | Description |
|-------|-------|-------------|
| `tool.parameter.missing` | INFO | Missing required tool parameters identified |
| `tool.parameter.clarified` | INFO | Tool parameter obtained via clarification |
| `tool.validation.failed` | WARN | Tool parameter validation failed |
| `tool.execution.enhanced` | DEBUG | Tool executed with enhanced parameter processing |
| `tool.context.applied` | DEBUG | User context applied to tool parameters |


## Multilingual & Detection

Events for language detection, multilingual processing, and pattern recognition.

| Event | Level | Description |
|-------|-------|-------------|
| `language.detection.started` | DEBUG | LLM-based language detection initiated |
| `language.detection.completed` | DEBUG | Input language identified |
| `language.processing.multilingual` | DEBUG | Multilingual processing mode activated |
| `language.fallback.activated` | WARN | Fallback to regex patterns |
| `intent.proactive.detected` | INFO | Proactive clarification intent identified |
| `intent.planning.detected` | INFO | Planning workflow intent identified |
| `intent.goal.extracted` | DEBUG | User goal extracted from natural language |
| `pattern.llm.analysis` | DEBUG | LLM-based pattern analysis completed |
| `pattern.detection.failed` | WARN | Pattern detection failed, using fallback |


## Context & Information Management

Events for information requirements analysis, gap identification, and context management.

| Event | Level | Description |
|-------|-------|-------------|
| `info.requirements.analyzed` | DEBUG | Information requirements determined |
| `info.gaps.identified` | INFO | Information gaps found in user request |
| `info.confidence.scored` | DEBUG | Information confidence level calculated |
| `info.extraction.automated` | DEBUG | Automatic information extraction attempted |
| `info.context.insufficient` | WARN | Insufficient context for parameter filling |
| `goal.type.determined` | DEBUG | Goal type classification completed |
| `goal.progress.tracked` | DEBUG | Progress toward goal completion measured |
| `goal.completion.criteria` | DEBUG | Goal completion criteria evaluated |
| `goal.context.created` | INFO | Goal context established for session |
| `goal.achievement.measured` | DEBUG | Goal achievement percentage calculated |


## Session & State Management

Events for clarification session lifecycle, state management, and mode transitions.

| Event | Level | Description |
|-------|-------|-------------|
| `session.clarification.created` | INFO | New clarification session established |
| `session.clarification.updated` | DEBUG | Session state updated with new information |
| `session.clarification.cleaned` | DEBUG | Session cleanup and resource release |
| `session.mode.switched` | INFO | Clarification mode changed within session |
| `session.progress.checkpoint` | DEBUG | Session progress checkpoint reached |
| `conversation.turn.clarification` | DEBUG | Clarification turn in multi-turn conversation |
| `conversation.context.maintained` | DEBUG | Context preserved across clarification turns |
| `conversation.flow.interrupted` | WARN | Normal conversation flow interrupted for clarification |
| `conversation.flow.resumed` | INFO | Normal conversation flow resumed after clarification |


## Clarification Performance & Quality

Events for performance metrics, quality tracking, and system health monitoring.

| Event | Level | Description |
|-------|-------|-------------|
| `performance.clarification.duration` | DEBUG | Time spent in clarification process |
| `performance.question.generation` | DEBUG | Question generation latency measured |
| `performance.llm.detection` | DEBUG | LLM-based detection performance |
| `quality.parameter.completion` | INFO | Parameter completion rate measured |
| `quality.clarification.success` | INFO | Clarification success rate tracked |
| `health.clarification.enabled` | INFO | Clarification system initialized successfully |
| `health.clarification.disabled` | WARN | Clarification system disabled/failed |
| `health.mode.manager.active` | DEBUG | Mode manager health check passed |
| `health.session.cleanup.completed` | DEBUG | Session cleanup completed successfully |


## Wildcard Patterns

Use these patterns in `events` filters to match groups of events.

| Pattern | Description |
|---------|-------------|
| `request.*` | All request-related events |
| `error.*` | All error events |
| `mcp.*` | All MCP/tool-related events |
| `agent.*` | All agent processing events |
| `memory.*` | All memory operation events |
| `response.*` | All response generation events |
| `a2a.*` | All agent-to-agent communication events |
| `performance.*` | All performance monitoring events |
| `clarification.*` | All clarification-related events |
| `planning.*` | All planning workflow events |
| `session.clarification.*` | All clarification session events |
| `tool.parameter.*` | All tool parameter collection events |
| `intent.*` | All intent detection events |
| `goal.*` | All goal tracking and achievement events |
| `info.*` | All information analysis and management events |
| `language.*` | All multilingual processing events |
| `quality.*` | All quality metrics and success rates |
| `health.*` | All system health monitoring events |
| `conversation.*` | All conversation flow and context events |
| `*` | All events (use with caution in production) |


## Event Selection Strategies

| Use Case | Recommended Filter |
|----------|-------------------|
| Development | `["*"]` or `level: "debug"` |
| Production monitoring | `["request.*", "response.*", "error.*"]` |
| Performance analysis | `["performance.*", "resource.*", "model.inference.*"]` |
| Debugging issues | `["error.*", "*.failed", "*.timeout.*"]` |
| Security auditing | `["request.denied.*", "auth.*", "security.*"]` |
| Clarification quality | `["clarification.*", "planning.*", "quality.*"]` |
| User experience | `["conversation.*", "session.clarification.*", "intent.*"]` |
| Multilingual support | `["language.*", "intent.*", "pattern.*"]` |


## Routing Configuration

Route events using `logging.system` for infrastructure and `logging.conversation.streams` for user-facing events:

```yaml
logging:
  system:
    level: info
    destination: stdout

  conversation:
    enabled: true
    streams:
      - transport: stdout
        level: info
        format: jsonl
        events: ["request.*", "response.*", "error.*"]

      - transport: file
        destination: /var/log/muxi/debug.jsonl
        level: debug
        events: ["*"]

      - transport: stream
        destination: https://logs.example.com/ingest
        format: datadog_json
        events: ["error.*", "performance.*"]
        auth:
          type: bearer
          token: "${{ secrets.LOG_TOKEN }}"
```

See [Observability](observability.md) for full configuration options.
