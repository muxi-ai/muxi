---
title: Agents & Orchestration
description: How MUXI agents work together to accomplish complex tasks
---
# Agents & Orchestration

## How MUXI agents work together to accomplish complex tasks


MUXI agents are specialized workers that collaborate, delegate, and adapt. The Overlord orchestrates them automatically, routing tasks to the right agent and breaking complex requests into manageable steps.

---

## How Agents Work

Each agent is a specialized AI persona with:
- A **role** defining its expertise and behavior
- **Tools** it can use (web search, databases, APIs)
- **Knowledge** it can reference (documents, FAQs)

```yaml
agents:
  - id: researcher
    role: Research topics thoroughly with web search
    mcps:
      - web-search

  - id: writer
    role: Write clear, engaging content
```

When a request arrives, MUXI's **Overlord** decides which agent handles it - or coordinates multiple agents for complex tasks.

---

## The Overlord Pattern

```mermaid
graph TD
    U[User Request] --> O[Overlord]
    O --> |Simple| A1[Single Agent]
    O --> |Complex| W[Workflow]
    W --> A1[Researcher]
    W --> A2[Writer]
    W --> A3[Reviewer]
    A1 --> R[Response]
    A2 --> R
    A3 --> R
```

The Overlord:
1. **Analyzes** incoming requests for complexity
2. **Routes** to the best agent (or creates a workflow)
3. **Coordinates** multi-agent execution
4. **Synthesizes** the final response

> [!TIP]
> You don't need to specify which agent to use - MUXI figures it out. But you can override with `agent: researcher` in your request.

---

## Intelligent Task Decomposition

Complex requests are automatically broken into steps:

```
User: "Research AI trends and write a blog post about them"

Overlord analyzes:
  - Complexity score: 8/10
  - Requires: research + writing
  - Creates workflow:
    1. Researcher gathers information
    2. Writer creates draft
    3. (Optional) Reviewer checks quality
```

This happens automatically based on your formation's `complexity_threshold`:

```yaml
overlord:
  auto_decomposition: true
  complexity_threshold: 7.0
```

---

## Agent Collaboration (A2A)

Agents within your formation work together seamlessly. For cross-formation or cross-organization collaboration, MUXI implements the **A2A protocol**:

```
┌──────────────────┐           ┌───────────────────┐
│  Your Formation  │ ←- A2A -→ │ Partner Formation │
│  (Research Bot)  │           │   (Legal Review)  │
└──────────────────┘           └───────────────────┘
```

Agents can:
- **Delegate** tasks to specialists in other formations
- **Collaborate** with external agents from other organizations
- **Discover** capabilities through protocol negotiation

---

## Routing Priority

When a request arrives, routing follows this order:

1. **SOP Match** - If request matches a Standard Operating Procedure, execute it
2. **Explicit Agent** - If request specifies an agent, use it
3. **Complexity Analysis** - Score the request complexity
4. **Best Agent Selection** - Route to most capable agent

```
Request: "onboard new customer"
         ↓
    SOP "customer-onboarding" matches!
         ↓
    Execute SOP (bypass other routing)
```

---

## Why This Matters

| Traditional Approach | MUXI Approach |
|---------------------|---------------|
| One monolithic prompt | Specialized agents |
| Manual workflow code | Automatic orchestration |
| Static task routing | Dynamic complexity analysis |
| Siloed agents | Collaborative A2A |

The result: **agents that work together**, not chat interfaces with different prompts.

---

## Learn More

- [Configure agents](../reference/agents.md) - YAML syntax for agent definitions
- [Multi-Agent Guide](../guides/multi-agent.md) - Build your first multi-agent system
- [Orchestration Deep Dive](../deep-dives/orchestration.md) - Technical internals
