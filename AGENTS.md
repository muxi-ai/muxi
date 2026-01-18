# AGENTS.md

> This file helps AI coding assistants understand the MUXI project.

## MUXI Ecosystem

**MUXI is infrastructure for AI agents** - a complete platform for deploying and managing agents in production.

For full architecture details, see **[ARCHITECTURE.md](https://github.com/muxi-ai/muxi/blob/main/ARCHITECTURE.md)**.

### Quick Reference

| Component | Purpose | Language |
|-----------|---------|----------|
| **Server** | Orchestration, routing, lifecycle | Go |
| **Runtime** | Formation execution environment | Python |
| **CLI** | Management tool | Go |
| **Registry** | Distribution hub | PHP |
| **SDKs** | Client libraries | 12 languages |

### Key Conventions

- **Branch workflow:** `develop` → `rc` → `main` (PRs target `develop`)
- **Commits:** [Conventional Commits](https://www.conventionalcommits.org/)
- **Style:** See [style-guides/](./style-guides/) for language-specific conventions

### More info

The `muxi` meta-repo (https://github.com/muxi-ai/muxi) contains most everything you need to understand the MUXI stack, including docs, development workflows, style-guides, etc.


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


## Template for Other Repos

When adding AGENTS.md to other repositories, use this structure:

```markdown
# AGENTS.md

> This file helps AI coding assistants understand this repository.

## MUXI Ecosystem

This repo is part of **MUXI** - infrastructure for AI agents.

| Component | Purpose | Language |
|-----------|---------|----------|
| **Server** | Orchestration, routing, lifecycle | Go |
| **Runtime** | Formation execution environment | Python |
| **CLI** | Management tool | Go |
| **Registry** | Distribution hub | PHP |
| **SDKs** | Client libraries | 12 languages |

Full architecture: [muxi-ai/muxi/ARCHITECTURE.md](https://github.com/muxi-ai/muxi/blob/main/ARCHITECTURE.md)


## This Repository

**Repository:** `muxi-ai/{repo-name}`

### Purpose

{One paragraph describing what this repo does}

### Structure

{Directory tree of key files/folders}

### Development

{How to run, test, build}

### For AI Agents Working Here

{Repo-specific guidance: gotchas, patterns, things to avoid}
```
