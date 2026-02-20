# Formation Examples

## Simple Chatbot

Single agent, no tools, minimal memory.

```yaml
# formation.afs
schema: "1.0.0"
id: simple-chatbot
description: The simplest MUXI formation - a single assistant agent
version: "1.0.0"

llm:
  api_keys:
    openai: "${{ secrets.OPENAI_API_KEY }}"
  models:
    - text: "openai/gpt-4o"
      settings:
        temperature: 0.7
        max_tokens: 1000

agents:
  - id: assistant
    name: Assistant
    description: General-purpose assistant for answering questions
    system_message: You are a helpful AI assistant.

overlord:
  soul: You are a helpful, professional assistant.
  workflow:
    auto_decomposition: false

memory:
  buffer:
    size: 10
    vector_search: false
```

**Secrets required:** `OPENAI_API_KEY`

---

## Customer Support Bot

Agent with knowledge base (RAG).

```yaml
# formation.afs
schema: "1.0.0"
id: customer-support
description: Customer support agent with FAQ knowledge base
version: "1.0.0"

llm:
  api_keys:
    openai: "${{ secrets.OPENAI_API_KEY }}"
  models:
    - text: "openai/gpt-4o"
    - embedding: "openai/text-embedding-3-large"

overlord:
  soul: |
    You are a friendly customer support representative for Acme Corp.
    Always be helpful and empathetic. If you don't know the answer,
    say so and offer to escalate.

memory:
  buffer:
    size: 50
    vector_search: true
  persistent:
    connection_string: "sqlite:///data/memory.db"

agents: []
```

```yaml
# agents/support.afs
schema: "1.0.0"
id: support
name: Support Agent
description: Answers customer questions using knowledge base

system_message: |
  You are a customer support specialist for Acme Corp.
  Use the knowledge base to answer questions accurately.
  Be friendly and empathetic.

knowledge:
  files:
    - "knowledge/refund-policy.md"
    - "knowledge/shipping-info.md"
  directories:
    - "knowledge/"
```

**Secrets required:** `OPENAI_API_KEY`

---

## Research Assistant with Tools

Agent with web search and file system access.

```yaml
# formation.afs
schema: "1.0.0"
id: research-assistant
description: AI research assistant with web search and file access
version: "1.0.0"

llm:
  api_keys:
    openai: "${{ secrets.OPENAI_API_KEY }}"
  models:
    - text: "openai/gpt-4o"
      settings:
        temperature: 0.7
        max_tokens: 4096
    - embedding: "openai/text-embedding-3-large"

overlord:
  soul: |
    You are a professional research assistant who is knowledgeable,
    thorough, and analytical.
  workflow:
    auto_decomposition: true
    complexity_threshold: 7.0

memory:
  buffer:
    size: 50
    vector_search: true
  persistent:
    connection_string: "sqlite:///data/memory.db"

mcp:
  default_timeout_seconds: 60
  max_tool_iterations: 10

agents: []
```

```yaml
# agents/researcher.afs
schema: "1.0.0"
id: researcher
name: Research Specialist
description: Information gathering specialist

system_message: |
  You are an expert researcher who gathers information from multiple sources.
  Always provide URLs and cite your sources.

llm_models:
  - text: "openai/gpt-4o-mini"
    settings:
      temperature: 0.5
```

```yaml
# mcp/brave-search.afs
schema: "1.0.0"
id: brave-search
description: Brave Web Search for research
type: command
command: npx
args: ["-y", "@modelcontextprotocol/server-brave-search"]
timeout_seconds: 30
auth:
  type: env
  BRAVE_API_KEY: "${{ secrets.BRAVE_SEARCH_API_KEY }}"
```

```yaml
# mcp/filesystem.afs
schema: "1.0.0"
id: filesystem
description: File system access
type: command
command: npx
args: ["-y", "@modelcontextprotocol/server-filesystem", "./workspace"]
```

**Secrets required:** `OPENAI_API_KEY`, `BRAVE_SEARCH_API_KEY`

---

## Code Reviewer

Agent with GitHub MCP integration.

```yaml
# formation.afs
schema: "1.0.0"
id: code-reviewer
description: AI code reviewer with GitHub access
version: "1.0.0"

llm:
  api_keys:
    openai: "${{ secrets.OPENAI_API_KEY }}"
  models:
    - text: "openai/gpt-4o"

overlord:
  soul: You are a senior software engineer who reviews code thoroughly.

agents: []
```

