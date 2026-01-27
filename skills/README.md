# MUXI Skills

Agent Skills for the MUXI platform, following the open [Agent Skills](https://agentskills.io) specification.

## Available Skills

| Skill | Description |
|-------|-------------|
| [`muxi`](muxi/) | Complete MUXI platform guide -- installation, server setup, CLI, secrets, formations, deployment, SDKs, and APIs |

## What Are Agent Skills?

Agent Skills are folders of instructions, scripts, and resources that AI agents can discover and use. They work with any compatible agent tool, including Claude Code, Cursor, GitHub Copilot, VS Code, Factory, Amp, Goose, and many others.

Skills use a progressive disclosure model:
1. **Metadata** (~100 tokens) -- name and description, loaded at startup
2. **Instructions** (< 500 lines) -- full `SKILL.md` body, loaded when activated
3. **References** (on demand) -- detailed docs in `references/`, loaded as needed

## Installation

### Claude Code

```bash
# From the root of this repo
claude mcp add-skill skills/muxi
```

Or add to your `.claude/settings.json`:
```json
{
  "skills": ["skills/muxi"]
}
```

### Cursor

Add to `.cursor/skills/` by copying or symlinking:
```bash
ln -s $(pwd)/skills/muxi .cursor/skills/muxi
```

### Generic (any compatible agent)

Point your agent's skill discovery directory at `skills/muxi/`. The agent will parse the `SKILL.md` frontmatter at startup and activate the full skill when MUXI-related tasks come up.

For filesystem-based agents, the skill is activated when the agent reads `skills/muxi/SKILL.md`. Reference files in `skills/muxi/references/` are loaded on demand.

### Manual / Custom Integration

Parse the YAML frontmatter from `SKILL.md` and inject into your agent's system prompt:

```xml
<available_skills>
  <skill>
    <name>muxi</name>
    <description>Guide users through the MUXI platform -- infrastructure for AI agents. Covers installation, server setup, CLI, secrets, formations, deployment, SDKs, and APIs.</description>
    <location>/path/to/skills/muxi/SKILL.md</location>
  </skill>
</available_skills>
```

When the skill is activated, load the full `SKILL.md` body. For detailed reference, the agent can read files from `references/`:

```
skills/muxi/
├── SKILL.md                           # Main instructions (448 lines)
└── references/
    ├── formation-schema.md            # Complete .afs schema reference
    ├── cli-reference.md               # All CLI commands and flags
    ├── server-config.md               # Server config, API, services
    ├── formation-api.md               # Runtime API endpoints
    ├── sdks.md                        # Go, Python, TypeScript SDK reference
    └── examples.md                    # Full formation examples + SDK usage
```

## Validation

Validate the skill using the [skills-ref](https://github.com/agentskills/agentskills/tree/main/skills-ref) CLI:

```bash
skills-ref validate skills/muxi
```

## Learn More

- [Agent Skills Specification](https://agentskills.io/specification) -- the full format spec
- [Agent Skills GitHub](https://github.com/agentskills/agentskills) -- reference implementation
- [MUXI Documentation](https://muxi.org/docs) -- full platform docs
