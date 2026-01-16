---
title: How MUXI Works
description: Understand what you just built and how the pieces connect
---

# How MUXI Works

## The 30-second mental model

You completed the quickstart. Here's what you actually built:

<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 500 950" width="500" height="950">
  <defs>
    <!-- Arrow marker -->
    <marker id="arrow" markerWidth="8" markerHeight="6" refX="7" refY="3" orient="auto">
      <polygon points="0 0, 8 3, 0 6" fill="#000"/>
    </marker>
  </defs>

  <!-- Background -->
  <rect width="500" height="950" fill="#fff"/>

  <!-- YOUR REQUEST (top) -->
  <ellipse cx="250" cy="50" rx="80" ry="30" fill="none" stroke="#000" stroke-width="1.5"/>
  <text x="250" y="45" text-anchor="middle" fill="#000" font-family="'Courier New', monospace" font-size="11" font-weight="bold">YOUR</text>
  <text x="250" y="58" text-anchor="middle" fill="#000" font-family="'Courier New', monospace" font-size="11" font-weight="bold">REQUEST</text>

  <!-- Smooth curve: Request to Server -->
  <path d="M 250 80 C 250 100, 250 110, 250 130" fill="none" stroke="#000" stroke-width="1.5" marker-end="url(#arrow)"/>

  <!-- MUXI SERVER -->
  <ellipse cx="250" cy="170" rx="90" ry="32" fill="none" stroke="#000" stroke-width="1.5"/>
  <text x="250" y="165" text-anchor="middle" fill="#000" font-family="'Courier New', monospace" font-size="11" font-weight="bold">MUXI SERVER</text>
  <text x="250" y="180" text-anchor="middle" fill="#000" font-family="'Courier New', monospace" font-size="9">:7890</text>

  <!-- Label for server -->
  <text x="250" y="220" text-anchor="middle" fill="#000" font-family="'Courier New', monospace" font-size="8">ROUTES REQUESTS, MANAGES FORMATIONS</text>

  <!-- Smooth curve: Server to Formation -->
  <path d="M 250 202 C 250 230, 250 245, 250 265" fill="none" stroke="#000" stroke-width="1.5" marker-end="url(#arrow)"/>

  <!-- FORMATION RUNTIME (large circle, no fill) -->
  <circle cx="250" cy="420" r="130" fill="none" stroke="#000" stroke-width="1.5"/>

  <!-- Formation label at top of circle -->
  <text x="250" y="305" text-anchor="middle" fill="#000" font-family="'Courier New', monospace" font-size="10" font-weight="bold">FORMATION RUNTIME</text>
  <text x="250" y="318" text-anchor="middle" fill="#000" font-family="'Courier New', monospace" font-size="8">:8001-8999</text>

  <!-- Side label -->
  <text x="30" y="420" text-anchor="start" fill="#000" font-family="'Courier New', monospace" font-size="8">YOUR AI SYSTEM:</text>
  <text x="30" y="433" text-anchor="start" fill="#000" font-family="'Courier New', monospace" font-size="8">AGENTS + MEMORY</text>
  <text x="30" y="446" text-anchor="start" fill="#000" font-family="'Courier New', monospace" font-size="8">+ TOOLS + KNOWLEDGE</text>

  <!-- Curved line pointing to formation -->
  <path d="M 118 420 C 105 420, 100 420, 95 420" fill="none" stroke="#000" stroke-width="1"/>

  <!-- OVERLORD (inside formation) -->
  <ellipse cx="250" cy="370" rx="70" ry="25" fill="#fff" stroke="#000" stroke-width="1.5"/>
  <text x="250" y="374" text-anchor="middle" fill="#000" font-family="'Courier New', monospace" font-size="11" font-weight="bold">OVERLORD</text>

  <!-- Smooth curves: Overlord to Agents -->
  <path d="M 200 390 C 190 410, 175 425, 170 445" fill="none" stroke="#000" stroke-width="1.2" marker-end="url(#arrow)"/>
  <path d="M 250 395 C 250 415, 250 430, 250 445" fill="none" stroke="#000" stroke-width="1.2" marker-end="url(#arrow)"/>
  <path d="M 300 390 C 310 410, 325 425, 330 445" fill="none" stroke="#000" stroke-width="1.2" marker-end="url(#arrow)"/>

  <!-- AGENTS (three circles inside formation) -->
  <circle cx="170" cy="475" r="25" fill="#fff" stroke="#000" stroke-width="1.5"/>
  <text x="170" y="479" text-anchor="middle" fill="#000" font-family="'Courier New', monospace" font-size="9" font-weight="bold">AGENT</text>

  <circle cx="250" cy="475" r="25" fill="#fff" stroke="#000" stroke-width="1.5"/>
  <text x="250" y="479" text-anchor="middle" fill="#000" font-family="'Courier New', monospace" font-size="9" font-weight="bold">AGENT</text>

  <circle cx="330" cy="475" r="25" fill="#fff" stroke="#000" stroke-width="1.5"/>
  <text x="330" y="479" text-anchor="middle" fill="#000" font-family="'Courier New', monospace" font-size="9" font-weight="bold">AGENT</text>

  <!-- Smooth curves: Agents converge and exit formation -->
  <path d="M 170 500 C 170 530, 200 555, 250 570" fill="none" stroke="#000" stroke-width="1.2"/>
  <path d="M 250 500 C 250 530, 250 545, 250 570" fill="none" stroke="#000" stroke-width="1.2"/>
  <path d="M 330 500 C 330 530, 300 555, 250 570" fill="none" stroke="#000" stroke-width="1.2"/>

  <!-- Arrow exiting formation -->
  <path d="M 250 550 C 250 580, 250 600, 250 620" fill="none" stroke="#000" stroke-width="1.5" marker-end="url(#arrow)"/>

  <!-- Bottom layer - smooth branching curves -->
  <path d="M 250 640 C 200 660, 130 680, 100 700" fill="none" stroke="#000" stroke-width="1.5" marker-end="url(#arrow)"/>
  <path d="M 250 640 C 250 660, 250 680, 250 700" fill="none" stroke="#000" stroke-width="1.5" marker-end="url(#arrow)"/>
  <path d="M 250 640 C 300 660, 370 680, 400 700" fill="none" stroke="#000" stroke-width="1.5" marker-end="url(#arrow)"/>

  <!-- LLM -->
  <ellipse cx="100" cy="740" rx="55" ry="28" fill="none" stroke="#000" stroke-width="1.5"/>
  <text x="100" y="736" text-anchor="middle" fill="#000" font-family="'Courier New', monospace" font-size="11" font-weight="bold">LLM</text>
  <text x="100" y="750" text-anchor="middle" fill="#000" font-family="'Courier New', monospace" font-size="8">(OpenAI, etc.)</text>

  <!-- TOOLS -->
  <ellipse cx="250" cy="740" rx="50" ry="28" fill="none" stroke="#000" stroke-width="1.5"/>
  <text x="250" y="736" text-anchor="middle" fill="#000" font-family="'Courier New', monospace" font-size="11" font-weight="bold">TOOLS</text>
  <text x="250" y="750" text-anchor="middle" fill="#000" font-family="'Courier New', monospace" font-size="8">(MCP)</text>

  <!-- KNOWLEDGE -->
  <ellipse cx="400" cy="740" rx="60" ry="28" fill="none" stroke="#000" stroke-width="1.5"/>
  <text x="400" y="736" text-anchor="middle" fill="#000" font-family="'Courier New', monospace" font-size="10" font-weight="bold">KNOWLEDGE</text>
  <text x="400" y="750" text-anchor="middle" fill="#000" font-family="'Courier New', monospace" font-size="8">(RAG)</text>

  <!-- Smooth curves converging to Response -->
  <path d="M 100 768 C 100 800, 180 840, 250 855" fill="none" stroke="#000" stroke-width="1.2"/>
  <path d="M 250 768 C 250 800, 250 830, 250 855" fill="none" stroke="#000" stroke-width="1.2"/>
  <path d="M 400 768 C 400 800, 320 840, 250 855" fill="none" stroke="#000" stroke-width="1.2"/>

  <!-- Arrow to Response -->
  <path d="M 250 855 C 250 865, 250 875, 250 885" fill="none" stroke="#000" stroke-width="1.5" marker-end="url(#arrow)"/>

  <!-- RESPONSE (pink circle) -->
  <circle cx="250" cy="915" r="25" fill="#FF6B8A" stroke="none"/>
  <text x="250" y="919" text-anchor="middle" fill="#000" font-family="'Courier New', monospace" font-size="9" font-weight="bold">RESPONSE</text>

  <!-- Feedback loop - smooth curve back up -->
  <path d="M 275 915 C 450 915, 470 500, 470 170 C 470 80, 400 50, 330 50" fill="none" stroke="#000" stroke-width="1" stroke-dasharray="4,4" marker-end="url(#arrow)"/>

  <text x="480" y="500" text-anchor="start" fill="#000" font-family="'Courier New', monospace" font-size="7" writing-mode="vertical-rl">CONVERSATION CONTINUES</text>
