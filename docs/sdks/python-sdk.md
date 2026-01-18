---
title: Python SDK
description: Native Python client for MUXI formations
---
# Python SDK

## Pythonic access to your agents

Build Python applications that interact with MUXI formations. Full support for chat, streaming, async operations, sessions, and all Formation API operations.

**GitHub:** [`muxi-ai/muxi-python`](https://github.com/muxi-ai/muxi-python)

## Installation

```bash
pip install muxi-client
```

## Quick Start

```python
from muxi import FormationClient

formation = FormationClient(
    server_url="http://localhost:7890",
    formation_id="my-assistant",
    client_key="your_client_key",
)

# Check health
print(formation.health())
```

## Formation Client

### Initialize

```python
from muxi import FormationClient

# Via server proxy (recommended)
formation = FormationClient(
    server_url="http://localhost:7890",
    formation_id="my-assistant",
    client_key="your_client_key",
    admin_key="your_admin_key",  # Optional, for admin operations
)

# Direct connection (for local dev)
formation = FormationClient(
    url="http://localhost:8001",  # Direct to formation
    client_key="your_client_key",
)
```

### Chat (Streaming)

```python
# Streaming chat (recommended)
for event in formation.chat_stream({"message": "Hello!"}, user_id="user_123"):
    if event.get("type") == "text":
        print(event.get("text"), end="", flush=True)
    elif event.get("type") == "done":
        break
```

### Agents

```python
# List agents (requires admin key)
agents = formation.get_agents()
for agent in agents:
    print(agent["id"], agent["name"])
```

### Sessions

```python
# List sessions
sessions = formation.get_sessions(user_id="user_123")
for session in sessions["sessions"]:
    print(session["session_id"])

# Get session messages
messages = formation.get_session_messages(session_id="sess_abc123", user_id="user_123")
```

### Memory

```python
# Get memory config
config = formation.get_memory_config()

# Get memories for user
memories = formation.get_memories(user_id="user_123")

# Add a memory (user_id, mem_type, detail)
formation.add_memory(
    user_id="user_123",
    mem_type="preference",
    detail="User prefers Python"
)

# Delete a memory
formation.delete_memory(user_id="user_123", memory_id="mem_abc123")

# Clear user buffer
formation.clear_user_buffer(user_id="user_123")
```

### Triggers

```python
# Fire a trigger (name, data, async_mode, user_id)
response = formation.fire_trigger(
    name="github-issue",
    data={
        "repository": "muxi/runtime",
        "issue": {"number": 123, "title": "Bug report"}
    },
    async_mode=False,
    user_id="user_123"
)

# Fire async trigger
formation.fire_trigger("daily-report", {"date": "2024-01-15"}, async_mode=True, user_id="user_123")
```

### Scheduler

```python
# List scheduled jobs
jobs = formation.get_scheduler_jobs(user_id="user_123")

# Create a job (job_type, schedule, message, user_id)
job = formation.create_scheduler_job(
    job_type="prompt",
    schedule="0 9 * * *",  # 9am daily (cron)
    message="Generate daily summary",
    user_id="user_123"
)

# Delete a job
formation.delete_scheduler_job(job_id="job_abc123")
```


## Server Client

For managing formations (deploy, start, stop):

```python
from muxi import ServerClient

server = ServerClient(
    url="http://localhost:7890",
    key_id="muxi_pk_...",
    secret_key="muxi_sk_...",
)

# Check server status
status = server.status()
print(status)

# List formations
formations = server.list_formations()

# Deploy a formation
result = server.deploy_formation(bundle_path="my-bot.tar.gz")
print(f"Deployed: {result['formation_id']}")

# Stop/start/restart
server.stop_formation(formation_id="my-bot")
server.start_formation(formation_id="my-bot")
server.restart_formation(formation_id="my-bot")
```


## Async Client

For async/await usage:

```python
import asyncio
from muxi import AsyncFormationClient

async def main():
    formation = AsyncFormationClient(
        server_url="http://localhost:7890",
        formation_id="my-assistant",
        client_key="your_client_key",
    )

    # Async streaming
    async for event in await formation.chat_stream({"message": "Hello!"}, user_id="user_123"):
        if event.get("type") == "text":
            print(event.get("text"), end="", flush=True)
        elif event.get("type") == "done":
            break

asyncio.run(main())
```


## Error Handling

```python
from muxi import (
    MuxiError,
    AuthenticationError,
    AuthorizationError,
    NotFoundError,
    ValidationError,
    RateLimitError,
    ServerError,
    ConnectionError,
)

try:
    response = formation.chat_stream({"message": "Hello!"}, user_id="user_123")
except AuthenticationError as e:
    print(f"Auth failed: {e.message}")
except RateLimitError as e:
    print(f"Rate limited, retry after: {e.retry_after}s")
except MuxiError as e:
    print(f"Error: {e.code} - {e.message}")
```

All errors include:
- `code` - Error code string
- `message` - Human-readable message
- `status_code` - HTTP status code
- `retry_after` - Seconds to wait (for rate limits)


## Webhook Handlers

Handle incoming async/trigger webhook callbacks with signature verification:

```python
from muxi import webhook

@app.post("/webhooks/muxi")
async def handle_webhook(request: Request):
    payload = await request.body()
    signature = request.headers.get("X-Muxi-Signature")
    
    # Verify signature (prevents spoofing and replay attacks)
    if not webhook.verify_signature(payload, signature, WEBHOOK_SECRET):
        raise HTTPException(401, "Invalid signature")
    
    # Parse into typed WebhookEvent
    event = webhook.parse(payload)
    
    if event.status == "completed":
        for item in event.content:
            if item.type == "text":
                print(item.text)
    elif event.status == "failed":
        print(f"Error: {event.error.message}")
    elif event.status == "awaiting_clarification":
        print(f"Question: {event.clarification.question}")
```

**WebhookEvent fields:**
- `request_id`, `status`, `timestamp`
- `content` - List of `ContentItem` (type, text, file)
- `error` - `ErrorDetails` (code, message, trace)
- `clarification` - `Clarification` (question, clarification_request_id)
- `formation_id`, `user_id`, `processing_time`, `raw`


## Configuration

```python
formation = FormationClient(
    server_url="http://localhost:7890",
    formation_id="my-assistant",
    client_key="your_client_key",
    timeout=30,        # Request timeout (seconds)
    max_retries=3,     # Retry count for 429/5xx
    debug=True,        # Enable debug logging
)
```

Environment variable `MUXI_DEBUG=1` also enables debug logging.


## Examples

### Chat Bot

```python
from muxi import FormationClient

formation = FormationClient(
    server_url="http://localhost:7890",
    formation_id="my-assistant",
    client_key="your_client_key",
)

while True:
    user_input = input("You: ")
    if user_input.lower() == "quit":
        break

    print("Assistant: ", end="")
    for event in formation.chat_stream({"message": user_input}, user_id="user_123"):
        if event.get("type") == "text":
            print(event.get("text"), end="", flush=True)
    print()
```

### With Session Persistence

```python
from muxi import FormationClient

formation = FormationClient(
    server_url="http://localhost:7890",
    formation_id="my-assistant",
    client_key="your_client_key",
)

session_id = None

while True:
    user_input = input("You: ")
    if user_input.lower() == "quit":
        break

    print("Assistant: ", end="")
    for event in formation.chat_stream(
        {"message": user_input, "session_id": session_id},
        user_id="user_123"
    ):
        if event.get("type") == "text":
            print(event.get("text"), end="", flush=True)
        elif event.get("session_id"):
            session_id = event.get("session_id")
    print()
```


## Learn More

- [TypeScript SDK](typescript.md)
- [Go SDK](go.md)
- [API Reference](reference/api-reference.md)
