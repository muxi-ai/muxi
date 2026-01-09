# Cross-Linking Strategy

**Purpose:** Improve documentation navigation and discoverability through strategic API endpoint links and cross-references.

**Date:** 2026-01-09

---

## Part 1: API Endpoint Links

Link to specific API endpoints when examples or discussions reference those operations.

**API URL Format:** `docs/api/formation#tag/{tag}/{METHOD}/{path}` or `docs/api/server#tag/{tag}/{METHOD}/{path}`

---

### 1. Chat Endpoint Links

**Endpoint:** `docs/api/formation#tag/Chat/POST/chat`

#### Add links in:

**📄 guides/custom-ui.md** (Line ~12, ~52)
- **Where:** In the API Endpoints table
- **Change:** `/v1/chat` → Add link text: `[See full endpoint documentation →](docs/api/formation#tag/Chat/POST/chat)`
- **Why:** Developers building UIs need to understand all chat parameters (streaming, webhooks, files, etc.)

**📄 deep-dives/streaming.md** (Line ~45)
- **Where:** In the curl example section
- **Change:** Add after curl example: `[View complete /v1/chat endpoint documentation →](docs/api/formation#tag/Chat/POST/chat)`
- **Why:** Streaming is one mode of the chat endpoint; users should see all options

**📄 quickstart.md** (Line ~60)
- **Where:** After the curl test command
- **Change:** Add: `> [!TIP]\n> See the [complete chat API reference](docs/api/formation#tag/Chat/POST/chat) for streaming, async, and advanced options.`
- **Why:** Gets beginners aware of advanced features early

**📄 sdks/python.md** (where chat examples appear)
- **Where:** After the `client.chat()` example
- **Change:** Add: `[API Reference: POST /v1/chat →](docs/api/formation#tag/Chat/POST/chat)`

**📄 sdks/typescript.md** (where chat examples appear)
- **Where:** After the `client.chat()` example
- **Change:** Add: `[API Reference: POST /v1/chat →](docs/api/formation#tag/Chat/POST/chat)`

---

### 2. Agents Endpoint Links

**Endpoints:** 
- List: `docs/api/formation#tag/Agents/GET/agents`
- Get: `docs/api/formation#tag/Agents/GET/agents/{agent_id}`

#### Add links in:

**📄 reference/agents.md** (Top of page)
- **Where:** After the intro paragraph
- **Change:** Add callout:
  ```
  > [!NOTE]
  > **API Reference:** [GET /v1/agents](docs/api/formation#tag/Agents/GET/agents) | [GET /v1/agents/{id}](docs/api/formation#tag/Agents/GET/agents/{agent_id})
  ```
- **Why:** Reference docs should link to API specs

**📄 guides/custom-ui.md** (Line ~12)
- **Where:** In the API Endpoints table for `/v1/agents`
- **Change:** Add link: `[GET /v1/agents](docs/api/formation#tag/Agents/GET/agents)`

---

### 3. Secrets Endpoint Links

**Endpoints:**
- List: `docs/api/formation#tag/Secrets/GET/secrets`
- Create: `docs/api/formation#tag/Secrets/POST/secrets`
- Update: `docs/api/formation#tag/Secrets/PUT/secrets/{key}`
- Delete: `docs/api/formation#tag/Secrets/DELETE/secrets/{key}`

#### Add links in:

**📄 reference/secrets.md** (Top of page)
- **Where:** After intro
- **Change:** Add:
  ```
  > [!NOTE]
  > **API Reference:** 
  > - [GET /v1/secrets](docs/api/formation#tag/Secrets/GET/secrets) - List secrets
  > - [POST /v1/secrets](docs/api/formation#tag/Secrets/POST/secrets) - Create secret
  > - [PUT /v1/secrets/{key}](docs/api/formation#tag/Secrets/PUT/secrets/{key}) - Update secret
  > - [DELETE /v1/secrets/{key}](docs/api/formation#tag/Secrets/DELETE/secrets/{key}) - Delete secret
  ```

