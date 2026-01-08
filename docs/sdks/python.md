# Python SDK

Python client for MUXI formations.

## Installation

```bash
pip install muxi-sdk
```

## Quick Start

```python
from muxi import Formation

formation = Formation(
    url="http://localhost:8001",
    client_key="fmc_..."
)

response = formation.chat("Hello!")
print(response.text)
```

## Formation Client

### Initialize

```python
from muxi import Formation

# With client key
formation = Formation(
    url="http://localhost:8001",
    client_key="fmc_..."
)

# With admin key
formation = Formation(
    url="http://localhost:8001",
    admin_key="fma_..."
)
```

### Chat

```python
# Simple message
response = formation.chat("Hello!")
print(response.text)

# With options
response = formation.chat(
    message="Research AI trends",
    agent="researcher",
    session_id="sess_123",
    user_id="user_456"
)
```

### Streaming

```python
for chunk in formation.chat_stream("Tell me a story"):
    print(chunk.text, end="", flush=True)
```

### Sessions

```python
# Create session
session = formation.create_session()
print(session.id)  # sess_abc123

# Chat with session
response = formation.chat(
    "Hello!",
    session_id=session.id
)

# Get session history
history = formation.get_session(session.id)
for message in history.messages:
    print(f"{message.role}: {message.content}")
```

### Agents

```python
# List agents
agents = formation.list_agents()
for agent in agents:
    print(agent.id, agent.name)

# Target specific agent
response = formation.chat(
    "Research this topic",
    agent="researcher"
)
```

### Triggers

```python
response = formation.trigger(
    "github-issue",
    data={
        "repository": "muxi/runtime",
        "issue": {
            "number": 123,
            "title": "Bug report"
        }
    }
)
```

### Multi-User

```python
# User isolation
response = formation.chat(
    "Remember my preference",
    user_id="user_123"
)

# Different user
response = formation.chat(
    "What's my preference?",
    user_id="user_456"
)
```

## Server Client

### Initialize

```python
from muxi import ServerClient

server = ServerClient(
    url="http://localhost:7890",
    key_id="MUXI_...",
    secret_key="sk_..."
)
```

### List Formations

```python
formations = server.list_formations()
for f in formations:
    print(f.id, f.status, f.port)
```

### Deploy Formation

```python
result = server.deploy("/path/to/formation")
print(result.port)
```

### Stop/Start/Restart

```python
server.stop_formation("my-assistant")
server.start_formation("my-assistant")
server.restart_formation("my-assistant")
```

### Delete Formation

```python
server.delete_formation("my-assistant")
```

## Async Support

```python
import asyncio
from muxi import AsyncFormation

async def main():
    formation = AsyncFormation(
        url="http://localhost:8001",
        client_key="fmc_..."
    )
    
    response = await formation.chat("Hello!")
    print(response.text)
    
    # Async streaming
    async for chunk in formation.chat_stream("Story"):
        print(chunk.text, end="")

asyncio.run(main())
```

## Error Handling

```python
from muxi import MuxiError, AuthenticationError, FormationError

try:
    response = formation.chat("Hello!")
except AuthenticationError:
    print("Invalid API key")
except FormationError as e:
    print(f"Formation error: {e}")
except MuxiError as e:
    print(f"MUXI error: {e}")
```

## Configuration

```python
Formation(
    url="http://localhost:8001",
    client_key="fmc_...",
    timeout=30,           # Request timeout
    max_retries=3,        # Retry on failure
)
```

## Examples

### Chat Bot

```python
from muxi import Formation

formation = Formation(url="...", client_key="...")

session = formation.create_session()

while True:
    user_input = input("You: ")
    if user_input == "/exit":
        break
    
    response = formation.chat(
        user_input,
        session_id=session.id
    )
    print(f"Assistant: {response.text}")
```

### Streaming Response

```python
print("Assistant: ", end="")
for chunk in formation.chat_stream("Tell me about AI"):
    print(chunk.text, end="", flush=True)
print()
```
