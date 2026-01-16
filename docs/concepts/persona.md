---
title: Overlord Persona
description: Configure MUXI's personality, communication style, and tone
---
# Overlord Persona

## Configure MUXI's personality, communication style, and tone

The Overlord persona defines how MUXI communicates with users. Since users talk to MUXI (not individual agents), this persona shapes the entire user experience.

> [!IMPORTANT]
> As the main entity users users interact with, the Overlord uses its persona to synthesize all communication with the same tone and style.

## Why Persona Matters

Users interact with MUXI as a single entity. The persona ensures consistent communication:

```
Without persona:
  Response varies based on which agent handled the task
  Inconsistent tone, style, formatting

With persona:
  All responses filtered through Overlord's persona
  Consistent voice across all interactions
```

---

## How It Works

The Overlord synthesizes agent outputs and applies the persona before responding:

```
Agent outputs (raw):
  Researcher: "Data indicates 15% growth in Q3"
  Analyst: "Trend analysis shows positive trajectory"
         ↓
Overlord synthesizes + applies persona
         ↓
User sees (with persona):
  "Great news! Your Q3 growth hit 15%, and the trend looks strong."
```

---

## Configuration

```yaml
overlord:
  persona: |
    You are a professional, helpful assistant.
    Be concise and data-driven.
    Use clear language without jargon.
```

The persona is a freeform text description that the LLM interprets.

---

## Persona Examples

### Professional Business

```yaml
overlord:
  persona: |
    You are a professional business analyst. Communicate clearly and concisely,
    back statements with data, and maintain a helpful but formal tone.
    Focus on actionable insights.
```

**Output style:**
```
I've analyzed your data and identified three key trends:
1. Revenue increased 15% quarter-over-quarter
2. Customer acquisition costs decreased 8%
3. Retention rates improved to 94%
```

### Friendly & Casual

```yaml
overlord:
  persona: |
    You're a friendly assistant who explains things simply.
    Use conversational language and help users feel comfortable.
    Be knowledgeable but never condescending.
```

**Output style:**
```
Great news! Your numbers look really solid this quarter -
revenue's up 15% and you're keeping 94% of your customers!
```

### Technical Expert

```yaml
overlord:
  persona: |
    You are a technical expert who values precision.
    Use exact numbers, technical terminology, and structured formats.
    Assume the user has technical knowledge.
```

**Output style:**
```
Analysis results (Q3 2024):
- Revenue: +15% QoQ ($2.3M → $2.65M)
- CAC: -8% ($120 → $110)
- Retention: 94% (industry avg: 87%)
```

---

## Controlling Verbosity

### Concise
```yaml
overlord:
  persona: |
    Be efficient and concise. Direct answers, no fluff.
    Use bullet points and short sentences.
```

### Detailed
```yaml
overlord:
  persona: |
    Provide thorough explanations. Walk through processes step-by-step,
    explain reasoning, and offer to elaborate further.
```

---

## Agents vs Overlord

| Overlord | Agents |
|----------|--------|
| Has **persona** | Has **system prompt** |
| Shapes user-facing communication | Shapes task execution |
| One persona for all responses | Different instructions per agent |
| User talks to Overlord | Overlord delegates to agents |

### Agent System Prompts

Agents have system prompts that instruct them on their role:

```yaml
# agents/researcher.afs
system_message: |
  You are a research specialist. Your job is to find accurate,
  well-sourced information. Focus on facts, cite sources,
  and be thorough in your research.
```

This affects how the agent performs tasks, but the **Overlord's persona** shapes how results are communicated to users.

### Agent Capabilities

Agents also have capabilities metadata that the Overlord uses for routing:

```yaml
# agents/researcher.afs
role: researcher
specialties:
  - web research
  - data gathering
  - fact checking
```

This is routing metadata, not personality.

---

## Writing Effective Personas

### Be Specific

```yaml
# ❌ Too vague
persona: "Be helpful"

# ✅ Specific
persona: |
  You are a helpful customer support agent. Acknowledge concerns,
  provide clear step-by-step solutions, use friendly but professional
  language.
```

### Include Communication Preferences

```yaml
persona: |
  Use precise terminology. Provide code examples.
  Structure responses with clear headers.
  Keep explanations concise.
```

### Define Boundaries

```yaml
persona: |
  You are a financial information assistant.
  Provide education about financial concepts.
  Never give specific investment advice.
  Always remind users to consult licensed professionals.
```

---

## Best Practices

**DO:**
- ✅ Be specific about tone, style, and formatting
- ✅ Test with real queries
- ✅ Match your brand voice
- ✅ Define what NOT to do (constraints work better than aspirations)

**DON'T:**
- ❌ Be too vague
- ❌ Mix conflicting instructions
- ❌ Expect agents to have personas (only Overlord does)

---

## Learn More

- [The Overlord](overlord.md) - How the Overlord works
- [Agents & Orchestration](agents-and-orchestration.md) - Agent system prompts
- [Structured Output](structured-output.md) - Control output format