</svg>

```
┌─────────────────────────────────────────────────────────────┐
│                        Your Request                         │
│                     "Hello, assistant!"                     │
└──────────────────────────────┬──────────────────────────────┘
                               ↓
┌─────────────────────────────────────────────────────────────┐
│                   MUXI Server (:7890)                       │
│         Routes requests, manages formations, handles auth   │
└──────────────────────────────┬──────────────────────────────┘
                               ↓
┌─────────────────────────────────────────────────────────────┐
│               Formation Runtime (:8001-8999)                │
│     Your AI system: agents + memory + tools + knowledge     │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   ┌─────────────────────────────────────────────────────┐   │
│   │                     Overlord                        │   │
│   │  • Loads memory context    • Routes to agents       │   │
│   │  • Applies persona         • Updates memory         │   │
│   └──────────────────────────┬──────────────────────────┘   │
│                              ↓                              │
│       ┌───────────┐    ┌───────────┐    ┌───────────┐       │
│       │   Agent   │    │   Agent   │    │   Agent   │       │
│       │           │    │           │    │           │       │
│       └─────┬─────┘    └─────┬─────┘    └─────┬─────┘       │
│             │                │                │             │
│             └────────────────┼────────────────┘             │
│                              ↓                              │
│             ┌────────────────┼────────────────┐             │
│             ↓                ↓                ↓             │
│       ┌───────────┐    ┌───────────┐    ┌───────────┐       │
│       │    LLM    │    │   Tools   │    │ Knowledge │       │
│       │  (OpenAI) │    │   (MCP)   │    │   (RAG)   │       │
│       └───────────┘    └───────────┘    └───────────┘       │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

**Think of it as:**
- **Server** = Traffic controller (like Nginx for AI)
- **Formation** = Your complete AI system (like a Docker container)
- **Overlord** = The brain that manages memory, routes requests, and applies persona
- **Agent** = A specialized worker that uses tools and knowledge to complete tasks

---

## What happens when you send a message

[[steps]]

[[step Your app sends a request]]

```bash
curl -X POST http://localhost:8001/v1/chat \
  -d '{"message": "What can you help me with?"}'
