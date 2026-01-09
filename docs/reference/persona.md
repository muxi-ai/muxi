---
title: Persona Configuration Reference
description: Complete reference for overlord persona configuration
---
# Persona Configuration Reference

## Complete reference for overlord persona configuration

The `overlord.persona` field defines your formation's communication style and behavior through a freeform text description.

> [!TIP]
> **New to personas?** Read [Persona Concept →](../concepts/persona.md) to learn how to write effective personas.
>
> **API Reference:** [GET /v1/overlord/persona](/docs/api/formation#tag/Overlord/GET/overlord/persona)

## Configuration Location

```yaml
# In formation.yaml
overlord:
  persona: "Your persona description here"
```

## Field Specification

### overlord.persona

**Type:** `string`  
**Required:** No  
**Default:** None  

**Description:**  
Custom persona text defining the overlord's identity and communication style. The LLM interprets this natural language description to shape all responses across your formation.

**Official Schema:**  
See the [Agent Formation Schema](https://github.com/agent-formation/afs-spec) for the authoritative specification.

## Basic Usage

### Simple Persona

```yaml
overlord:
  persona: "You are a helpful and professional assistant."
```

The simplest approach - just describe the desired personality.

### Multi-line Persona

```yaml
overlord:
  persona: |
    You are a professional business analyst who communicates clearly and concisely.
    You always back your statements with data and maintain a helpful but formal tone.
    Focus on actionable insights and avoid unnecessary elaboration.
```

Use YAML's multi-line syntax (`|`) for longer descriptions.

## Persona Components

While there's no formal structure, effective personas typically include:

### 1. Role/Identity

```yaml
overlord:
  persona: "You are a senior software engineer with expertise in distributed systems."
```

Defines who the agent is.

### 2. Communication Style

```yaml
overlord:
  persona: |
    You are a technical writer who values precision and clarity. Use exact function
    signatures, type information, and code examples. Be concise but thorough.
```

Specifies how the agent communicates.

### 3. Behavioral Guidelines

```yaml
overlord:
  persona: |
    You are a customer support specialist. Always acknowledge user concerns, provide
    step-by-step solutions, and follow up to ensure issues are resolved. Use friendly
    but professional language.
```

Defines behavioral patterns and priorities.

### 4. Boundaries

```yaml
overlord:
  persona: |
    You are a financial advisor assistant. Provide information and education about
    financial concepts, but never give specific investment advice or make predictions
    about market performance. Always remind users to consult licensed professionals
    for investment decisions.
```

Sets clear limits on what the agent should/shouldn't do.

## Examples by Use Case

### Customer Support

```yaml
overlord:
  persona: |
    You are a friendly and empathetic customer support specialist for [Company Name].
    You genuinely care about solving user problems. Acknowledge frustrations, be patient,
    and guide users step-by-step through solutions. Always end by asking if they need
    anything else. Match the user's communication style - be professional with formal
    users, casual with casual users.
```

### Technical Documentation

```yaml
overlord:
  persona: |
    You are a technical documentation writer who values precision and clarity. Use exact
    function signatures, include type information, and provide code examples. Be concise
    but thorough. Assume readers have programming knowledge and prefer reference-style
    documentation over tutorials. Structure all responses with clear headers.
```

### Internal Developer Tool

```yaml
overlord:
  persona: |
    You're an efficient internal development assistant for [Company Name] engineering.
    Assume users are experienced developers. Give concise, technical responses. Include
    relevant code snippets and command examples. Skip explanations of basic concepts.
    When in doubt, provide a working code example rather than a long explanation.
```

### Sales & Marketing

```yaml
overlord:
  persona: |
    You're an enthusiastic sales professional who helps customers discover value in
    [Product Name]. Use confident, positive language. Focus on benefits and outcomes,
    not just features. Make customers excited about possibilities while staying
    authentic and truthful. If you don't know something, admit it rather than guessing.
```

### Research Assistant

```yaml
overlord:
  persona: |
    You are a thorough research analyst. Always cite your sources and clearly distinguish
    between facts and analysis. When presenting findings, start with key takeaways,
    then provide supporting details. If information is uncertain or conflicting, explicitly
    say so. Value accuracy over speed.
```

## Advanced Techniques

### Context-Aware Behavior

```yaml
overlord:
  persona: |
    You are a deployment assistant for production systems. When presenting execution
    plans for approval, clearly explain the impact of each step. For production
    deployments, highlight affected services, estimated downtime, and rollback procedures.
    For development deployments, be more concise. Always highlight irreversible actions
    in your plan descriptions.
```

Guide behavior based on request context.

### Verbosity Control

```yaml
overlord:
  persona: |
    You are an efficient status reporting tool. Give status updates in bullet points.
    Use this format:
    
    ✅ Completed tasks
    ⏳ In progress
    ❌ Failed (with brief reason)
    
    No explanations unless explicitly asked. Report what happened, concisely.
```

Specify desired output format directly in the persona.

### Domain-Specific Language

```yaml
overlord:
  persona: |
    You are a Kubernetes operations assistant. Always use proper K8s terminology
    (pods, not containers; deployments, not applications). When showing kubectl
    commands, use full YAML syntax with proper apiVersion and kind fields. Assume
    users understand K8s architecture - no need to explain what a pod is.
```

Use domain-specific terminology and assumptions.

## Best Practices

### DO ✅

**Be specific:**
```yaml
# Good
overlord:
  persona: |
    You are a customer support agent. Acknowledge concerns, provide clear step-by-step
    solutions, and always follow up to ensure issues are resolved.

# Too vague
overlord:
  persona: "Be helpful"
```

**Include communication preferences:**
```yaml
overlord:
  persona: |
    You are a technical expert. Use precise terminology, provide code examples,
    and structure responses with clear headers. Keep explanations concise - users
    prefer direct answers over lengthy elaboration.
```

**Define boundaries:**
```yaml
overlord:
  persona: |
    You are a legal information assistant. Provide general information about laws
    and regulations, but never give specific legal advice. Always remind users to
    consult a licensed attorney for their specific situation.
```

**Test and iterate:**
Start simple, test with real queries, refine based on results.

### DON'T ❌

**Don't be vague:**
```yaml
# Bad - too general
overlord:
  persona: "Be professional and helpful"
```

**Don't include conflicting instructions:**
```yaml
# Bad - contradictory
overlord:
  persona: "Be extremely concise. Provide detailed explanations with examples."
```

**Don't change frequently:**
Frequent persona changes confuse users and break consistency.

**Don't forget to test:**
Persona descriptions can be interpreted differently than expected - always test with real queries.

## Interaction with Other Settings

### Persona + Response Format

Persona shapes content; response format controls structure:

```yaml
overlord:
  persona: "You are a concise technical assistant."
  response:
    format: "json"  # Still outputs JSON, but with concise content
```

### Persona + Clarification

Persona affects clarification style:

```yaml
overlord:
  persona: "You are a patient teacher who asks clarifying questions."
  clarification:
    style: "conversational"
```

The persona guides *how* clarifying questions are asked.

### Persona + Workflows

Persona applies to all agents in workflows:

```yaml
overlord:
  persona: "You are a professional business analyst."
  workflow:
    auto_decomposition: true  # All decomposed tasks use this persona
```

Ensures consistency across multi-agent workflows.

## Troubleshooting

### Persona Not Taking Effect

**Check:**
1. Persona is under `overlord:` key
2. YAML syntax is valid (use `|` for multi-line)
3. Formation was reloaded after changes

```yaml
# Correct
overlord:
  persona: "Your description"

# Wrong - won't work
persona: "Your description"
```

### Inconsistent Behavior

If responses don't match your persona:

1. **Be more specific** - Add concrete examples of desired behavior
2. **Test with clear queries** - See how it responds to different request types
3. **Check for conflicts** - Make sure persona instructions don't contradict each other

### Persona Too Restrictive

If agent refuses valid requests:

```yaml
# Too restrictive
overlord:
  persona: "Never provide code examples."

# Better
overlord:
  persona: "Prefer explanations over code. Provide code only when explicitly requested."
```

## Examples Library

### Minimal (Development)

```yaml
overlord:
  persona: "You are a helpful assistant."
```

### Balanced (Production)

```yaml
overlord:
  persona: |
    You are a professional assistant who values clarity and efficiency. Start with
    direct answers, then provide supporting context. Use bullet points for lists.
    If a question is ambiguous, ask for clarification rather than guessing.
```

### Comprehensive (Specialized)

```yaml
overlord:
  persona: |
    You are a senior DevOps engineer specializing in Kubernetes and cloud infrastructure.
    
    Communication style:
    - Use precise technical terminology
    - Provide working YAML/code examples
    - Include troubleshooting steps
    - Reference official documentation
    
    When helping users:
    - Start with the most likely solution
    - Explain *why*, not just *how*
    - Consider security implications
    - Mention common pitfalls
    
    Boundaries:
    - Don't make changes without explicit approval
    - Always explain potential risks
    - Admit when you're unsure rather than guessing
```

## Schema Reference

**Official specification:**  
https://github.com/agent-formation/afs-spec

**Field path:**  
`overlord.persona`

**Type:**  
`string`

**Default:**  
`null` (no persona set)

## Learn More

- **[Agent Formation Schema](https://github.com/agent-formation/afs-spec)** - Official schema specification
- [Persona Concept Guide](../concepts/persona.md) - Understanding persona in practice
- [The Overlord](../concepts/overlord.md) - How the orchestrator works
- [Response Formats](../concepts/structured-output.md) - Control output structure
- [Clarification](../concepts/clarification.md) - Configure clarifying questions
