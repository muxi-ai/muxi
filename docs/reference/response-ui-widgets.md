---
title: Response UI Widgets
description: Optional typed affordances the runtime attaches to chat responses
---

# Response UI Widgets

## Optional, typed affordances on a chat response

A chat response can carry an optional `ui` array of **widgets** - small, typed
affordances a client can render natively: a set of choices to pick from, a link
to send the user somewhere, or a UI resource relayed from an MCP server.

The runtime never renders anything. Clients that understand a widget type render
it; clients that don't ignore it. The response `text` always carries the
fallback, so the interaction works even with no widget support at all. Treat
unknown widget types as a no-op (progressive enhancement) - never as an error.

> [!NOTE]
> Widgets are produced only by trusted runtime producers (formation config, tool
> results, clarification state, MCP servers), never from free-form LLM output. This
> is what makes an `action_link` URL safe to render: it can only reach a widget
> through a producer that names its source.


## How widgets arrive

For non-streaming chat, widgets appear in the response message's optional
`ui` array. For streaming chat, they ride a dedicated `event: ui` SSE frame
emitted at end-of-turn, just before `event: done`:

```
data: {"token": "Which region should I use?"}

event: ui
data: {"ui": [{"type": "options", "id": "ui_a1b2c3", "prompt": "Which region?", "options": [{"value": "us", "label": "United States"}, {"value": "eu", "label": "Europe"}], "multi": false}]}

event: done
data: {"finished": true}
```

A turn with no widgets is byte-identical to a stream that never had this
feature - the `event: ui` frame is simply absent.

In a non-streaming response with no widgets, the message omits `ui` rather than
serializing it as `null` or an empty array.


## Widget types

### `options`

A short question with choices to pick from instead of typing.

```json
{
  "type": "options",
  "id": "ui_a1b2c3d4e5f6g7h8i9j0k",
  "prompt": "Which region should I deploy to?",
  "options": [
    { "value": "us", "label": "United States" },
    { "value": "eu", "label": "Europe" }
  ],
  "multi": false
}
```

| Field | Type | Description |
|-------|------|-------------|
| `type` | string | Always `"options"` |
| `id` | string | Runtime-assigned widget id (`ui_` + nanoid); use it on the reply path |
| `prompt` | string | Short question the options answer |
| `options` | array | `{value, label}` items (up to 25); `label` falls back to `value` |
| `multi` | bool | Whether multiple options may be selected (currently always `false`) |

### `action_link`

Send the user somewhere external - a credential portal, OAuth consent screen, or
dashboard.

```json
{
  "type": "action_link",
  "id": "ui_k0j9i8h7g6f5e4d3c2b1a",
  "label": "Connect GitHub",
  "url": "https://github.com/login/oauth/authorize?...",
  "hint": "Opens GitHub to authorize access"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `type` | string | Always `"action_link"` |
| `id` | string | Runtime-assigned widget id |
| `label` | string | Button/link text |
| `url` | string | Destination (only `http`/`https` URLs are ever emitted) |
| `hint` | string | Optional secondary text |

### `mcp_resource`

A gateway passthrough of an MCP App / UI resource returned by an external MCP
server. MUXI relays the embedded `ui://` resource **verbatim** - no rendering,
no interpretation, no execution. It is untrusted external content; the producing
server and tool are recorded on the widget as provenance.

```json
{
  "type": "mcp_resource",
  "id": "ui_z9y8x7w6v5u4t3s2r1q0p",
  "resource": "ui://weather-card",
  "data": "<!doctype html>...",
  "server": "weather-mcp",
  "tool": "get_forecast",
  "mime_type": "text/html",
  "encoding": "base64"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `type` | string | Always `"mcp_resource"` |
| `id` | string | Runtime-assigned widget id |
| `resource` | string | The resource URI (only the `ui://` scheme is accepted) |
| `data` | string | Embedded content, verbatim (text, or base64 blob) |
| `server` | string | MCP server id that returned the resource (provenance) |
| `tool` | string | Tool name whose result carried the resource (provenance) |
| `mime_type` | string | Declared mimeType, when present |
| `encoding` | string | `"text"` (default, omitted on the wire) or `"base64"` |


