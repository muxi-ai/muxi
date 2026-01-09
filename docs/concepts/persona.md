---
title: Persona & Personality
description: Configure agent personality, communication style, and tone
---
# Persona & Personality

## Configure agent personality, communication style, and tone

Give your agents a consistent voice. Configure personality, tone, communication style - MUXI ensures every response matches your brand and user preferences, regardless of which agents are involved.

## Why Persona Matters

**Without persona configuration:**
```
Agent A: "Analysis complete. Data indicates..."
Agent B: "Hey! I found some cool stuff in the data..."
Agent C: "The analytical results demonstrate..."
```
Inconsistent, confusing experience.

**With persona:**
```
All agents: "I've analyzed the data for you. Here's what I found..."
```
Consistent, professional, branded experience.

## What Persona Controls

**Tone:**
- Professional vs casual
- Formal vs friendly
- Technical vs accessible

**Style:**
- Verbose vs concise
- Active vs passive voice
- Use of examples and analogies

**Format:**
- Markdown structure
- Header usage
- Code block formatting

**Personality Traits:**
- Helpful, efficient, knowledgeable
- Playful, serious, empathetic
- Confident, humble, enthusiastic

## Configuration

```yaml
overlord:
  persona:
    name: "Assistant"
    style: "professional"  # professional, casual, technical, friendly
    tone: "helpful"
    traits:
      - knowledgeable
      - efficient
      - precise
    communication_preferences:
      use_examples: true
      explain_reasoning: false
      include_confidence: false
      max_verbosity: "balanced"  # concise, balanced, detailed
```

## Style Options

### Professional
```yaml
persona:
  style: "professional"
  tone: "helpful"
```

**Output:**
```
I've analyzed your data and identified three key trends:
1. Revenue increased 15% quarter-over-quarter
2. Customer acquisition costs decreased 8%
3. Retention rates improved to 94%
```

### Casual
```yaml
persona:
  style: "casual"
  tone: "friendly"
```

**Output:**
```
Great news! Your numbers are looking really solid:
- Revenue's up 15% from last quarter
- You're spending 8% less to get new customers
- And you're keeping 94% of them around!
```

### Technical
```yaml
persona:
  style: "technical"
  tone: "precise"
```

**Output:**
```
Analysis results (Q3 2024):
- Revenue: +15% QoQ ($2.3M → $2.65M)
- CAC: -8% ($120 → $110)
- Retention: 94% (industry avg: 87%)
```

### Friendly
```yaml
persona:
  style: "friendly"
  tone: "enthusiastic"
```

**Output:**
```
Awesome news! 🎉 Your business is crushing it:
- Revenue jumped 15% this quarter
- It's costing less to bring in new customers
- And almost everyone's sticking around (94%!)
```

## Verbosity Levels

### Concise
```yaml
communication_preferences:
  max_verbosity: "concise"
```

**Output:**
```
Task completed. 3 files processed, 0 errors.
```

### Balanced
```yaml
communication_preferences:
  max_verbosity: "balanced"
```

**Output:**
```
I've completed the task. Processed 3 files successfully with no errors.
The results are ready for review.
```

### Detailed
```yaml
communication_preferences:
  max_verbosity: "detailed"
```

**Output:**
```
I've completed the task you requested. Here's what I did:

1. Processed file1.csv (1,234 rows)
2. Processed file2.json (567 entries)
3. Processed file3.txt (89 lines)

All files were processed successfully with zero errors. The results have
been compiled and are ready for your review. Would you like me to explain
the processing steps in more detail?
```

## Personality Traits

Add traits to influence response style:

```yaml
traits:
  - knowledgeable    # Shows expertise
  - efficient        # Gets to the point
  - precise          # Uses exact numbers
  - empathetic       # Acknowledges user feelings
  - playful          # Uses humor/creativity
  - confident        # Assertive recommendations
  - humble           # Acknowledges limitations
  - enthusiastic     # Shows excitement
```

**Example with different traits:**

**Knowledgeable + Precise:**
```
Based on industry benchmarks, your 94% retention rate significantly
outperforms the 87% average, placing you in the top quartile.
```

**Empathetic + Helpful:**
```
I can see you're concerned about retention. The good news is you're doing
great at 94% - that's well above the industry average. Let me help you
understand what's working.
```

**Enthusiastic + Playful:**
```
Wow, 94% retention? You're basically a customer magnet! 🧲 That's way
better than the competition. Want to know your secret sauce?
```

## Per-User Personalization

Adapt persona based on user preferences:

```yaml
overlord:
  persona:
    adapt_to_user: true  # Learn user communication style
```

When enabled:
- Learns from user's language (formal vs casual)
- Matches user's verbosity
- Adapts to user's technical level
- Remembers user preferences

