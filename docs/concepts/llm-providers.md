---
title: LLM Providers
description: How MUXI works with any language model
---
# LLM Providers

## Use any model, mix and match per agent

MUXI is LLM-agnostic. You can run proprietary APIs (OpenAI, Anthropic, Google), managed services (Bedrock, Vertex), or self-hosted models (Ollama, vLLM, llama.cpp) in the same formation.

### Key ideas

- **Per-agent model selection:** Choose the best model per agent (e.g., reasoning vs. writing vs. high-volume), and change without redeploying code.
- **Provider flexibility:** Any OpenAI-compatible endpoint works; custom providers can be added via HTTP.
- **Failover ready:** Swap providers when rate limits or outages occur.
- **Token efficiency:** Combine with synopsis caching and tool-indexing to minimize context size.

### How to configure

```yaml
agents:
  - id: researcher
    llm:
      provider: openai
      model: gpt-4.1

  - id: writer
    llm:
      provider: anthropic
      model: claude-3.5-sonnet

  - id: high-volume
    llm:
      provider: http
      base_url: https://llama.example.com/v1
      model: llama-3-70b-instruct
```

### When to choose which model

- **Reasoning-heavy:** GPT-4.1, Claude 3.5
- **Long-form writing:** Claude 3.5, Gemini 1.5
- **High-volume or on-prem:** Llama 3 (vLLM/llama.cpp), Mistral

### Related docs

- [Agent Formation Schema](../reference/schema.md)
- [Memory](./memory.md)
- [Tools & MCP](./tools.md)
