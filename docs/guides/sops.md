# Write SOPs

Create standard operating procedures for consistent workflows.

## Overview

SOPs define repeatable workflows that trigger automatically when detected.

## Step 1: Create SOP

```bash
muxi new sop customer-onboarding
```

Creates `sops/customer-onboarding.md`:

```markdown
---
name: Customer Onboarding
tags: [customer, onboarding, new user]
triggers:
  keywords: [new customer, onboard, setup customer]
---

# Customer Onboarding Procedure

1. Collect customer information:
   - Company name
   - Primary contact
   - Email address

2. Create account in system

3. Send welcome email with:
   - Login credentials
   - Getting started guide

4. Schedule kickoff call

5. Document in CRM
```

## Step 2: Test

```bash
muxi dev
```

Chat:

```
You: I need to onboard a new customer
Assistant: I'll follow the customer onboarding procedure...
[Executes SOP steps]
```

## SOP Structure

### Frontmatter

```yaml
---
name: Display Name
tags: [tag1, tag2]
triggers:
  keywords: [phrase1, phrase2]
---
```

### Body

Write as numbered steps:

```markdown
# Procedure Name

1. First step
2. Second step
3. Third step

## Expected Output

Describe what should result.
```

## Matching

SOPs match via:

1. **Keywords** - Exact phrase match
2. **Tags** - Semantic similarity
3. **Content** - Semantic search (0.7 threshold)

## Examples

### Bug Triage

```markdown
---
name: Bug Triage
tags: [bug, issue, priority]
triggers:
  keywords: [triage bug, prioritize issue]
---

# Bug Triage Procedure

1. Analyze the bug report

2. Assess impact:
   - Users affected
   - Severity
   - Workaround available?

3. Assign priority:
   - P0: Critical
   - P1: High
   - P2: Medium
   - P3: Low

4. Create tracking issue
```

### Weekly Report

```markdown
---
name: Weekly Report
tags: [weekly, summary, report]
triggers:
  keywords: [weekly report, week summary]
---

# Weekly Report Procedure

1. Gather data from past week

2. Identify highlights:
   - Accomplishments
   - Challenges
   - Lessons

3. Note blockers

4. Plan next week

5. Format as executive summary
```

## SOP Priority

SOPs have highest routing priority:
- Bypass complexity analysis
- Bypass approval flows
- Execute immediately when matched

## Testing SOPs

```bash
# Check if SOP triggers
muxi chat "onboard a new customer"

# Should trigger customer-onboarding SOP
```

## Next Steps

- [SOPs Reference](../formations/sops.md) - Full details
- [Multi-Agent Systems](multi-agent.md) - Complex workflows
