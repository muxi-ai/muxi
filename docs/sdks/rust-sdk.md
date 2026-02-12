---
title: Rust SDK
description: Native Rust client for MUXI formations
---
# Rust SDK

## Rust access to your agents

Build Rust applications that interact with MUXI formations. Full support for chat, streaming with async/await, and all Formation API operations.

**GitHub:** [`muxi-ai/muxi-rust`](https://github.com/muxi-ai/muxi-rust)  
**crates.io:** [`muxi-rust`](https://crates.io/crates/muxi-rust)

## Installation

```bash
cargo add muxi-rust
```

Or add to your `Cargo.toml`:

```toml
[dependencies]
muxi-rust = "0.1"
tokio = { version = "1", features = ["full"] }
```

## Quick Start

```rust
use muxi_rust::FormationClient;

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let client = FormationClient::new(
        "http://localhost:7890",
        "my-assistant",
        "your_client_key",
    );

    // Check health
    let health = client.health().await?;
    println!("{:?}", health);
    
    Ok(())
}
```

## Chat (Streaming)

```rust
use futures::StreamExt;

let mut stream = client.chat_stream("Hello!", "user_123").await?;

while let Some(event) = stream.next().await {
    match event?.event_type.as_str() {
        "text" => print!("{}", event.text.unwrap_or_default()),
        "done" => break,
        _ => continue,
    }
}
```

## Chat (Non-Streaming)

```rust
let response = client.chat("Hello!", "user_123").await?;
println!("{}", response.response);
```

## Memory

```rust
// Get memories
let memories = client.get_memories("user_123").await?;

// Add memory
client.add_memory("user_123", "preference", "User prefers Rust").await?;

// Clear buffer
client.clear_user_buffer("user_123").await?;
```

## Sessions

```rust
// List sessions
let sessions = client.get_sessions("user_123").await?;

// Get session messages
let messages = client.get_session_messages("sess_abc123", "user_123").await?;
```

## Server Client

For managing formations (deploy, start, stop):

```rust
use muxi_rust::ServerClient;

let server = ServerClient::new(
    "http://localhost:7890",
    "muxi_pk_...",
    "muxi_sk_...",
);

// List formations
let formations = server.list_formations().await?;

// Deploy
server.deploy_formation("my-bot.tar.gz").await?;

// Stop/start/restart
server.stop_formation("my-bot").await?;
server.start_formation("my-bot").await?;
```

## Error Handling

```rust
use muxi_rust::{MuxiError, FormationClient};

match client.chat("Hello!", "user_123").await {
    Ok(response) => println!("{}", response.response),
    Err(MuxiError::Authentication(msg)) => {
        eprintln!("Auth failed: {}", msg);
    }
    Err(MuxiError::RateLimit { retry_after }) => {
        eprintln!("Rate limited, retry after: {}s", retry_after);
    }
    Err(e) => {
        eprintln!("Error: {}", e);
    }
}
```

## Features

Enable optional features in `Cargo.toml`:

```toml
[dependencies]
muxi-rust = { version = "0.1", features = ["rustls-tls"] }
```

Available features:
- `native-tls` - Use native TLS (default)
- `rustls-tls` - Use rustls for TLS
- `tracing` - Enable tracing support

## Learn More

- [Full documentation on GitHub](https://github.com/muxi-ai/muxi-rust)
- [API Reference](../reference/api-reference.md)