**📄 cli/secrets.md** (Throughout)
- **Where:** In command descriptions (setup, set, delete)
- **Change:** Add "See also: [Secrets API Reference](docs/api/formation#tag/Secrets)" at bottom

---

### 4. Sessions Endpoint Links

**Endpoints:**
- List: `docs/api/formation#tag/Sessions/GET/sessions`
- Get: `docs/api/formation#tag/Sessions/GET/sessions/{session_id}`
- Delete: `docs/api/formation#tag/Sessions/DELETE/sessions/{session_id}`

#### Add links in:

**📄 reference/session-restore.md** (Top of page)
- **Where:** After intro
- **Change:** Add API reference links

**📄 guides/custom-ui.md** (Line ~12)
- **Where:** In API endpoints table for `/v1/sessions`
- **Change:** Add link: `[GET /v1/sessions](docs/api/formation#tag/Sessions/GET/sessions)`

---

### 5. Memory Endpoint Links

**Endpoints:**
- Get config: `docs/api/formation#tag/Memory/GET/memory`
- Clear buffer: `docs/api/formation#tag/Memory/DELETE/memory/buffer`

#### Add links in:

**📄 reference/memory.md** (Top of page)
- **Where:** After intro
- **Change:** Add API reference callout

**📄 guides/add-memory.md** (Bottom)
- **Where:** In "Next Steps" or "Learn More"
- **Change:** Add: `- [Memory API Reference](docs/api/formation#tag/Memory)`

---

### 6. Triggers Endpoint Links

**Endpoints:**
- List: `docs/api/formation#tag/Triggers/GET/triggers`
- Get: `docs/api/formation#tag/Triggers/GET/triggers/{trigger_id}`
- Invoke: `docs/api/formation#tag/Triggers/POST/triggers/{trigger_id}/invoke`

#### Add links in:

**📄 reference/triggers.md** (Top of page)
- **Where:** After intro
- **Change:** Add API links

**📄 guides/triggers.md** (Where showing webhook URL)
- **Where:** When explaining how to call triggers
- **Change:** Add: `[See the Triggers API reference](docs/api/formation#tag/Triggers) for authentication and payload options.`

---

### 7. Formation Deployment Links (Server API)

**Endpoints:**
- Deploy: `docs/api/server#tag/Formations/POST/rpc/formations`
- List: `docs/api/server#tag/Formations/GET/rpc/formations`
- Get: `docs/api/server#tag/Formations/GET/rpc/formations/{formation_id}`
- Update: `docs/api/server#tag/Formations/PUT/rpc/formations/{formation_id}`
- Delete: `docs/api/server#tag/Formations/DELETE/rpc/formations/{formation_id}`
- Stop: `docs/api/server#tag/Formations/POST/rpc/formations/{formation_id}/stop`
- Start: `docs/api/server#tag/Formations/POST/rpc/formations/{formation_id}/start`
- Restart: `docs/api/server#tag/Formations/POST/rpc/formations/{formation_id}/restart`
- Rollback: `docs/api/server#tag/Formations/POST/rpc/formations/{formation_id}/rollback`

#### Add links in:

**📄 server/formations.md** (Throughout)
- **Line ~7:** After "Deploy a formation" → Add: `[Server API: POST /rpc/formations](docs/api/server#tag/Formations/POST/rpc/formations)`
- **Line ~50:** After "List formations" → Add: `[Server API: GET /rpc/formations](docs/api/server#tag/Formations/GET/rpc/formations)`
- **Line ~65:** After "Stop formation" → Add: `[Server API: POST /rpc/formations/{id}/stop](docs/api/server#tag/Formations/POST/rpc/formations/{formation_id}/stop)`
- **Line ~95:** After "Rollback" heading → Add link to rollback endpoint

**📄 cli/deploy.md**
- **Where:** After deploy examples
- **Change:** Add: `> [!NOTE]\n> The CLI uses the [POST /rpc/formations endpoint](docs/api/server#tag/Formations/POST/rpc/formations) under the hood.`

**📄 cli/formation.md**
- **Where:** Throughout command descriptions
- **Change:** Add "API: [endpoint]" for each command that maps to an API operation

