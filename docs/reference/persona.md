---
title: Persona Configuration
description: Complete reference for persona and personality settings
---
# Persona Configuration

## Complete reference for persona and personality settings

Configure agent personality, communication style, tone, and format preferences. This reference covers all available options.

## Configuration Location

```yaml
# In formation.yaml
overlord:
  persona:
    # Persona settings here
```

## Basic Settings

### name
```yaml
persona:
  name: "Assistant"
```

**Type:** `string`  
**Default:** `"Assistant"`  
**Description:** Agent's display name

### style
```yaml
persona:
  style: "professional"
```

**Type:** `string`  
**Options:**
- `professional` - Formal, business-appropriate
- `casual` - Relaxed, conversational
- `technical` - Precise, technical terminology
- `friendly` - Warm, approachable

**Default:** `"professional"`

### tone
```yaml
persona:
  tone: "helpful"
```

**Type:** `string`  
**Options:**
- `helpful` - Supportive and guiding
- `empathetic` - Understanding and caring
- `enthusiastic` - Energetic and positive
- `precise` - Exact and detailed
- `efficient` - Direct and to-the-point

**Default:** `"helpful"`

### traits
```yaml
persona:
  traits:
    - knowledgeable
    - efficient
    - precise
```

**Type:** `array<string>`  
**Options:**
- `knowledgeable` - Shows expertise
- `efficient` - Gets to the point
- `precise` - Uses exact numbers/details
- `empathetic` - Acknowledges feelings
- `playful` - Uses humor/creativity
- `confident` - Assertive recommendations
- `humble` - Acknowledges limitations
- `enthusiastic` - Shows excitement
- `patient` - Takes time to explain
- `curious` - Asks clarifying questions

**Default:** `[]` (no traits)  
**Recommendation:** 2-3 traits maximum

## Communication Preferences

```yaml
persona:
  communication_preferences:
    use_examples: true
    explain_reasoning: false
    include_confidence: false
    max_verbosity: "balanced"
```

### use_examples
```yaml
use_examples: true
```

**Type:** `boolean`  
**Default:** `true`  
**Description:** Include examples in explanations

**Example:**
```
With examples (true):
"To deploy, run: kubectl apply -f deployment.yaml
 For example: kubectl apply -f api-deployment.yaml"

Without examples (false):
"To deploy, run: kubectl apply -f deployment.yaml"
```

### explain_reasoning
```yaml
explain_reasoning: false
```

**Type:** `boolean`  
**Default:** `false`  
**Description:** Explain why decisions were made

**Example:**
```
With reasoning (true):
"I recommend Option A because it's more cost-effective
 and scales better for your use case."

Without reasoning (false):
"I recommend Option A."
```

### include_confidence
```yaml
include_confidence: false
```

**Type:** `boolean`  
**Default:** `false`  
**Description:** Show confidence level in responses

**Example:**
```
With confidence (true):
"I'm 95% confident that the issue is caused by the API timeout."

Without confidence (false):
"The issue is likely caused by the API timeout."
```

### max_verbosity
```yaml
max_verbosity: "balanced"
```

**Type:** `string`  
**Options:**
- `concise` - Minimal, direct responses
- `balanced` - Moderate detail
- `detailed` - Comprehensive explanations

**Default:** `"balanced"`

**Examples:**
```yaml
# Concise
"Task complete. 3 files processed."

# Balanced
"I've completed the task. Processed 3 files successfully."

# Detailed
"I've completed the task you requested. Here's what I did:
 1. Processed file1.csv (1,234 rows)
 2. Processed file2.json (567 entries)
 3. Processed file3.txt (89 lines)
 All files processed successfully with zero errors."
```

## Personalization

### adapt_to_user
```yaml
persona:
  adapt_to_user: true
```

**Type:** `boolean`  
**Default:** `false`  
**Description:** Learn and adapt to user's communication style

**Behavior:**
- Learns from user's language (formal vs casual)
- Matches user's verbosity
- Adapts to technical level
- Remembers user preferences

**Example:**
```
User 1 (formal): "Please provide analysis"
→ Agent: "I've prepared the analysis as requested..."

User 2 (casual): "what's up with the data?"
→ Agent: "Hey! Let me break down the data for you..."
```

### learn_from_feedback
```yaml
persona:
  learn_from_feedback: true
```

**Type:** `boolean`  
**Default:** `false`  
**Description:** Adjust based on user feedback

