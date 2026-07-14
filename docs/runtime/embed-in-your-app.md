---
title: Embedding the Runtime
description: Run MUXI formations directly in your Python application
---
# Embedding the Runtime

## Run formations without a server

While most users access MUXI through the server, you can embed the runtime directly into your Python application. This enables custom deployments, integration with existing systems, and streamlined local development.

**When to embed vs. use the server:**

| Use Case | Recommendation |
|----------|----------------|
| Production deployment with multiple formations | Use the server |
| Adding AI to an existing Python app | Embed the runtime |
| Local development and testing | Embed the runtime |
| Multi-language environment (JS, Go, etc.) | Use the server + SDK |
| Single formation, simple deployment | Either works |

**Example:** You have an existing Django app and want to add an AI assistant. Instead of running a separate MUXI server and making HTTP calls, embed the runtime directly - your assistant runs in-process with zero network overhead.


## Installation

```bash
pip install muxi-runtime
```

## Basic Usage

```python
import asyncio
from muxi.runtime import Formation

async def main():
    formation = Formation()
    await formation.load("./my-formation")
    await formation.start_overlord()

    try:
        response = await formation.get_overlord().chat(
            "Hello!",
            user_id="user_123",
        )
        print(response)
    finally:
        await formation.ashutdown()

asyncio.run(main())
```

`Formation` manages the runtime lifecycle. Conversations run through the loaded
formation's Overlord.

## Running the Formation API Server

```python
import asyncio
from muxi.runtime import Formation

async def main():
    formation = Formation()
    await formation.load("./my-formation")
    await formation.start_server(host="0.0.0.0", port=8001)

asyncio.run(main())
```

`start_server()` starts the Overlord automatically and uses the formation's
`server` configuration unless `host` or `port` is overridden.

### FastAPI

```python
from contextlib import asynccontextmanager
from fastapi import FastAPI
from muxi.runtime import Formation

formation = Formation()

@asynccontextmanager
async def lifespan(app: FastAPI):
    await formation.load("./my-formation")
    await formation.start_overlord()
    yield
    await formation.ashutdown()

app = FastAPI(lifespan=lifespan)

@app.post("/chat")
async def chat(message: str, user_id: str):
    response = await formation.get_overlord().chat(
        message,
        user_id=user_id,
    )
    return {"response": response}
```

## Operational Tips

- **Load once, reuse:** Create and load one `Formation` during application
  startup instead of loading per request.
- **Configure in the formation:** Models, memory, agents, server settings, and
  observability remain in the formation files.
- **Protect secrets:** Store credentials in `secrets.enc`; do not embed them in
  application code.
- **Shut down cleanly:** Await `formation.ashutdown()` so background services
  and connections close.
- **Preserve user isolation:** Supply a stable `user_id` for every chat call.

## Limitations

Embedding gives your application direct ownership of the runtime process,
resource lifecycle, scaling, and failure recovery. Use MUXI Server when you
need managed multi-formation deployment, routing, draft environments, or
blue-green rollback.
