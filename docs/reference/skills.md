---
title: Skills
description: Give agents specialized knowledge and executable scripts
---

# Skills

## Teach agents how to do things

Skills are self-contained packages of instructions, references, and scripts. They follow the open [Agent Skills specification](https://agentskills.io/specification) and give agents domain expertise that loads on demand.

> [!TIP]
> **New to skills?** Read [Skills Concepts](../concepts/skills.md) first for an overview of how skills work in MUXI.
>
> **API Reference:** [`GET /v1/skills`](api/formation#tag/Skills/GET/skills) | [`GET /v1/skills/{name}`](api/formation#tag/Skills/GET/skills/{name}) | [`GET /v1/agents/{id}/skills`](api/formation#tag/Skills/GET/agents/{agent_id}/skills)


## Quick Setup

[[steps]]

[[step Create skills directory]]

```bash
mkdir -p skills/my-skill
```
[[/step]]

[[step Write SKILL.md]]

```yaml
# skills/my-skill/SKILL.md
---
name: my-skill
description: Generates weekly summary reports
---
```

```markdown
# Weekly Report Generation

When asked to create a weekly report, follow these steps:

1. Gather data from the provided sources
2. Summarize key metrics
3. Highlight trends and anomalies
4. Format as a clean markdown document
```
[[/step]]

[[step Test it]]

```bash
muxi dev
# Ask: "Generate a weekly report"
```

All agents in the formation see formation-level skills automatically.
[[/step]]

[[/steps]]


## SKILL.md Format

Every skill is a directory containing a `SKILL.md` file with YAML frontmatter and a markdown body.

### Frontmatter Fields

| Field | Required | Description |
|-------|----------|-------------|
| `name` | No | Skill name (defaults to directory name). Lowercase, hyphens only. |
| `description` | **Yes** | What the skill does. Shown in the agent's catalog. |
| `license` | No | License identifier (e.g., `MIT`, `Apache-2.0`) |
| `compatibility` | No | Compatible platforms (e.g., `muxi cursor claude-code`) |
| `allowed-tools` | No | Space-delimited list of tools the skill needs |
| `metadata` | No | Arbitrary key-value pairs |

### Example

```yaml
---
name: data-analysis
description: Analyze datasets, compute statistics, and produce visualizations
license: MIT
compatibility: muxi cursor
allowed-tools: run_skill generate_file
metadata:
  author: analytics-team
  version: "1.0.0"
---
```

```markdown
# Data Analysis

You are now operating as a data analysis specialist.

## Capabilities

- Statistical analysis (mean, median, std dev, percentiles)
- Trend detection and forecasting
- Chart generation (bar, line, scatter, heatmap)

## Constraints

- Always validate input data before processing
- Use pandas for tabular data
- Use matplotlib or seaborn for charts

## Examples

When asked "Analyze sales data":
1. Load the dataset
2. Compute summary statistics
3. Identify top performers and trends
4. Generate visualization
5. Return findings with chart
```

### Name Validation

Skill names must match the pattern `[a-z0-9][a-z0-9-]*[a-z0-9]`:

```
my-skill        ✓
data-analysis   ✓
MySkill         ✗ (uppercase)
my_skill        ✗ (underscores)
-my-skill       ✗ (leading hyphen)
```

The name should match the directory name. Mismatches produce a warning but don't fail.


## Directory Layout

```
skills/
└── my-skill/
    ├── SKILL.md              # Required: frontmatter + instructions
    ├── scripts/              # Optional: executable scripts
    │   └── generate.py
    ├── references/           # Optional: detailed reference docs
    │   └── api-guide.md
    └── assets/               # Optional: data files, templates
        └── template.xlsx
```

Files in `scripts/`, `references/`, and `assets/` are automatically discovered and listed as resources when the skill is activated.


## Skill Placement

### Formation-level (public)

Skills in the top-level `skills/` directory are visible to **all** agents:

```
my-formation/
├── formation.afs
└── skills/
    └── report-generation/
        └── SKILL.md
```

### Agent-level (private)

Skills inside an agent's directory are visible only to **that agent**:

```
my-formation/
├── formation.afs
└── agents/
    └── analyst/
        ├── analyst.afs
        └── skills/
            └── forecasting/
                └── SKILL.md
```


## Formation Configuration

### Basic (no RCE)

Knowledge-only skills need no extra configuration. Place them in `skills/` and they work:

```yaml
# formation.afs
schema: "1.0.0"
id: my-formation
description: Formation with skills

llm:
  models:
    - text: "openai/gpt-4o"

agents:
  - assistant
```

### With RCE (script execution)

To execute skill scripts, you need the Skills RCE service.

**Server-managed formations** get RCE automatically -- the MUXI Server includes a built-in instance. No `rce:` config needed.

**Custom RCE instance** -- point your formation at your own:

```yaml
# formation.afs
schema: "1.0.0"
id: my-formation
description: Formation with executable skills

llm:
  models:
    - text: "openai/gpt-4o"

rce:
  url: "http://my-rce-server:7891"
  token: "${{ secrets.RCE_TOKEN }}"

agents:
  - assistant
```

### RCE Fields

| Field | Required | Description |
|-------|----------|-------------|
| `url` | Yes | RCE service URL |
| `token` | Yes | Authentication token |

See [Skills RCE](../server/skills-rce.md) for deployment options, available runtimes, and packages.


## Allowed Tools

Skills can declare which tools they need:

```yaml
---
name: report-generation
description: Generate formatted reports
allowed-tools: run_skill generate_file
---
```

When a skill specifies `allowed-tools`, the agent is restricted to those tools during skill execution. This prevents skills from accessing capabilities they shouldn't have.

If `allowed-tools` is omitted, no restriction is applied.


## Script Execution

Skills with scripts in `scripts/` can be executed via the `run_skill` tool. The agent calls this tool after activating the skill:

```
Agent: [Activates report-generation skill]
Agent: [Calls run_skill with command "python scripts/generate.py --format pdf"]
RCE:   [Executes in sandboxed container]
RCE:   [Returns output files]
Agent: [Delivers results to user]
```

The runtime handles:

- **Upload** -- Skill directories are zipped and uploaded to RCE at startup
- **Cache busting** -- Content hash ensures re-upload only when files change
- **Execution** -- Scripts run in isolated containers with configurable timeout


## Built-in Skills

The runtime ships with built-in skills that are always available:

### file-generation

Generates files (PDFs, images, spreadsheets, charts) via a Python script executed in the RCE sandbox.

```
User: "Create a bar chart of Q4 revenue by region"
Agent: [Activates file-generation → runs generate.py]
Agent: [Returns chart image]
```

Built-in skills can be disabled in the formation config if not needed.


## REST API

### List all skills

```
GET /v1/skills
```

Returns metadata for all loaded skills (public + built-in).

### Get skill details

```
GET /v1/skills/{name}
```

Returns full metadata and resource list for a specific skill.

### List agent skills

```
GET /v1/agents/{agent_id}/skills
```

Returns skills available to a specific agent (public + agent-private).


## Troubleshooting

[[toggle Skill not appearing in catalog]]
Verify the `SKILL.md` file exists and has valid frontmatter:

```bash
cat skills/my-skill/SKILL.md
```

The `description` field is required. Missing it causes the skill to be skipped.
[[/toggle]]

[[toggle run_skill tool not available]]
The `run_skill` tool only appears when:
1. The skill has files in `scripts/`
2. RCE is configured in the formation
3. The RCE service is reachable

Check your `rce:` config in `formation.afs`.
[[/toggle]]

[[toggle Scripts not executing]]
Verify the RCE service is running:

```bash
curl http://localhost:6000/health
```

Check that skill files were uploaded (look for "uploaded to RCE cache" in logs).
[[/toggle]]

[[toggle Skill name warning]]
The skill name in SKILL.md frontmatter should match the directory name. Mismatches produce a warning:

```
skills/my-skill/SKILL.md  →  name: my-skill  ✓
skills/my-skill/SKILL.md  →  name: other      ⚠ warning
```
[[/toggle]]


## Next Steps

[+] [Skills Concepts](../concepts/skills.md) - How skills work
[+] [Add Skills Guide](../guides/add-skills.md) - Step-by-step tutorial
[+] [Agents Reference](agents.md) - Agent configuration
[+] [Agent Skills specification](https://agentskills.io/specification) - The open standard
