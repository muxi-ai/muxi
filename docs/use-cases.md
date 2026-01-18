---
title: Common Use Cases
description: How teams use MUXI in production
searchable: true
---

# Use Cases

## Who uses MUXI and what do they build?

MUXI is production infrastructure. It's built for teams that need to run AI agents reliably, not just prototype them.


## Platform Builders

**Building AI-powered products for customers.**

You're building a SaaS product with AI agents at the core. Your customers expect reliability, and you need infrastructure that scales without babysitting.

### Common patterns

[[toggle Customer Support Platforms]]

Multi-tenant support agents that handle inquiries, route tickets, and escalate to humans when needed.

**What you get:**
- Each customer gets isolated agents with their own knowledge base
- Per-tenant memory (conversations don't leak between customers)
- Webhooks to integrate with existing ticketing systems
- Observability to track resolution rates and agent performance

**Example:** A SaaS company embeds MUXI-powered agents into their product. Each of their customers gets a white-labeled support agent trained on their specific docs.

[[/toggle]]

[[toggle AI Writing & Content Tools]]

Agents that help users draft, edit, and research content with context from their documents.

**What you get:**
- Knowledge ingestion from user-uploaded docs
- Multi-agent workflows (researcher → writer → editor)
- Streaming responses for real-time typing feel
- User credential storage for integrations (Google Docs, Notion, etc.)

**Example:** A content platform lets users upload their brand guidelines. Agents write drafts that match the user's voice and style.

[[/toggle]]

[[toggle Vertical AI Agents]]

Industry-specific agents for legal, healthcare, finance, real estate, etc.

**What you get:**
- Domain knowledge from specialized documents
- Compliance-friendly (self-hosted, data stays on your infra)
- SOPs to enforce industry-specific workflows
- Human-in-the-loop for high-stakes decisions

**Example:** A legal tech startup runs contract review agents. Each law firm client gets isolated agents trained on their precedent library.

[[/toggle]]

[[toggle AI Development Tools]]

Code assistants, documentation bots, PR reviewers for developer products.

**What you get:**
- Knowledge from codebases and docs
- Tool access (GitHub, Jira, Slack, databases)
- Multi-agent teams (code reviewer + security scanner + docs writer)
- API-first for IDE and CLI integrations

**Example:** A DevTools company offers an AI assistant that understands their SDK. Agents answer questions, generate code samples, and debug issues.

[[/toggle]]


## Internal Tools Teams

**Building agents for your own organization.**

You need AI assistants for internal teams but can't send company data to third-party services. Self-hosted, behind the firewall, integrated with your systems.

### Common patterns

[[toggle Internal Knowledge Assistants]]

Agents that answer questions from company wikis, policies, and documentation.

**What you get:**
- Knowledge ingestion from Confluence, Notion, SharePoint, or local files
- Answers grounded in your actual docs (not hallucinations)
- Access controls (different agents for different teams)
- Incremental indexing (docs update automatically)

**Example:** A 500-person company deploys an agent that answers HR policy questions, onboarding procedures, and IT help desk queries.

[[/toggle]]

[[toggle Workflow Automation Agents]]

Agents that execute multi-step processes across internal systems.

**What you get:**
- Tool integrations (Slack, Jira, Salesforce, internal APIs)
- SOPs to define step-by-step procedures
- Triggers to kick off workflows from events
- Approval gates for sensitive actions

**Example:** An ops team builds an agent that handles employee offboarding - disables accounts, revokes access, updates HR systems, notifies managers.

[[/toggle]]

[[toggle Data & Analytics Assistants]]

Agents that query databases and generate reports in natural language.

**What you get:**
- Database tools (PostgreSQL, MySQL, BigQuery, etc.)
- Natural language to SQL
- Scheduled reports via triggers
- Charts and artifacts in responses

**Example:** A sales team asks "What were our top 10 deals last quarter?" and gets a formatted report without touching a dashboard.

[[/toggle]]

[[toggle Customer-Facing Support (Self-Hosted)]]

Run your own support agents without sending customer data to third parties.

**What you get:**
- Complete data control (runs on your infrastructure)
- Integration with existing CRM/ticketing
- Custom knowledge from your support docs
- Escalation workflows to human agents

**Example:** A fintech company needs AI support but can't use cloud AI services due to compliance. They self-host MUXI behind their firewall.

[[/toggle]]


## Why MUXI for these use cases?

| Need | MUXI provides |
|------|---------------|
| Multi-tenancy | Built-in isolation per user/customer |
| Self-hosted | Runs on your infrastructure |
| Reliability | Circuit breakers, retries, observability |
| Integration | 1,000+ MCP tools, webhooks, triggers |
| Compliance | Data never leaves your control |
| Scale | One server, many agents, many users |


## Not sure if MUXI fits?

[+] [Talk to us](support.md) - We'll help you figure it out
[+] [Examples](examples/README.md) - See real formations
[+] [How Do I...?](faq.md) - Common questions
[+] [Quickstart](quickstart.md) - Try it in 5 minutes
