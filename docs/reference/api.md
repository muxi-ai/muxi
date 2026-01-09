---
title: API Reference
description: Direct API access for advanced users, SDK developers, and custom integrations in languages without SDK support.
section: reference
doc-type: doc
---

# API Reference

## Direct API access for advanced users, SDK developers, and custom integrations in languages without SDK support.

> [!NOTE]
> **Most developers should use the SDKs or CLI instead of calling these APIs directly.**
>
> These API references are designed for:
> - SDK developers building/maintaining MUXI client libraries
> - Custom integrations in languages without SDK support yet
> - Advanced automation and deployment pipelines
> - Understanding MUXI's capabilities at the protocol level


## Which API Do I Need?

MUXI provides two complementary APIs that work together to orchestrate and run AI formations:

:::: cols=2

[[card]]
### 🏗️ Server Management API

**For:** Platform engineers, DevOps teams, deployment automation

**What it does:**
- Deploy and update formations
- Manage formation lifecycle (start, stop, restart)
- Monitor server health and formation status
- Control port allocation and routing
- Track deployments with audit logs

**Authentication:** HMAC signatures (management access)

**Base URL:** `http://localhost:7890`

<a href="/docs/api/server" target="server-api">View Server API Reference →</a>
[[/card]]

[[card]]
### 🤖 Formation Runtime API

**For:** Application developers building custom integrations

**What it does:**
- Send chat messages to formations
- Manage user sessions and history
- Stream real-time responses via SSE
- Configure agents and secrets
- Monitor formation activity

**Authentication:** Admin Key (management) / Client Key (user interactions)

**Base URL:** `http://localhost:8000-9000/v1` (per formation)

<a href="/docs/api/formation" target="formation-api">View Formation API Reference →</a>
[[/card]]

::::

---

## Quick Decision Guide

[[steps]]

[[step What are you trying to do?]]

**Deploying or managing formations?**
→ Use the **Server Management API** (or better yet, the CLI!)

**Building an app that talks to formations?**
→ Use the **Formation Runtime API** (or better yet, the Python/JS SDK!)

[[/step]]

[[step Are you using the right tool?]]

Before diving into raw APIs, consider these higher-level tools:

**For Python developers:**
```python
from muxi import Formation

formation = Formation("my-team")
response = formation.chat("What's the weather?")
```
[Python SDK Documentation →](sdks/python)

**For JavaScript/TypeScript developers:**
```javascript
import { Formation } from '@muxi/sdk';

const formation = new Formation('my-team');
const response = await formation.chat("What's the weather?");
```
[JavaScript SDK Documentation →](sdks/javascript)

**For deployment automation:**
```bash
muxi deploy my-formation.tar.gz
muxi formations list
muxi formations logs my-team
```
[CLI Documentation →](cli)

[[/step]]

[[step Still need the raw API?]]

Great! Here's what to know:

**Server API:**
- Requires HMAC authentication
- Manages formations on port 7890
- Used for infrastructure/DevOps operations

**Formation API:**
- Each formation runs on its own port (8000-9000)
- Admin Key for configuration, Client Key for user interactions
- Used for building applications

[[/step]]

[[/steps]]

---

## API Comparison

| Feature | Server API | Formation API |
|---------|------------|---------------|
| **Purpose** | Manage formations (infrastructure) | Interact with formations (application) |
| **Audience** | Platform engineers, DevOps | Application developers |
| **Authentication** | HMAC signatures | Admin/Client API keys |
| **Base Port** | 7890 | 8000-9000 (per formation) |
| **Use Cases** | Deploy, update, monitor | Chat, sessions, configuration |
| **Best For** | Automation pipelines | User-facing applications |

---

## Authentication Overview

### Server API: HMAC Signatures

The Server API uses HMAC-SHA256 signatures to authenticate management requests:

```
Authorization: MUXI-HMAC key={YOUR_KEY}, timestamp={UNIX_TIME}, signature={BASE64_SIGNATURE}
```

**How to create a signature:**
1. Build message: `"{timestamp};{method};{path}"`
2. Sign with HMAC-SHA256: `HMAC-SHA256(your_secret, message)`
3. Encode to base64: `base64(signature)`

**Example:**
```bash
# Generate signature (pseudo-code)
timestamp=$(date +%s)
message="$timestamp;POST;/rpc/formations"
signature=$(echo -n "$message" | openssl dgst -sha256 -hmac "$SECRET" -binary | base64)

curl -X POST http://localhost:7890/rpc/formations \
  -H "Authorization: MUXI-HMAC key=MUXI_key123, timestamp=$timestamp, signature=$signature" \
  -H "Content-Type: application/gzip" \
  --data-binary @formation.tar.gz
```

