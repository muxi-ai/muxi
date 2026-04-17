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

### Runtime v0.20260417.2

#### Planning prompt hardening

Codified the placeholder contract directly in the agent planning prompt so the LLM produces valid plans on the first pass, reducing how often the v0.20260417.1 runtime guards have to fire.

- **Syntax pinning** -- only `{{UPPERCASE_NAME}}` / `{{UPPERCASE_NAME.field}}` is valid; `<<NAME>>`, `${{NAME}}`, and `{NAME}` are rejected.
- **Reference consistency** -- a later step may reference a prior output only by the exact name assigned in its `output_placeholder`; invented names now carry a "silently fails" warning with correct/wrong examples.
- **Sentinel values banned** -- explicit list (`auto-injected`, `auto_fill`, `from_server`, `from_context`, `server_default`, `<to-be-provided>`, `to_be_injected`) with instruction to omit the key instead.
- **Array extrapolation banned** -- explicit prohibition on fabricating additional IDs/emails/hashes; runtime guards remain as the safety net.

### Runtime v0.20260417.1

#### Placeholder substitution & inference guards

- **Free-text MCP result extraction** -- Array placeholders like `{{APRIL_10_MESSAGES.message_ids}}` now resolve against label-style (`Field: value`, `**Field:** value`) and inline-JSON patterns in text chunks, killing the Gmail "incrementing hex" hallucination pattern.
- **Cross-placeholder fallback** -- When the planner emits a placeholder name it never assigned (e.g. `{{EVENT_ID_FROM_SEARCH}}` when only `{{EVENT_DETAILS}}` exists), the runtime now tries to bind the parameter against the union of all successful prior results (including text-scanned `id:` labels), committing only when exactly one candidate matches.
- **Literal placeholders stripped before MCP call** -- Any non-required parameter still shaped like `{{...}}` / `<<...>>` after all substitution / context / inference attempts is dropped before `_validate_tool_parameters`; required placeholders are preserved so the repair-plan flow can handle them.
- **Array inference validation** -- Every LLM-inferred list item must literally appear in a prior successful result (record value or text chunk); fabricated items are dropped and fully-fabricated arrays are removed so repair replanning runs instead.

### Runtime v0.20260417.0

#### Repair-tool domain scoring

The auto-discovery repair scorer now has a resource-domain taxonomy (mail, calendar, drive, sharepoint, chat, contact, task, note) so tools like `list-mail-folders` or `search-sharepoint-sites` can no longer be chosen to repair a failed `get-drive-root-item` on the same MCP server. Cross-domain candidates incur a -15 penalty; same-domain candidates get +4. Generic tokens stay untagged to avoid punishing legitimately ambiguous tools.

### Runtime v0.20260416.3

#### Sentinel values, dotted placeholders & scheduler completion

- **LLM-emitted sentinel values no longer block MCP server-default injection** -- Strings like `"driveId": "auto-injected"` are now treated as unresolved so real server defaults can overwrite them.
- **Dotted placeholder references resolve correctly** -- `{{SPARK_EVENT.event_id}}` now looks up the `{{SPARK_EVENT}}` result and extracts `event_id` (case-insensitive, ignoring underscores/dashes) instead of passing the literal string through to MCP.
- **Whole-payload fallback no longer returns entire result dicts** -- For LLM-hallucinated parameters that aren't in a tool's schema (e.g. `user_google_email` on `manage_event`), the fallback now only applies for scalar schemas, preventing pydantic errors from bogus full-result payloads.
- **Scheduler marks job success without a webhook** -- When a formation has no `async.webhook_url`, jobs now run synchronously and `mark_job_execution_success` / `complete_onetime_job` are called directly so `total_runs` and `last_run_status` stay correct.

### Runtime v0.20260416.1 / v0.20260416.0

#### Planning-mode parameter preservation & date awareness

- **Planning mode no longer strips tool parameters (CRITICAL)** -- `_finalize_execution_plan` was rebuilding `my_steps` from the unified `steps` list and silently replacing every parameter set with `{}`. Parameters from the LLM's original `my_steps` are now preserved by FIFO match on `tool_name`, so repeated uses of the same tool keep their own params.
- **Planner resolves relative date references** -- The planning prompt now includes a `## Current date/time:` section so "today" and "tomorrow" produce concrete RFC3339 dates instead of literal strings.
- **Parameter-free planned MCP steps no longer crash** -- Fixed an `UnboundLocalError` on `server_default_param_names` for tools like `list-mail-messages`, `get_events`, and `search_gmail_messages` that require no user-supplied parameters.
- **Scheduler dispatches job execution to the main event loop** -- Fixes "Future attached to a different loop" crashes on scheduled jobs whose chat path uses asyncpg/httpx/MCP transports bound to the uvicorn loop.
- **Repair-tool selection respects server affinity** -- Added +4 same-server / -3 cross-server scoring so a `todo-helper-mcp` tool can no longer outscore same-server candidates during repair planning.

### Runtime v0.20260415.0

#### Delegation, context hints & dependency floors

