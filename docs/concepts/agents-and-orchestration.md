---
title: Agents & Orchestration
description: How MUXI agents work together to accomplish complex tasks
---
# Agents & Orchestration

## How MUXI agents work together to accomplish complex tasks

Agents are workers. The Overlord delegates tasks to them, they execute with their tools and knowledge, and return results. Users never interact with agents directly - they talk to MUXI.

## Agents Are Workers

Think of agents as specialized employees who:
1. Receive task instructions from the Overlord
2. Execute using their tools and knowledge
3. Return results to the Overlord
4. Can collaborate with other agents if needed

```plain
       User request
             ↓
         Overlord
             ↓
    Task decomposition
             ↓
    Delegate to agents
             ↓
   ┌─────────┬─────────┐
   ↓         ↓         ↓
 Agent A   Agent B   Agent N
   ↓         ↓         ↓
   └─────────┴─────────┘
             ↓
Overlord synthesizes results
             ↓
      Response to user
```


## Agent Definition

Agents are defined in `agents/*.afs` files:

```yaml
# agents/researcher.afs
schema: "1.0.0"
id: researcher
name: Research Specialist
description: Gathers information from web sources
role: researcher

# Instructions for how agent should behave
system_message: |
  Research topics thoroughly using web search.
  Always cite your sources.
  Return structured findings.

# Metadata for Overlord's routing decisions
specialties:
  - web research
  - data gathering
  - fact checking
```

| Field | Purpose |
|-------|---------|
| `system_message` | Instructions for task execution |
| `description` | Human-readable summary |
| `specialties` | Routing metadata for Overlord |
| `role` | Category (researcher, writer, etc.) |

> **Note:** Agents don't have personas - only the Overlord does. The `system_message` is instructions, not personality.


## The Overlord Orchestrates

The Overlord is responsible for all orchestration:

1. **Analyzes** user requests for complexity
2. **Creates workflows** dynamically (not predefined)
3. **Selects agents** based on capabilities
4. **Delegates tasks** with context and tools
5. **Synthesizes** results into coherent response

```yaml
overlord:
  workflow:
    auto_decomposition: true
    complexity_threshold: 7.0  # Above this, show plan for approval
```

Complex tasks are broken down automatically:

```
User:  "Research AI trends and write a blog post"
         ↓
Overlord creates workflow:
  Task 1: researcher → "Research current AI trends"
  Task 2: writer → "Write blog post from research"
```


## Agent-to-Agent (A2A) Collaboration

Agents can collaborate with each other. This is enabled by default for agents within the same formation.

### How It Works

When an agent realizes it can't complete a task alone:

```
Agent working on task
         ↓
Realizes it lacks tools/knowledge
         ↓
Checks: Local agents have capability?
         ↓ YES
A2A request to local agent
         ↓ NO (or not available)
Check: External A2A configured?
         ↓ YES
A2A request to external formation
```

### Priority Order

1. **Local agents first** - Fastest, no network overhead
2. **External formations** - Only if local can't help AND configured

### Internal A2A (Default)

Agents in the same formation can collaborate automatically:

```
Developer Agent: "I need security review of this code"
         ↓
A2A to Security Agent (same formation)
         ↓
Security Agent: Returns findings
         ↓
Developer Agent: Continues with recommendations
```

This happens without Overlord involvement - agents make tactical decisions.

### External A2A (Requires Config)

For cross-formation collaboration, you must configure A2A:

```yaml
# formation.afs
a2a:
  # Allow receiving requests from external formations
  receiver:
    enabled: true
    authentication:
      type: api_key
      allowed_sources:
        - partner-formation-id

  # Allow sending requests to external formations
  sender:
    enabled: true
    registries:
      - url: https://partner.example.com/a2a
        auth: ${{ secrets.PARTNER_API_KEY }}
```

**Security is critical for external A2A:**
- Authentication required (API keys, signed requests)
- Rate limiting to prevent abuse
- Scoped capabilities per partner
- All events logged for audit


## Routing Priority

When a request arrives:

```
1. SOP match? → Execute SOP (highest priority)
         ↓ No
2. Explicit agent specified? → Use that agent
         ↓ No
3. Complexity analysis → Score request
         ↓
4. Select best agent(s) based on capabilities
```

SOPs (Standard Operating Procedures) always take priority when matched.


## What Agents Receive

When the Overlord delegates a task, the agent receives:

1. **Task instructions** - What to do
2. **Context** - Relevant information from the conversation
3. **Available tools** - MCP tools relevant to this task (not all tools)
4. **Global tools** - Tools available to all agents

```
Overlord → Agent:
  "Research AI trends for a blog post"
  + Context from user conversation
  + web_search, browse_url tools (relevant)
  + NOT database tools (not relevant)
```

> **Key insight:** The Overlord passes only relevant tool capabilities to each agent. This solves context contamination - agents don't get overwhelmed with irrelevant tools.


## Example: Multi-Agent Workflow

```
User:  "Build a customer analytics dashboard"

Overlord decomposes:
  1. Data Engineer → Design database schema
  2. Backend Dev → Build API endpoints
  3. Frontend Dev → Create dashboard UI
  4. DevOps → Set up monitoring

During execution (A2A collaboration):
  Backend Dev: "Frontend, what data format do you need?"
  Frontend Dev: "JSON with these fields..."

  Backend Dev: "Stuck on this SQL query"
  Data Engineer: "Try this approach..."
```

The Overlord sets strategy, agents execute and collaborate as needed.


## Why This Matters

| Without Orchestration | With MUXI |
|---------------------|-----------|
| Manual routing logic | Automatic agent selection |
| Hard-coded workflows | Dynamic task decomposition |
| Siloed agents | Collaborative A2A |
| One agent does everything | Right specialist for each task |


## Learn More

- [The Overlord](overlord.md) - How orchestration works
- [SOPs](standard-operating-procedures.md) - Predefined workflow templates
- [Configure Agents](../reference/agents.md) - Agent file syntax
- [Multi-Agent Guide](../guides/build-multi-agent-systems.md) - Build your first system
