# Simple Chatbot

## The simplest possible MUXI formation - a single AI assistant agent with no memory, tools, or knowledge.

**Difficulty:** Beginner
**Time to setup:** 2 minutes


## What It Does

- Responds to chat messages
- Uses GPT-4
- No memory (stateless)
- No tools
- Perfect for testing MUXI basics

## Prerequisites

- MUXI Server running
- OpenAI API key

## Setup

```bash
# 1. Copy this example
cp -r examples/01-simple-chatbot my-chatbot
cd my-chatbot

# 2. Set your OpenAI API key
muxi secrets setup
# Enter your OpenAI API key when prompted

# 3. Run locally
muxi dev
```

## Test It

```bash
# Using curl
curl -X POST http://localhost:8001/v1/chat \
  -H "Content-Type: application/json" \
  -d '{"message": "Hello! What can you help me with?"}'

# Or open browser
open http://localhost:8001/chat
```

## Expected Response

```json
{
  "text": "Hello! I'm an AI assistant. I can help with answering questions, providing information, brainstorming ideas, and having conversations. What would you like to know or discuss?",
  "request_id": "req_abc123"
}
```

## Configuration Explained

### formation.yaml
```yaml
name: simple-chatbot
version: 1.0.0

# Single agent, no tools, no memory
agents:
  - name: assistant
    role: "You are a helpful AI assistant"
    llm:
      provider: openai
      model: gpt-4

# Use the assistant for all requests
overlord:
  default_agent: assistant
```

## Next Steps

This is the simplest formation. Try:

- [02-customer-support](../02-customer-support/) - Add memory and knowledge
- [03-research-assistant](../03-research-assistant/) - Add web search tools
- [Add Memory Guide](../../guides/add-memory.md) - Persist conversations
- [Add Tools Guide](../../guides/add-tools.md) - Give agent capabilities

## Common Issues

### "Missing API key"
```bash
muxi secrets set OPENAI_API_KEY sk-...
```

### "Formation won't start"
```bash
# Check MUXI Server is running
muxi formation list

# Check logs
muxi logs simple-chatbot
```

### "Connection refused"
Make sure MUXI Server is running:
```bash
muxi-server start
```