- **MCP default-backed required params no longer trigger redundant inference** -- Runtime-injected values like `driveId` are now treated as satisfiable, preventing accidental replacement with guessed values such as `"me"`.
- **Generalized named-resource hints** -- Context-hint extraction now recognizes `#channel`, `@name`, quoted names, and filenames without service-specific heuristics.
- **Delegated prompts carry prior tool results** -- Downstream agents receive compact summaries of successful prior results instead of reasoning without them.
- **Planning discourages unnecessary delegation** -- Agents keep arithmetic/summarization/analysis with themselves when their own tools already fetch the needed data.
- **Dependency floor bumps** -- `onellm[cache] >= 0.20260415.0`, `fastmcp >= 3.2.0`, `pypdf >= 6.10.0`, `Pillow >= 12.2.0`, `aiohttp >= 3.13.4`, `requests >= 2.33.0`, `cryptography >= 46.0.7`, `pytest >= 9.0.3`, `black >= 26.3.1`.

### Runtime v0.20260414.0

#### Snake_case normalization & result-recency bias

- **Most-recent-step record preference** -- Alias extraction now iterates prior results in reverse chronological order, so a worksheet GUID from step 3 wins over a file's `driveItemId` from step 2 when binding `workbookWorksheetId`.
- **Snake_case parameter matching** -- Parameter names are normalized (underscores stripped) before alias suffix lookup and exact-key matching, so Slack's `channel_id` binds against records keyed on `channelId` and vice versa.
- **Snake_case context signals** -- `_record_matches_context_hints` and `_compact_planning_record` now recognize snake_case fields (`display_name`, `channel_name`, `drive_id`, `drive_item_id`, `created_at`, etc.), and explicit text lines like `channel_id = X` resolve `channelId` params across casings.

### Runtime v0.20260413.1

#### Worksheet & entity-ID binding from prior results

- **Alias suffix list expanded** -- Added `worksheetid`, `sheetid`, `notebookid`, `sectionid`, `pageid`, `channelid`, `teamid`, `planid`, `listid`, `eventid`, `contactid` so MS365/Graph entity IDs flow between steps (e.g. the 4-step Excel chain get-drive-root-item -> list-folder-files -> list-excel-worksheets -> get-excel-range).
- **Real GUIDs no longer rejected as placeholders** -- Values like `{4C35B2DD-58DF-4BDB-B806-E0421A3D5456}` are now exempted from the unresolved-placeholder check before `_is_nonempty_parameter_candidate` runs.
- **JSON-string `result` fields parsed** -- When a tool returns `{"result": "{\"value\":[...]}", "status": "success"}`, the string payload is now parsed so `_iter_result_records` can extract records for downstream steps (same class of fix previously landed for `content`).

### Runtime v0.20260413.0

#### Planning JSON robustness & truncation fix

- **Prose-preamble JSON parsing** -- Some models emit natural-language before the JSON execution plan, sometimes in a markdown fence that doesn't start at char 0. Planning extraction now uses a three-stage approach (direct parse, regex for code-fenced JSON anywhere, brace-matched search for the outermost `{...}` containing `"steps"`).
- **Planner no longer truncated on tool-heavy formations** -- Planning calls now set `max_tokens=16384` explicitly. Formations with 100+ MCP tools producing 4-step plans were hitting the Anthropic 4096 default and falling back to delegation; the new cap is 4-5x larger than any realistic multi-step plan.

### Runtime v0.20260403.0

#### MCP Accept header & faissx bump

- **Strict FastMCP servers no longer reject transport detection** -- The detector's ping request now sends `Accept: application/json, text/event-stream, */*` explicitly, satisfying strict servers that previously returned `406 Not Acceptable` (community PR #139).
- **faissx >= 0.20260403.0** -- Improved vector-store persistence across restarts; no data loss on formation restart.

### CLI v0.20260417.0

- `muxi scheduler list` and `show` views no longer crash on timestamps with fractional seconds and timezone offsets (e.g. `2026-04-16T09:00:00.123456+00:00`), and fractional precision is preserved when normalizing one-time `scheduled_for` values.

### CLI v0.20260416.0

- `muxi scheduler list` / `show` map the runtime's actual job fields (`status`, `is_recurring`, `cron_expression`, `scheduled_for`, `original_prompt`, `last_run_at`, `total_failures`) so active jobs render correctly instead of appearing disabled with empty columns.
- Recurring cron next-run values are now computed client-side; one-time jobs reuse `scheduled_for` when the runtime does not provide a precomputed `next_run`.

### CLI v0.20260413.0

- `muxi validate` now recognizes the updated MCP declaration spec (`mcps.servers`) while remaining compatible with legacy `mcp.servers` manifests.
- MCP config files are matched from both `mcps/` and legacy `mcp/` directories so declaration checks and required-field validation report correctly during migration.

### Runtime v0.20260410.0

#### MCP default parameters

MCP servers now support a `parameters` field: a flat key-value map of default values injected into every tool call on that server. This removes infrastructure constants (org-level drive IDs, tenant IDs, project keys) from agent prompts and LLM inference, making multi-step tool chains deterministic regardless of model.