**📄 guides/deploy.md**
- **Where:** When discussing deployment
- **Change:** Link to Server API deployment endpoint

---

### 8. A2A (Agent-to-Agent) Links

**Endpoints:**
- Get config: `docs/api/formation#tag/A2A/GET/a2a`
- Get outbound: `docs/api/formation#tag/A2A/GET/a2a/outbound`

#### Add links in:

**📄 concepts/agents.md** (When discussing A2A)
- **Where:** In the A2A communication section
- **Change:** Add: `[Configure via the A2A API](docs/api/formation#tag/A2A)`

**📄 guides/multi-agent.md**
- **Where:** When explaining A2A setup
- **Change:** Link to A2A endpoints

---

### 9. Overlord Endpoint Links

**Endpoints:**
- Get config: `docs/api/formation#tag/Overlord/GET/overlord`
- Get persona: `docs/api/formation#tag/Overlord/GET/overlord/persona`

#### Add links in:

**📄 reference/persona.md** (Top of page)
- **Where:** After intro
- **Change:** Add: `> [!NOTE]\n> **API Reference:** [GET /v1/overlord/persona](docs/api/formation#tag/Overlord/GET/overlord/persona)`

**📄 concepts/overlord.md**
- **Where:** When discussing configuration
- **Change:** Add API reference link

---

### 10. Configuration Endpoints

**Endpoints:**
- Status: `docs/api/formation#tag/Configuration/GET/status`
- Full config: `docs/api/formation#tag/Configuration/GET/config`
- Formation info: `docs/api/formation#tag/Configuration/GET/formation`

#### Add links in:

**📄 server/monitoring.md**
- **Where:** When discussing health checks
- **Change:** Add link to status endpoint

**📄 guides/monitoring.md**
- **Where:** In observability section
- **Change:** Link to config/status endpoints

---

## Part 2: Cross-Reference Links

Strategic links between related documentation pages.

---

### Concept → Reference Links

Connect high-level concepts to detailed reference docs.

#### Add in Concept Pages:

**📄 concepts/agents.md**
- **Add at bottom:**
  ```markdown
  ## Learn More
  - [Agent Reference →](../reference/agents.md) - Complete agent configuration
  - [Multi-Agent Guide →](../guides/multi-agent.md) - Building teams
  - [Example: Multi-Agent Team →](../examples/05-multi-agent-team/)
  ```

**📄 concepts/memory.md**
- **Add at bottom:**
  ```markdown
  ## Learn More
  - [Memory Reference →](../reference/memory.md) - Configuration options
  - [Add Memory Guide →](../guides/add-memory.md) - Step-by-step setup
  - [Deep Dive: Memory Systems →](../deep-dives/memory.md)
  ```

**📄 concepts/tools.md**
- **Add at bottom:**
  ```markdown
  ## Learn More
  - [Tools Reference →](../reference/tools.md) - MCP configuration
  - [Add Tools Guide →](../guides/add-tools.md) - Integration tutorial
  ```

**📄 concepts/triggers.md**
- **Add at bottom:**
  ```markdown
  ## Learn More
  - [Triggers Reference →](../reference/triggers.md) - Complete syntax
  - [Triggers Guide →](../guides/triggers.md) - Setup webhooks
  ```

**📄 concepts/secrets.md**
- **Add at bottom:**
  ```markdown
  ## Learn More
  - [Secrets Reference →](../reference/secrets.md) - Configuration options
  - [CLI: muxi secrets →](../cli/secrets.md) - Command reference
  ```

**📄 concepts/workflows.md**
- **Add at bottom:**
  ```markdown
  ## Learn More
  - [Workflows Reference →](../reference/workflows.md) - All options
  - [Approvals Reference →](../reference/approvals.md) - Plan approval
  ```

**📄 concepts/persona.md**
- **Add at bottom:**
  ```markdown
  ## Learn More
  - [Persona Reference →](../reference/persona.md) - Configuration details
  - [Overlord Concept →](./overlord.md) - How the overlord works
  ```

