---
title: Add Knowledge
description: Give your agents domain expertise from your documents
---
# Add Knowledge

## Let agents answer questions from your docs

Add PDFs, markdown, spreadsheets, and more to give agents domain expertise. MUXI indexes your documents automatically and retrieves relevant context when users ask questions.

## Overview

Knowledge sources let agents answer questions from your documents.

## Step 1: Create Knowledge Directory

```bash
mkdir -p knowledge/docs
```

Add your documents:

```
knowledge/
└── docs/
    ├── getting-started.md
    ├── api-reference.md
    └── faq.md
```

## Step 2: Configure Agent

Create `agents/assistant.afs`:

```yaml
schema: "1.0.0"
id: assistant
name: Assistant
description: Helpful assistant with knowledge access

system_message: You are a helpful assistant.

knowledge:
  enabled: true
  sources:
    - path: knowledge/docs/
      description: Product documentation
```

## Step 3: Test

```bash
muxi dev
```

Ask about your docs:

```
You: How do I get started?
Assistant: Based on the documentation... [uses knowledge]
```

## Multiple Sources

```yaml
knowledge:
  sources:
    - path: knowledge/docs/
      description: Product documentation
    - path: knowledge/faq/
      description: Frequently asked questions
    - path: knowledge/policies/
      description: Company policies
```

## Configuration Options

```yaml
knowledge:
  enabled: true
  embed_batch_size: 50
  max_files_per_source: 10
  sources:
    - path: knowledge/docs/
      description: Documentation
      recursive: true              # Include subdirs
      allowed_extensions: [".md"]  # Only markdown
      file_limit: 20               # Max files
      max_file_size: 5242880       # 5MB limit
```

## Supported Formats

MUXI uses [MarkItDown](https://github.com/microsoft/markitdown) for document conversion - any format MarkItDown supports works with MUXI.

| Category | Formats | Notes |
|----------|---------|-------|
| **Text & Documents** | `.md`, `.txt`, `.pdf`, `.docx`, `.pptx`, `.xlsx` | Structure preserved |
| **Data** | `.csv`, `.json`, `.html` | Structure-aware chunking |
| **Images** | `.jpg`, `.png`, `.gif` | OCR + vision model analysis |

> [!TIP]
> Vision models (GPT-4V, Claude, Gemini) can analyze screenshots, diagrams, and charts in your knowledge base.

## Agent-Specific Knowledge

Different agents, different knowledge:

```yaml
agents:
  - id: support
    knowledge:
      sources:
        - path: knowledge/support/
          description: Support FAQs
  
  - id: sales
    knowledge:
      sources:
        - path: knowledge/pricing/
          description: Pricing info
```

## Update Knowledge

When files change:

1. MUXI detects changes (MD5 hash)
2. Re-indexes updated files
3. Cache invalidates automatically

Or force reindex:

```bash
muxi dev --reindex
```

## Troubleshooting

### Knowledge Not Found

Check paths are relative to formation:

```yaml
# Good
path: knowledge/docs/

# Bad
path: /absolute/path/to/docs/
```

### Files Not Loading

Check file size limits:

```yaml
knowledge:
  sources:
    - path: knowledge/
      max_file_size: 10485760  # 10MB
```

## Next Steps

- [Knowledge Reference](../reference/knowledge.md) - Full configuration
- [Add Memory](add-memory.md) - Conversation memory
