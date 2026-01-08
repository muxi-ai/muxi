# Request Lifecycle

How requests flow through MUXI from receipt to response.

## Overview

```
Client Request
      ↓
┌─────────────────┐
│ MUXI Server     │ ← Authentication
│ (Port 7890)     │
└────────┬────────┘
         ↓
┌─────────────────┐
│ Formation       │ ← Request Processing
│ (Port 8001)     │
└────────┬────────┘
         ↓
┌─────────────────┐
│ Overlord        │ ← Orchestration
└────────┬────────┘
         ↓
┌─────────────────┐
│ Agent(s)        │ ← Execution
└────────┬────────┘
         ↓
Response to Client
```

## Phase 1: Request Receipt

### Server Level

1. Request arrives at MUXI Server (`:7890`)
2. Server authenticates (HMAC or API key)
3. Routes to target formation

### Formation Level

1. Request arrives at formation (`:800x`)
2. Formation validates API key
3. Identifies user from headers

## Phase 2: Orchestration

The Overlord analyzes the request:

```
User Message
     ↓
┌─────────────────┐
│ SOP Matching    │ → Match? → Execute SOP
└────────┬────────┘
         ↓ No match
┌─────────────────┐
│ Agent Specified?│ → Yes → Use that agent
└────────┬────────┘
         ↓ No
┌─────────────────┐
│ Complexity      │ → Complex → Multi-step workflow
│ Analysis        │
└────────┬────────┘
         ↓ Simple
┌─────────────────┐
│ Agent Selection │ → Select best agent
└─────────────────┘
```

## Phase 3: Agent Execution

Agent processes the request:

1. **Context Loading**
   - Load memory (buffer + persistent)
   - Load relevant knowledge (RAG)
   - Prepare system prompt

2. **LLM Call**
   - Send to language model
   - Receive response (streamed or complete)

3. **Tool Execution** (if needed)
   - Agent requests tool use
   - MUXI executes MCP tool
   - Returns result to agent
   - Agent continues

## Phase 4: Response

### Non-Streaming

1. Wait for complete response
2. Update memory
3. Return JSON response

### Streaming

1. Open SSE connection
2. Stream chunks as received
3. Update memory on completion
4. Close connection

## Memory Updates

After response:

1. **Buffer Memory** - Add to conversation
2. **Vector Index** - Embed for search (if enabled)
3. **Persistent** - Save to database (if enabled)

## Event Emission

Events emitted throughout:

| Phase | Events |
|-------|--------|
| Receipt | `request.received`, `auth.validated` |
| Orchestration | `orchestration.started`, `agent.selected` |
| Execution | `llm.call.started`, `tool.invoked` |
| Response | `response.completed`, `memory.updated` |

## Timing

Typical latency breakdown:

| Phase | Typical Time |
|-------|--------------|
| Auth + Routing | 1-5ms |
| Context Loading | 10-100ms |
| LLM Call | 500ms-10s |
| Tool Execution | 100ms-5s |
| Memory Update | 5-50ms |

## Error Handling

Errors at each phase:

| Phase | Handling |
|-------|----------|
| Auth | 401 response |
| Orchestration | 400 response |
| LLM | Retry or 500 |
| Tool | Agent sees error, decides next |
| Memory | Log, continue |
