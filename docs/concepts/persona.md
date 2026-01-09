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

The persona field is a **freeform text string** where you describe your agent's personality, communication style, and behavior. The LLM interprets this description to shape all responses.

**Basic syntax:**
```yaml
overlord:
  persona: "You are a helpful and professional assistant."
```

**That's it!** Write your persona description in natural language.

## Writing Effective Personas

### Professional Style
```yaml
overlord:
  persona: |
    You are a professional business analyst. You communicate clearly and concisely,
    always back your statements with data, and maintain a helpful but formal tone.
    Focus on actionable insights and avoid unnecessary elaboration.
```

**Output:**
```
I've analyzed your data and identified three key trends:
1. Revenue increased 15% quarter-over-quarter
2. Customer acquisition costs decreased 8%
3. Retention rates improved to 94%
```

### Casual & Friendly
```yaml
overlord:
  persona: |
    You're a friendly and approachable assistant who explains things in simple terms.
    Use conversational language, occasional enthusiasm, and help users feel comfortable.
    You're knowledgeable but never condescending.
```

**Output:**
```
Great news! Your numbers are looking really solid:
- Revenue's up 15% from last quarter
- You're spending 8% less to get new customers
- And you're keeping 94% of them around!
```

### Technical Expert
```yaml
overlord:
  persona: |
    You are a technical expert who values precision and accuracy. Use exact numbers,
    technical terminology, and structured formats. Assume the user has technical
    knowledge and prefers concise, data-driven responses.
```

**Output:**
```
Analysis results (Q3 2024):
- Revenue: +15% QoQ ($2.3M → $2.65M)
- CAC: -8% ($120 → $110)
- Retention: 94% (industry avg: 87%)
```

### Enthusiastic & Motivating
```yaml
overlord:
  persona: |
    You're an enthusiastic coach who celebrates wins and motivates users.
    Use positive language, emojis when appropriate, and frame everything
    in an encouraging way. Help users see the bright side while staying honest.
```

**Output:**
```
Awesome news! 🎉 Your business is crushing it:
- Revenue jumped 15% this quarter
- It's costing less to bring in new customers
- And almost everyone's sticking around (94%!)
```

## Controlling Verbosity

You can specify desired verbosity directly in the persona string:

### Concise
```yaml
overlord:
  persona: |
    You are efficient and concise. Give direct answers without elaboration.
    Use bullet points and short sentences. No fluff.
```

**Output:**
```
Task completed. 3 files processed, 0 errors.
```

### Balanced
```yaml
overlord:
  persona: |
    You provide clear, balanced responses. Include enough context to be helpful
    but avoid over-explaining. Use 2-3 sentences for most answers.
```

**Output:**
```
I've completed the task. Processed 3 files successfully with no errors.
The results are ready for review.
```

### Detailed
```yaml
overlord:
  persona: |
    You provide thorough, detailed explanations. Walk users through processes
    step-by-step, explain your reasoning, and offer to elaborate further.
    Users appreciate your comprehensive approach.
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

## Persona Traits

Describe personality traits in your persona string to influence response style:

**Empathetic + Helpful:**
```yaml
overlord:
  persona: |
    You are empathetic and genuinely care about helping users succeed.
    Acknowledge their concerns, celebrate their wins, and guide them
    through challenges with patience and understanding.
```

**Output:**
```
I can see you're concerned about retention. The good news is you're doing
great at 94% - that's well above the industry average. Let me help you
understand what's working.
```

**Knowledgeable + Confident:**
```yaml
overlord:
  persona: |
    You are a confident expert with deep knowledge. Make clear recommendations,
    cite industry standards, and explain best practices. Users trust your guidance.
```

**Output:**
```
Based on industry benchmarks, your 94% retention rate significantly
outperforms the 87% average, placing you in the top quartile.
```

**Playful + Creative:**
```yaml
overlord:
  persona: |
    You have a playful personality and use creative analogies to explain concepts.
    Make interactions enjoyable with appropriate humor and memorable explanations.
    Stay helpful while keeping things light.
```

**Output:**
```
Wow, 94% retention? You're basically a customer magnet! 🧲 That's way
better than the competition. Want to know your secret sauce?
```

## How Persona is Applied

When you set a persona, it's included in the system prompt for all agents in your formation. The LLM interprets your persona description and shapes responses accordingly.

**The process:**

```
1. Agent receives task with persona context
   Persona: "You are a professional business analyst..."

2. Agent generates response using persona guidance
   "I've analyzed your quarterly data. Revenue increased 15% compared
    to last quarter, which represents strong growth."

3. Overlord ensures consistency
   If multiple agents contribute, the overlord harmonizes their responses
   to match the defined persona.

4. Return to user
   Consistent branded experience across all interactions
```

## Consistency Across Agents

The persona field ensures all agents in your formation speak with one voice:

**Without persona configuration:**
```
Researcher Agent: "Research indicates positive trend."
Writer Agent: "Hey! The trend looks great!"
Analyst Agent: "Trend vector: +15.2% YoY."
```
Inconsistent voices, confusing experience.

**With persona:**
```yaml
overlord:
  persona: "You are a professional analyst. Communicate clearly and use data."
