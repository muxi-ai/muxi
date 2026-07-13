---
title: LLM Providers
description: How MUXI works with any language model
---
# LLM Providers

## Use any model, mix and match per agent

MUXI is LLM-agnostic. You can run proprietary APIs (OpenAI, Anthropic, Google), managed services (Bedrock, Vertex), or self-hosted models (Ollama, vLLM, llama.cpp) in the same formation.

> [!TIP]
> **Test different models.** LLM performance varies significantly by task. A model that excels at reasoning may struggle with creative writing. Run your actual prompts through several models to find the best fit for each agent's role.

## Powered by OneLLM

Under the hood, MUXI uses [OneLLM](https://github.com/muxi-ai/onellm) - a unified interface for 200+ language models across all major providers. Every model OneLLM supports works out of the box with MUXI.

**Supported providers include:**
- **Proprietary APIs:** OpenAI, Anthropic, Google (Gemini), Mistral, Cohere, AI21
- **Cloud platforms:** AWS Bedrock, Google Vertex AI, Azure OpenAI
- **Self-hosted:** Any model supported via Ollama or llama.cpp
- **Any OpenAI-compatible endpoint**

---

### Key ideas

- **Per-agent model selection:** Choose the best model per agent (e.g., reasoning vs. writing vs. high-volume), and change without redeploying code.
- **Provider flexibility:** Any OpenAI-compatible endpoint works; custom providers can be added via HTTP.
- **Failover ready:** Swap providers when rate limits or outages occur.
- **Token efficiency:** Combine with synopsis caching and tool-indexing to minimize context size.

### How to configure

Agent-specific model overrides in `agents/*.afs`:

```yaml
# agents/researcher.afs
schema: "1.0.0"
id: researcher
name: Researcher
description: Research specialist

system_message: |
  You are a research specialist.
  Your job is to gather accurate information...

llm_models:
  - text: "openai/gpt-4.1"
```

```yaml
# agents/writer.afs
schema: "1.0.0"
id: writer
name: Writer
description: Content writer

system_message: |
  You are a content writer.
  Your job is to create clear, engaging content...

llm_models:
  - text: "anthropic/claude-3.5-sonnet"
```

```yaml
# agents/high-volume.afs
schema: "1.0.0"
id: high-volume
name: High Volume Agent
description: High volume processing

system_message: |
  You are a data processor.
  Your job is to handle high-volume tasks efficiently...

llm_models:
  - text: "http://llama.example.com/v1/llama-3-70b-instruct"
```

## Hierarchical model selection

Model choice follows the formation hierarchy with **lowest-level-wins**
overrides. Authors specify a model where the knowledge lives; there is no
capability inference.

```
formation llm.models  →  agent llm_models  →  SOP/trigger/skill frontmatter model:  →  per-step [model:x]
        (broadest)                                                                        (most specific)
```

Every model reference in `sops/`, `triggers/`, and `skills/` is validated at
load time (fail-fast). At request time, model selection never crashes a turn: an
unresolvable override falls back to the agent default, and a failing override
call retries on the agent's own model.

### Aliases

`llm.aliases` maps semantic names to `provider/model`, resolved before cache
keying so an alias and its target share one cached instance. A broken alias
errors both at its definition and at every usage site.

```yaml
# formation.yaml
llm:
  models:
    - text: "openai/gpt-4o-mini"
  aliases:
    fast: "openai/gpt-4o-mini"
    premium: "anthropic/claude-opus-4.5"
```

```markdown
<!-- sops/deep-analysis.md frontmatter -->
---
model: premium
---
```

> [!NOTE]
> Formations without `llm.aliases` or frontmatter `model:` fields behave exactly
> as before. When the same underlying model appears under several capabilities,
> overrides deterministically prefer the `text` capability's entry.

## Model choice matrix example

- **Reasoning-heavy:** GPT-5, Claude Opus 4.5
- **Long-form writing:** Claude Sonnet 4.5, Gemini 3
- **High-volume or on-prem:** Llama 3 (vLLM/llama.cpp), Mistral

### Related docs

- [Agent Formation Schema](../reference/formation-schema.md)
- [Memory](../concepts/memory-system.md)
- [Tools & MCP](../concepts/tools-and-mcp.md)
