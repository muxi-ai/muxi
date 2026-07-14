---
title: C++ SDK
description: Native C++ client for MUXI formations
---
# C++ SDK

**GitHub:** [`muxi-ai/muxi-cpp`](https://github.com/muxi-ai/muxi-cpp)

## Formation Client

```cpp
#include <muxi/formation_client.hpp>

muxi::FormationConfig config;
config.server_url = "http://localhost:7890";
config.formation_id = "my-assistant";
config.client_key = "fmc_...";
config.admin_key = "fma_...";

muxi::FormationClient client(config);
```

Set `config.mode = "draft"` for local drafts, or set `config.base_url` to a
direct Runtime `/v1` base URL.

## Chat

```cpp
auto response = client.chat(
    {{"message", "Hello!"}, {"session_id", "sess_abc123"}},
    "user_123"
);
std::cout << response["response"].get<std::string>();
```

## Streaming

```cpp
client.chat_stream(
    {{"message", "Tell me a story"}},
    "user_123",
    [](const muxi::SseEvent& event) {
        if (event.event != "message") return;
        auto data = nlohmann::json::parse(event.data);
        if (data.contains("token") && data["token"].is_string()) {
            std::cout << data["token"].get<std::string>();
        }
    }
);
```

SSE events expose `event` and `data`. Named `ui` events carry optional response
widgets; error frames are terminal.

## Sessions and Memory

```cpp
auto sessions = client.get_sessions("user_123", 50);
auto messages = client.get_session_messages("sess_abc123", "user_123");
auto memories = client.get_memories("user_123");
client.add_memory("user_123", "preference", "User prefers C++");
client.clear_user_buffer("user_123");
```

## Learn More

- [SDK Overview](README.md)
- [Streaming Responses](../deep-dives/real-time-streaming.md)
- [API Reference](../reference/api-reference.md)
