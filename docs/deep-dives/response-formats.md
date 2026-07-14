---
title: Response Format Internals
description: How markdown, text, and JSON response modes are applied
---
# Response Format Internals

Response formatting is a formation-wide Overlord setting:

```yaml
overlord:
  response:
    format: markdown
```

Current validation accepts `markdown`, `text`, and `json`. The chat request
model does not provide a per-request format override.

## Processing Stages

1. The formation loads `overlord.response.format`.
2. Markdown and text modes add format guidance while the Overlord prepares the
   final response.
3. The selected agent generates its content.
4. In JSON mode, the runtime serializes the final text into a valid outer JSON
   object.
5. The Formation API places that formatted text in its normal response
   envelope.

## Markdown

Markdown is the default. The Overlord asks for headings, lists, emphasis, and
code fences where appropriate. The result remains a string and is not rendered
or sanitized by the runtime.

```yaml
overlord:
  response:
    format: markdown
```

Render Markdown with a suitable library in the client and apply the security
policy required by the application.

## Plain Text

Text mode asks the model to avoid Markdown and HTML formatting:

```yaml
overlord:
  response:
    format: text
```

This is prompt-guided formatting, not a character-level sanitizer. Treat model
output as untrusted text at application boundaries.

## JSON

JSON mode wraps the generated content as a JSON string:

```json
{
  "content": "The agent-generated response",
  "type": "response",
  "format": "json"
}
```

The wrapper itself is valid JSON. MUXI does not validate the value of `content`
against a domain schema, and it does not transform arbitrary prose into a
domain-specific object.

Because the Formation API has its own response envelope, SDK users typically
receive the JSON wrapper as the response text:

```python
import json

response = formation.chat(
    {"message": "Extract the customer details"},
    user_id="user_123",
)
formatted = json.loads(response["response"])
content = formatted["content"]
```

## Domain-Specific Structured Data

For an application object such as an invoice, ticket, or customer record:

1. Configure the formation for JSON mode if a JSON outer wrapper is useful.
2. Describe the required fields and constraints in the agent instructions.
3. Parse the wrapper returned by MUXI.
4. Parse the agent-generated `content` if it is intended to contain JSON.
5. Validate that object in the application with JSON Schema, Pydantic, Zod, or
   an equivalent validator.
6. Reject, repair, or retry invalid data according to the application's risk
   policy.

Example agent instruction:

```yaml
agents:
  - id: extractor
    system_message: |
      Return only a JSON object with these fields:
      - name: string
      - email: string
      - phone: string or null
```

Application validation remains necessary because the instruction guides a
model; it does not establish a runtime schema contract.

## Format Selection Guidance

| Format | Use when |
|--------|----------|
| `markdown` | People will read rich, formatted answers |
| `text` | The consumer expects uncomplicated plain text |
| `json` | The consumer benefits from a predictable outer JSON wrapper |

HTML is not accepted by current formation validation. If an application needs
HTML, render validated Markdown or transform trusted structured data in the
application instead of requesting an unsupported response mode.

## Operational Notes

- Changing the format requires updating and reloading or redeploying the
  formation.
- A client cannot safely select a different format by adding `format` to the
  chat payload; that field is not part of the chat contract.
- Response format and streaming are separate settings. Consumers should follow
  the SSE token contract while streaming and the configured format of the
  completed response.
- Log validation failures without recording sensitive response bodies.
