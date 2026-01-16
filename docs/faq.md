---
title: Common Questions and Use-cases
description: Find your question, get the answer
---

# Common Questions and Use-cases

## What can MUXI do? How do I...?

Find your question, get the answer. Each section links to deeper documentation.


## Building Agents

[[toggle How do I define an agent?]]

In YAML. Create an `.afs` file (Agent Formation Schema) with your agent's name, persona, LLM provider, and capabilities. No framework code required.

```yaml
name: support-agent
agents:
  support:
    persona: "Helpful customer support specialist"
    provider: openai/gpt-4o
    tools: [search, ticket-system]
    knowledge: ./docs/
```

**Learn more:** [Formation Schema](concepts/formation-schema.md) | [Quickstart](quickstart.md)

[[/toggle]]

[[toggle How do agents remember context?]]

MUXI has a **four-layer memory system**:

1. **Buffer** - Recent messages (fast, in-memory)
2. **Working** - Session state and tool outputs (FAISSx vector store)
3. **User Synopsis** - Who the user is (LLM-synthesized profile)
4. **Persistent** - Long-term storage (Postgres for multi-tenancy, SQLite for single-user)

User Synopsis reduces token usage by 80%+ by replacing full history with a concise profile.

> **Note:** Long-term memory requires either Postgres (multi-tenant) or SQLite (single-user).

**Learn more:** [Memory System](concepts/memory-system.md) | [Memory Internals](deep-dives/memory-internals.md) | [Add Memory](guides/add-memory.md)

[[/toggle]]

[[toggle How do I give agents access to tools and APIs?]]

Via **MCP (Model Context Protocol)**. MUXI supports 1,000+ pre-built tools: web search, databases, file systems, APIs, and more.

```yaml
tools:
  - mcp: brave-search
  - mcp: postgres
  - mcp: slack
```

Tools are loaded efficiently - schemas indexed once, not dumped into every request.

**Learn more:** [Tools & MCP](concepts/tools-and-mcp.md) | [Add Tools](guides/add-mcp-tools.md)

[[/toggle]]

[[toggle What happens when tools fail?]]

Agents **self-heal**. When a tool fails, agents analyze the error and take corrective action:

```
User:  "Create file at /reports/q4/summary.txt"
Agent: write_file(...) → Error: "Directory doesn't exist"
Agent: create_directory("/reports/q4") → Success
Agent: write_file(...) → Success!
```

The user never sees the error - the agent just handles it. This works for missing directories, authentication issues, rate limits, and more.