```yaml
# agents/code-reviewer.afs
schema: "1.0.0"
id: code-reviewer
name: Code Reviewer
description: Reviews code for quality, security, and best practices

system_message: |
  You are an expert code reviewer. Review code for:
  - Correctness and bugs
  - Security vulnerabilities
  - Performance issues
  - Code style and readability
  Provide specific, actionable feedback.
```

```yaml
# mcp/github.afs
schema: "1.0.0"
id: github
description: GitHub repository access
type: command
command: npx
args: ["-y", "@modelcontextprotocol/server-github"]
auth:
  type: env
  GITHUB_TOKEN: "${{ secrets.GITHUB_TOKEN }}"
```

**Secrets required:** `OPENAI_API_KEY`, `GITHUB_TOKEN`

---

## Multi-Agent Team

Multiple specialized agents with automatic orchestration.

```yaml
# formation.afs
schema: "1.0.0"
id: multi-agent-team
description: Team of specialized AI agents with automatic orchestration
version: "1.0.0"

llm:
  api_keys:
    openai: "${{ secrets.OPENAI_API_KEY }}"
  models:
    - text: "openai/gpt-4o"
      settings:
        temperature: 0.7
        max_tokens: 4096
    - embedding: "openai/text-embedding-3-large"

overlord:
  soul: |
    You are a professional team lead who coordinates between specialized agents.
    You're knowledgeable, thorough, and efficient in delegation and synthesis.
  workflow:
    auto_decomposition: true
    complexity_threshold: 7.0
    max_parallel_tasks: 5
    timeouts:
      task_timeout: 120
      workflow_timeout: 600

memory:
  buffer:
    size: 50
    vector_search: true
  persistent:
    connection_string: "sqlite:///data/memory.db"

mcp:
  default_timeout_seconds: 60
  max_tool_iterations: 15

agents: []
```

```yaml
# agents/researcher.afs
schema: "1.0.0"
id: researcher
name: Research Specialist
description: Information gathering specialist
system_message: |
  You are an expert researcher.
  Always provide URLs and cite your sources.
llm_models:
  - text: "openai/gpt-4o-mini"
    settings:
      temperature: 0.5
```

```yaml
# agents/analyst.afs
schema: "1.0.0"
id: analyst
name: Data Analyst
description: Analyzes data and identifies patterns and insights
system_message: |
  You are a data analyst who excels at finding patterns and insights.
  Present findings clearly with supporting data.
```

```yaml
# agents/writer.afs
schema: "1.0.0"
id: writer
name: Content Writer
description: Creates well-structured written content
system_message: |
  You are a professional content writer.
  Write clearly, concisely, and engagingly.
  Structure content with headings and key points.
```

**Secrets required:** `OPENAI_API_KEY`, `BRAVE_SEARCH_API_KEY`

---

## SDK Integration Examples

### Python

```bash
pip install muxi-client
```

```python
from muxi import FormationClient

client = FormationClient(url="http://localhost:8001")

for event in client.chat_stream({"message": "Hello!"}):
    if event.get("type") == "text":
        print(event.get("text"), end="")
```

### TypeScript

```bash
npm install @muxi-ai/muxi-typescript
```

```typescript
import { FormationClient } from "@muxi-ai/muxi-typescript";

const client = new FormationClient({ url: "http://localhost:8001" });

for await (const event of client.chatStream({ message: "Hello!" })) {
  if (event.type === "text") process.stdout.write(event.text);
}
```

### Go

```bash
go get github.com/muxi-ai/muxi-go
```

```go
client := muxi.NewFormationClient(&muxi.FormationConfig{
    URL: "http://localhost:8001",
})

stream, _ := client.ChatStream(ctx, &muxi.ChatRequest{
    Message: "Hello!",
})
for chunk := range stream {
    if chunk.Type == "text" {
        fmt.Print(chunk.Text)
    }
}
```

### curl

```bash
curl -X POST http://localhost:8001/v1/chat \
  -H "Content-Type: application/json" \
  -H "X-MUXI-CLIENT-KEY: your-client-key" \
  -H "X-Muxi-User-ID: user-123" \
  -d '{"message": "Hello!"}'
```
