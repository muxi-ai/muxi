---
title: Go SDK
description: Native Go client for MUXI formations
---
# Go SDK

## Idiomatic Go for MUXI

Build Go applications that interact with MUXI formations. Full support for chat, streaming, context cancellation, and all Formation API operations.

**GitHub:** [`muxi-ai/muxi-go`](https://github.com/muxi-ai/muxi-go)

## Installation

```bash
go get github.com/muxi-ai/muxi-go
```

## Quick Start

```go
package main

import (
    "context"
    "fmt"
    "log"
    "os"

    muxi "github.com/muxi-ai/muxi-go"
)

func main() {
    ctx := context.Background()

    client := muxi.NewFormationClient(&muxi.FormationConfig{
        FormationID: "my-assistant",
        ServerURL:   os.Getenv("MUXI_SERVER_URL"),
        ClientKey:   os.Getenv("MUXI_CLIENT_KEY"),
    })

    resp, err := client.Chat(ctx, &muxi.ChatRequest{
        Message: "Hello!",
        UserID:  "user_123",
    })
    if err != nil {
        log.Fatal(err)
    }
    fmt.Println(resp.Response)
}
```

## Formation Client

### Initialize

```go
import muxi "github.com/muxi-ai/muxi-go"

// Via server proxy (recommended)
client := muxi.NewFormationClient(&muxi.FormationConfig{
    FormationID: "my-assistant",
    ServerURL:   "http://localhost:7890",
    ClientKey:   "your_client_key",
    AdminKey:    "your_admin_key",  // Optional, for admin operations
})

// Direct connection (for local dev)
client := muxi.NewFormationClient(&muxi.FormationConfig{
    URL:       "http://localhost:8001",  // Direct to formation
    ClientKey: "your_client_key",
})
```

### Chat

```go
// Simple chat
resp, err := client.Chat(ctx, &muxi.ChatRequest{
    Message: "Hello!",
    UserID:  "user_123",
})
if err != nil {
    log.Fatal(err)
}
fmt.Println(resp.Response)
```

### Streaming

```go
stream, errs := client.ChatStream(ctx, &muxi.ChatRequest{
    Message: "Tell me a story",
    UserID:  "user_123",
})

for stream != nil {
    select {
    case chunk, ok := <-stream:
        if !ok {
            stream = nil
            continue
        }
        if chunk.Type == "text" {
            fmt.Print(chunk.Text)
        }
    case err := <-errs:
        if err != nil {
            log.Fatal(err)
        }
        errs = nil
    }
}
```

Chunk types: `text`, `tool_call`, `tool_result`, `agent_handoff`, `thinking`, `error`, `done`.

### Memory

```go
// Get memory config
config, err := client.GetMemoryConfig(ctx)

// Get memories for user
memories, err := client.GetMemories(ctx, "user_123")

// Add a memory
err = client.AddMemory(ctx, "User prefers Go", "user_123")

// Clear user buffer
err = client.ClearUserBuffer(ctx, "user_123")

// Get buffer stats
stats, err := client.GetBufferStats(ctx)
```

### Scheduler

```go
// List scheduled jobs
jobs, err := client.GetSchedulerJobs(ctx, "user_123")

// Create a job
job, err := client.CreateSchedulerJob(ctx, &muxi.SchedulerJobRequest{
    Title:    "Daily report",
    Schedule: "0 9 * * *",
    Prompt:   "Generate daily summary",
}, "user_123")

// Delete a job
err = client.DeleteSchedulerJob(ctx, "job_abc123", "user_123")
```

### Sessions & Requests

```go
// Get sessions
sessions, err := client.GetSessions(ctx, "user_123")

// Get request status
status, err := client.GetRequestStatus(ctx, "req_abc123")

// Cancel a request
err = client.CancelRequest(ctx, "req_abc123")

// Stream request events
stream, errs := client.StreamRequest(ctx, "req_abc123")
```

### Event & Log Streaming

```go
// Stream events for a user
events, errs := client.StreamEvents(ctx, "user_123")
for event := range events {
    fmt.Println(event)
}

// Stream logs (admin)
logs, errs := client.StreamLogs(ctx, &muxi.LogFilter{Level: "info"})
for log := range logs {
    fmt.Println(log)
}
```

---

## Server Client

For managing formations (deploy, start, stop):

```go
import muxi "github.com/muxi-ai/muxi-go"

server := muxi.NewServerClient(&muxi.ServerConfig{
    URL:       os.Getenv("MUXI_SERVER_URL"),
    KeyID:     os.Getenv("MUXI_KEY_ID"),
    SecretKey: os.Getenv("MUXI_SECRET_KEY"),
})

// Check server status
status, err := server.Status(ctx)
fmt.Println(status)

// List formations
formations, err := server.ListFormations(ctx)

// Deploy a formation
res, err := server.DeployFormation(ctx, &muxi.DeployRequest{
    BundlePath: "my-bot.tar.gz",
})
fmt.Println("Deployed:", res.FormationID)

// Stop/start/restart
server.StopFormation(ctx, "my-bot")
server.StartFormation(ctx, "my-bot")
server.RestartFormation(ctx, "my-bot")

// Get server logs
logs, err := server.GetServerLogs(ctx, 200)
```

---

## Error Handling

```go
resp, err := client.Chat(ctx, &muxi.ChatRequest{Message: "Hello!", UserID: "u1"})
if err != nil {
    var authErr *muxi.AuthenticationError
    var notFound *muxi.NotFoundError
    var rateLimit *muxi.RateLimitError

    switch {
    case errors.As(err, &authErr):
        log.Println("Authentication failed")
    case errors.As(err, &notFound):
        log.Println("Not found:", notFound.Message)
    case errors.As(err, &rateLimit):
        log.Printf("Rate limited, retry after %d seconds", rateLimit.RetryAfter)
    default:
        log.Fatal(err)
    }
}
```

Error types: `AuthenticationError`, `AuthorizationError`, `NotFoundError`, `ValidationError`, `RateLimitError`, `ServerError`, `ConnectionError`.

---

## Configuration

- **Default timeout:** 30s (no timeout for streaming)
- **Retries:** GET/DELETE only, exponential backoff (500ms × 2, max 30s, ±10% jitter)
- **Idempotency:** `X-Muxi-Idempotency-Key` auto-generated on every request
- **Context:** All methods accept `context.Context` for cancellation

---

## Examples

### Chat Bot

```go
package main

import (
    "bufio"
    "context"
    "fmt"
    "os"
    "strings"

    muxi "github.com/muxi-ai/muxi-go"
)

func main() {
    ctx := context.Background()
    client := muxi.NewFormationClient(&muxi.FormationConfig{
        FormationID: "my-assistant",
        ServerURL:   os.Getenv("MUXI_SERVER_URL"),
        ClientKey:   os.Getenv("MUXI_CLIENT_KEY"),
    })

    reader := bufio.NewReader(os.Stdin)
    userID := "user_123"

    for {
        fmt.Print("You: ")
        input, _ := reader.ReadString('\n')
        input = strings.TrimSpace(input)

        if input == "quit" {
            break
        }

        fmt.Print("Assistant: ")
        stream, errs := client.ChatStream(ctx, &muxi.ChatRequest{
            Message: input,
            UserID:  userID,
        })

        for stream != nil {
            select {
            case chunk, ok := <-stream:
                if !ok {
                    stream = nil
                    continue
                }
                if chunk.Type == "text" {
                    fmt.Print(chunk.Text)
                }
            case err := <-errs:
                if err != nil {
                    fmt.Println("Error:", err)
                }
                errs = nil
            }
        }
        fmt.Println()
    }
}
```

### HTTP Handler

```go
package main

import (
    "context"
    "encoding/json"
    "net/http"
    "os"

    muxi "github.com/muxi-ai/muxi-go"
)

var client = muxi.NewFormationClient(&muxi.FormationConfig{
    FormationID: os.Getenv("MUXI_FORMATION_ID"),
    ServerURL:   os.Getenv("MUXI_SERVER_URL"),
    ClientKey:   os.Getenv("MUXI_CLIENT_KEY"),
})

func chatHandler(w http.ResponseWriter, r *http.Request) {
    var req struct {
        Message string `json:"message"`
        UserID  string `json:"user_id"`
    }
    json.NewDecoder(r.Body).Decode(&req)

    resp, err := client.Chat(r.Context(), &muxi.ChatRequest{
        Message: req.Message,
        UserID:  req.UserID,
    })
    if err != nil {
        http.Error(w, err.Error(), 500)
        return
    }

    json.NewEncoder(w).Encode(map[string]string{
        "response": resp.Response,
    })
}

func main() {
    http.HandleFunc("/chat", chatHandler)
    http.ListenAndServe(":3000", nil)
}
```

---

## Learn More

- [Python SDK](python.md)
- [TypeScript SDK](typescript.md)
- [API Reference](reference/api-reference.md)