```yaml
# mcp/ms365.afs
schema: "1.0.0"
id: ms365-mcp
type: http
endpoint: "https://mcp.example.com/ms365"
parameters:
  driveId: "${{ secrets.ORG_DRIVE_ID }}"
```

- Values support `${{ secrets.X }}` and `${{ user.credentials.X }}` interpolation
- Caller-supplied arguments always take precedence over defaults
- Works on both formation-level and agent-level MCP server declarations

→ [Tools Reference](reference/tools.md#default-parameters) for full documentation

#### Multi-step tool execution hardening

A set of fixes making multi-step MCP workflows (e.g., find user, get drive root, list files, open workbook) more reliable:

- **Execution-time parameter binding uses clean context** -- Planned tool steps now resolve parameters from the current request and successful prior results only, not the full enhanced prompt with stale memory/profile data.
- **Placeholder values blocked** -- Strings like `{{ROOT_FOLDER_ID}}` or `<<ID>>` are now treated as unresolved and block tool calls safely instead of being sent as literal arguments.
- **Failed steps excluded from chaining** -- Only successful prior tool results are used for parameter substitution and inference context. Error payloads no longer contaminate downstream steps.
- **Fabricated IDs rejected** -- A post-inference validation guard checks LLM-inferred ID-typed parameters against successful result records and rejects values not found in any discovered record.
- **Modern MCP result extraction fixed** -- Tool results returned as JSON strings (common with modern MCP protocol) are now properly parsed for downstream parameter extraction.

### Runtime v0.20260409.0

#### Faster persistent memory recall

- Single multi-collection query for memory search (previously fanned out into separate lookups)
- Lightweight fast-path for profile/synopsis queries before broader semantic search
- Best-effort PostgreSQL indexes for user/collection filtering (non-fatal if creation fails)

### Runtime v0.20260408.0

#### Routing & date-preservation hardening

- **Specialist agents no longer lose to generalists** -- Routing now considers agent MCP tools, specialties, and domain keywords, not just agent name and description.
- **Follow-up messages stay routed correctly** -- Session-aware routing biases follow-ups toward the agent that handled the previous turn.
- **Exact dates preserved in responses** -- Workflow synthesis and agent planning responses no longer rewrite concrete dates/times into relative language.
- **Delegated tasks use their tools** -- When a broad agent delegates to a specialist, the specialist now plans and executes its MCP tools instead of answering from model prior.

### Runtime v0.20260407.0

#### HTTP MCP reliability

- Per-request timeouts now enforced on all MCP HTTP operations (previously only on connect/init)
- Explicit transport type preserved across reconnects (no more re-detection on every reconnect)
- Client disconnects now cancel in-flight MCP requests and close their pooled connections

### Runtime v0.20260402.0

#### Workflow tool-call reliability

- **Workflow tasks use isolated context** -- Workflow-dispatched agents no longer inherit full conversation history, which was causing them to reproduce prior tool calls as XML text instead of issuing real API calls.
- **System date injected** -- Agents now know today's date instead of using their training data cutoff.

### Runtime v0.20260401.0

#### Skill secrets

Skills can now use `${{ secrets.X }}` directly in their `SKILL.md` instructions -- the same syntax used everywhere else in MUXI. The runtime scans skill files at load time, resolves secrets at activation, and injects them into the agent's context. For bundled scripts, secrets are passed as environment variables to the RCE subprocess.

> **Note:** Skills RCE must not be exposed publicly -- it is an internal service intended for trusted network boundaries only.

#### SOP synthesis fixes

- **Synthesis step instructions no longer truncated** -- The 500-character cap on SOP step body text is removed; instructions are passed verbatim.
- **Prior step results now reach the synthesis agent** -- Dependency outputs are extracted and presented as clear content instead of raw metadata blobs.

### Server v0.20260402.0

- Creates `{dataDir}/tmp` on startup so `TMPDIR` works out of the box in Docker deployments
- Default health check endpoint changed from `/health` to `/v1/health` to match the runtime API

### Server v0.20260401.1

- Auto-installs Apptainer on `muxi-server start` if not found on Linux (survives container restarts)
- Fixed Apptainer/Singularity binary lookup to prefer `apptainer` over `singularity`

### CLI v0.20260408.0

- Chat sessions no longer time out during slow-starting formations that emit SSE keepalive frames
- SSE stream parsing handles full event blocks, surfacing route-level errors and preserving progress/tool activity events

### SDKs v0.20260408.0

- SSE parsing across all 12 SDKs is now keepalive-aware (`: keepalive` comments no longer cause false idle failures)
- Route-level `event: error` frames surfaced as SDK errors instead of being silently dropped
- New runtime event types (`progress`, `thinking`, `planning`, `tool_call`) preserved in chat streams

### SDKs v0.20260324.0

- Added `updateSchedulerJob`, `pauseSchedulerJob`, and `resumeSchedulerJob` methods to all SDKs

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
