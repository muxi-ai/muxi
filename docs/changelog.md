---
title: Changelog
description: Release history and updates for MUXI
---

# Changelog

## Notable changes across all MUXI components

> [!TIP]
> For detailed release notes, see individual component repositories:
>
> - [Server Releases](https://github.com/muxi-ai/server/releases)
> - [Runtime Releases](https://github.com/muxi-ai/runtime/releases)
> - [CLI Releases](https://github.com/muxi-ai/cli/releases)
> - [SDKs Releases](https://github.com/muxi-ai/sdks/releases)
> - [OneLLM Releases](https://github.com/muxi-ai/onellm/releases)
> - [FAISSx Releases](https://github.com/muxi-ai/faissx/releases)

## April 2026

### Runtime v0.20260401.0

#### Skill secrets

Skills can now use `${{ secrets.X }}` directly in their `SKILL.md` instructions — the same syntax used everywhere else in MUXI. The runtime scans skill files at load time, resolves secrets at activation, and injects them into the agent's context.

For bundled scripts, secrets are passed as environment variables to the RCE subprocess (`${{ secrets.NOTION_KEY }}` → `NOTION_KEY` env var). They are never written to disk or cached by the RCE service.

> **Note:** The HTTP channel between the runtime and skills-rce carries plaintext secret values. Skills RCE must not be exposed publicly — it is an internal service intended for trusted network boundaries only.

## March 2026

### Runtime v0.20260330.1

#### MCP connection keep-alive

MCP tool calls no longer reconnect and disconnect for every invocation. After the first tool call, the connection is kept alive and reused for subsequent calls. Each call resets an idle timer; a background reaper closes connections that exceed the configured TTL (default: 5 minutes). Frequently-used servers stay connected indefinitely; idle servers close automatically.

- **Configurable TTL**: Set `connection_ttl` globally under `mcp:` or per-server in individual MCP server configs. Use `connection_ttl: 0` to restore the previous connect-per-call behavior.
- **User isolation**: Connections are keyed by server ID and credential hash, so different users never share a connection.

### Runtime v0.20260330.0

#### Response latency fixes

- **Workflow synthesis no longer times out on large results**: When SOPs fetched large amounts of data (calendar events, emails, tasks), the synthesis step timed out after 30 seconds, causing 120-175 second silent gaps while retries ran. The synthesis timeout is now 120 seconds.
- **User identifier resolution cached**: A database lookup that ran up to 8 times per request for the same immutable mapping is now cached for the formation's lifetime.
- **PDF processing works in SIF containers**: `libpoppler.so` was not discoverable inside Apptainer containers because the container's `LD_LIBRARY_PATH` excluded system library paths. The entrypoint now appends standard system library paths when running in SIF mode.

### Runtime v0.20260329.0

#### MCP HTTP transport CPU fix

Fixed a bug where MCP servers using `type: http` caused 90%+ idle CPU after the first tool call. The root cause is an upstream SDK bug ([python-sdk#1805](https://github.com/modelcontextprotocol/python-sdk/issues/1805)) where memory object streams with zero-buffer capacity create an infinite busy-loop during context teardown. The runtime now closes transport streams before SDK context exit, preventing the spin. This workaround will become a harmless no-op once the upstream fix ships.

### Runtime v0.20260326.3

#### Generative UI skill

MUXI now ships with a built-in `generative-ui` skill for requests that are better shown than described. Agents can use it to create self-contained interactive HTML widgets, dashboards, diagrams, and visual explainers through the existing file-generation flow.

- **Interactive HTML artifacts**: Steers agents toward single-file `.html` outputs with inline CSS/JS, responsive layouts, and dark-mode-friendly presentation.
- **Covered by tests**: Added unit coverage for skill loading and activation, plus an end-to-end test that verifies RCE-backed HTML widget generation.

### Runtime v0.20260326.2

#### XML tool parameter extraction

Improved tool-call parameter extraction so agents can recover parameters from more XML-shaped model outputs, not just the wrapped JSON form. This makes tool execution more reliable when the model emits individual parameter tags.

- **Broader XML support**: The runtime now extracts values from individual XML parameter tags and assembles them into tool arguments automatically.
- **Fewer dropped tool calls**: This reduces cases where a tool call was planned correctly but skipped because argument parsing failed.

### Runtime v0.20260326.1

#### MCP error handling & conversation-aware routing

- **MCP error flag now propagated correctly**: When an MCP tool returned an error (e.g., "Item not found" from Microsoft Graph), the system incorrectly reported it as a success. Error responses are now detected and surfaced properly.
- **Follow-up messages stay with the right agent**: Short follow-up messages like "change it to normal" were routed to the wrong agent because the router had no memory of the previous conversation. The system now tracks which agent handled the last message per session and biases follow-ups toward the same agent.

### Runtime v0.20260325.1

#### MCP tool chaining reliability

Fixed a bug where multi-step MCP tool chains silently failed. When an agent needed to call multiple tools in sequence (e.g., list tasks then update a specific task), the second tool call could be skipped without error if the LLM returned a non-standard response format during parameter inference. The agent would report success despite never executing the action.

- **Parameter inference resilience**: The inference step now extracts JSON parameters from non-standard LLM responses (XML tool-call wrappers, embedded JSON in prose) and retries on failure.
- **Smarter planning**: Agents now automatically add prerequisite lookup steps when a tool requires an ID they don't have (e.g., fetching the task list before updating a task).

### Runtime v0.20260325.0

#### SOP synthesis control

SOPs can now declare `synthesis: false` in their frontmatter to bypass the Overlord's response synthesis step. When set, the last completed task's raw output is returned as-is -- useful for SOPs that specify strict output formats like JSON, CSV, or structured templates. Default remains `true` for backward compatibility.

#### Parallel SOP execution

Independent SOP steps now execute concurrently in guide mode. The workflow engine detects fan-in patterns (e.g., three parallel data-fetching steps feeding one synthesis step) and runs them simultaneously instead of forcing sequential execution. Linear chains (A->B->C) still execute in order.

#### Scheduler chat integration

- **Job creation no longer times out**: Fixed a missing streaming event that caused chat-created scheduled jobs to hang indefinitely despite being written to the database.
- **Scheduler intent routing**: Scheduler-related requests are now detected before SOP matching, preventing unrelated SOPs from hijacking scheduler intents.
- **List jobs via chat**: Users can now ask "show my scheduled jobs" or "list my reminders" directly in chat. Detected via LLM analysis with keyword heuristic fallback.
- **Multi-day scheduling**: "every Tuesday and Thursday at 3pm" now correctly produces `0 15 * * 2,4` instead of only capturing the first day.

#### Streaming reliability

- **Broken pipe recovery**: When a client disconnects mid-stream, the background processing task is now cancelled with a 5-second timeout. A 10-minute subscribe ceiling prevents zombie streams, and a stale request reaper force-fails requests stuck in processing.
- **Job title expansion**: Chat-created job titles now support up to 500 characters (previously truncated at 61).

#### MCP tool chaining

Fixed sequential MCP tool calls losing context between steps. When an agent's execution plan chains multiple tools (e.g., list task lists, then fetch tasks from a specific list), the parameter inference LLM now receives results from previous steps, allowing it to extract IDs and values needed for subsequent calls.

### Runtime v0.20260324.0

#### Scheduler API persistence & job lifecycle

Fixed critical data loss where all scheduler jobs vanished on restart -- route handlers fell back to in-memory dicts instead of calling `JobManager` for database persistence. All CRUD routes now use async `JobManager` methods with PostgreSQL via SQLAlchemy.

- **Jobs not persisted**: `create`, `list`, `get`, `delete` all used in-memory fallback. Rewired to `JobManager`.
- **pause/resume/delete missing user_id**: `SchedulerService` methods omitted required `user_id`, causing `TypeError`.
- **FK constraint on delete**: Audit records now deleted before the job to avoid `ForeignKeyViolation`.
- **Double-call bug**: `get_default_nanoid()()` raised `'str' object is not callable`. Fixed to single call.

#### New scheduler endpoints

- `PUT /v1/scheduler/jobs/{job_id}` -- Update job message, schedule, or title.
- `POST /v1/scheduler/jobs/{job_id}/pause` -- Pause an active job.
- `POST /v1/scheduler/jobs/{job_id}/resume` -- Resume a paused job.

### CLI v0.20260324.0

#### Chat stream timeout guard

Prevent the CLI chat TUI from hanging indefinitely when the server stops emitting SSE events. A 60-second inactivity timeout now surfaces a clear error instead of spinning the thinking indicator forever. (Addresses the "NLP scheduling hangs" report where certain natural-language patterns caused stalled streams.)

### Runtime v0.20260323.0

#### Memory recall fixes

Fixed three bugs in the overlord message processing pipeline that caused buffer memory recall failures:

- **Non-actionable path lost all context**: When a recall question (e.g., "what is my favorite turtle?") was classified as non-actionable, the persona model received zero memory context and could not answer. The non-actionable path now preserves relevant memories and conversation context.
- **Recall questions misclassified**: The LLM actionability check could classify memory recall questions as non-actionable. Messages with relevant long-term memories are now forced through the full agent pipeline.
- **Duplicate buffer storage**: Both the chat orchestrator and overlord independently stored messages in buffer memory, halving effective capacity. Removed duplicate; chat orchestrator is the sole owner.

#### Scheduler and Memobase fixes

- Scheduler worker now runs in a daemon thread instead of blocking the event loop during formation startup.
- Memobase collection passthrough, search filter deduplication, and parameter compatibility fixes.

### Server v0.20260323.0

#### Docker networking for host services

Added `--add-host localhost:host-gateway` and `--add-host host.docker.internal:host-gateway` to runtime-runner Docker commands so formations can reach host-local services (e.g., PostgreSQL) via `localhost`.

#### CDN release downloads

Switched release download URLs from `github.com` to `releases.muxi.org` for server and runtime binaries.

### CLI v0.20260323.0 + Installer v0.20260323.0 

#### CDN release downloads

CLI upgrade flow and installer's scripts (`install.sh`, `install.ps1`) now use CDN-based release download URLs (`releases.muxi.org`) instead of direct GitHub release URLs.

### CLI v0.20260314.0

#### `muxi server` renamed to `muxi remote`

All deployed formation management commands moved from `muxi server <action>` to `muxi remote <action>` to avoid confusion with `muxi-server` (the server daemon). `muxi server` will become a passthrough to `muxi-server` in a future release.

### Runtime v0.20260312.0

#### Formation init hook

Formations now support a top-level `init:` field that runs a shell command before any services are initialized. Use it for environment setup like creating directories, installing tools, or seeding data. The command runs with a 120-second timeout, uses the formation directory as working directory, and fails the formation on non-zero exit.

#### MCP path diagnostics

When a command-type MCP server fails with "Connection closed", the runtime now checks if any args look like filesystem paths that don't exist and prints a hint pointing to the `init` hook.

### Runtime v0.20260311.0

#### Agent Skills

Formations now support skills -- self-contained packages of instructions, references, and executable scripts that give agents deep expertise on demand. Skills follow the open [Agent Skills specification](https://agentskills.io/specification).

- **Discovery**: Skills in `skills/` directories are parsed at startup. Agents receive a lightweight catalog (~100 tokens per skill) in their system prompt.
- **Progressive disclosure**: Full skill content loads only when the agent activates it, keeping baseline context lean.
- **Script execution**: Skills with `scripts/` directories can execute code in sandboxed containers via the RCE service. Configure with `rce: { url, token }` in your formation.
- **Three-layer isolation**: Catalog filtering, tool restriction (`allowed-tools`), and planning prompt scoping ensure agents only see and activate authorized skills.
- **Built-in `file-generation` skill** for producing PDFs, images, spreadsheets, and charts.
- **REST API**: `GET /v1/skills`, `GET /v1/skills/{name}`, `GET /v1/agents/{agent_id}/skills`.

[>] [Skills documentation](concepts/skills.md)

#### MCP transport reliability

MCP streamable HTTP transport operations now have timeouts (`asyncio.wait_for`): 30s for connect, 10s for cleanup. Invalid auth tokens fail in under 1 second instead of hanging indefinitely.

#### Credential selection fixes

Fixed 7 bugs in multi-credential MCP flows covering sync state management, credential caching after clarification, cache-aware skip to prevent re-asking, and string/dict type handling.

#### Skills RCE Integration

- **Built-in code execution**: formations now ship with a managed RCE (Remote Code Execution) service for Skills
- **`muxi-server init`**: downloads RCE automatically (SIF on Linux, Docker image on macOS/Windows)
- **`muxi-server start`**: launches RCE as a managed process, injects `MUXI_RCE_URL` and `MUXI_RCE_TOKEN` into all formations
- **Auto port discovery**: if default port 7891 is occupied, scans upward for an available port

#### Upgrade Command

- **`muxi-server upgrade`**: self-update the server binary, pull latest RCE, and migrate config
- Downloads latest server binary from GitHub releases (atomic swap with rollback)
- Adds missing config fields (e.g. RCE auth token) to existing configurations

#### Fixes

- **HuggingFace model cache**: pass `HF_HOME=/opt/hf-cache` to containers so the pre-cached embedding model is used instead of re-downloading on every startup (~80s), which caused health check timeouts
- **npm/npx in containers**: npm and npx are symlinks that use relative `require('../lib/cli.js')`; bind-mounting the resolved path broke the import. Now creates wrapper scripts that invoke node with the full path to the npm module
- **Exact runtime version pinning**: versions like `muxi_runtime: "0.20260220.0"` were rejected by the resolver if not in the local registry. Now passes exact versions through to the downloader, which checks disk and downloads if needed
- **Restore path**: use downloader in the restore path to resolve `latest` runtime from GitHub instead of building a literal `muxi-runtime-latest-*.sif` filename
- **Runtime resolution**: always resolve `latest` runtime from GitHub instead of using stale locally-cached version
- **Host tools**: add `npm`, `npx`, `bun`, `uv`, `uvx`, `tar`, and `gzip` to tools bind-mounted into containers

### CLI v0.20260306.0

#### Explicit component declaration (CLI support)

The CLI now fully supports the runtime's explicit declaration model. When you create a component with `muxi new agent`, `muxi new mcp`, or `muxi new a2a-service`, it automatically registers the ID in your formation manifest. `muxi validate` warns about undeclared files ("it will not be loaded") and errors on declared IDs with no matching file. All templates have `active:` removed.

#### File artifacts in chat

Formations that generate files (PDFs, images, data) now have their artifacts saved to `~/.muxi/cli/artifacts/`. New management commands:

- `muxi artifacts list` - List saved artifacts grouped by formation
- `muxi artifacts open` - Open artifacts directory in file manager
- `muxi artifacts cleanup` - Remove old artifacts (`--days N`, `--formation <name>`)

All commands auto-scope to the current formation when run from inside a formation directory.

### Runtime v0.20260306.1

#### Explicit component declaration

Formations now require explicit declaration of agents, MCP servers, and A2A services. Files in subdirectories (`agents/`, `mcp/`, `a2a/`) are pure definitions -- only components listed in the formation manifest are loaded. This replaces auto-discovery and the `active` field, giving full control over what gets loaded.

```yaml
agents:
  - support-agent       # Loads agents/support-agent.yaml by ID
  - research-agent      # Loads agents/research-agent.yaml by ID

mcp:
  servers:
    - github-mcp        # Loads mcp/github-mcp.yaml by ID
```

String entries reference files by ID. Dict entries are inline definitions. Omitted or empty lists mean nothing is loaded. Agents can now reference formation-level MCP servers by string ID in their `mcp_servers` field.

> [!IMPORTANT]
> This is a breaking change. Remove `active:` from all component files and add explicit `agents:` / `mcp.servers:` lists to your `formation.yaml`.

### Runtime v0.20260306.0 + Server v0.20260305.0

#### Use your formation from Claude Desktop, Cursor, and any MCP client

Every formation now exposes an MCP server at `/mcp`. Connect from Claude Desktop, Cursor, or any MCP-compatible client and interact with your agents using the standard Model Context Protocol – same memory, same tools, same auth. All 33 client endpoints are available as MCP tools with clean names (`chat`, `list_sessions`, `search_memories`, etc.). Admin and internal endpoints are never exposed.

[>] [Connect via MCP guide →](guides/connect-via-mcp.md)

MCP authentication works exactly like the REST API: pass `X-Muxi-Client-Key` in your transport headers. No key, no access.

#### Async without webhooks

You can now fire off async requests and poll for results without setting up a webhook. The response includes a poll URL – just check back when you're ready. Per-request `threshold_seconds` and `webhook_url` overrides are now available in the chat request body too.

#### Faster responses

Context loading (memory, synopsis, buffer) now runs in parallel, saving 300-500ms per request. Simple greetings skip context entirely, cutting response time from ~4.4s to ~2.4s. JSON serialization switched to orjson (6x faster encoding).

### Runtime v0.20260302.0

#### Mix and match embedding models

Formations can now use any embedding dimension – the runtime creates dimension-specific memory tables automatically. A 384-dim local model and a 1536-dim OpenAI model can coexist in the same database. Local embedding models (`local/all-MiniLM-L6-v2`, etc.) run via sentence-transformers with no API key required.

> [!NOTE]
> If upgrading from an earlier version, rename your existing table: `ALTER TABLE memories RENAME TO memories_1536;`


---

## February 2026: Initial Release

**Server**
- Production-ready orchestration platform with HMAC authentication
- Multi-formation support with zero-downtime deployment
- Circuit breakers and resilience patterns
- Hot-reload formations and health endpoints
- Local development mode: `/rpc/dev/run` and `/rpc/dev/stop` endpoints, `/draft/{id}/*` proxy route
- SDK update notifications via `X-Muxi-SDK-Latest` header (fetches release versions from GitHub API, refreshes every 24h)

**Runtime**
- 4-layer memory system (buffer, working, user synopsis, persistent)
- FAISSx vector store for semantic search
- Full MCP tool integration for agents
- Observability system with 350+ typed events
- Topic tagging for analytics
- Self-healing tool chaining (agents recover from tool failures automatically)
- User synopsis caching (80%+ token reduction)

**CLI**
- `muxi dev` / `muxi up` for local development, `muxi down` to stop
- `muxi deploy` for production deployment
- Secrets management
- Formation scaffolding
- Draft and live formations can run simultaneously with the same ID

**SDKs**
- Python, TypeScript, and Go client libraries
- `mode` parameter (`"live"` or `"draft"`) to switch between live and draft formations
- Streaming support

**OneLLM**
- Unified LLM interface supporting 15+ providers (OpenAI, Anthropic, Google, Ollama, and any OpenAI-compatible API)
- Semantic caching (50-80% cost savings)
- Streaming support

**Documentation**
- Complete documentation restructure with concept pages, guides, deep dives, and reference
- Added: Sessions, Multi-Identity Users, Scheduled Tasks, self-healing tool chaining, topic tagging, semantic caching
