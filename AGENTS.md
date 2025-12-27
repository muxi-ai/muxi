# AGENTS.md

> This file helps AI coding assistants understand the MUXI ecosystem.
> Copy the **MUXI Ecosystem Overview** section to each repo's AGENTS.md, then add repo-specific details below.

---

## MUXI Ecosystem Overview

**MUXI is infrastructure for AI agents** - a complete platform for deploying and managing agents in production.

### Architecture at a Glance

```
┌─────────────────────────────────────────────────────────────┐
│                     Your Application                        │
└──────────────────────────┬──────────────────────────────────┘
                           │ SDK / CLI / API
                           ▼
┌─────────────────────────────────────────────────────────────┐
│                      MUXI Server                            │
│              Orchestration • Routing • Auth                 │
│                       Port 7890                             │
└──────────────────────────┬──────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────┐
│                     MUXI Runtime                            │
│               Formation Execution (SIF)                     │
└──────────────────────────┬──────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────┐
│                  LLM APIs / MCP Tools                       │
└─────────────────────────────────────────────────────────────┘
```

### Component Analogy

| Component | Analogy | Purpose |
|-----------|---------|---------|
| **Server** | Docker Engine | Orchestration, routing, lifecycle |
| **Runtime** | Container Image | Execution environment |
| **Registry** | Docker Hub | Distribution & discovery |
| **CLI** | kubectl / docker CLI | Management tool |
| **SDKs** | Client libraries | Programmatic access |

### Repository Map

| Repo | Lang | Purpose | Status |
|------|------|---------|--------|
| [server](https://github.com/muxi-ai/server) | Go | Orchestration platform | Production |
| [runtime](https://github.com/muxi-ai/runtime) | Python | Formation execution | Production |
| [cli](https://github.com/muxi-ai/cli) | Go | Command-line interface | Complete |
| [registry](https://github.com/muxi-ai/registry) | PHP | Formation distribution | Complete |
| [schemas](https://github.com/muxi-ai/schemas) | YAML | Formation specification | Finalized |
| [onellm](https://github.com/muxi-ai/onellm) | Python | Unified LLM interface | Production |
| [faissx](https://github.com/muxi-ai/faissx) | Python | Vector search server | Production |
| SDKs | Various | 12 language SDKs | In Progress |

### Key Conventions

- **Branch workflow:** `develop` → `rc` → `main` (all PRs target `develop`)
- **Commit style:** [Conventional Commits](https://www.conventionalcommits.org/)
- **Default port:** 7890 (MUXI Server)
- **Container format:** SIF (Singularity Image Format)

### Request Flow

```
Client → Server (:7890) → Reverse Proxy → Runtime (SIF) → LLM API
```

### Design Principles

1. **Agents as infrastructure** - First-class primitives, not scripts
2. **Self-hostable** - No required cloud services
3. **Observable by default** - Structured traces for every invocation
4. **Model agnostic** - Swap providers via config

---

## This Repository

<!-- REPO-SPECIFIC SECTION - Replace this with actual repo details -->

**Repository:** `muxi-ai/muxi` (meta/docs repository)

### Purpose

This is the **meta repository** containing:
- Project-wide documentation (CONTRIBUTING, ARCHITECTURE, etc.)
- Style guides for all supported languages
- Shared configuration and guidelines

### Structure

```
muxi/
├── ARCHITECTURE.md      # System architecture overview
├── CONTRIBUTING.md      # Contribution guidelines
├── DEVELOPMENT.md       # Dev environment setup
├── GIT-WORKFLOW.md      # Branching and release process
├── REPOSITORIES.md      # All MUXI repositories
├── SECURITY.md          # Security policy
├── style-guides/        # Language-specific coding standards
│   ├── go.md
│   ├── python.md
│   ├── typescript.md
│   └── ...
└── docs/                # Additional documentation
```

### For AI Agents Working Here

When making changes to this repo:

1. **Documentation changes** - Keep docs concise and scannable
2. **Style guides** - Follow the established format (principles, examples, conventions)
3. **Cross-repo consistency** - Changes here may need propagation to other repos
4. **No code** - This repo contains documentation only

### Related Resources

- [CONTRIBUTING.md](./CONTRIBUTING.md) - How to contribute
- [style-guides/](./style-guides/) - Language-specific conventions
- [ARCHITECTURE.md](./ARCHITECTURE.md) - Detailed architecture

---

## Template for Other Repos

When adding AGENTS.md to other repositories, use this structure:

```markdown
# AGENTS.md

<!-- Copy the "MUXI Ecosystem Overview" section from muxi/AGENTS.md -->

---

## This Repository

**Repository:** `muxi-ai/{repo-name}`

### Purpose

{One paragraph describing what this repo does}

### Structure

{Directory tree of key files/folders}

### Key Files

| File | Purpose |
|------|---------|
| `...` | ... |

### Development

{How to run, test, build}

### For AI Agents Working Here

{Repo-specific guidance: gotchas, patterns, things to avoid}

### Dependencies

{What this repo depends on, what depends on it}
```
