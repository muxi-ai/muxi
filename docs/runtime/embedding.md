# Embedding the Runtime

Run the MUXI runtime in your own application.

## Overview

While most users access MUXI through the server, you can embed the runtime directly for:

- Custom deployment scenarios
- Integration with existing systems
- Development and testing

## Installation

```bash
pip install muxi-runtime
```

## Basic Usage

[[tabs]]

[[tab Python]]
```python
from muxi.runtime import Formation, Config

# Load formation
formation = Formation.load("./my-formation")

# Send a message
response = formation.chat("Hello!")
print(response.text)
```
[[/tab]]

[[tab TypeScript]]
Runtime embedding is Python-first. Use the SDK to call a running formation:

```typescript
import { Formation } from '@muxi/sdk';

const formation = new Formation({ url: 'http://localhost:8001', clientKey: '...' });
const res = await formation.chat('Hello!');
console.log(res.text);
```
[[/tab]]

[[tab Go]]
Runtime embedding is Python-first. Use the Go SDK to call a running formation:

```go
formation := muxi.NewFormation(muxi.Config{
    URL:       "http://localhost:8001",
    ClientKey: "...",
})
resp, _ := formation.Chat("Hello!")
fmt.Println(resp.Text)
```
[[/tab]]

[[/tabs]]

## Configuration

```python
from muxi.runtime import Formation, Config

config = Config(
    port=8001,
    host="0.0.0.0",
    log_level="info"
)

formation = Formation.load(
    "./my-formation",
    config=config
)
```

## Running as Server

```python
from muxi.runtime import Formation

formation = Formation.load("./my-formation")
formation.serve(port=8001)
```

Or with uvicorn:

```python
from muxi.runtime import Formation

formation = Formation.load("./my-formation")
app = formation.as_asgi()

# Run with uvicorn
import uvicorn
uvicorn.run(app, host="0.0.0.0", port=8001)
```

## Programmatic Access

### Chat

```python
response = formation.chat(
    message="Hello!",
    user_id="user_123",
    session_id="sess_abc"
)

print(response.text)
print(response.agent)
```

### Streaming

```python
for chunk in formation.chat_stream("Tell me a story"):
    print(chunk.text, end="")
```

### Sessions

```python
# Create session
session = formation.create_session(user_id="user_123")

# Chat with session
response = formation.chat(
    "Hello",
    session_id=session.id
)

# Get history
history = formation.get_session(session.id)
```

### Agents

```python
# List agents
agents = formation.list_agents()

# Target specific agent
response = formation.chat(
    "Research this",
    agent="researcher"
)
```

### Triggers

```python
response = formation.trigger(
    "github-issue",
    data={
        "repository": "muxi/runtime",
        "issue": {"number": 123}
    }
)
```

## Events

Subscribe to runtime events:

```python
from muxi.runtime import Formation, EventType

formation = Formation.load("./my-formation")

@formation.on(EventType.CHAT_STARTED)
def on_chat(event):
    print(f"Chat started: {event.session_id}")

@formation.on(EventType.CHAT_COMPLETED)
def on_complete(event):
    print(f"Chat completed: {event.response.text[:50]}...")
```

## Custom Integrations

### FastAPI

```python
from fastapi import FastAPI
from muxi.runtime import Formation

app = FastAPI()
formation = Formation.load("./my-formation")

@app.post("/chat")
async def chat(message: str):
    response = await formation.chat_async(message)
    return {"response": response.text}
```

### Django

```python
from django.http import JsonResponse
from muxi.runtime import Formation

formation = Formation.load("./my-formation")

def chat_view(request):
    message = request.POST.get("message")
    response = formation.chat(message)
    return JsonResponse({"response": response.text})
```

## Use Cases

1. **Microservice** - Run as dedicated service
2. **Embedded** - Within existing application
3. **Serverless** - AWS Lambda, Cloud Functions
4. **Testing** - Unit/integration tests
5. **Development** - Custom dev workflows

## Limitations

When embedding:

- No server management features
- No multi-formation support
- Manual scaling
- No built-in monitoring

For production, consider using the full MUXI Server.