```

The request hits your formation's API and goes to the **Overlord**.

[[/step]]

[[step The Overlord builds context]]

The Overlord loads context from three memory tiers:

- **Buffer memory** - Recent conversation messages
- **Long-term memory** - User preferences and history (if enabled)
- **Working memory** - Current session state

This context is attached to your message before any agent sees it.

[[/step]]

[[step The Overlord routes to an agent]]

The Overlord decides how to handle your request:

1. **SOP match?** → Execute the standard procedure
2. **Complex request?** → Decompose into multi-agent workflow
3. **Simple request?** → Route to the best-suited agent

```
User: "What can you help me with?"
  ↓
Overlord: "Simple question → route to 'assistant' agent"
```

[[/step]]

[[step The agent processes with tools]]

The selected agent:

- Receives the message + context from the Overlord
- Calls MCP tools if needed (web search, databases, etc.)
- Retrieves relevant knowledge (RAG)
- Sends everything to the LLM

[[/step]]

[[step The Overlord applies persona and responds]]

The Overlord:

- Applies the configured persona (tone, style) to the response
- Streams the response back to your app
- Updates all memory tiers with the conversation

[[/step]]

[[/steps]]

> [!TIP]
> For a deep dive into every step, see [Request Lifecycle](deep-dives/request-lifecycle.md).

---

## The four things you configure

Every formation has four core building blocks. You don't need all of them - start simple and add as needed.

:::: cols=2

[[card]]

#### 1. Agents

**What they do:** Specialized workers with specific roles.

**Example:** A "researcher" agent that finds information, a "writer" agent that drafts content.

```yaml
# agents/assistant.afs
id: assistant
name: My Assistant
system_message: You are a helpful assistant.
```

[Learn more →](concepts/agents-and-orchestration.md)

[[/card]]

[[card]]

#### 2. Tools (MCP)

**What they do:** Give agents capabilities beyond text generation.

**Example:** Web search, database queries, file operations, API calls.

```yaml
# mcp/web-search.afs
id: web-search
server: "@anthropic/web-search"
```

[Learn more →](concepts/tools-and-mcp.md)

[[/card]]

[[card]]

#### 3. Memory

**What it does:** Remembers conversations across sessions.

**Example:** User preferences, conversation history, learned context.

```yaml
# formation.afs
memory:
  persistent:
    enabled: true