[Learn more about HMAC authentication →](server/authentication)

---

### Formation API: API Keys

The Formation API uses two types of keys:

**Admin Key** (`X-MUXI-ADMIN-KEY` header):
- For managing formation configuration
- Controls agents, secrets, settings, logs
- Keep this private! It's like your master key 🔐

**Client Key** (`X-MUXI-CLIENT-KEY` header):
- For application code interacting with users
- Handles chat, sessions, events
- Safe to use in application code ✨

**Example:**
```bash
# Admin request (configure formation)
curl http://localhost:8271/v1/agents \
  -H "X-MUXI-ADMIN-KEY: fma_admin_key_here"

# Client request (send chat message)
curl http://localhost:8271/v1/chat \
  -H "X-MUXI-CLIENT-KEY: fmc_client_key_here" \
  -H "X-Muxi-User-ID: user-123" \
  -d '{"message": "Hello!"}'
```

[Learn more about API keys →](concepts/authentication)

---

## Response Formats

### Server API Response Format

```json
{
  "success": true,
  "data": {
    // Response payload
  }
}
```

**Error format:**
```json
{
  "success": false,
  "error": "ValidationError",
  "message": "Formation 'my-team' already exists",
  "code": 409
}
```

---

### Formation API Response Format

```json
{
  "object": "chat_response",
  "timestamp": 1706616000000,
  "type": "chat.completed",
  "request": {
    "id": "req_abc123",
    "idempotency_key": null
  },
  "success": true,
  "error": null,
  "data": {
    // Response payload
  }
}
```

**Error format:**
```json
{
  "object": "error",
  "timestamp": 1706616000000,
  "type": "error.validation",
  "request": {
    "id": "req_abc123",
    "idempotency_key": null
  },
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "The 'message' field is required",
    "data": {
      "validation_errors": [...]
    }
  },
  "data": {}
}
```

---

## Common Use Cases

### Deploying a Formation (Server API)

```bash
curl -X POST http://localhost:7890/rpc/formations \
  -H "Authorization: MUXI-HMAC key=$KEY, timestamp=$TS, signature=$SIG" \
  -H "Content-Type: application/gzip" \
  -H "X-Formation-ID: my-team" \
  -H "X-Formation-Version: 1.0.0" \
  --data-binary @my-team.tar.gz
```

[Full endpoint documentation →](api/server#deploy-formation)

---

### Sending a Chat Message (Formation API)

```bash
curl -X POST http://localhost:8271/v1/chat \
  -H "X-MUXI-CLIENT-KEY: $CLIENT_KEY" \
  -H "X-Muxi-User-ID: user-123" \
  -H "Content-Type: application/json" \
  -d '{
    "message": "What is the weather in San Francisco?"
  }'
```

[Full endpoint documentation →](api/formation#chat)

---

### Streaming Responses (Formation API)

```bash
curl -X POST http://localhost:8271/v1/chat \
  -H "X-MUXI-CLIENT-KEY: $CLIENT_KEY" \
  -H "X-Muxi-User-ID: user-123" \
  -H "Accept: text/event-stream" \
  -H "Content-Type: application/json" \
  -d '{
    "message": "Write a poem about AI"
  }'
```

Responses stream as Server-Sent Events (SSE):

```
data: {"token": "Once"}

data: {"token": " upon"}

data: {"token": " a"}

data: {"token": " time"}

event: done
data: {"finished": true}
```

[Learn about streaming →](api/formation#streaming)

---

## Next Steps

### View Full API References

- [Server Management API Reference →](api/server)
- [Formation Runtime API Reference →](api/formation)

### Prefer High-Level Tools?

- [Python SDK →](../sdks/python)
- [JavaScript/TypeScript SDK →](../sdks/javascript)
- [Go SDK →](../sdks/go)
- [CLI Reference →](../cli)

### Learn More

- [Authentication Guide →](../concepts/authentication)
- [Deployment Guide →](../server/deployment)
- [Formation Configuration →](../reference/schema)

---

> [!TIP]
> **Building an SDK for a new language?**
>
> We'd love to have you contribute! Check out our [SDK Development Guide](https://muxi-ai.org/contributing)
> or reach out on [GitHub Discussions](https://github.com/org/muxi-ai/discussions).
