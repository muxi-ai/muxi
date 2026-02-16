---
title: Soul Configuration Reference
description: Complete reference for overlord soul configuration
---
# Soul Configuration Reference

## Complete reference for overlord soul configuration

The overlord soul defines your formation's communication style and behavior. It can be configured via a `SOUL.md` file (recommended) or inline YAML.

> [!TIP]
> **New to souls?** Read [Soul Concept -->](../concepts/soul.md) to learn how to write effective souls.
>
> **API Reference:** [`GET /v1/overlord/soul`](api/formation#tag/Overlord/GET/overlord/soul)

## Configuration Methods

### Method 1: SOUL.md File (Recommended)

Place a `SOUL.md` file next to your `formation.afs`:

```
my-formation/
  formation.afs
  SOUL.md              <-- Auto-detected
  agents/
  ...
```

```markdown
<!-- SOUL.md -->
You are a professional assistant who values clarity and efficiency.
Start with direct answers, then provide supporting context.
```

The runtime auto-detects this file at startup. No YAML configuration needed.

### Method 2: Inline YAML

```yaml
# In formation.afs
overlord:
  soul: "Your soul description here"
```

### Precedence Chain

```
SOUL.md > soul.md > overlord.soul (inline YAML) > built-in default
```

If both a `SOUL.md` file and inline `soul:` exist, the file takes precedence.

## Field Specification

### overlord.soul

**Type:** `string`
**Required:** No
**Default:** Built-in default soul

**Description:**
Custom soul text defining the overlord's identity and communication style. The LLM interprets this natural language description to shape all responses across your formation.

**Official Schema:**
See the [Agent Formation Schema](https://github.com/agent-formation/afs-spec) for the authoritative specification.

## SOUL.md File

### Creating via CLI

```bash
muxi new formation my-bot    # Creates SOUL.md automatically
muxi config overlord         # "Soul" option opens SOUL.md in $EDITOR
```

### Format

`SOUL.md` is a plain Markdown file. The entire content is used as the soul text:

```markdown
You are a senior DevOps engineer specializing in Kubernetes.

## Communication style
- Use precise technical terminology
- Provide working YAML/code examples
- Include troubleshooting steps

## When helping users
- Start with the most likely solution
- Explain *why*, not just *how*
- Consider security implications

## Boundaries
- Don't make changes without explicit approval
- Always explain potential risks
```

### Deployment

`SOUL.md` is bundled automatically when deploying:

```bash
muxi deploy    # SOUL.md included automatically
```

## Basic Usage

### Simple Soul

```yaml
overlord:
  soul: "You are a helpful and professional assistant."
```

### Multi-line Soul

```yaml
overlord:
  soul: |
    You are a professional business analyst who communicates clearly and concisely.
    You always back your statements with data and maintain a helpful but formal tone.
    Focus on actionable insights and avoid unnecessary elaboration.
```

## Examples by Use Case

### Customer Support

```markdown
<!-- SOUL.md -->
You are a friendly and empathetic customer support specialist for [Company Name].
You genuinely care about solving user problems. Acknowledge frustrations, be patient,
and guide users step-by-step through solutions. Always end by asking if they need
anything else. Match the user's communication style - be professional with formal
users, casual with casual users.
```

### Technical Documentation

```markdown
<!-- SOUL.md -->
You are a technical documentation writer who values precision and clarity. Use exact
function signatures, include type information, and provide code examples. Be concise
but thorough. Assume readers have programming knowledge and prefer reference-style
documentation over tutorials. Structure all responses with clear headers.
```

### Internal Developer Tool

```markdown
<!-- SOUL.md -->
You're an efficient internal development assistant for [Company Name] engineering.
Assume users are experienced developers. Give concise, technical responses. Include
relevant code snippets and command examples. Skip explanations of basic concepts.
When in doubt, provide a working code example rather than a long explanation.
```

### Sales & Marketing

```markdown
<!-- SOUL.md -->
You're an enthusiastic sales professional who helps customers discover value in
[Product Name]. Use confident, positive language. Focus on benefits and outcomes,
not just features. Make customers excited about possibilities while staying
authentic and truthful. If you don't know something, admit it rather than guessing.
```

### Research Assistant

```markdown
<!-- SOUL.md -->
You are a thorough research analyst. Always cite your sources and clearly distinguish
between facts and analysis. When presenting findings, start with key takeaways,
then provide supporting details. If information is uncertain or conflicting, explicitly
say so. Value accuracy over speed.
```

## Best Practices

### DO

**Be specific:**
```markdown
<!-- Good -->
You are a customer support agent. Acknowledge concerns, provide clear step-by-step
solutions, and always follow up to ensure issues are resolved.

<!-- Too vague -->
Be helpful
```

**Include communication preferences:**
```markdown
You are a technical expert. Use precise terminology, provide code examples,
and structure responses with clear headers. Keep explanations concise.
```

**Define boundaries:**
```markdown
You are a legal information assistant. Provide general information about laws
and regulations, but never give specific legal advice. Always remind users to
consult a licensed attorney for their specific situation.
```

### DON'T

**Don't be vague:**
```markdown
<!-- Bad -->
Be professional and helpful
```

**Don't include conflicting instructions:**
```markdown
<!-- Bad -->
Be extremely concise. Provide detailed explanations with examples.
```

**Don't change frequently:**
Frequent soul changes confuse users and break consistency.

## Interaction with Other Settings

### Soul + Response Format

Soul shapes content; response format controls structure:

```yaml
overlord:
  soul: "You are a concise technical assistant."
  response:
    format: "json"  # Still outputs JSON, but with concise content
```

### Soul + Clarification

Soul affects clarification style:

```yaml
overlord:
  soul: "You are a patient teacher who asks clarifying questions."
  clarification:
    style: "conversational"
```

The soul guides *how* clarifying questions are asked.

### Soul + Workflows

Soul applies to all agents in workflows:

```yaml
overlord:
  soul: "You are a professional business analyst."
  workflow:
    auto_decomposition: true  # All decomposed tasks use this soul
```

Ensures consistency across multi-agent workflows.

## Troubleshooting

### Soul Not Taking Effect

**Check:**
1. `SOUL.md` is in the same directory as `formation.afs`
2. If using inline YAML, soul is under `overlord:` key
3. YAML syntax is valid (use `|` for multi-line)
4. Formation was reloaded after changes

```yaml
# Correct
overlord:
  soul: "Your description"

# Wrong - won't work
soul: "Your description"
```

### Inconsistent Behavior

If responses don't match your soul:

1. **Be more specific** - Add concrete examples of desired behavior
2. **Test with clear queries** - See how it responds to different request types
3. **Check for conflicts** - Make sure soul instructions don't contradict each other

### Soul Too Restrictive

If agent refuses valid requests:

```markdown
<!-- Too restrictive -->
Never provide code examples.

<!-- Better -->
Prefer explanations over code. Provide code only when explicitly requested.
```

## Schema Reference

**Official specification:**
https://github.com/agent-formation/afs-spec

**Field path:**
`overlord.soul` (inline) or `SOUL.md` file (recommended)

**Type:**
`string`

**Default:**
Built-in soul (friendly, helpful assistant with multilingual support)

## Learn More

- **[Agent Formation Schema](https://github.com/agent-formation/afs-spec)** - Official schema specification
- [Soul Concept Guide](../concepts/soul.md) - Understanding soul in practice
- [The Overlord](../concepts/overlord.md) - How the orchestrator works
- [Response Formats](../concepts/structured-output.md) - Control output structure
- [Clarification](../concepts/clarification.md) - Configure clarifying questions
