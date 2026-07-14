---
title: muxi chat
description: Interactive chat with your deployed formations
---
# muxi chat

## Talk to your agents from the terminal

Chat with a running formation interactively or send one-off messages. Great for testing, debugging, and quick interactions.

## Usage

```bash
muxi chat [message] [options]
```

## Interactive Mode

```bash
muxi chat
```

Opens interactive chat:

```
Connected to my-assistant
Type /help for commands, /exit to quit

You: Hello!
Assistant: Hello! How can I help you today?

You: What can you do?
Assistant: I can help with research, writing, and analysis...

You: /exit
```

## Single Message

```bash
muxi chat "What is the capital of France?"
```

Output:

```
The capital of France is Paris.
```

## Options

| Flag | Description |
|------|-------------|
| `--formation <id>` | Target formation |
| `--agent <id>` | Specific agent |
| `--profile <name>` | Server profile |
| `--session <id>` | Resume session |
| `--no-stream` | Disable streaming |

## Target Formation

```bash
muxi chat --formation support-bot
```

If only one formation is running, it's selected automatically.

## Interactive Commands

| Command | Description |
|---------|-------------|
| `/help` | Show commands |
| `/exit` | Exit chat |
| `/clear` | Clear screen |
| `/session` | Show session ID |
| `/agents` | List agents |
| `/agent <id>` | Switch agent |
| `/history` | Show history |

## Session Management

### Resume Session

```bash
muxi chat --session sess_abc123
```

Continues previous conversation.

### View Session ID

```
You: /session
Session: sess_abc123
```

## Output Formats

### Streaming (Default)

Response streams as it's generated.

### No Streaming

```bash
muxi chat "Hello" --no-stream
```

Waits for complete response.

## Pipe Input

```bash
echo "Summarize this" | muxi chat
cat document.txt | muxi chat "Analyze this document"
```

## Examples

```bash
# Interactive chat
muxi chat

# Single message
muxi chat "Hello!"

# Specific formation
muxi chat --formation my-bot "Hi"

# Resume session
muxi chat --session sess_abc123

# Pipe document
cat report.md | muxi chat "Summarize this"
```
