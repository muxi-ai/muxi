---
title: Response Formats Deep Dive
description: Technical implementation of structured output in MUXI
---
# Response Formats Deep Dive

## Technical implementation of LLM persona-based formatting

MUXI's response format system uses **LLM persona instructions** combined with **post-processing validation** to generate naturally formatted content while maintaining technical correctness.


## Architecture

### Hybrid Approach

```
User Request
     ↓
Persona Enhancement (LLM instructions)
     ↓
Agent Processing (natural generation)
     ↓
Post-Processing Validation (JSON/HTML only)
     ↓
Formatted Response
```

**Why hybrid?**
- **LLM instructions**: Natural, contextually appropriate formatting
- **Post-processing**: Ensures technical validity for structured formats

---

## Persona Enhancement

### How It Works

The system modifies the agent's system prompt based on format:

```python
# Internal implementation
def _get_format_instruction(format: str) -> str:
    if format == "markdown":
        return """
        Format your response using proper markdown with headers (# ## ###),
        bullet points, bold/italic text, and code blocks where appropriate.
        Use markdown syntax for structure and emphasis.
        """
    
    elif format == "text":
        return """
        Format your response as plain text with no markdown formatting,
        special characters, or HTML. Use simple text formatting like
        line breaks and spacing for structure.
        """
    
    elif format == "json":
        return """
        Format your response as valid JSON. Use appropriate data structures
        (objects, arrays, strings, numbers, booleans). Ensure all JSON is
        properly formatted and parseable.
        """
    
    elif format == "html":
        return """
        Format your response as valid HTML with proper semantic tags like
        <h1>, <h2>, <p>, <ul>, <li>, <strong>, <em>, and <code>. Include
        proper structure and ensure all tags are properly closed.
        """
```

### Persona Injection

```python
# Simplified flow
enhanced_persona = f"{base_persona}\n\n{format_instruction}"

llm_response = await llm.chat(
    messages=messages,
    system=enhanced_persona  # Enhanced with format instructions
)
```

The LLM naturally generates formatted responses based on enhanced instructions.

---

## Post-Processing Validation

### JSON Validation

```python
def validate_json_response(content: str) -> str:
    """Validate and reformat JSON response."""
    try:
        # Parse to ensure validity
        parsed = json.loads(content)
        
        # Re-serialize with consistent formatting
        return json.dumps(parsed, indent=2, ensure_ascii=False)
    
    except json.JSONDecodeError as e:
        # Invalid JSON - log and return as-is
        logger.warning(f"Invalid JSON response: {e}")
        return content
```

**Why validate?**
- Ensures response is parseable
- Consistent formatting (indentation, spacing)
- Catches LLM errors early

### HTML Validation

```python
from bs4 import BeautifulSoup

def validate_html_response(content: str) -> str:
    """Validate and reformat HTML response."""
    try:
        # Parse with BeautifulSoup
        soup = BeautifulSoup(content, 'html.parser')
        
        # Validate structure
        if not soup.find():
            raise ValueError("No valid HTML tags found")
        
        # Return prettified HTML
        return soup.prettify()
    
    except Exception as e:
        logger.warning(f"Invalid HTML response: {e}")
        return content
```

**Validation includes:**
- Proper tag nesting
- Closed tags
- Semantic structure
- Auto-correction of minor issues

---

## Format-Specific Behavior

### Real-Time Streaming

MUXI streams tokens as the model generates them. Use SSE for simple, one-way streams (chat, dashboards) or WebSockets for bidirectional interactions. Long tasks can start streaming, then hand off to async workflows with progress events.

- **Protocols:** SSE, WebSockets
- **When to use:** Chat UIs, live dashboards, long-running jobs needing updates
- **See also:** [Streaming](./streaming.md), [Async Operations](./async.md)

### Markdown

**No validation** - LLMs are good at markdown naturally.

**Features:**
- Headers (`#`, `##`, `###`)
- **Bold** and *italic*
- Code blocks with syntax highlighting
- Lists (bulleted and numbered)
- Links and references
- Tables

**Example response:**
```markdown
# Analysis Results

## Key Findings

1. **Performance**: 95% improvement
2. **Cost**: Reduced by 40%

### Recommendations

- Optimize caching strategy
- Scale horizontally for peak loads

```bash
# Deploy command
kubectl apply -f deployment.yaml
```
```

### Plain Text

**No validation** - flexible by design.

**Features:**
- Clean, unformatted text
- Line breaks for structure
- No special characters
- Maximum compatibility

**Example response:**
```
Analysis Results

Key Findings:

1. Performance improved by 95%
2. Cost reduced by 40%

Recommendations:
- Optimize caching strategy  
- Scale horizontally for peak loads

Deployment: kubectl apply -f deployment.yaml
```

### JSON

**Validated and reformatted.**

**Structure:**
```json
{
  "content": "The actual response content",
  "type": "response",
  "format": "json",
  "metadata": {
    // Optional metadata
  }
}
```

**Validation:**
- Must be valid JSON
- Parsed with `json.loads()`
- Re-serialized with consistent formatting

**Error handling:**
```python
if validation_fails:
    # Return original content with error metadata
    return {
        "content": original_content,
        "error": "Invalid JSON",
        "format": "json"
    }
```

### HTML

**Validated with BeautifulSoup.**

**Allowed tags:**
- Structural: `<div>`, `<span>`, `<section>`
- Headers: `<h1>` through `<h6>`
- Text: `<p>`, `<strong>`, `<em>`, `<code>`
- Lists: `<ul>`, `<ol>`, `<li>`
- Links: `<a>`
- Pre-formatted: `<pre>`, `<code>`

