---
title: Structured Output
description: Get type-safe JSON responses from agents for API integration
---
# Structured Output

## Get type-safe JSON responses from agents for API integration


Agents can return structured data - not just text. Define schemas, get validated JSON, build APIs. Perfect for data extraction, form filling, and programmatic access.


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

---

## The Solution: Structured Output

Tell the agent what format you want:

```yaml
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

**Machine-readable, type-safe, ready to use.**

---

## Response Formats

MUXI supports 4 response formats:

### 1. Markdown (Default)

```yaml
response:
  format: markdown
```

**Best for:**
- Documentation
- Rich text with formatting
- Human-readable content

**Example output:**
```markdown
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

### 4. HTML

```yaml
response:
  format: html
```

**Best for:**
- Web applications
- Email templates
- Content management systems

**Example output:**
```html
<h1>Cloud Computing Benefits</h1>
<p>Key advantages include:</p>
<ul>
  <li><strong>Cost Efficiency:</strong> Pay-as-you-use pricing</li>
  <li><strong>Scalability:</strong> Elastic resources</li>
</ul>
```

---

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

The agent naturally generates responses in the requested format.

### Validation

**JSON and HTML are validated:**
- **JSON**: Parsed with `json.loads()` - must be valid JSON
- **HTML**: Validated with BeautifulSoup - proper tag structure

**Markdown and Text are not validated** (by design - more flexible).

---

## Use Cases

### Data Extraction

```yaml
# Extract structured data from text
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

```python
# REST API endpoint
@app.post("/api/analyze")
async def analyze(text: str):
    formation = Formation()
    formation.overlord.response_format = "json"

    response = await formation.overlord.chat(f"Analyze: {text}")
    return JSONResponse(response.content)  # Already JSON!
```

### Form Filling

```yaml
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

```python
# Extract data and insert into database
response = await overlord.chat(
    "Extract product info from this description: ..."
)

# response.content is already JSON
product_data = json.loads(response.content)
db.insert("products", product_data)
```

---

## Configuration

### Formation-Wide Default

```yaml
# formation.afs
overlord:
  response:
    format: json  # All responses are JSON
```

### Runtime Override

```python
# Override for specific requests
overlord.response_format = "json"
response1 = await overlord.chat("Extract data...")

overlord.response_format = "text"
response2 = await overlord.chat("Summarize...")

# Reset to formation default
overlord.response_format = None
```

### Per-Request Format

```bash
# Via API
curl -X POST http://localhost:8001/v1/chat \
  -H "Content-Type: application/json" \
  -d '{
    "message": "Extract customer info",
    "format": "json"
  }'
```

---

## With Streaming

All formats work with streaming:

```python
# Stream JSON response
overlord.response_format = "json"
async for chunk in overlord.chat_stream("Analyze this..."):
    print(chunk.text, end="", flush=True)
```

The agent streams the JSON as it's generated:

```
{"
"name": "John"
, "email": "john@ex
ample.com"
}
```

Collect chunks and parse at the end.

---

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

```python
schema = {
    "name": "string",
    "email": "string",
    "phone": "string | null",
    "company": "string | null"
}

response = await overlord.chat(f"""
Extract customer info. Return JSON matching this schema:
{json.dumps(schema, indent=2)}

Text: {input_text}
""")
```

Providing schema improves accuracy.

### Error Handling

```python
try:
    data = json.loads(response.content)
except json.JSONDecodeError:
    # Invalid JSON - retry or use fallback
    logger.error("Agent returned invalid JSON")
```

Always validate, even with structured output.

---

## Limitations

### Not a Schema Validator

MUXI validates that JSON is valid, but doesn't enforce a specific schema:

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

Better models (GPT-4, Claude) → better structured output.

### Not Perfect

Agents may occasionally:
- Return invalid JSON (rare with good models)
- Include extra fields
- Use unexpected data types

Always validate in production.

---

## Why This Matters

| Text Responses | Structured Output |
|---------------|-------------------|
| Parse with regex | Use as-is |
| Fragile, breaks easily | Robust, type-safe |
| Extra code needed | Machine-readable |
| Hard to integrate | API-ready |
| Unvalidated | Validated format |

The result: **Agents that return data**, not just text.

---

## Quick Setup

```yaml
# formation.afs
overlord:
  response:
    format: json  # or "text", "markdown", "html"

agents:
  - id: assistant
    system_message: |
      You are a helpful assistant.
      Return responses as valid JSON.
```

---

## Learn More

- [Deep Dive: Response Formats](../deep-dives/response-formats.md) - Technical implementation
- [Tools & MCP](tools.md) - Tools can also return structured data
- [Build Custom UI](../guides/build-custom-ui.md) - Using structured output in UIs
