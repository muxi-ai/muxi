# Customer Support Bot

## A customer support agent with memory (remembers conversations) and knowledge base (company FAQs, policies).

**Difficulty:** Intermediate
**Time to setup:** 5 minutes


## What It Does

- Answers customer questions using company knowledge
- Remembers conversation history
- Maintains consistent tone
- References policies and FAQs
- Escalates complex issues

## Features

- ✅ **Memory**: Remembers customer conversations
- ✅ **Knowledge**: Company FAQs and policies
- ✅ **Persona**: Professional, empathetic support tone
- ✅ **Clarification**: Asks for details when needed

## Prerequisites

- MUXI Server running
- OpenAI API key
- Your company documentation (optional - example included)

## Setup

```bash
# 1. Copy this example
cp -r examples/02-customer-support my-support-bot
cd my-support-bot

# 2. (Optional) Add your company docs
#    Put .txt or .md files in knowledge/
echo "Our refund policy is 30 days from purchase." > knowledge/refund-policy.txt
echo "Shipping takes 3-5 business days." > knowledge/shipping-info.txt

# 3. Set API key
muxi secrets setup

# 4. Run locally
muxi dev
```

## Test It

```bash
# First question
curl -X POST http://localhost:8001/v1/chat \
  -H "Content-Type: application/json" \
  -d '{
    "message": "What is your refund policy?",
    "session_id": "customer_123",
    "user_id": "alice@example.com"
  }'

# Follow-up (uses memory)
curl -X POST http://localhost:8001/v1/chat \
  -H "Content-Type: application/json" \
  -d '{
    "message": "And what about shipping?",
    "session_id": "customer_123",
    "user_id": "alice@example.com"
  }'
```

## Expected Behavior

**First request:**
```json
{
  "text": "Our refund policy is 30 days from purchase. If you're unsatisfied with your order, you can request a full refund within 30 days of receiving it. Would you like help initiating a refund?",
  "sources": ["refund-policy.txt"]
}
```

**Follow-up request (remembers context):**
```json
{
  "text": "Shipping takes 3-5 business days. Since you asked about refunds earlier, I should mention that return shipping is free if you need to send something back.",
  "sources": ["shipping-info.txt"]
}
```

## Configuration Highlights

### Memory
```yaml
memory:
  buffer:
    enabled: true
    size: 50  # Last 50 messages

  persistent:
    enabled: true
    provider: sqlite
    path: ./data/memory.db
```

### Knowledge Base
```yaml
knowledge:
  - name: company-docs
    type: directory
    path: ./knowledge
    chunk_size: 512
    chunk_overlap: 50
```

### Persona
```yaml
overlord:
  persona: |
    You are a professional customer support representative.
    Be empathetic, helpful, patient, and knowledgeable.
```

## Customization

### Add Your Company Knowledge

Replace the example knowledge files:

```bash
# Remove examples
rm knowledge/*.txt

# Add your docs (supports .txt, .md, .pdf)
cp /path/to/your/docs/*.md knowledge/
cp /path/to/your/policies/*.pdf knowledge/
```

### Adjust Memory Size

```yaml
memory:
  buffer:
    size: 100  # Remember more messages
```

### Change Tone

```yaml
overlord:
  persona: "You are a casual, enthusiastic support representative."
```

## Deploy to Production

```bash
# 1. Create profile
muxi profiles add production \
  --server https://muxi.yourcompany.com:7890

# 2. Deploy
muxi deploy production
```

## Next Steps

- [Add Tools](../../guides/add-mcp-tools.md) - Let agent check order status, create tickets
- [Multi-Agent Team](05-multi-agent-team/) - Add specialized agents
- [Triggers](../../guides/create-triggers.md) - Integrate with Zendesk/Intercom

## Common Issues

### "No knowledge results found"
Check knowledge directory has files:
```bash
ls knowledge/
```

Re-index knowledge:
```bash
muxi server restart my-support-bot
```

### "Memory not persisting"
Check database file exists:
```bash
ls data/memory.db
```

Ensure persistent memory is enabled in formation.afs.

### "Agent doesn't remember context"
Make sure you're using the same `session_id` across requests:
```json
{"message": "...", "session_id": "customer_123"}
```


## Learn More

[+] [Knowledge Systems](../../reference/knowledge.md) - RAG configuration
[+] [Multi-Agent Guide](../../guides/build-multi-agent-systems.md) - Building teams
[+] [Memory Reference](../../reference/memory.md) - Conversation persistence
