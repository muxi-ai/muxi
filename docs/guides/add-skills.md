---
title: Add Skills
description: Give your agents specialized knowledge and executable scripts
---
# Add Skills

## Teach agents how to do specific tasks

Add skills to give agents deep expertise -- instructions, references, and optional scripts that load on demand. Skills follow the open [Agent Skills specification](https://agentskills.io/specification).

## Overview

Skills let agents learn procedures and execute scripts without bloating their context window. Instructions load only when the agent decides it needs them.

## Step 1: Create a Skill

```bash
mkdir -p skills/report-generation
```

Create `skills/report-generation/SKILL.md`:

```yaml
---
name: report-generation
description: Generate formatted reports from data
---
```

```markdown
# Report Generation

When asked to generate a report:

1. Identify the data source and format
2. Extract key metrics and trends
3. Format the output as requested (PDF, markdown, etc.)
4. Include a summary section at the top

## Formatting Rules

- Use clear section headers
- Include data tables where appropriate
- Add a "Key Takeaways" section at the end
```

## Step 2: Test It

```bash
muxi dev
```

Ask your agent something that matches the skill:

```
You: Generate a summary report of our Q4 metrics
Assistant: [Activates report-generation skill, follows instructions]
```

The agent sees the skill in its catalog and activates it automatically when the request matches.


## Add References

Give the skill detailed reference material:

```bash
mkdir -p skills/report-generation/references
```

Create `skills/report-generation/references/templates.md`:

```markdown
# Report Templates

## Executive Summary Template
- Title and date
- 3-5 bullet key findings
- Metrics table
- Recommendations

## Detailed Analysis Template
- ...
```

References are listed as resources when the skill activates. The agent can request them as needed.


## Add Executable Scripts

Skills can include scripts that run in a sandboxed container via RCE.

### Create the script

```bash
mkdir -p skills/report-generation/scripts
```

Create `skills/report-generation/scripts/generate.py`:

```python
import sys
from reportlab.lib.pagesizes import letter
from reportlab.pdfgen import canvas

def main():
    output_path = "report.pdf"
    c = canvas.Canvas(output_path, pagesize=letter)
    c.drawString(100, 750, "Q4 Summary Report")
    c.save()
    print(f"Generated: {output_path}")

if __name__ == "__main__":
    main()
```

### Configure RCE

Add the RCE service to your formation:

```yaml
# formation.afs
rce:
  url: "http://localhost:6000"
  token: "${{ secrets.RCE_TOKEN }}"
```

Set the secret:

```bash
muxi secrets set RCE_TOKEN
```

Now the agent can execute `generate.py` via the `run_skill` tool.


## Agent-Specific Skills

Give a skill to only one agent:

```
my-formation/
└── agents/
    └── analyst/
        ├── analyst.afs
        └── skills/
            └── forecasting/
                └── SKILL.md
```

Only the `analyst` agent sees the `forecasting` skill. Other agents don't know it exists.


## Restrict Tool Access

Declare which tools a skill needs:

```yaml
---
name: report-generation
description: Generate formatted reports
allowed-tools: run_skill generate_file
---
```

The agent is restricted to these tools during skill execution.


## Final Directory Structure

```
my-formation/
├── formation.afs
├── agents/
│   ├── assistant.afs
│   └── analyst/
│       ├── analyst.afs
│       └── skills/
│           └── forecasting/
│               └── SKILL.md
├── skills/
│   └── report-generation/
│       ├── SKILL.md
│       ├── scripts/
│       │   └── generate.py
│       └── references/
│           └── templates.md
└── secrets.example
```


## Troubleshooting

### Skill Not Appearing

Check that `SKILL.md` exists and has a `description` field:

```yaml
---
name: my-skill
description: This field is required
---
```

### Scripts Not Running

1. Verify RCE is configured in `formation.afs`
2. Check the RCE service is running: `curl http://localhost:6000/health`
3. Ensure the skill has files in `scripts/`

### Agent Can't See Skill

- Formation-level skills (`skills/`): visible to all agents
- Agent-level skills (`agents/{id}/skills/`): visible only to that agent

Check the skill is in the right directory.


## Next Steps

- [Skills Reference](../reference/skills.md) - Full SKILL.md syntax
- [Skills Concepts](../concepts/skills.md) - How skills work
- [Add Tools (MCP)](add-mcp-tools.md) - External service integration
