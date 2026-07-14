---
title: Response Formats
description: Configure formation-wide markdown, text, or JSON responses
---
# Response Formats

## Configure how a formation returns text


MUXI can instruct the Overlord to return markdown, plain text, or a
JSON-wrapped response. For domain-specific structured data, describe the
required shape in the agent instructions and validate it in your application.


## The Problem: Text Responses

Traditional agents return unstructured text:

```
User:  "Extract customer info from this email"
Agent: "The customer's name is John Smith, email is john@example.com,
        and phone is 555-0123."
```

**Problem:** How do you extract that data programmatically?
- Parse with regex? (brittle)
- Use another LLM call? (expensive)
- String manipulation? (error-prone)


## The Solution: Formation-Wide Formatting

Tell the agent what format you want:

```yaml
overlord:
  response:
    format: json
```

Now you get:

```json
{
  "name": "John Smith",
  "email": "john@example.com",
  "phone": "555-0123"
}
```

The JSON wrapper is machine-readable. MUXI does not enforce a domain schema for
the content inside it.


## Response Formats

MUXI supports three response formats:

### 1. Markdown (Default)

```yaml
overlord:
  response:
    format: markdown
```

**Best for:**
- Documentation
- Rich text with formatting
- Human-readable content

**Example output:**
```
# Cloud Computing Benefits

## Cost Efficiency
- **Pay-as-you-use** pricing
- Reduced infrastructure costs

## Scalability
- Elastic resources
- Automatic scaling
```

### 2. Plain Text

```yaml
overlord:
  response:
    format: text
```

**Best for:**
- CLI applications
- Log files
- Simple integrations

**Example output:**
```
Cloud computing offers three key benefits:

1. Cost Efficiency
   Reduces infrastructure costs through pay-as-you-use pricing.

2. Scalability
   Provides elastic resources that grow with your needs.
```

### 3. JSON (Structured)

```yaml
overlord:
  response:
    format: json
```

**Best for:**
- REST APIs
- Data extraction
- Programmatic processing
- Integration with JSON-based systems

**Example output:**
```json
{
  "content": "Cloud computing offers cost efficiency, scalability, and global accessibility.",
  "type": "response",
  "format": "json"
}
```

## How It Works

### Format Instructions

MUXI adds format-specific instructions to the agent's prompt:

```
For JSON mode:
"Format your response as valid JSON. Use appropriate data structures
 (objects, arrays, strings, numbers, booleans) for the content."

For Plain Text:
"Format your response as plain text with no markdown, HTML, or special
 characters. Use simple line breaks and spacing only."
```

The setting applies to the formation. The chat API does not accept a
per-request `format` override.

### Validation

JSON mode serializes the final response into a valid JSON wrapper. It does not
validate a custom schema. Markdown and text remain strings.


## Use Cases

### Data Extraction

```yaml
# Extract structured data from text
overlord:
  response:
    format: json

agents:
  - id: extractor
    system_message: |
      Extract customer information and return as JSON with fields:
      name, email, phone, company
```

**Input:** "John Smith from Acme Corp called, email john@acme.com, phone 555-0123"

**Output:**
```json
{
  "name": "John Smith",
  "email": "john@acme.com",
  "phone": "555-0123",
  "company": "Acme Corp"
}
```

### API Responses

[[tabs]]

[[tab Python]]
```python
import json
from muxi import FormationClient

formation = FormationClient(
    server_url="http://localhost:7890",
    formation_id="my-assistant",
    client_key="<your-client-key>",
)

# Request JSON format
response = formation.chat(
    {"message": "Analyze: " + text},
    user_id="user_123",
)

# The formation is configured for JSON mode.
return json.loads(response["response"])
```
[[/tab]]

[[tab TypeScript]]
```typescript
import { FormationClient } from '@muxi/sdk';

const formation = new FormationClient({
  serverUrl: 'http://localhost:7890',
  formationId: 'my-assistant',
  clientKey: '<your-client-key>'
});

// Request JSON format
const response = await formation.chat({
  message: `Analyze: ${text}`
}, 'user_123');

return JSON.parse(response.response);
```
[[/tab]]