**Learn more:** [Tools & MCP](concepts/tools-and-mcp.md#self-healing-agents-tool-chaining)

[[/toggle]]

[[toggle How do I give agents domain knowledge?]]

Point agents at documents. MUXI indexes PDFs, Markdown, Word, Excel, images, and more. Each agent can have its own knowledge set.

```yaml
agents:
  legal:
    knowledge: ./contracts/
  support:
    knowledge: ./help-docs/
```

Uses semantic search (meaning, not keywords) and incremental indexing (only re-indexes changed files).

**Learn more:** [Knowledge & RAG](concepts/knowledge-and-rag.md) | [Add Knowledge](guides/add-knowledge.md)

[[/toggle]]

[[toggle Can I define workflows or procedures for agents?]]

Yes - **Standard Operating Procedures (SOPs)**. Create markdown files in `sops/` that define step-by-step procedures:

```markdown
<!-- sops/refund-request.md -->
---
type: sop
name: Refund Processing
mode: guide
tags: refund, customer, payment
---

## Steps

1. **Verify Purchase** - Look up order in system
2. **Check Policy** - Verify refund eligibility
3. **Process or Deny** - Issue refund or explain denial
```

When a user request matches an SOP (via semantic search), the agent follows that procedure. SOPs have highest routing priority.

> **Note:** SOPs are predefined templates. Regular workflows are created dynamically by the Overlord for each request.

**Learn more:** [SOPs](concepts/standard-operating-procedures.md) | [Create SOPs](guides/create-sops.md)

[[/toggle]]

[[toggle Can agents ask clarifying questions?]]

Yes. When a request is ambiguous, agents can **ask for clarification** instead of guessing:

```
User:  "Book a flight"
Agent: "I'd be happy to help. Where are you flying to, and what dates work for you?"
```

Configure clarification behavior per agent - when to ask, what to ask, how many times.

**Learn more:** [Clarifications](concepts/clarification.md)

[[/toggle]]

[[toggle Can agents generate files?]]

Yes - **artifacts**. Agents can create documents, images, code files, reports, and more:

```
User:  "Create a sales report for Q4"
Agent: "Here's your Q4 sales report" + [sales-report-q4.pdf]
```

Artifacts are returned alongside the response and can be downloaded or processed by your app.

**Learn more:** [Artifacts](concepts/artifacts.md)

[[/toggle]]

[[toggle Can I customize the agent's personality?]]

Yes, but only the **Overlord** has a persona. Users talk to MUXI (the Overlord), not individual agents - so the persona defines how MUXI communicates:

```yaml
overlord:
  persona: |
    You are a friendly, professional assistant.
    Be concise but warm. Use simple language.
    Never use jargon unless the user does first.
```

Individual agents have **system prompts** (instructions for task execution) and **capabilities** (metadata for routing) - but not personas.

**Learn more:** [Overlord Persona](concepts/persona.md) | [The Overlord](concepts/overlord.md)

[[/toggle]]

[[toggle Can I get structured JSON responses?]]

Yes - **structured output**. Define a schema and get validated JSON back:

```yaml
agents:
  extractor:
    output_format:
      type: object
      properties:
        name: { type: string }
        email: { type: string }
        sentiment: { type: string, enum: [positive, neutral, negative] }
```

Response is guaranteed to match your schema.

**Learn more:** [Structured Output](concepts/structured-output.md)

[[/toggle]]

---

## Multi-Agent Systems

[[toggle How do multiple agents work together?]]

The **Overlord** orchestrates everything:

- **Intelligent Routing** - Requests go to the best agent automatically
- **Task Decomposition** - Complex requests broken into steps
- **Agent-to-Agent (A2A)** - Agents delegate to specialists across formations

You define agents with specialties; the Overlord figures out who handles what.

**Learn more:** [The Overlord](concepts/overlord.md) | [Agents & Orchestration](concepts/agents-and-orchestration.md) | [How Orchestration Works](deep-dives/how-orchestration-works.md)

[[/toggle]]

[[toggle Which LLMs are supported?]]

All of them. MUXI is **LLM-agnostic**:

- OpenAI (GPT-4o, GPT-4, etc.)
- Anthropic (Claude 3.5, Claude 3, etc.)
- Google (Gemini)
- Ollama (local models)
- Any OpenAI-compatible API

Different agents can use different providers in the same formation.

**Learn more:** [LLM Providers](concepts/llm-providers.md) | [Formation Schema](reference/formation-schema.md)

[[/toggle]]

---

## Production & Scale

[[toggle Can multiple users share one deployment?]]

Yes - **multi-tenancy** is built in. Each user gets:

- Isolated memory (no data leakage)
- Separate conversation history
- Their own API credentials for tools

One server, many users, complete isolation.

**Learn more:** [Multi-Tenancy](concepts/multi-tenancy.md) | [Multi-Tenancy Deep Dive](deep-dives/multi-tenancy.md)

[[/toggle]]

[[toggle Can the same user chat on Slack AND web?]]

Yes - **multi-identity** lets you link multiple identifiers to one user:

```python
# Same user, different platforms
formation.associate_user_identifiers(
    identifiers=[
        "alice@email.com",          # Email
        "U12345ABC",                 # Slack ID
        "user_123"                   # Your internal ID
    ]
)

# Now they all share the same memory
formation.chat("Hi", user_id="alice@email.com")  # Same user
formation.chat("Hi", user_id="U12345ABC")        # Same user
```

User chats on Slack, email, or web - same context everywhere.

**Learn more:** [Multi-Identity Users](concepts/multi-identity.md) | [SDKs](sdks/README.md)

[[/toggle]]

[[toggle Can users recall past conversations?]]

Yes - **cross-session memory**. Working memory spans sessions:

```
User:  "Remember when we discussed Japan last week?"
MUXI: "Yes! You mentioned a 2-week trip in March..."
↑ Found in working memory from previous session
```

Sessions are separate conversation threads, but memory flows across them. Users can reference past chats without specifying which session.

**Learn more:** [Sessions](concepts/sessions.md) | [Memory System](concepts/memory-system.md)

[[/toggle]]

[[toggle How do I deploy to production?]]

One command:

```bash
muxi deploy
```

MUXI is a **single binary** - no dependencies, no Docker required (though Docker is supported). Supports GitOps workflows for PR-based deployments.

**Learn more:** [Deploy to Production](guides/deploy-to-production.md) | [Set Up CI/CD](guides/setup-ci-cd.md) | [Production Checklist](server/production-checklist.md)

[[/toggle]]

[[toggle How do I monitor my agents?]]

**Built-in observability**. Every agent action emits structured events:

- LLM calls (tokens, latency, cost)
- Tool usage
- Workflow steps
- Errors

Export to Datadog, Elastic, Splunk, OpenTelemetry, or webhooks - no sidecars needed.

**Learn more:** [Observability](deep-dives/observability.md) | [Observability Events](deep-dives/observability-events.md) | [Set Up Monitoring](guides/setup-monitoring.md)

[[/toggle]]

[[toggle Can I see what users are asking about?]]

Yes - **automatic topic tagging**. Every request gets 1-5 semantic topic tags:

```
Request: "Debug the OAuth authentication flow"
Topics: ["debugging", "oauth", "authentication", "api"]
```

No extra LLM calls - topics are extracted during request analysis. Use topics for dashboards, filtering, trend analysis, and alerting.

**Learn more:** [Observability](concepts/observability.md#topic-tagging-analytics)

[[/toggle]]

[[toggle Is it reliable enough for production?]]

Yes. **Enterprise resilience patterns** are baked in:

- Circuit breakers for failing dependencies
- Retries with exponential backoff
- Graceful degradation
- LLM response caching to reduce load and latency
- Auto-restart for crashed formations

**Learn more:** [Resilience](deep-dives/resilience.md) | [Security Model](deep-dives/security-model.md)

[[/toggle]]

[[toggle What do users see when something fails?]]

**Helpful messages, not cryptic errors.** The resilience layer translates technical failures:

```
Traditional: "Error: 401 Unauthorized"

MUXI: "Unable to connect to Linear. Please check that your
      API token is configured correctly in settings."
```

Plus automatic context-aware handling:
- Rate limited? → "Waiting 30 seconds before retrying..."
- Timeout? → "The service is slow, retrying..."
- Partial failure? → Delivers what succeeded + explains what didn't

Users stay informed, not frustrated.

[[/toggle]]

[[toggle How do I reduce LLM costs?]]

Several built-in features help:

- **Semantic LLM Caching** - Not just exact matches! Similar requests hit cache:
  ```
  "What's the weather?" ≈ "How's the weather today?" → Cache hit!
  ```
  Typical savings: **70%+ cost reduction**

- **User Synopsis Caching** - Compress long conversation history (80%+ token reduction)
- **Efficient tool loading** - Tool schemas indexed once, not sent every request
- **Model mixing** - Use cheaper models for simple tasks, expensive ones for complex

**Learn more:** [LLM Caching](reference/llm-caching.md) | [Memory Internals](deep-dives/memory-internals.md)

[[/toggle]]

[[toggle Can I schedule recurring tasks?]]

Yes - **scheduled tasks**. Cron-style or natural language:

```yaml
triggers:
  daily-report:
    schedule: "0 9 * * *"    # Every day at 9am
    action: "Generate daily analytics summary"

  weekly-cleanup:
    schedule: "every monday at 6am"
    action: "Archive old conversations"
```

**Learn more:** [Scheduled Tasks](concepts/scheduled-tasks.md) | [Triggers & Webhooks](concepts/triggers-and-webhooks.md)

[[/toggle]]

[[toggle What if I need human approval for certain actions?]]

Use **human-in-the-loop** (HITL). Define which actions require approval:

```yaml
agents:
  finance:
    approvals:
      - action: "process_refund"
        when: "amount > 1000"
        notify: "finance-team@company.com"
```

The agent pauses, notifies the approver, and continues only after approval.

**Learn more:** [Human-in-the-Loop](concepts/human-in-the-loop.md)

[[/toggle]]

---

## Real-Time & Async

[[toggle Can I stream responses in real-time?]]

Yes. MUXI supports:

- **SSE (Server-Sent Events)** - Stream tokens as the model thinks
- **WebSockets** - Bidirectional real-time communication

Perfect for chat UIs and live dashboards.

**Learn more:** [Real-Time Streaming](deep-dives/real-time-streaming.md) | [Response Formats](deep-dives/response-formats.md)

[[/toggle]]

[[toggle What about long-running tasks?]]

Use **async operations**. Long tasks run in the background with status updates. Users aren't left waiting.

**Learn more:** [Async Operations](deep-dives/async-operations.md)

[[/toggle]]

[[toggle Can external systems trigger agents?]]

Yes - **triggers and webhooks**:

- HTTP webhooks trigger agent actions
- Cron-style scheduled tasks
- Natural language scheduling ("remind me tomorrow")

**Learn more:** [Triggers & Webhooks](concepts/triggers-and-webhooks.md) | [Create Triggers](guides/create-triggers.md)

[[/toggle]]

---

## Security

[[toggle How are secrets and API keys handled?]]

**Encrypted at rest**. API keys never stored in plain text. Per-user credentials supported - each user can store their own keys for tools.

**Learn more:** [Secrets & Security](concepts/secrets-and-security.md)

[[/toggle]]

[[toggle How does authentication work?]]

**HMAC-signed requests** between CLI, SDKs, and server. Plus complete user isolation - data never leaks between users.

**Learn more:** [Authentication](server/authentication.md) | [Security Model](deep-dives/security-model.md)

[[/toggle]]

[[toggle Can users bring their own API keys?]]

Yes - **per-user credentials**. Each user can store their own API keys for tools:

```python
# User stores their own OpenAI key
client.set_user_secret("openai_api_key", "sk-...")

# Agent uses user's key for their requests
# Other users' requests use other keys (or formation default)
```

Great for platforms where users pay for their own LLM usage.

**Learn more:** [User Credentials](concepts/user-credentials.md) | [Secrets & Security](concepts/secrets-and-security.md)

[[/toggle]]

---

## Developer Experience

[[toggle How do I develop locally?]]

```bash
muxi dev
```

Runs your formation locally with hot reload. Change your `.afs` file, see changes immediately.

**Learn more:** [Quickstart](quickstart.md) | [CLI Cheatsheet](cli/cheatsheet.md)

[[/toggle]]

[[toggle Are there SDKs?]]

Yes - **Python, TypeScript, and Go**:

```python
from muxi import Muxi
client = Muxi()
response = client.chat("Hello!")
```

**Learn more:** [Python SDK](sdks/python-sdk.md) | [TypeScript SDK](sdks/typescript-sdk.md) | [Go SDK](sdks/go-sdk.md)

[[/toggle]]

[[toggle Can I embed MUXI in my own app?]]

Yes. The **embeddable runtime** lets you run MUXI inside your application - no separate server process.

**Learn more:** [Embed in Your App](runtime/embed-in-your-app.md)

[[/toggle]]

---

## Registry & Sharing

[[toggle Can I use pre-built agents?]]

Yes. Pull formations from the **MUXI Registry**:

```bash
muxi pull @acme/customer-support
muxi pull @acme/code-reviewer
```

**Learn more:** [Registry](concepts/registry.md) | [Examples](examples/README.md)

[[/toggle]]

[[toggle Can I share my formations?]]

Yes. Publish to the registry:

```bash
muxi publish
```

Supports semantic versioning, private formations, and organization sharing.

**Learn more:** [Publish Formations](registry/publish-formations.md) | [Versioning](registry/versioning.md)

[[/toggle]]

---

## Business & Licensing

[[toggle Is MUXI really free?]]

Yes. **The core stack is always free and self-hostable.** No paywalls, no feature restrictions, no bait-and-switch licensing.

- Server, Runtime, CLI, SDKs - all open source
- Self-host on your infrastructure forever
- No usage limits, no "free tier"

**Learn more:** [Our creed](https://muxi.org/creed)

[[/toggle]]

[[toggle How do you make money?]]

MUXI follows the standard open-source infrastructure model:

1. **GitHub Sponsors** - Supporting OSS development
2. **Professional services** - Priority support and deployment help for production teams

The core product stays free. Revenue comes from optional services, not from restricting features.

[>] [Get Support](https://muxi.org/support)

[[/toggle]]

[[toggle What's the licensing model?]]

| Component | License | What it means |
|-----------|---------|---------------|
| Server & Runtime | Elastic License 2.0 | Free to use, can't resell as a hosted service |
| CLI & SDKs | Apache 2.0 | Fully permissive, do anything |
| Formations | Apache 2.0 | Your agents are yours |

**You CAN:**
- Use freely for internal projects, products, research
- Use freely for building platforms and commercial applications
- Build and sell products that use MUXI
- Deploy for clients and customers
- Self-host on your infrastructure

**You CAN'T:**
- Offer MUXI itself as a hosted service (that's it)

[[/toggle]]

[[toggle What if you disappear tomorrow?]]

You're protected:

- **Self-hosted** - Runs on your infrastructure, not ours
- **Open source** - Fork and maintain the code yourself
- **Portable formats** - Formations are YAML, not proprietary
- **Standard protocols** - MCP and A2A work with other systems

MUXI is backed by VarOps LLC with a long-term commitment. Revenue comes from sustainable services, not VC burn rate.

[[/toggle]]

[[toggle How is this different from LangChain/CrewAI?]]

Different layer of the stack:

| | LangChain/CrewAI | MUXI |
|---|---|---|
| **What** | Python frameworks | Production server |
| **How** | Write code | Declare in YAML |
| **Deploy** | You figure it out | `muxi deploy` |
| **Memory** | BYO | Built-in (3-tier) |
| **Multi-tenant** | BYO | Built-in |
| **Observability** | BYO | Built-in |

LangChain helps you build agents in code. MUXI runs agents in production.

[[/toggle]]

---

## Next Steps

[+] [Quickstart](quickstart.md) - Build your first agent in 5 minutes
[+] [Installation](installation/README.md) - Get MUXI running on your machine
[+] [Architecture](concepts/architecture.md) - Understand how it all fits together
[+] [Examples](examples/README.md) - See real formations in action