**Example:**

```
User 1 (formal): "Please provide quarterly analysis"
Response: "I've prepared the quarterly analysis as requested..."

User 2 (casual): "hey what's up with Q3 numbers?"
Response: "Hey! Let me break down Q3 for you..."
```

## How Persona is Applied

**The process:**

```
1. Agent generates response
   "The analysis has been completed. Results show revenue increase of 15%."

2. Overlord analyzes persona configuration
   style: professional, tone: helpful, verbosity: balanced

3. Transform response
   "I've analyzed the data for you. Revenue increased 15% this quarter."

4. Apply personality traits
   (knowledgeable + helpful)
   "I've analyzed the data for you. Revenue increased 15% this quarter,
    which is a strong performance compared to industry benchmarks."

5. Return to user
   Consistent branded experience
```

## Consistency Across Agents

**Same request, different agents:**

```
Without persona:
Researcher: "Research indicates positive trend."
Writer: "Hey! The trend looks great!"
Analyst: "Trend vector: +15.2% YoY."

With persona (professional):
All agents: "The analysis shows a positive trend with 15% growth this year."
```

## Use Cases

### Customer Support (Empathetic + Helpful)
```yaml
persona:
  style: "friendly"
  tone: "empathetic"
  traits: [helpful, patient, knowledgeable]
```

**Response:**
```
I understand this is frustrating. Let me help you resolve this right away.
I've found the issue and here's what we'll do...
```

### Technical Documentation (Precise + Knowledgeable)
```yaml
persona:
  style: "technical"
  tone: "precise"
  traits: [knowledgeable, efficient]
```

**Response:**
```
Function signature: `analyze(data: DataFrame, threshold: float = 0.05)`
Returns: Dict[str, Any] containing analysis results and metadata.
```

### Sales & Marketing (Enthusiastic + Confident)
```yaml
persona:
  style: "friendly"
  tone: "enthusiastic"
  traits: [confident, persuasive, helpful]
```

**Response:**
```
This is a game-changer! Your product perfectly addresses the market need.
Here's why customers will love it...
```

### Internal Tools (Efficient + Concise)
```yaml
persona:
  style: "casual"
  tone: "efficient"
  traits: [efficient, precise]
  communication_preferences:
    max_verbosity: "concise"
```

**Response:**
```
Done. 3 issues created, 2 PRs merged, 1 deployment triggered.
```

## Configuration Tips

### 1. Match Your Brand
```yaml
# Professional company
persona:
  style: "professional"
  tone: "helpful"
  traits: [knowledgeable, efficient]

# Startup/casual
persona:
  style: "friendly"
  tone: "enthusiastic"
  traits: [helpful, playful]
```

### 2. Consider Your Audience
```yaml
# Technical audience
persona:
  style: "technical"
  tone: "precise"
  communication_preferences:
    use_examples: false
    explain_reasoning: false

# Non-technical audience
persona:
  style: "friendly"
  tone: "helpful"
  communication_preferences:
    use_examples: true
    explain_reasoning: true
```

### 3. Balance Personality
```yaml
# Too much personality can be annoying
traits: [enthusiastic, playful, casual]  # Might be too much

# Balanced approach
traits: [helpful, knowledgeable, friendly]  # Better balance
```

### 4. Test Different Styles
Start with a default persona, then adjust based on user feedback:

```yaml
# Week 1: Test professional
persona:
  style: "professional"

# Week 2: Test friendly
persona:
  style: "friendly"

# Week 3: Settle on balanced
persona:
  style: "professional"
  tone: "helpful"  # Professional but approachable
```

## Advanced: Dynamic Persona

Change persona based on context:

```yaml
overlord:
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
```

**Example:**
```
Normal: "I've completed the analysis."
Error: "I'm sorry, I encountered an issue. Let me help you fix this."
Success: "Great! Your deployment was successful! 🎉"
```

## Best Practices

**DO:**
- ✅ Keep persona consistent across your formation
- ✅ Match your brand voice
- ✅ Consider your audience
- ✅ Test different styles with users
- ✅ Use adapt_to_user for personalization

**DON'T:**
- ❌ Mix conflicting styles (professional + playful)
- ❌ Over-use personality traits (too many = diluted)
- ❌ Ignore user feedback
- ❌ Change persona frequently (confuses users)

## Learn More

- [Response Formats](structured-output.md) - Control output format (JSON, HTML, etc.)
- [The Overlord](overlord.md) - How the orchestrator works
- [Persona Configuration Reference](../reference/persona.md) - Full config options
- [Request Lifecycle](../deep-dives/request-lifecycle.md) - See how persona is applied
