---
title: Changelog
description: Release history and updates for MUXI
---
# Changelog

All notable changes to MUXI components.

For detailed release notes, see individual component repositories:
- [Server Releases](https://github.com/muxi-ai/server/releases)
- [Runtime Releases](https://github.com/muxi-ai/runtime/releases)
- [CLI Releases](https://github.com/muxi-ai/cli/releases)
- [OneLLM Releases](https://github.com/muxi-ai/onellm/releases)


## February 2026

### Local Development Mode
Fast iteration without full deploy cycle:
- **Server**: `/rpc/dev/run` and `/rpc/dev/stop` endpoints, `/draft/{id}/*` proxy route
- **CLI**: `muxi up [path]` to start local formation, `muxi down` to stop
- **SDKs**: `mode` parameter (`"live"` or `"draft"`) to switch between live and draft formations
- Draft and live formations can run simultaneously with the same ID

### SDK Update Notifications
Server now notifies SDKs when updates are available:
- Parses `X-Muxi-SDK` header from requests
- Responds with `X-Muxi-SDK-Latest` header containing latest version
- Fetches release versions from GitHub API (refreshes every 24h)

### Bug Fixes
- Fixed Docker container naming for draft formations (use `-draft` suffix)
- Improved error messages when formations crash during startup (removed ASCII art noise)

---

## January 2026

### Documentation
- Major documentation restructure with improved navigation
- Added concept pages: Sessions, Multi-Identity Users, Scheduled Tasks
- Added "why" rationale throughout guides
- Highlighted key features: self-healing tool chaining, topic tagging, semantic caching
- Reorganized sidebar for better onboarding flow


## December 2025

### Server v1.0
- Production-ready release
- 91.2% test coverage
- HMAC authentication
- Multi-formation support
- Zero-downtime deployment

### Runtime v1.0
- Stable API
- 85% test coverage
- Full MCP integration
- 4-layer memory system
- FAISSx vector store

### CLI v1.0
- `muxi dev` for local development
- `muxi deploy` for production
- Secrets management
- Formation scaffolding


## November 2025

### OneLLM v1.0
- Unified LLM interface
- 96% test coverage
- Semantic caching (50-80% cost savings)
- 15+ provider support
- Streaming support


## October 2025

### Runtime
- Observability system with 350+ event types
- Topic tagging for analytics
- Self-healing tool chaining
- User synopsis caching

### Server
- Circuit breakers and resilience patterns
- Hot-reload formations
- Health endpoints


## Earlier Releases

See [GitHub Releases](https://github.com/muxi-ai) for complete history.
