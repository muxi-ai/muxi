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

Runtime events use ordinary SSE message frames. Each frame carries one event object
in a JSON `token` field, and the object's `type` says what kind of event it is:

```text
data: {"token":{"type":"progress","content":"Getting started","stage":"init"}}

data: {"token":{"type":"content","content":"Hello"}}

data: {"token":{"type":"content","content":" there"}}

data: {"token":{"type":"completed","content":"Hello there","status":"success"}}

event: done
data: {"finished":true}
```

Every event object carries `request_id`, `user_id`, `session_id`, `type`, `content`,
and `timestamp`, plus event-specific metadata such as `stage` or `status`.

A response with UI widgets emits one `ui` event immediately before `done`:

```text
event: ui
data: {"ui":[{"type":"options","id":"region","prompt":"Choose a region","options":[{"value":"us","label":"United States"}]}]}
```

The chat stream contract defines message, `ui`, `done`, and `error` frames. Tool
execution details belong to the observability event stream, not the client chat
stream.

## Token Streaming

The final response streams as incremental `content` events - one per model chunk,
passed through exactly as the provider chunks them, with no coalescing. The terminal
`completed` event still carries the full text, so concatenating the deltas and
reading `completed.content` give you the same answer. Clients written before token
streaming keep working unchanged.

```yaml
overlord:
  response:
    streaming: true
    stream_tokens: true    # default: true
```

Deltas are suppressed when `stream_tokens` is `false`, and when the response format
is `json` or `html` - both rewrite the text after generation, so a partial stream
would not be valid output.

### Discontinuity

If the response stream fails *after* at least one delta was published, the terminal
`completed` event carries `stream_discontinuity: true`. The deltas you accumulated
may be incomplete or inconsistent: discard them and render `completed.content`, which
is always authoritative.

The flag is additive and only ever appears as `true` - a turn that streamed cleanly
simply omits it, as does a failure that produced no deltas at all.

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