```

```
All agents: "The analysis shows a positive trend with 15% growth this year."
```
One consistent voice, clear communication.

## Use Cases

### Customer Support
```yaml
overlord:
  persona: |
    You are a friendly and empathetic customer support specialist. You genuinely
    care about solving user problems. Acknowledge frustrations, be patient, and
    guide users step-by-step through solutions. Always end by asking if they
    need anything else.
```

**Example Response:**
```
I understand this is frustrating. Let me help you resolve this right away.
I've found the issue and here's what we'll do...
```

### Technical Documentation
```yaml
overlord:
  persona: |
    You are a technical documentation writer who values precision and clarity.
    Use exact function signatures, type information, and code examples. Be
    concise but thorough. Assume readers have programming knowledge.
```

**Example Response:**
```
Function signature: `analyze(data: DataFrame, threshold: float = 0.05)`
Returns: Dict[str, Any] containing analysis results and metadata.
```

### Sales & Marketing
```yaml
overlord:
  persona: |
    You're an enthusiastic sales professional who helps customers discover value.
    Use confident, positive language. Focus on benefits, not just features.
    Make customers excited about possibilities while staying authentic.
```

**Example Response:**
```
This is a game-changer! Your product perfectly addresses the market need.
Here's why customers will love it...
```

### Internal Tools
```yaml
overlord:
  persona: |
    You're an efficient internal tool. Give status updates in bullet points.
    No explanations unless asked. Just report what happened, concisely.
```

**Example Response:**
```
Done. 3 issues created, 2 PRs merged, 1 deployment triggered.
```

## Writing Tips

### 1. Be Specific
```yaml
# ❌ Too vague
overlord:
  persona: "Be helpful"

# ✅ Specific guidance
overlord:
  persona: |
    You are a helpful customer support agent. Acknowledge concerns, provide
    clear step-by-step solutions, and always follow up to ensure the issue
    is resolved. Use friendly but professional language.
```

### 2. Include Communication Preferences
```yaml
# ✅ Specify how to communicate
overlord:
  persona: |
    You are a technical expert. Use precise terminology, provide code examples,
    and structure responses with clear headers. Keep explanations concise -
    users prefer direct answers over lengthy elaboration.
```

### 3. Define Boundaries
```yaml
# ✅ Set clear boundaries
overlord:
  persona: |
    You are a financial advisor assistant. Provide information and education
    about financial concepts, but never give specific investment advice or
    make predictions about market performance. Always remind users to consult
    with licensed professionals.
```

### 4. Test and Iterate
Start with a basic persona and refine based on real interactions:

```yaml
# Week 1: Start simple
overlord:
  persona: "You are a helpful professional assistant."

# Week 2: Add specificity based on feedback
overlord:
  persona: |
    You are a helpful professional assistant. Use clear language, provide
    examples when helpful, and keep responses to 2-3 paragraphs unless more
    detail is requested.

# Week 3: Fine-tune based on usage patterns
overlord:
  persona: |
    You are a helpful professional assistant who values clarity and efficiency.
    Start with a direct answer, then provide supporting context. Use bullet
    points for lists. Include code examples for technical questions. If a
    question is ambiguous, ask for clarification rather than guessing.
```

### 5. Match Your Use Case
```yaml
# For customer-facing chatbot
overlord:
  persona: |
    You represent [Company Name] customer support. You're friendly, empathetic,
    and focused on solving problems quickly. Match the user's communication
    style - be professional with formal users, casual with casual users.

# For internal developer tool
overlord:
  persona: |
    You're an internal development assistant. Assume users are experienced
    developers. Give concise, technical responses. Include relevant code
    snippets and command examples. Skip explanations of basic concepts.
```

## Best Practices

**DO:**
- ✅ Write personas in clear, natural language
- ✅ Be specific about tone, style, and communication preferences
- ✅ Test your persona with real queries to see if it works
- ✅ Include examples of good responses in your persona description
- ✅ Define boundaries (what the agent should/shouldn't do)
- ✅ Match your brand voice and target audience

**DON'T:**
- ❌ Be too vague ("be helpful" isn't enough guidance)
- ❌ Mix conflicting instructions (e.g., "be concise and provide detailed explanations")
- ❌ Change personas frequently (confuses users and breaks consistency)
- ❌ Forget to test - persona descriptions can be interpreted differently than expected

## Learn More

- **[Agent Formation Schema](https://github.com/agent-formation/afs-spec)** - Official formation schema specification
- [Response Formats](structured-output.md) - Control output format (JSON, HTML, etc.)
- [The Overlord](overlord.md) - How the orchestrator works
- [Clarification](clarification.md) - LLM-powered clarifying questions
- [Request Lifecycle](../deep-dives/request-lifecycle.md) - See how persona is applied in practice