**Not allowed:**
- Scripts: `<script>`
- Styles: `<style>` (inline styles ok)
- Forms: `<form>`, `<input>` (security)
- Iframes: `<iframe>` (security)

**Validation:**
```python
# Auto-closes unclosed tags
<p>Text<p>More  →  <p>Text</p><p>More</p>

# Fixes nesting
<strong><em>Text</strong></em>  →  <strong><em>Text</em></strong>

# Removes dangerous tags
<p>Text<script>alert()</script></p>  →  <p>Text</p>
```

---

## Configuration

### Formation YAML

```yaml
# formation.afs
overlord:
  persona: "You are a helpful assistant"
  
  response:
    format: "markdown"           # Default format
    streaming: true              # Works with all formats
    validation: true             # Enable post-processing (JSON/HTML)
```

### Runtime Override

```python
from muxi.runtime.formation import Formation

formation = Formation()
await formation.load("formation.afs")

# Get overlord instance
overlord = await formation.start_overlord()

# Override format
overlord.response_format = "json"
response = await overlord.chat("Extract data...")

# Reset to formation default
overlord.response_format = None
```

### API Request

```bash
curl -X POST http://localhost:8001/v1/chat \
  -H "Content-Type: application/json" \
  -d '{
    "message": "Analyze this data",
    "format": "json"
  }'
```

---

## Streaming Behavior

### Format-Specific Streaming

**Markdown and Text:**
```
event: chunk
data: {"text": "# Header\n\n"}

event: chunk
data: {"text": "Some **bold** text"}
```

Streams naturally, readable as it arrives.

**JSON:**
```
event: chunk
data: {"text": "{\"name\": "}

event: chunk
data: {"text": "\"John\""}

event: chunk  
data: {"text": ", \"email\": "}

event: chunk
data: {"text": "\"john@example.com\""}

event: chunk
data: {"text": "}"}
```

Client must collect all chunks, then parse complete JSON.

**HTML:**
```
event: chunk
data: {"text": "<h1>"}

event: chunk
data: {"text": "Header"}

event: chunk
data: {"text": "</h1>"}
```

Similar to JSON - collect and validate at end.

---

## Performance Considerations

### Token Overhead

Format instructions add ~50-100 tokens to system prompt:

| Format | Instruction Tokens | Impact |
|--------|-------------------|---------|
| Markdown | ~60 tokens | Minimal |
| Text | ~50 tokens | Minimal |
| JSON | ~70 tokens | Minimal |
| HTML | ~80 tokens | Minimal |

**Cost impact:** <0.01% for typical conversations.

### Validation Overhead

| Operation | Time | When |
|-----------|------|------|
| JSON parse | <1ms | Every JSON response |
| HTML parse | 1-5ms | Every HTML response |
| Markdown | 0ms | No validation |
| Text | 0ms | No validation |

**Latency impact:** Negligible (<0.1% of total).

---

## Error Handling

### Invalid JSON

```python
# Agent returns invalid JSON
response_text = "{name: 'John'}"  # Missing quotes

# Validation fails
try:
    json.loads(response_text)
except JSONDecodeError:
    # Log error, return wrapped response
    return {
        "content": response_text,
        "error": "Invalid JSON returned by agent",
        "format": "json"
    }
```

### Invalid HTML

```python
# Agent returns malformed HTML
response_text = "<p>Text<strong>Bold"  # Unclosed tags

# BeautifulSoup auto-corrects
soup = BeautifulSoup(response_text, 'html.parser')
corrected = soup.prettify()
# Result: <p>Text<strong>Bold</strong></p>
```

BeautifulSoup is forgiving - auto-closes tags, fixes nesting.

---

## Best Practices

### 1. Clear Schema Expectations

```yaml
agents:
  - id: extractor
    system_message: |
      Extract customer information as JSON with fields:
      - name (string, required)
      - email (string, required)
      - phone (string, optional)
      - company (string, optional)
      
      Use null for missing optional fields.
```

### 2. Validation in Code

```python
from pydantic import BaseModel

class Customer(BaseModel):
    name: str
    email: str
    phone: str | None = None
    company: str | None = None

# Validate response
response = await overlord.chat("Extract customer info...")
data = json.loads(response.content)
customer = Customer(**data)  # Validates schema
```

### 3. Retry on Failure

```python
max_retries = 3
for attempt in range(max_retries):
    response = await overlord.chat(message)
    
    try:
        data = json.loads(response.content)
        break  # Success
    except JSONDecodeError:
        if attempt == max_retries - 1:
            raise  # Give up after retries
        # Retry with clarification
        message = f"{message}\n\nPrevious response was invalid JSON. Please return valid JSON."
```

---

## Future Enhancements

### Planned Features

1. **Pydantic Schema Enforcement**
   ```yaml
   response:
     format: json
     schema: "schemas/customer.json"
   ```

2. **Custom Validators**
   ```yaml
   response:
     format: json
     validator: "validators/custom.py"
   ```

3. **Format Auto-Detection**
   ```yaml
   response:
     format: auto  # Detect from context
   ```

---

## Debugging

### Enable Validation Logs

```yaml
logging:
  level: debug
  validators: true
```

Logs all validation attempts and failures.

### Inspect Raw Responses

```python
response = await overlord.chat("Extract data...")
print("Raw:", response.raw_content)       # Before validation
print("Validated:", response.content)    # After validation
```

---

## Learn More

- [Structured Output Concept](../concepts/structured-output.md) - User-facing explanation
- [Tools & MCP](../concepts/tools.md) - Tools also return structured data
- [API Reference](../reference/api.md) - Complete API documentation