**Requires:** `adapt_to_user: true`

## Context-Specific Personas

```yaml
persona:
  default:
    style: "professional"
    tone: "helpful"

  contexts:
    error_handling:
      tone: "empathetic"
      traits: [patient, helpful]

    celebrations:
      tone: "enthusiastic"
      traits: [encouraging, friendly]

    warnings:
      tone: "serious"
      traits: [precise, helpful]
```

### Context Options

**Available contexts:**
- `error_handling` - When errors occur
- `celebrations` - On successful completions
- `warnings` - For warnings/alerts
- `technical` - For technical discussions
- `onboarding` - For new users

## Complete Example

```yaml
overlord:
  persona:
    name: "Assistant"
    style: "professional"
    tone: "helpful"
    
    traits:
      - knowledgeable
      - efficient
      - empathetic
    
    communication_preferences:
      use_examples: true
      explain_reasoning: false
      include_confidence: false
      max_verbosity: "balanced"
    
    adapt_to_user: true
    learn_from_feedback: true
    
    contexts:
      error_handling:
        tone: "empathetic"
        traits: [patient, helpful]
        communication_preferences:
          explain_reasoning: true
          max_verbosity: "detailed"
      
      celebrations:
        tone: "enthusiastic"
        traits: [encouraging, friendly]
        communication_preferences:
          max_verbosity: "concise"
```

## Style Presets

### Professional (Default)
```yaml
persona:
  style: "professional"
  tone: "helpful"
  traits: [knowledgeable, efficient]
  communication_preferences:
    max_verbosity: "balanced"
```

### Casual
```yaml
persona:
  style: "casual"
  tone: "friendly"
  traits: [helpful, playful]
  communication_preferences:
    use_examples: true
    max_verbosity: "balanced"
```

### Technical
```yaml
persona:
  style: "technical"
  tone: "precise"
  traits: [knowledgeable, precise, efficient]
  communication_preferences:
    use_examples: false
    explain_reasoning: false
    max_verbosity: "concise"
```

### Support
```yaml
persona:
  style: "friendly"
  tone: "empathetic"
  traits: [helpful, patient, knowledgeable]
  communication_preferences:
    use_examples: true
    explain_reasoning: true
    max_verbosity: "detailed"
```

## Environment Variables

```bash
# Override persona settings via environment
MUXI_PERSONA_STYLE=casual
MUXI_PERSONA_TONE=friendly
MUXI_PERSONA_VERBOSITY=concise
```

## Validation

**Valid configuration:**
```yaml
persona:
  style: "professional"  # ✓ Valid option
  tone: "helpful"        # ✓ Valid option
  traits:                # ✓ Array of strings
    - knowledgeable
    - efficient
```

**Invalid configuration:**
```yaml
persona:
  style: "fancy"         # ✗ Invalid (not in options)
  tone: 123              # ✗ Invalid (must be string)
  traits: "helpful"      # ✗ Invalid (must be array)
```

## Testing Personas

**Test different personas:**

```bash
# Terminal 1: Professional
muxi chat --persona-style professional "Explain APIs"

# Terminal 2: Casual
muxi chat --persona-style casual "Explain APIs"

# Terminal 3: Technical
muxi chat --persona-style technical "Explain APIs"
```

## Migration

**From older versions:**

```yaml
# Old (pre-1.0)
overlord:
  system_prompt: "You are a helpful assistant"
  
# New (1.0+)
overlord:
  persona:
    name: "Assistant"
    style: "professional"
    tone: "helpful"
```

## Troubleshooting

### Persona Not Applied
```yaml
# Check overlord is enabled
overlord:
  enabled: true        # Must be true
  persona:
    style: "casual"
```

### Inconsistent Responses
```yaml
# Ensure persona is at overlord level (not agent level)
overlord:              # ✓ Correct
  persona:
    style: "professional"

agents:
  - name: researcher
    persona:           # ✗ Wrong (ignored)
      style: "casual"
```

### User Adaptation Not Working
```yaml
# Requires both settings
persona:
  adapt_to_user: true           # ✓ Must be true
  learn_from_feedback: true     # ✓ Enable this too
```

## Learn More

- [Persona & Personality Concept](../concepts/persona.md) - User-facing explanation
- [The Overlord](../concepts/overlord.md) - How orchestration works
- [Request Lifecycle](../deep-dives/request-lifecycle.md) - See persona application
