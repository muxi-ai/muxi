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

## March 2026

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