[[tab Go]]
```go
import (
    "encoding/json"

    muxi "github.com/muxi-ai/muxi-go"
)

formation := muxi.NewFormationClient(&muxi.FormationConfig{
    ServerURL:   "http://localhost:7890",
    FormationID: "my-assistant",
    ClientKey:   "<your-client-key>",
})

// Request JSON format
response, _ := formation.Chat(ctx, &muxi.ChatRequest{
    Message: "Analyze: " + text,
})

var result map[string]interface{}
json.Unmarshal([]byte(response.Response), &result)
return result
```
[[/tab]]

[[/tabs]]

### Form Filling

```yaml
overlord:
  response:
    format: json
agents:
  - id: form_filler
    system_message: |
      Fill out forms by extracting data from conversations.
      Return JSON matching the form schema.
```

**User:** "I want to sign up. I'm Alice Johnson, alice@example.com, in New York"

**Output:**
```json
{
  "first_name": "Alice",
  "last_name": "Johnson",
  "email": "alice@example.com",
  "city": "New York"
}
```

### Database Inserts

[[tabs]]

[[tab Python]]
```python
import json
from muxi import FormationClient
import json

formation = FormationClient(
    server_url="http://localhost:7890",
    formation_id="my-assistant",
    client_key="<your-client-key>",
)

# Extract data
response = formation.chat(
    {
        "message": "Extract product info from this description: ...",
    },
    user_id="user_123",
)

# JSON mode wraps the agent's text in a JSON response object.
wrapper = json.loads(response["response"])
product_data = json.loads(wrapper["content"])
db.insert("products", product_data)
```
[[/tab]]

[[tab TypeScript]]
```typescript
import { FormationClient } from '@muxi/sdk';

const formation = new FormationClient({
  serverUrl: 'http://localhost:7890',
  formationId: 'my-assistant',
  clientKey: '<your-client-key>'
});

// Extract data
const response = await formation.chat({
  message: 'Extract product info from this description: ...'
}, 'user_123');

const wrapper = JSON.parse(response.response);
const productData = JSON.parse(wrapper.content);
await db.insert('products', productData);
```
[[/tab]]

[[tab Go]]
```go
import (
    "encoding/json"

    muxi "github.com/muxi-ai/muxi-go"
)

formation := muxi.NewFormationClient(&muxi.FormationConfig{
    ServerURL:   "http://localhost:7890",
    FormationID: "my-assistant",
    ClientKey:   "<your-client-key>",
})

// Extract data
response, _ := formation.Chat(ctx, &muxi.ChatRequest{
    Message: "Extract product info from this description: ...",
})

var wrapper struct {
    Content string `json:"content"`
}
json.Unmarshal([]byte(response.Response), &wrapper)

var productData map[string]interface{}
json.Unmarshal([]byte(wrapper.Content), &productData)
db.Insert("products", productData)
```
[[/tab]]

[[/tabs]]


## Configuration

### Formation-Wide Default

```yaml
# formation.afs
overlord:
  response:
    format: json  # All responses are JSON
```

The configured format applies to every chat request handled by the formation.
Change the formation configuration and restart or redeploy it to change the
default.


## With Streaming

All formats work with streaming:

[[tabs]]

[[tab Python]]
```python
from muxi import FormationClient

formation = FormationClient(
    server_url="http://localhost:7890",
    formation_id="my-assistant",
    client_key="<your-client-key>",
)

# Stream JSON response
for event in formation.chat_stream(
    {"message": "Analyze this..."},
    user_id="user_123",
):
    if event.get("event") == "message":
        payload = json.loads(event.get("data", "{}"))
        token = payload.get("token")
        if isinstance(token, str):
            print(token, end="", flush=True)
```
[[/tab]]

[[tab TypeScript]]
```typescript
import { FormationClient } from '@muxi/sdk';

const formation = new FormationClient({
  serverUrl: 'http://localhost:7890',
  formationId: 'my-assistant',
  clientKey: '<your-client-key>'
});

// Stream JSON response
for await (const chunk of formation.chatStream(
  { message: 'Analyze this...' },
  'user_123'
)) {
  if (typeof chunk.token === 'string') {
    process.stdout.write(chunk.token);
  }
}
```
[[/tab]]

