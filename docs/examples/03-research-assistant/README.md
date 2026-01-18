# Research Assistant

## An AI research assistant with web search, file access, and memory. Great for gathering information, summarizing findings, and creating reports.

**Difficulty:** Intermediate
**Time to setup:** 10 minutes


## What It Does

- Searches the web for current information
- Reads and analyzes documents
- Creates research reports
- Remembers previous research sessions
- Can save findings to files

## Features

- ✅ **Web Search**: Via Brave Search API
- ✅ **File System**: Read/write research files
- ✅ **Memory**: Remember research context
- ✅ **Artifacts**: Generate PDF reports
- ✅ **Tool Chaining**: Automatic multi-step research

## Prerequisites

- MUXI Server running
- OpenAI API key
- Brave Search API key (get free at brave.com/search/api)

## Setup

```bash
# 1. Copy this example
cp -r examples/03-research-assistant my-researcher
cd my-researcher

# 2. Install MCP servers
npm install -g @modelcontextprotocol/server-brave-search
npm install -g @modelcontextprotocol/server-filesystem

# 3. Set API keys
muxi secrets setup
# You'll need:
# - OPENAI_API_KEY
# - BRAVE_SEARCH_API_KEY

# 4. Create research directory
mkdir -p research

# 5. Run locally
muxi dev
```

## Test It

### Simple Search
```bash
curl -X POST http://localhost:8001/v1/chat \
  -H "Content-Type: application/json" \
  -d '{
    "message": "Research the latest developments in AI agent frameworks in 2025",
    "session_id": "research_session_1"
  }'
```

### Multi-Step Research
```bash
curl -X POST http://localhost:8001/v1/chat \
  -H "Content-Type: application/json" \
  -d '{
    "message": "Research AI frameworks, compare top 3, and create a comparison report",
    "session_id": "research_session_2"
  }'
```

## Expected Behavior

The agent will:
1. Search the web for "AI agent frameworks 2025"
2. Analyze top results
3. Identify top 3 frameworks
4. Search for details on each
5. Create comparison report
6. Save as PDF artifact

Response:
```json
{
  "text": "I've researched the top AI agent frameworks...\n\nKey findings:\n1. LangChain - Most popular...\n2. AutoGPT - Best for autonomy...\n3. MUXI - Best for orchestration...",
  "artifacts": [
    {
      "name": "ai_frameworks_comparison.pdf",
      "type": "application/pdf",
      "size": 245678
    }
  ],
  "tools_used": [
    {"name": "brave_web_search", "calls": 4},
    {"name": "file_write", "calls": 1}
  ]
}
```

## Configuration Highlights

### MCP Tools

MCP servers are defined in `mcp/*.afs` files:

```yaml
# mcp/brave-search.afs
schema: "1.0.0"
id: brave-search
type: command
command: npx
args: ["-y", "@modelcontextprotocol/server-brave-search"]
auth:
  type: env
  BRAVE_API_KEY: "${{ secrets.BRAVE_SEARCH_API_KEY }}"
```

```yaml
# mcp/filesystem.afs
schema: "1.0.0"
id: filesystem
type: command
command: npx
args: ["-y", "@modelcontextprotocol/server-filesystem", "./research"]
```

### Tool Chaining
Agent automatically chains tools:
```
1. brave_web_search("AI frameworks")
   → Gets URLs and summaries
2. brave_web_search("LangChain features")
   → Detailed info on framework 1
3. brave_web_search("AutoGPT capabilities")
   → Detailed info on framework 2
4. file_write("comparison.md")
   → Saves findings
```

### Artifacts
```yaml
artifacts:
  enabled: true
  python_execution: true  # For creating PDFs
  allowed_packages:
    - reportlab  # PDF generation
    - matplotlib  # Charts
```

## Use Cases

### Market Research
```bash
"Research the electric vehicle market in Europe, focusing on Tesla, VW, and BYD. Create a market analysis report."
```

### Competitive Analysis
```bash
"Research our top 3 competitors, analyze their pricing, features, and customer reviews. Create a comparison table."
```

### Academic Research
```bash
"Research recent papers on transformer models published in 2024-2025. Summarize key findings and methodologies."
```

### News Monitoring
```bash
"Search for news about [your company/industry] from the past week. Summarize key stories and sentiment."
```

## Customization

### Add More Tools

Create additional `mcp/*.afs` files:

```yaml
# mcp/puppeteer.afs - Web scraping
schema: "1.0.0"
id: puppeteer
type: command
command: npx
args: ["-y", "@modelcontextprotocol/server-puppeteer"]
```

```yaml
# mcp/postgres.afs - Database access
schema: "1.0.0"
id: postgres
type: command
command: npx
args: ["-y", "@modelcontextprotocol/server-postgres"]
auth:
  type: env
  DATABASE_URL: "${{ secrets.DATABASE_URL }}"
```

### Change LLM

Update `formation.afs`:
```yaml
llm:
  models:
    - text: "openai/gpt-4-turbo"  # Faster, cheaper
    # or
    - text: "openai/gpt-4o"       # Best reasoning
```

## Deploy to Production

```bash
# 1. Install MCP servers on production
ssh production-server
npm install -g @modelcontextprotocol/server-brave-search
npm install -g @modelcontextprotocol/server-filesystem

# 2. Deploy formation
muxi deploy production

# 3. Test
curl https://api.yourcompany.com/research/v1/chat \
  -H "Authorization: Bearer $MUXI_API_KEY" \
  -d '{"message": "..."}'
```

## Next Steps

- [Add More Tools](../guides/add-mcp-tools.md) - Database, APIs, etc.
- [Code Reviewer](04-code-reviewer/) - GitHub integration example
- [Multi-Agent Team](05-multi-agent-team/) - Specialized researchers

## Common Issues

### "MCP server not found"
```bash
# Check MCP server is installed
npx @modelcontextprotocol/server-brave-search --version

# Reinstall if needed
npm install -g @modelcontextprotocol/server-brave-search
```

### "Brave API error: Unauthorized"
Check API key is correct:
```bash
muxi secrets get BRAVE_SEARCH_API_KEY
```

Get new key at: https://brave.com/search/api

### "Permission denied: /etc/passwd"
Filesystem MCP trying to access disallowed directory. Update `mcp/filesystem.afs`:
```yaml
schema: "1.0.0"
id: filesystem
type: command
command: npx
args: ["-y", "@modelcontextprotocol/server-filesystem", "./research"]
# Only allow research directory
```

### "Artifact generation failed"
Install Python packages:
```bash
pip install reportlab matplotlib
```
