---
title: MUXI Documentation
description: The infrastructure layer for AI agents - deploy to production with one command
doc-type: home
---

# MUXI Documentation

## Deploy AI agents to production with one command

<!-- TODO: Add hero video showing deployment flow -->
<!-- [Video placeholder: 60s demo of creating and deploying a formation] -->

MUXI is infrastructure for running AI agents. Define agents in YAML files called **formations**, deploy them to a server, and access them via API or SDK.

Think: **Docker for AI agents**.

> [!TIP]
> New to MUXI? Start with the [Quickstart](quickstart.md) to get running in 5 minutes.

---

## Get Started

:::: cols=3

(quickstart.md)[[card]]

#### Quickstart
Get your first formation running in 5 minutes.

[[/card]]

(installation/README.md)[[card]]

#### Installation
Install MUXI on macOS, Linux, Windows, or Docker.

[[/card]]

(formations/README.md)[[card]]

#### Formations
Learn how to define AI systems in YAML.

[[/card]]

::::

---

## How It Works

```
1. Define    →    2. Deploy    →    3. Use
   (YAML)           (CLI)           (API/SDK)
```

### Define a formation

```yaml
# formation.afs
schema: "1.0.0"
id: my-assistant

llm:
  models:
    text: openai/GPT-5

agents:
  - id: assistant
    role: helpful assistant
```

### Deploy to your server

```bash
muxi deploy
```

### Use via API or SDK

[[tabs]]

[[tab curl]]
```bash
curl -X POST http://localhost:8001/v1/chat \
  -H "Content-Type: application/json" \
  -H "X-Muxi-Client-Key: $CLIENT_KEY" \
  -d '{"message": "Hello!"}'
```
[[/tab]]

[[tab Python]]
```python
from muxi import Formation

formation = Formation(url="http://localhost:8001", client_key="...")
response = formation.chat("Hello!")
print(response.text)
```
[[/tab]]

[[tab TypeScript]]
```typescript
import { Formation } from '@muxi/sdk';

const formation = new Formation({ url: 'http://localhost:8001', clientKey: '...' });
const response = await formation.chat('Hello!');
console.log(response.text);
```
[[/tab]]

[[tab Go]]
```go
formation := muxi.NewFormation(muxi.Config{
    URL:       "http://localhost:8001",
    ClientKey: "...",
})
response, _ := formation.Chat("Hello!")
fmt.Println(response.Text)
```
[[/tab]]

[[/tabs]]

---

## Explore by Topic

:::: cols=2

(server/README.md)[[card]]
#### Server
Orchestration platform for running formations.
[[/card]]

(cli/README.md)[[card]]
#### CLI
Command-line tool for managing MUXI.
[[/card]]

(sdks/README.md)[[card]]
#### SDKs
Python, TypeScript, and Go client libraries.
[[/card]]

(registry/README.md)[[card]]
#### Registry
Discover and share formations.
[[/card]]

::::

---

## Deep Dives

:::: cols=3

(deep-dives/orchestration.md)[[card]]
#### Multi-Agent
How agents work together.
[[/card]]

(deep-dives/streaming.md)[[card]]
#### Streaming
Real-time response streaming.
[[/card]]

(deep-dives/security.md)[[card]]
#### Security
Authentication and encryption.
[[/card]]

::::

---

## Next Steps

[+] [Run the Quickstart](quickstart.md)
[+] [Install MUXI](installation/README.md)
[+] [Explore formations](formations/README.md)
[+] [Browse the registry](https://registry.muxi.org)
