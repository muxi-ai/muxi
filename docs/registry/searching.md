# Searching the Registry

Find formations for your use case.

## CLI Search

```bash
muxi search "customer support"
```

Output:

```
NAME                      DESCRIPTION                 DOWNLOADS
@muxi/support-bot         Customer support agent      1,234
@acme/helpdesk           Helpdesk assistant          567
@company/zendesk-agent   Zendesk integration         89
```

## Filter by Tag

```bash
muxi search --tag support
muxi search --tag research
muxi search --tag multi-agent
```

## Web Search

Visit [registry.muxi.org](https://registry.muxi.org) to:

- Browse by category
- View formation details
- Read documentation
- Check download stats

## Formation Details

```bash
muxi info @muxi/support-bot
```

Output:

```
@muxi/support-bot v1.2.0
========================
Customer support agent with knowledge base

Author:     muxi
License:    MIT
Downloads:  1,234
Updated:    2025-01-01

Agents:
  - support (main)

MCPs:
  - None

Requirements:
  - OPENAI_API_KEY
```

## Pull Formation

```bash
muxi pull @muxi/support-bot
```

Downloads to current directory.

### Specific Version

```bash
muxi pull @muxi/support-bot@1.0.0
```

### Custom Path

```bash
muxi pull @muxi/support-bot --output ~/formations/
```

## Categories

| Category | Description |
|----------|-------------|
| `assistant` | General assistants |
| `support` | Customer support |
| `research` | Research agents |
| `devops` | DevOps automation |
| `data` | Data processing |
| `multi-agent` | Multi-agent systems |

## Popular Tags

- `chat` - Conversational agents
- `tools` - Agents with MCP tools
- `memory` - Memory-enabled agents
- `knowledge` - RAG-enabled agents
- `streaming` - Streaming responses