```

[Learn more →](concepts/memory-system.md)

[[/card]]

[[card]]

#### 4. Knowledge (RAG)

**What it does:** Gives agents access to your documents.

**Example:** Product docs, FAQs, internal wikis.

```yaml
# agents/support.afs
knowledge:
  sources:
    - path: ./docs
```

[Learn more →](concepts/knowledge-and-rag.md)

[[/card]]

::::

---

## Formation file structure

When you ran `muxi new formation`, you got this structure:

```
my-assistant/
├── formation.afs      # Main configuration (LLM, memory, defaults)
├── agents/            # Agent definitions (auto-discovered)
│   └── assistant.afs  # Your agent
├── mcp/               # Tool configurations (optional)
├── knowledge/         # Documents for RAG (optional)
├── sops/              # Standard procedures (optional)
├── triggers/          # Webhook templates (optional)
└── secrets.example    # Required API keys template
```

### The key files

[[tabs]]

[[tab formation.afs]]

The main configuration file. Sets defaults for the entire formation.

```yaml
schema: "1.0.0"
id: my-assistant

llm:
  models:
    - text: "openai/gpt-4o"

memory:
  persistent:
    enabled: true
```

[[/tab]]

[[tab agents/*.afs]]

Each agent is a separate file. They're auto-discovered from the `agents/` directory.

```yaml
schema: "1.0.0"
id: assistant
name: Helpful Assistant
description: General-purpose assistant

system_message: |
  You are a helpful assistant. Be concise and friendly.

# Optional: agent-specific tools
mcp_servers:
  - web-search

# Optional: agent-specific knowledge
knowledge:
  sources:
    - path: ./docs/faq.md
```

[[/tab]]

[[tab mcp/*.afs]]

Tool servers that agents can use. Each file defines one MCP server.

```yaml
schema: "1.0.0"
id: web-search
server: "@anthropic/web-search"

# Some tools need API keys
env:
  API_KEY: ${{ secrets.SEARCH_API_KEY }}
```

[[/tab]]

[[/tabs]]

---

## Single agent vs. multi-agent

### Single agent (what you built)

Simple formations have one agent that handles everything:

```
User → Overlord → assistant → LLM → Response
```

Good for: chatbots, simple assistants, focused tools.

### Multi-agent (when you're ready)

Complex formations have specialized agents that collaborate:

```
User → Overlord ─┬→ researcher → find information
                 ├→ analyst    → analyze data
                 └→ writer     → draft response
```

The Overlord routes requests to the right agent or coordinates multiple agents for complex tasks.

Good for: customer support systems, research assistants, content pipelines.

[Build Multi-Agent Teams →](guides/build-multi-agent-systems.md)

---

## Development vs. production

| | `muxi dev` | `muxi deploy` |
|---|---|---|
| **Where it runs** | Your machine | MUXI Server |
| **Port** | 8001 (direct) | 7890 (proxied) |
| **Hot reload** | Yes | No |
| **Use for** | Development | Production |

### Local development

```bash
muxi dev
# Formation running at http://localhost:8001
```

### Production deployment

```bash
muxi deploy
# Formation deployed to server at http://server:7890/api/my-assistant/
```

[Deploy to Production →](guides/deploy-to-production.md)

---

## Next steps

Now that you understand how MUXI works, choose your path:

:::: cols=2

(guides/add-mcp-tools.md)[[card]]
#### Add Tools
Give your agent web search, file access, databases, and more via MCP.
[[/card]]

(guides/add-memory.md)[[card]]
#### Add Memory
Make conversations persist across sessions.
[[/card]]

(guides/add-knowledge.md)[[card]]
#### Add Knowledge
Let your agent reference your documents (RAG).
[[/card]]

(guides/build-multi-agent-systems.md)[[card]]
#### Build Multi-Agent Teams
Create specialized agents that work together.
[[/card]]

::::

---

## Go deeper

[+] [Architecture](concepts/architecture.md) - Complete system architecture
[+] [Formation Schema](concepts/formation-schema.md) - Full YAML specification
[+] [The Overlord](concepts/overlord.md) - How orchestration works
[+] [Request Lifecycle](deep-dives/request-lifecycle.md) - Every step of a request
