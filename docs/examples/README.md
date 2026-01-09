# Formation Examples

Complete, working MUXI formation examples. Copy, customize, and deploy.

## Quick Start

Each example includes:
- Complete `formation.yaml`
- README with setup instructions
- `secrets` template
- Testing commands
- Customization tips

## Examples

### [01. Simple Chatbot](01-simple-chatbot/)
**Difficulty:** Beginner | **Time:** 2 minutes

The simplest MUXI formation - one agent, no tools, no memory.

**Perfect for:**
- Learning MUXI basics
- Testing setup
- Building a foundation

```bash
cp -r examples/01-simple-chatbot my-bot
cd my-bot
muxi secrets setup
muxi dev
```

---

### [02. Customer Support](02-customer-support/)
**Difficulty:** Intermediate | **Time:** 5 minutes

Support bot with memory and knowledge base.

**Features:**
- ✅ Remembers conversations
- ✅ Company knowledge base (FAQs, policies)
- ✅ Empathetic persona
- ✅ Clarifies ambiguous questions

**Perfect for:**
- Customer service
- Internal helpdesk
- FAQ automation

```bash
cp -r examples/02-customer-support my-support
cd my-support
# Add your docs to knowledge/
muxi secrets setup
muxi dev
```

---

### [03. Research Assistant](03-research-assistant/)
**Difficulty:** Intermediate | **Time:** 10 minutes

AI researcher with web search and file access.

**Features:**
- ✅ Web search (Brave API)
- ✅ File system access
- ✅ Creates PDF reports
- ✅ Tool chaining

**Perfect for:**
- Market research
- Competitive analysis
- Information gathering

**Requirements:**
- Brave Search API key (free tier)

```bash
cp -r examples/03-research-assistant my-researcher
cd my-researcher
npm install -g @modelcontextprotocol/server-brave-search
npm install -g @modelcontextprotocol/server-filesystem
muxi secrets setup
muxi dev
```

---

### [04. Code Reviewer](04-code-reviewer/)
**Difficulty:** Advanced | **Time:** 15 minutes

Automated code reviewer integrated with GitHub.

**Features:**
- ✅ Reviews pull requests automatically
- ✅ Checks security, performance, quality
- ✅ Posts comments on GitHub
- ✅ Creates issues for critical problems

**Perfect for:**
- Open source projects
- Team code quality
- Automated reviews

**Requirements:**
- GitHub Personal Access Token
- Public MUXI server (for webhooks)

```bash
cp -r examples/04-code-reviewer my-reviewer
cd my-reviewer
npm install -g @modelcontextprotocol/server-github
muxi secrets setup
muxi deploy production  # Needs public URL
```

---

### [05. Multi-Agent Team](05-multi-agent-team/)
**Difficulty:** Advanced | **Time:** 10 minutes

Team of specialized agents with automatic coordination.

**Features:**
- ✅ Researcher, Analyst, Writer agents
- ✅ Automatic workflow decomposition
- ✅ Parallel task execution
- ✅ Agent-to-agent collaboration

**Perfect for:**
- Complex tasks requiring multiple skills
- Business intelligence
- Content creation at scale

```bash
cp -r examples/05-multi-agent-team my-team
cd my-team
npm install -g @modelcontextprotocol/server-brave-search
muxi secrets setup
muxi dev
```

---

## Example Comparison

| Example | Agents | Tools | Memory | Knowledge | Difficulty |
|---------|--------|-------|--------|-----------|------------|
| Simple Chatbot | 1 | 0 | ❌ | ❌ | ⭐ |
| Customer Support | 1 | 0 | ✅ | ✅ | ⭐⭐ |
| Research Assistant | 1 | 2 | ✅ | ❌ | ⭐⭐ |
| Code Reviewer | 1 | 1 | ✅ | ❌ | ⭐⭐⭐ |
| Multi-Agent Team | 3 | 2 | ✅ | ❌ | ⭐⭐⭐ |

## Learning Path

**Recommended order:**

1. **Start:** Simple Chatbot → Understand MUXI basics
2. **Add Memory:** Customer Support → Conversations persist
3. **Add Tools:** Research Assistant → Give agents capabilities
4. **Integration:** Code Reviewer → External systems (GitHub)
5. **Advanced:** Multi-Agent Team → Orchestration and workflows

## Testing Examples

All examples include test commands. General pattern:

```bash
# Start formation
muxi dev

# Test with curl
curl -X POST http://localhost:8001/v1/chat \
  -H "Content-Type: application/json" \
  -d '{"message": "Your test message"}'

# Or open browser
open http://localhost:8001/chat
```

## Customization

Each example is a starting point. Common customizations:

### Change LLM
```yaml
llm:
  model: gpt-4-turbo  # Faster, cheaper
  # or
  model: gpt-4o       # Best reasoning
  # or
  provider: anthropic
  model: claude-3-opus
```

### Add Tools
```yaml
mcps:
  - id: postgres
    command: npx
    args: ["-y", "@modelcontextprotocol/server-postgres"]
```

### Adjust Memory
```yaml
memory:
  buffer:
    size: 100  # More messages
```

### Change Persona
```yaml
overlord:
  persona:
    style: "casual"
    tone: "friendly"
```

## Deploy to Production

All examples can be deployed:

```bash
# 1. Create profile
muxi profile add production \
  --server https://muxi.yourcompany.com:7890 \
  --key-id $KEY_ID \
  --secret $SECRET

# 2. Deploy
muxi deploy production

# 3. Test
curl https://muxi.yourcompany.com:7890/formations/my-formation/v1/chat \
  -H "Authorization: Bearer $MUXI_API_KEY" \
  -d '{"message": "..."}'
```

## Next Steps

After trying examples:

- **[Formation Reference](../reference/schema.md)** - Complete YAML spec
- **[Add Tools Guide](../guides/add-tools.md)** - Integrate more capabilities
- **[Add Memory Guide](../guides/add-memory.md)** - Persistence options
- **[Deploy Guide](../guides/deploy.md)** - Production deployment
- **[Multi-Agent Guide](../guides/multi-agent.md)** - Build teams

## Contributing Examples

Have a great formation example? Share it!

1. Fork the [`muxi-ai/muxi`](https://github.com/muxi-ai/muxi) repo
2. Add your example to `docs/examples/`
3. Follow the format (README, formation.yaml, secrets)
4. Submit a pull request

## Troubleshooting

See [Troubleshooting Guide](../guides/troubleshooting.md) for common issues.

**Quick fixes:**
```bash
# Installation
curl -fsSL https://muxi.org/install | bash

# Server not running
muxi-server start

# Secrets missing
muxi secrets setup

# Check logs
muxi logs formation-name
```

## Get Help

- **Docs:** https://docs.muxi.org
- **Community:** https://community.muxi.org
- **Issues:** https://github.com/muxi-ai/muxi/issues
