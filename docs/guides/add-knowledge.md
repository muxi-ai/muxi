# Add Knowledge

Give your agent domain expertise with RAG.

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

Add to `formation.afs`:

```yaml
agents:
  - id: assistant
    role: helpful assistant
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

- `.md`, `.txt` - Text
- `.pdf` - PDFs
- `.docx` - Word documents
- `.csv`, `.json` - Data files

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

- [Knowledge Reference](../formations/knowledge.md) - Full configuration
- [Add Memory](add-memory.md) - Conversation memory