**📄 concepts/sops.md**
- **Add at bottom:**
  ```markdown
  ## Learn More
  - [SOPs Reference →](../reference/sops.md) - Complete syntax
  - [SOPs Guide →](../guides/sops.md) - Writing procedures
  ```

**📄 concepts/async.md**
- **Add at bottom:**
  ```markdown
  ## Learn More
  - [Deep Dive: Async Operations →](../deep-dives/async.md)
  - [Triggers Guide →](../guides/triggers.md) - Async webhooks
  ```

---

### Reference → Concept Links

Connect reference docs back to conceptual explanations.

#### Add in Reference Pages:

**📄 reference/agents.md** (Top of page)
- **Add after intro:**
  ```markdown
  > [!TIP]
  > New to agents? Read [Agent Concepts →](../concepts/agents.md) first for an overview.
  ```

**📄 reference/memory.md** (Top of page)
- **Add after intro:**
  ```markdown
  > [!TIP]
  > New to memory? Read [Memory Concepts →](../concepts/memory.md) first.
  ```

**📄 reference/workflows.md** (Top of page)
- **Add after intro:**
  ```markdown
  > [!TIP]
  > Understand workflows: [Workflows Concept →](../concepts/workflows.md)
  ```

**📄 reference/triggers.md** (Top of page)
- **Add after intro:**
  ```markdown
  > [!TIP]
  > New to triggers? Read [Triggers Concept →](../concepts/triggers.md) first.
  ```

---

### Guide → Reference Links

Connect tutorials to detailed configuration docs.

#### Add in Guide Pages:

**📄 guides/add-tools.md**
- **Add "Learn More" section at bottom:**
  ```markdown
  ## Learn More
  - [Tools Reference →](../reference/tools.md) - All MCP options
  - [Tools Concept →](../concepts/tools.md) - How MCP works
  - [MCP Servers Registry →](https://registry.muxi.org/mcps)
  ```

**📄 guides/add-memory.md**
- **Add at bottom:**
  ```markdown
  ## Learn More
  - [Memory Reference →](../reference/memory.md) - Configuration options
  - [Memory Concept →](../concepts/memory.md) - Architecture overview
  - [Deep Dive: Memory Systems →](../deep-dives/memory.md)
  ```

**📄 guides/triggers.md**
- **Add at bottom:**
  ```markdown
  ## Learn More
  - [Triggers Reference →](../reference/triggers.md) - Complete syntax
  - [Triggers Concept →](../concepts/triggers.md) - How they work
  ```

**📄 guides/sops.md**
- **Add at bottom:**
  ```markdown
  ## Learn More
  - [SOPs Reference →](../reference/sops.md) - Complete syntax
  - [SOPs Concept →](../concepts/sops.md) - When to use SOPs
  ```

**📄 guides/multi-agent.md**
- **Add at bottom:**
  ```markdown
  ## Learn More
  - [Agents Reference →](../reference/agents.md) - Configuration
  - [Agents Concept →](../concepts/agents.md) - Architecture
  - [Deep Dive: Orchestration →](../deep-dives/orchestration.md)
  - [Example: Multi-Agent Team →](../examples/05-multi-agent-team/)
  ```

**📄 guides/deploy.md**
- **Add at bottom:**
  ```markdown
  ## Learn More
  - [Server: Managing Formations →](../server/formations.md) - All operations
  - [CLI: muxi deploy →](../cli/deploy.md) - Command reference
  - [Production Guide →](../server/production.md) - Best practices
  ```

**📄 guides/custom-ui.md**
- **Add at bottom:**
  ```markdown
  ## Learn More
  - [Python SDK →](../sdks/python.md) - Full Python reference
  - [TypeScript SDK →](../sdks/typescript.md) - Full TypeScript reference
  - [API Reference →](../api-reference.md) - Direct API access
  - [Deep Dive: Streaming →](../deep-dives/streaming.md)
  ```

---

### Deep Dive → Reference Links

Connect deep technical docs to configuration references.

