---
title: Streaming Responses
description: Consume chat responses over Server-Sent Events
---
# Streaming Responses

MUXI streams chat responses with Server-Sent Events (SSE). Streaming avoids
waiting for the full response before rendering generated tokens.

## Request a Stream

Set `stream: true`, send `Accept: text/event-stream`, and keep the connection
open:

```bash
curl -N -X POST http://localhost:7890/api/my-assistant/v1/chat \
  -H "Content-Type: application/json" \
  -H "Accept: text/event-stream" \
  -H "X-Muxi-Client-Key: fmc_..." \
  -H "X-Muxi-User-ID: user_123" \
  -d '{"message":"Tell me a story","stream":true}'
```

The SDK streaming methods set `stream: true` automatically.

## Wire Format

Text tokens use ordinary SSE message frames. The runtime sends the token in a
JSON `token` field:

```text
data: {"token":"Hello"}

data: {"token":" there"}

event: done
data: {"finished":true}
```

A response with UI widgets emits one `ui` event immediately before `done`:

```text
event: ui
data: {"ui":[{"type":"options","id":"region","prompt":"Choose a region","options":[{"value":"us","label":"United States"}]}]}
```

The chat stream contract defines message, `ui`, `done`, and `error` frames. Tool
execution details belong to the observability event stream, not the client chat
stream.

## Python

```python
import json
from muxi import parse_ui_widgets

for event in formation.chat_stream(
    {"message": "Tell me a story"},
    user_id="user_123",
):
    if event["event"] == "message":
        payload = json.loads(event["data"])
        token = payload.get("token")
        if isinstance(token, str):
            print(token, end="", flush=True)
    elif event["event"] == "ui":
        widgets = parse_ui_widgets(event)
        render_widgets(widgets)
    elif event["event"] == "done":
        print()
```

The Python SDK yields raw SSE frames as dictionaries with `event` and `data`
fields. Stream error frames are raised as SDK exceptions.

## TypeScript

```typescript
for await (const chunk of formation.chatStream(
  { message: "Tell me a story" },
  "user_123"
)) {
  if (typeof chunk.token === "string") {
    process.stdout.write(chunk.token);
  } else if (chunk.type === "ui") {
    renderWidgets(chunk.ui);
  } else if (chunk.type === "done") {
    process.stdout.write("\n");
  }
}
```

The TypeScript SDK parses JSON data and adds `type` for named SSE events.

## Go

```go
stream, errs := formation.ChatStream(ctx, &muxi.ChatRequest{
    Message: "Tell me a story",
    UserID:  "user_123",
})

for chunk := range stream {
    if token, ok := chunk.Raw["token"].(string); ok {
        fmt.Print(token)
    }
    if chunk.Type == "ui" {
        renderWidgets(chunk.UI)
    }
}
if err := <-errs; err != nil {
    log.Fatal(err)
}
```

## Browser Clients

Browser `EventSource` supports only GET requests, while chat streaming is a
POST request. Use `fetch()` and parse the response body as SSE, or use the
TypeScript SDK in environments where its streaming transport is available.
Do not split solely on network chunks: one SSE frame can span reads, and one
read can contain several frames.

## Connection Handling

- Do not apply ordinary short request timeouts to a stream.
- Treat `done` as the successful end-of-turn marker.
- Treat `error` as terminal and surface the returned error payload.
- Ignore unknown named events for forward compatibility.
- Reconnect by starting a new chat request. Chat POST streams do not use SSE
  event IDs for automatic resume.
- Cancel the request or close the response body when the user stops generation.

Streaming latency depends on the model, provider, tools, network, and formation
workflow. Measure time-to-first-token in the deployed environment instead of
assuming a fixed baseline.

See [Response UI Widgets](../reference/response-ui-widgets.md) for supported
widget types and reply payloads.
