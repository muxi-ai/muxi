---
title: Rust SDK
description: Native Rust client for MUXI formations
---
# Rust SDK

**GitHub:** [`muxi-ai/muxi-rust`](https://github.com/muxi-ai/muxi-rust)

## Formation Client

```rust
use muxi::{FormationClient, FormationConfig};

let config = FormationConfig::new(
    "http://localhost:7890",
    "my-assistant",
    "fmc_...",
    "fma_...",
);
let client = FormationClient::new(config)?;
```

Set `config.mode` to `"draft"` before constructing the client for local drafts.
Use `FormationConfig::with_base_url` for a direct Runtime `/v1` connection.

## Chat

```rust
use serde_json::json;

let response = client.chat(
    json!({"message": "Hello!", "session_id": "sess_abc123"}),
    Some("user_123"),
).await?;
println!("{}", response["response"]);
```

## Streaming

```rust
use futures::StreamExt;

let stream = client.chat_stream(
    json!({"message": "Tell me a story"}),
    Some("user_123"),
);
futures::pin_mut!(stream);

while let Some(event) = stream.next().await {
    let event = event?;
    if event.event == "message" {
        let data: serde_json::Value = serde_json::from_str(&event.data)?;
        if let Some(token) = data["token"].as_str() {
            print!("{}", token);
        }
    }
}
```

SSE events expose `event` and `data`. Parse named `ui` events with the SDK's UI
helper and treat error frames as terminal.

## Learn More

- [SDK Overview](README.md)
- [Streaming Responses](../deep-dives/real-time-streaming.md)
- [API Reference](../reference/api-reference.md)