**📄 deep-dives/streaming.md**
- **Add at bottom:**
  ```markdown
  ## Learn More
  - [Response Formats Deep Dive →](./response-formats.md)
  - [Custom UI Guide →](../guides/custom-ui.md) - Building with streaming
  ```

**📄 deep-dives/async.md**
- **Add at bottom:**
  ```markdown
  ## Learn More
  - [Async Concept →](../concepts/async.md) - Configuration
  - [Triggers Guide →](../guides/triggers.md) - Webhook integration
  ```

**📄 deep-dives/orchestration.md**
- **Add at bottom:**
  ```markdown
  ## Learn More
  - [Agents Concept →](../concepts/agents.md)
  - [Workflows Reference →](../reference/workflows.md)
  - [Multi-Agent Guide →](../guides/multi-agent.md)
  ```

**📄 deep-dives/memory.md**
- **Add at bottom:**
  ```markdown
  ## Learn More
  - [Memory Reference →](../reference/memory.md) - Configuration
  - [Memory Concept →](../concepts/memory.md) - Overview
  - [Add Memory Guide →](../guides/add-memory.md)
  ```

---

### CLI → Reference Links

Connect CLI commands to what they configure.

**📄 cli/new.md**
- **Where:** After each command (new formation, new agent, new mcp)
- **Change:** Add "Configures: [Reference doc link]"
- Example for `muxi new agent`:
  ```markdown
  This generates an agent file. See [Agent Reference →](../reference/agents.md) for all configuration options.
  ```

**📄 cli/deploy.md**
- **Add at bottom:**
  ```markdown
  ## Learn More
  - [Server: Managing Formations →](../server/formations.md)
  - [Production Guide →](../server/production.md)
  - [Formation Schema →](../reference/schema.md)
  ```

**📄 cli/secrets.md**
- **Add at bottom:**
  ```markdown
  ## Learn More
  - [Secrets Reference →](../reference/secrets.md) - How secrets work
  - [Secrets Concept →](../concepts/secrets.md) - Why encrypted
  ```

---

### SDK → API Links

Connect SDK examples to underlying API endpoints.

**📄 sdks/python.md**
- **In each major section (Chat, Agents, Sessions, etc.):**
- **Change:** Add API reference link showing which endpoint the SDK uses
- Example for `client.chat()`:
  ```python
  response = client.chat("Hello!")
  ```
  Add after: `Uses: [POST /v1/chat](../api-reference.md#chat-endpoint)`

**📄 sdks/typescript.md**
- **Same as Python SDK**

**📄 sdks/go.md**
- **Same as Python SDK**

---

### Example → Reference Links

Connect examples to relevant config docs.

**📄 examples/01-simple-chatbot/README.md**
- **Add at bottom:**
  ```markdown
  ## Learn More
  - [Agent Reference →](../../reference/agents.md)
  - [LLM Configuration →](../../reference/schema.md#llm)
  ```

**📄 examples/02-customer-support/README.md**
- **Add at bottom:**
  ```markdown
  ## Learn More
  - [Knowledge Systems →](../../reference/knowledge.md)
  - [Multi-Agent Guide →](../../guides/multi-agent.md)
  ```

**📄 examples/03-research-assistant/README.md**
- **Add at bottom:**
  ```markdown
  ## Learn More
  - [Tools Reference →](../../reference/tools.md) - MCP integration
  - [Memory Reference →](../../reference/memory.md)
  ```

**📄 examples/04-code-reviewer/README.md**
- **Add at bottom:**
  ```markdown
  ## Learn More
  - [SOPs Reference →](../../reference/sops.md)
  - [Triggers Reference →](../../reference/triggers.md)
  ```

**📄 examples/05-multi-agent-team/README.md**
- **Add at bottom:**
  ```markdown
  ## Learn More
  - [Multi-Agent Guide →](../../guides/multi-agent.md)
  - [Workflows Reference →](../../reference/workflows.md)
  - [Deep Dive: Orchestration →](../../deep-dives/orchestration.md)
  ```

---

### Server → CLI Links

Connect server operations to CLI commands.

