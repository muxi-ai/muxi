---
title: C++ SDK
description: Native C++ client for MUXI formations
---
# C++ SDK

## C++ access to your agents

Build C++ applications that interact with MUXI formations. Header-only library with full support for chat, streaming, and all Formation API operations.

**GitHub:** [`muxi-ai/muxi-cpp`](https://github.com/muxi-ai/muxi-cpp)

## Installation

### CMake (FetchContent)

```cmake
include(FetchContent)
FetchContent_Declare(
    muxi
    GIT_REPOSITORY https://github.com/muxi-ai/muxi-cpp.git
    GIT_TAG main
)
FetchContent_MakeAvailable(muxi)

target_link_libraries(your_target PRIVATE muxi::muxi)
```

### Manual Installation

Copy the `include/muxi` directory to your project and include it:

```cpp
#include <muxi/muxi.hpp>
```

## Dependencies

- C++17 or later
- libcurl (for HTTP)
- nlohmann/json (included as header-only)

## Quick Start

```cpp
#include <muxi/muxi.hpp>
#include <iostream>

int main() {
    muxi::FormationClient client(
        "http://localhost:7890",
        "my-assistant",
        "your_client_key"
    );

    // Check health
    auto health = client.health();
    std::cout << health << std::endl;

    return 0;
}
```

## Chat (Streaming)

```cpp
client.chat_stream(
    {{"message", "Hello!"}},
    "user_123",
    [](const muxi::StreamEvent& event) {
        if (event.type == "text") {
            std::cout << event.text;
        } else if (event.type == "done") {
            return false; // Stop streaming
        }
        return true; // Continue
    }
);
```

## Chat (Non-Streaming)

```cpp
auto response = client.chat({{"message", "Hello!"}}, "user_123");
std::cout << response["response"] << std::endl;
```

## Memory

```cpp
// Get memories
auto memories = client.get_memories("user_123");

// Add memory
client.add_memory("user_123", "preference", "User prefers C++");

// Clear buffer
client.clear_user_buffer("user_123");
```

## Sessions

```cpp
// List sessions
auto sessions = client.get_sessions("user_123");

// Get session messages
auto messages = client.get_session_messages("sess_abc123", "user_123");
```

## Server Client

For managing formations (deploy, start, stop):

```cpp
#include <muxi/muxi.hpp>

muxi::ServerClient server(
    "http://localhost:7890",
    "muxi_pk_...",
    "muxi_sk_..."
);

// List formations
auto formations = server.list_formations();

// Deploy
server.deploy_formation("my-bot.tar.gz");

// Stop/start/restart
server.stop_formation("my-bot");
server.start_formation("my-bot");
```

## Error Handling

```cpp
#include <muxi/muxi.hpp>
#include <iostream>

try {
    auto response = client.chat({{"message", "Hello!"}}, "user_123");
    std::cout << response["response"] << std::endl;
} catch (const muxi::AuthenticationError& e) {
    std::cerr << "Auth failed: " << e.what() << std::endl;
} catch (const muxi::RateLimitError& e) {
    std::cerr << "Rate limited, retry after: " << e.retry_after << "s" << std::endl;
} catch (const muxi::MuxiError& e) {
    std::cerr << "Error: " << e.code << " - " << e.what() << std::endl;
}
```

## CMake Example

Complete `CMakeLists.txt`:

```cmake
cmake_minimum_required(VERSION 3.14)
project(my_muxi_app)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

include(FetchContent)
FetchContent_Declare(
    muxi
    GIT_REPOSITORY https://github.com/muxi-ai/muxi-cpp.git
    GIT_TAG main
)
FetchContent_MakeAvailable(muxi)

add_executable(my_app main.cpp)
target_link_libraries(my_app PRIVATE muxi::muxi)
```

## Learn More

- [Full documentation on GitHub](https://github.com/muxi-ai/muxi-cpp)
- [API Reference](../reference/api-reference.md)
