# Go SDK

## SDK documentation


Go client for MUXI formations.

## Installation

```bash
go get github.com/muxi-ai/sdk-go
```

## Quick Start

```go
package main

import (
    "fmt"
    "github.com/muxi-ai/sdk-go/muxi"
)

func main() {
    formation := muxi.NewFormation(muxi.Config{
        URL:       "http://localhost:8001",
        ClientKey: "fmc_...",
    })

    response, _ := formation.Chat("Hello!")
    fmt.Println(response.Text)
}
```

## Formation Client

### Initialize

```go
import "github.com/muxi-ai/sdk-go/muxi"

// With client key
formation := muxi.NewFormation(muxi.Config{
    URL:       "http://localhost:8001",
    ClientKey: "fmc_...",
})

// With admin key
formation := muxi.NewFormation(muxi.Config{
    URL:      "http://localhost:8001",
    AdminKey: "fma_...",
})
```

### Chat

```go
// Simple message
response, err := formation.Chat("Hello!")
if err != nil {
    log.Fatal(err)
}
fmt.Println(response.Text)

// With options
response, err := formation.ChatWithOptions("Research AI", muxi.ChatOptions{
    Agent:     "researcher",
    SessionID: "sess_123",
    UserID:    "user_456",
})
```

### Streaming

```go
stream, err := formation.ChatStream("Tell me a story")
if err != nil {
    log.Fatal(err)
}

for chunk := range stream.Chunks {
    fmt.Print(chunk.Text)
}
```

### Sessions

```go
// Create session
session, err := formation.CreateSession()
fmt.Println(session.ID) // sess_abc123

// Chat with session
response, err := formation.ChatWithOptions("Hello!", muxi.ChatOptions{
    SessionID: session.ID,
})

// Get session history
history, err := formation.GetSession(session.ID)
for _, msg := range history.Messages {
    fmt.Printf("%s: %s\n", msg.Role, msg.Content)
}
```

### Agents

```go
// List agents
agents, err := formation.ListAgents()
for _, agent := range agents {
    fmt.Println(agent.ID, agent.Name)
}

// Target specific agent
response, err := formation.ChatWithOptions("Research this", muxi.ChatOptions{
    Agent: "researcher",
})
```

### Triggers

```go
response, err := formation.Trigger("github-issue", muxi.TriggerData{
    "repository": "muxi/runtime",
    "issue": map[string]interface{}{
        "number": 123,
        "title":  "Bug report",
    },
})
```

### Multi-User

```go
// User isolation
formation.ChatWithOptions("Remember my preference", muxi.ChatOptions{
    UserID: "user_123",
})

// Different user
formation.ChatWithOptions("What's my preference?", muxi.ChatOptions{
    UserID: "user_456",
})
```

## Server Client

### Initialize

```go
import "github.com/muxi-ai/sdk-go/muxi"

server := muxi.NewServerClient(muxi.ServerConfig{
    URL:       "http://localhost:7890",
    KeyID:     "MUXI_...",
    SecretKey: "sk_...",
})
```

### List Formations

```go
formations, err := server.ListFormations()
for _, f := range formations {
    fmt.Println(f.ID, f.Status, f.Port)
}
```

### Deploy Formation

```go
result, err := server.Deploy("/path/to/formation")
fmt.Println(result.Port)
```

### Stop/Start/Restart

```go
server.StopFormation("my-assistant")
server.StartFormation("my-assistant")
server.RestartFormation("my-assistant")
```

### Delete Formation

```go
server.DeleteFormation("my-assistant")
```

## Error Handling

```go
response, err := formation.Chat("Hello!")
if err != nil {
    switch e := err.(type) {
    case *muxi.AuthenticationError:
        log.Println("Invalid API key")
    case *muxi.FormationError:
        log.Printf("Formation error: %v", e)
    case *muxi.MuxiError:
        log.Printf("MUXI error: %v", e)
    default:
        log.Fatal(err)
    }
}
```

## Configuration

```go
muxi.NewFormation(muxi.Config{
    URL:        "http://localhost:8001",
    ClientKey:  "fmc_...",
    Timeout:    30 * time.Second,
    MaxRetries: 3,
})
```

## Types

```go
type ChatResponse struct {
    Text      string
    Agent     string
    SessionID string
    Metadata  map[string]interface{}
}

type Session struct {
    ID        string
    CreatedAt time.Time
    Messages  []Message
}

type Message struct {
    Role      string
    Content   string
    Timestamp time.Time
}
```

## Examples

### Chat Bot

```go
package main

import (
    "bufio"
    "fmt"
    "os"
    "strings"
    
    "github.com/muxi-ai/sdk-go/muxi"
)

func main() {
    formation := muxi.NewFormation(muxi.Config{
        URL:       "http://localhost:8001",
        ClientKey: "fmc_...",
    })

    session, _ := formation.CreateSession()
    reader := bufio.NewReader(os.Stdin)

    for {
        fmt.Print("You: ")
        input, _ := reader.ReadString('\n')
        input = strings.TrimSpace(input)

        if input == "/exit" {
            break
        }

        response, _ := formation.ChatWithOptions(input, muxi.ChatOptions{
            SessionID: session.ID,
        })
        fmt.Printf("Assistant: %s\n", response.Text)
    }
}
```

### HTTP Handler

```go
package main

import (
    "encoding/json"
    "net/http"
    
    "github.com/muxi-ai/sdk-go/muxi"
)

var formation = muxi.NewFormation(muxi.Config{
    URL:       "http://localhost:8001",
    ClientKey: "fmc_...",
})

func chatHandler(w http.ResponseWriter, r *http.Request) {
    var req struct {
        Message string `json:"message"`
        UserID  string `json:"user_id"`
    }
    json.NewDecoder(r.Body).Decode(&req)

    response, err := formation.ChatWithOptions(req.Message, muxi.ChatOptions{
        UserID: req.UserID,
    })
    if err != nil {
        http.Error(w, err.Error(), 500)
        return
    }

    json.NewEncoder(w).Encode(map[string]string{
        "response": response.Text,
    })
}

func main() {
    http.HandleFunc("/chat", chatHandler)
    http.ListenAndServe(":3000", nil)
}
```
