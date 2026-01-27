---
title: Agent Skills
description: Give AI coding assistants deep knowledge of the MUXI platform
---

# Agent Skills

## Give AI coding assistants deep knowledge of MUXI

The MUXI repository includes an [Agent Skill](https://agentskills.io) -- a structured knowledge package that gives AI coding assistants deep understanding of the MUXI platform.

Once installed, your AI agent can help with installation, server setup, CLI usage, formation authoring, secrets management, SDK integration, deployment, and troubleshooting -- without you having to explain MUXI from scratch.

## Compatibility

The skill follows the open [Agent Skills specification](https://agentskills.io/specification) and works with:

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code)
- [Cursor](https://cursor.sh)
- [GitHub Copilot](https://github.com/features/copilot)
- [VS Code](https://code.visualstudio.com) (with compatible extensions)
- [Factory](https://factory.ai)
- 20+ other compatible agent tools

## Installation

### Claude Code

```bash
claude mcp add-skill skills/muxi
```

### Cursor

Copy or symlink into `.cursor/skills/`:

```bash
ln -s $(pwd)/skills/muxi .cursor/skills/muxi
```

### Other agents

Point your agent's skill directory at `skills/muxi/`. The agent will discover the skill automatically via the `SKILL.md` frontmatter.

## What's Included

The skill uses **progressive disclosure** to keep context usage low:

| Layer | What | Loaded when |
|-------|------|-------------|
| **Metadata** | Name and description (~100 tokens) | Agent startup |
| **Instructions** | Full `SKILL.md` (448 lines) | Skill activated |
| **References** | 6 detailed reference files (~2,000 lines) | On demand |

**Reference files:**

- `formation-schema.md` -- complete `.afs` schema reference
- `cli-reference.md` -- all CLI commands and flags
- `server-config.md` -- server configuration and API
- `formation-api.md` -- runtime API endpoints
- `sdks.md` -- Go, Python, TypeScript SDK reference
- `examples.md` -- full formation examples and SDK usage

## Learn More

- [Agent Skills specification](https://agentskills.io/specification)
- [Skill source on GitHub](https://github.com/muxi-ai/muxi/tree/main/skills/muxi)