**📄 server/formations.md**
- **Where:** After each operation (deploy, stop, restart, etc.)
- **Change:** Add "CLI: `muxi [command]`" with link
- Example:
  ```markdown
  ## Deploy
  ...deployment info...
  
  **CLI:** [`muxi deploy`](../cli/deploy.md)
  ```

**📄 server/monitoring.md**
- **Add links to:**
  - `muxi logs` command
  - `muxi formation get` command

---

### Installation → Quickstart Links

Connect installation success to next steps.

**📄 installation/macos.md** (Bottom)
- **Add:**
  ```markdown
  ## Next Steps
  - [Quickstart →](../quickstart.md) - Build your first formation in 5 minutes
  - [CLI Commands →](../cli/README.md) - Learn the commands
  ```

**📄 installation/linux.md** (Bottom)
- **Same as macOS**

**📄 installation/windows.md** (Bottom)
- **Same as macOS**

**📄 installation/docker.md** (Bottom)
- **Same as macOS**

---

### Quickstart → Deeper Learning Links

Already good, but ensure completeness.

**📄 quickstart.md** (Bottom "Next Steps" section)
- **Verify it includes:**
  - Formation concepts
  - Reference docs
  - Guides
  - Examples

---

### Registry → CLI Links

**📄 registry/publishing.md**
- **Add at bottom:**
  ```markdown
  ## Learn More
  - [CLI: muxi push →](../cli/registry.md#push) - Command reference
  - [Versioning →](./versioning.md) - Version scheme
  ```

**📄 registry/searching.md**
- **Add at bottom:**
  ```markdown
  ## Learn More
  - [CLI: muxi search →](../cli/registry.md#search) - Command reference
  - [CLI: muxi pull →](../cli/registry.md#pull) - Download formations
  ```

---

## Part 3: Bidirectional Links

Ensure these important relationships go both ways:

### 1. Concept ↔ Reference
- ✅ Concepts link to detailed references
- ✅ References link back to concepts (for beginners)

### 2. Guide ↔ Reference
- ✅ Guides link to config details
- ✅ References link to tutorials

### 3. Example ↔ Reference
- ✅ Examples link to what they demonstrate
- Examples page should list all examples with their focus

### 4. CLI ↔ Server
- ✅ CLI commands link to server operations
- ✅ Server docs mention CLI equivalents

### 5. SDK ↔ API
- ✅ SDKs link to underlying API endpoints
- API reference should mention SDK availability

---

## Implementation Priority

### Phase 1: High-Impact API Links (Do First)
1. Chat endpoint links (most used)
2. Deployment endpoint links (server operations)
3. Agents/Secrets endpoint links (configuration)

### Phase 2: Concept ↔ Reference Links
4. Add "Learn More" to all concept pages
5. Add "New to X?" callouts to reference pages

### Phase 3: Guide Interconnections
6. Add "Learn More" to all guides
7. Ensure deep-dives link back to guides

### Phase 4: Polish
8. SDK → API links
9. Example → Reference links
10. Bidirectional verification

---

## Linking Guidelines

### When to Link
✅ **Do link when:**
- Examples reference a feature (link to reference/concept)
- Discussing an API operation (link to endpoint)
- Mentioning a prerequisite (link to setup)
- Referencing related functionality

❌ **Don't link when:**
- Link would be to the current page
- Concept is mentioned only in passing
- Link would clutter more than clarify

### Link Format
```markdown
[Link text →](../path/to/doc.md)           # Internal doc
[API: GET /v1/agents →](docs/api/...)     # API endpoint
```

### Callout Format for API Links
```markdown
> [!NOTE]
> **API Reference:** [GET /v1/endpoint](docs/api/...)
```

---

## Success Metrics

After implementation:
- Every reference page links to related concepts
- Every concept page links to detailed references
- Every guide links to configuration docs
- Every API example links to full endpoint docs
- Reduced "how do I..." questions
- Improved docs time-on-site (users find what they need)

---

**Next Step:** Implement Phase 1 (High-Impact API Links) first for maximum value.