## Producing widgets

Widgets can only be created by trusted producers - never from free-form LLM
output. This is what makes an `action_link` URL safe to render.

- **`options`** - produced automatically by [clarifications](../concepts/clarification.md)
  that offer enumerable choices (e.g. credential account selection).
- **`action_link`** - a URL can only enter a widget through a producer that
  names its source:
  - A top-level `links:` formation section maps a name to a link, surfaced on
    credential-redirect responses:
    ```yaml
    # formation.afs
    links:
      github:
        label: "Connect GitHub"
        url: "https://github.com/login/oauth/authorize?..."
        hint: "Opens GitHub to authorize access"
    ```
    Use the credential service name as the key, or `credential_portal` as a
    generic fallback.
  - A tool result may carry a `_link` key (mirroring the `_artifact` convention)
    to surface an action link produced by a tool.
- **`mcp_resource`** - relayed verbatim when an external MCP tool result carries
  an embedded `ui://` resource block. No configuration; provenance (server +
  tool) is recorded on the widget.

## Replying to a widget

An `options` widget that came from a clarification can be answered on the next
turn. Send the selected value back as a `ui_response` hint alongside the message
text (the text is always sufficient on its own; the hint just pins the choice
deterministically):

```json
{
  "message": "United States",
  "ui_response": { "id": "ui_a1b2c3d4e5f6g7h8i9j0k", "value": "us" }
}
```

The runtime resolves the hint only when `id` matches the widget that asked the
question **and** `value` is one of the offered options. Unknown or stale ids are
ignored and the message stands alone. The runtime stays stateless - there is no
server-side widget store.

## Channel-native rendering

The bundled Telegram, Slack, and Discord transformers render `options` and
`action_link` widgets as native buttons while preserving the complete text
fallback. Email remains text-only. Formation-local transformer files are
unchanged unless they explicitly use the channel UI namespace:

| Channel | Template value |
|---------|----------------|
| Telegram | `${{ ui.telegram.reply_markup }}` |
| Slack | `${{ ui.slack.blocks }}` |
| Discord | `${{ ui.discord.components }}` |

Option callbacks encode `<widget_id>#<option_index>`, keeping Telegram callback
data within its 64-byte limit and avoiding server-side widget storage. Configure
the inbound trigger's `parse.ui_response` path to round-trip button presses:

```yaml
# Telegram
parse:
  ui_response: "$.callback_query.data"

# Slack
parse:
  ui_response: "$.actions[0].value"

# Discord
parse:
  ui_response: "$.data.custom_id"
```

Foreign callback values decode to no hint, so the accompanying message still
stands alone. Discord component buttons require a bot/application bridge;
plain Discord webhooks cannot send components.


## SDK helpers

The SDKs surface widgets idiomatically:

- **Go** decodes the frame into `ChatChunk.UI` (`[]UIWidget` with typed
  `UIOption`s).
- **TypeScript** yields the frame as a chunk of `{ type: "ui", ui: UIWidget[] }`
  (`UIWidget`/`UIOption` interfaces exported).
- **Python, Ruby, PHP, C#, Java, Kotlin, Swift, Dart, Rust, C++** expose a
  `parse_ui_widgets` / `parseUiWidgets` helper that extracts the widgets array
  from a `ui` stream frame and returns empty for any other or malformed frame.

See the [SDK reference](../sdks/README.md) for per-language signatures.


## Size limits

Widgets are clamped so a misbehaving producer cannot bloat responses. Oversized
widgets are dropped whole (never truncated - the text fallback is always
complete):

| Limit | Default |
|-------|---------|
| Widgets per envelope | 8 |
| Bytes per standard widget | 4 KB |
| Combined standard-widget bytes | 16 KB |
| Options per `options` widget | 25 |
| Bytes per `mcp_resource` widget | 64 KB |
| Combined `mcp_resource` bytes | 128 KB |


## Learn More

- [Build Custom UI](../guides/build-custom-ui.md) - render widgets in a frontend
- [Real-Time Streaming](../deep-dives/real-time-streaming.md) - SSE event stream
- [Clarifications](../concepts/clarification.md) - where `options` widgets come from