[[tab Go]]
```go
formation := muxi.NewFormationClient(&muxi.FormationConfig{
    ServerURL:   "http://localhost:7890",
    FormationID: "my-assistant",
    ClientKey:   "<your-client-key>",
})

stream, _ := formation.ChatStream(ctx, &muxi.ChatRequest{
    Message: "Analyze this...",
    UserID:  "user_123",
})
for chunk := range stream {
    if token, ok := chunk.Raw["token"].(string); ok {
        fmt.Print(token)
    }
}
```
[[/tab]]

[[/tabs]]

Collect the text chunks and parse the completed value according to the
formation's response format.


## Best Practices

### Clear Instructions

```yaml
agents:
  - id: extractor
    system_message: |
      Extract information and return as JSON.
      Always include fields: name, email, phone, company.
      Use null for missing fields.
```

Clear instructions → better structured output.

### Schema in Prompt

[[tabs]]

[[tab Python]]
```python
from muxi import FormationClient
import json

formation = FormationClient(
    server_url="http://localhost:7890",
    formation_id="my-assistant",
    client_key="<your-client-key>",
)

schema = {
    "name": "string",
    "email": "string",
    "phone": "string | null",
    "company": "string | null"
}

response = formation.chat(
    {"message": f"""Extract customer info. Return JSON matching this schema:
{json.dumps(schema, indent=2)}

Text: {text}"""},
    user_id="user_123",
)
```
[[/tab]]

[[tab TypeScript]]
```typescript
import { FormationClient } from '@muxi/sdk';

const formation = new FormationClient({
  serverUrl: 'http://localhost:7890',
  formationId: 'my-assistant',
  clientKey: '<your-client-key>'
});

const schema = {
  name: 'string',
  email: 'string',
  phone: 'string | null',
  company: 'string | null'
};

const response = await formation.chat({
  message: `Extract customer info. Return JSON matching this schema:
${JSON.stringify(schema, null, 2)}

Text: ${inputText}`,
}, 'user_123');
```
[[/tab]]

[[/tabs]]

Providing schema improves accuracy.

### Error Handling

[[tabs]]

[[tab Python]]
```python
import json

try:
    wrapper = json.loads(response["response"])
    data = json.loads(wrapper["content"])
except json.JSONDecodeError:
    # Invalid JSON - retry or use fallback
    logger.error("Agent returned invalid JSON")
```
[[/tab]]

[[tab TypeScript]]
```typescript
try {
  const wrapper = JSON.parse(response.response);
  const data = JSON.parse(wrapper.content);
} catch (e) {
  // Invalid JSON - retry or use fallback
  console.error('Agent returned invalid JSON');
}
```
[[/tab]]

[[/tabs]]

Always validate, even with structured output.


## Limitations

### Not a Schema Validator

JSON mode guarantees a valid outer wrapper, but it does not validate a schema
for the agent-generated content:

```json
// Agent might return this:
{"name": "John", "extra_field": "something"}

// Even if you only asked for name and email
```

Use Pydantic or JSON Schema validation if you need strict schema enforcement.

### LLM Quality Dependent

Quality depends on:
- LLM model capabilities
- Prompt clarity
- Input complexity

More capable models generally produce more reliable structured content.

### Not Perfect

Agents may occasionally:
- Return invalid JSON (rare with good models)
- Include extra fields
- Use unexpected data types

Always validate in production.


## Why This Matters

| Text Responses | Structured Output |
|---------------|-------------------|
| Parse with regex | Parse a JSON wrapper |
| Fragile, breaks easily | Explicit application validation |
| Extra code needed | Machine-readable |
| Hard to integrate | API-ready |
| Unstructured | Machine-readable envelope |

The result: **Agents that return data**, not just text.


## Quick Setup

```yaml
# formation.afs
overlord:
  response:
    format: json  # or "text", "markdown"

agents:
  - id: assistant
    system_message: |
      You are a helpful assistant.
      Return responses as valid JSON.
```


## Learn More

- [Deep Dive: Response Formats](../deep-dives/response-formats.md) - Technical implementation
- [Tools & MCP](../concepts/tools-and-mcp.md) - Tools can also return structured data
- [Build Custom UI](../guides/build-custom-ui.md) - Using structured output in UIs
