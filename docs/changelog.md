---
title: Changelog
description: Release history and updates for MUXI
---

# Changelog

## Notable changes across all MUXI components

> For detailed release notes, see individual component repositories:
>
> - [Server Releases](https://github.com/muxi-ai/server/releases)
> - [Runtime Releases](https://github.com/muxi-ai/runtime/releases)
> - [CLI Releases](https://github.com/muxi-ai/cli/releases)
> - [SDKs Releases](https://github.com/muxi-ai/sdks/releases)
> - [OneLLM Releases](https://github.com/muxi-ai/onellm/releases)
> - [FAISSx Releases](https://github.com/muxi-ai/faissx/releases)

## March 2026

### Runtime v0.20260306.0 + Server v0.20260305.0

#### Use your formation from Claude Desktop, Cursor, and any MCP client

Every formation now exposes an MCP server at `/mcp`. Connect from Claude Desktop, Cursor, or any MCP-compatible client and interact with your agents using the standard Model Context Protocol – same memory, same tools, same auth. All 33 client endpoints are available as MCP tools with clean names (`chat`, `list_sessions`, `search_memories`, etc.). Admin and internal endpoints are never exposed.

[Connect via MCP guide →](guides/connect-via-mcp.md)

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
