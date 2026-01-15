---
title: Observability
description: Why visibility matters for non-deterministic AI systems
---
# Observability

## Why you need to see inside your AI systems

AI agents are **non-deterministic**. The same input can produce different outputs. An agent might choose different tools, retrieve different context, or generate different responses - even with identical prompts.

This is fundamentally different from traditional software where you can predict behavior from code inspection. With agents, you need to **observe what actually happened**, not what you expected to happen.

## The Challenge

**Traditional software:**
```
Input → Deterministic Code → Predictable Output
        (inspect code to debug)
```

**AI agents:**
```
Input → LLM Reasoning → Tool Selection → Memory Retrieval → Response
        (unpredictable) (unpredictable)  (context-dependent)
```

When something goes wrong, you can't just read the code. You need to trace:
- Which agent was selected and why
- What the LLM was thinking (reasoning)
- Which tools were called with what parameters
- What context was retrieved from memory
- How the final response was generated

Without observability, you're debugging a black box.

## What MUXI Tracks

Every action in the system emits structured events:

| Category | What's tracked |
|----------|----------------|
| **Routing** | Agent selection decisions, routing scores |
| **LLM Calls** | Prompts, completions, tokens, latency, cost |
| **Tools** | Tool calls, parameters, results, errors |
| **Memory** | Retrievals, what context was used |
| **Workflows** | Task decomposition, step execution |
| **Errors** | Failures, retries, fallbacks |

**~350 typed events** across the full request lifecycle.

### Two Types of Events

| Type | Audience | Purpose | Examples |
|------|----------|---------|----------|
| **Request lifecycle** | Users (via SDK) | Trace what happened to their request | Agent selected, task progress, response generated |
| **System/error** | Developers only | Debug infrastructure and errors | MCP server connected, memory operation failed |

System-level events (connecting to MCP servers, error handling, file creation, memory operations) are never exposed to users - only developers can see them for debugging.

## Example: Debugging a Wrong Answer

User reports: "The agent gave me incorrect information about our refund policy."

**Without observability:** 🤷 No idea what happened.

**With observability:**
```
1. Request received → routed to "support" agent ✓
2. Knowledge retrieval → searched "refund policy" 
   → Retrieved: outdated-policy-2023.md ❌ (found the bug!)
3. LLM call → generated response based on wrong doc
4. Response sent
```

Now you know: the knowledge index has stale documents. Fix: re-index with current docs.

## Export Targets

Stream events to your existing infrastructure:

- **Datadog** - Logs, metrics, APM
- **Elastic** - ELK stack
- **Splunk** - Enterprise logging
- **OpenTelemetry** - OTLP protocol
- **Webhooks** - Custom integrations

No sidecars or agents required - MUXI exports directly.

## Configuration

```yaml
observability:
  enabled: true
  export:
    type: datadog          # datadog, elastic, splunk, otlp, webhook
    endpoint: "https://..."
    api_key: ${{ secrets.DATADOG_API_KEY }}
  
  # What to track
  events:
    llm_calls: true        # Token usage, latency, cost
    tool_calls: true       # All tool executions
    memory_ops: true       # Memory reads/writes
    routing: true          # Agent selection decisions
```

## Topic Tagging (Analytics)

Every request automatically gets 1-5 semantic topic tags:

```
Request: "Analyze customer feedback survey data from last quarter"
Topics: ["data-analysis", "customer-feedback", "surveys", "insights"]
```

**No extra LLM calls** - topics are extracted during request analysis that already happens.

### What Topics Enable

| Use Case | Example |
|----------|---------|
| **Dashboards** | "What are users asking about this week?" |
| **Filtering** | "Show me all debugging-related requests" |
| **Trends** | "API questions up 40% this month" |
| **Alerting** | "Alert if >10 'authentication' requests fail" |

### Event Format

```json
{
  "event": "request.topics.extracted",
  "data": {
    "topics": ["data-analysis", "customer-feedback", "surveys"],
    "topic_count": 3,
    "complexity_score": 7.5
  }
}
```

Topics are normalized to lowercase-with-hyphens for consistency.

---

## Use Cases

### Debugging
Trace why an agent made a specific decision. See the full context it had access to.

### Cost Tracking
Monitor token usage per user, per agent, per formation. Set alerts for runaway costs.

### Performance
Identify slow LLM calls, memory lookups, or tool executions. Optimize bottlenecks.

### Compliance
Audit log of all data access and tool calls. Required for regulated industries.

### Alerting
Get notified when error rates spike, latency degrades, or specific events occur.

## Why This Matters More for AI

In traditional software, bugs are reproducible. Run the same input, get the same bug.

In AI systems:
- Same input → different outputs (non-deterministic)
- Behavior depends on context (memory, retrieved docs)
- Failures can be subtle (wrong answer vs. crash)
- Root causes are often in data, not code

**Observability isn't optional for production AI. It's how you maintain quality.**

---

## Learn More

[+] [Observability Deep Dive](../deep-dives/observability.md) - Full event reference and configuration
[+] [Event Types](../deep-dives/observability-events.md) - All 349 event types
[+] [Set Up Monitoring](../guides/setup-monitoring.md) - Step-by-step guide
