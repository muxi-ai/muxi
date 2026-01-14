---
title: LLM Providers
description: How MUXI works with any language model
---
# LLM Providers

## Use any model, mix and match per agent

MUXI is LLM-agnostic. You can run proprietary APIs (OpenAI, Anthropic, Google), managed services (Bedrock, Vertex), or self-hosted models (Ollama, vLLM, llama.cpp) in the same formation.

---

## Powered by OneLLM

Under the hood, MUXI uses [OneLLM](https://github.com/muxi-ai/onellm) - a unified interface for 200+ language models across all major providers. Every model OneLLM supports works out of the box with MUXI.

**Supported providers include:**
- **Proprietary APIs:** OpenAI, Anthropic, Google (Gemini), Mistral, Cohere, AI21
- **Cloud platforms:** AWS Bedrock, Google Vertex AI, Azure OpenAI
- **Self-hosted:** Ollama, vLLM, llama.cpp, LM Studio, text-generation-webui
- **Any OpenAI-compatible endpoint**

> [!TIP]
> **Test different models.** LLM performance varies significantly by task. A model that excels at reasoning may struggle with creative writing. Run your actual prompts through several models to find the best fit for each agent's role.

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

system_message: Research specialist.

llm_models:
  - text: "openai/gpt-4.1"
```

```yaml
# agents/writer.afs
schema: "1.0.0"
id: writer
name: Writer
description: Content writer

system_message: Content writer.

llm_models:
  - text: "anthropic/claude-3.5-sonnet"
```

```yaml
# agents/high-volume.afs
schema: "1.0.0"
id: high-volume
name: High Volume Agent
description: High volume processing

system_message: High volume processing agent.

llm_models:
  - text: "http://llama.example.com/v1/llama-3-70b-instruct"
```

## Model choice matrix example

- **Reasoning-heavy:** GPT-5, Claude Opus 4.5
- **Long-form writing:** Claude Sonnet 4.5, Gemini 3
- **High-volume or on-prem:** Llama 3 (vLLM/llama.cpp), Mistral

### Related docs

- [Agent Formation Schema](../reference/formation-schema.md)
- [Memory](./memory.md)
- [Tools & MCP](./tools.md)
