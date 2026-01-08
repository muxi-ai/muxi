# Create Triggers

Automate workflows with webhooks.

## Overview

Triggers let external systems send events to your formation.

## Step 1: Create Trigger Template

```bash
muxi new trigger github-issue
```

Creates `triggers/github-issue.md`:

```markdown
---
name: GitHub Issue Handler
tags: [github, issue]
---

New issue from ${{ data.repository }}:

**Issue #${{ data.issue.number }}**: ${{ data.issue.title }}
**Author**: ${{ data.issue.author }}

Analyze and provide:
1. Summary
2. Priority recommendation
3. Suggested next steps
```

## Step 2: Deploy Formation

```bash
muxi deploy
```

## Step 3: Call Trigger

```bash
curl -X POST http://localhost:8001/v1/triggers/github-issue \
  -H "X-Muxi-Client-Key: fmc_..." \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "repository": "muxi/runtime",
      "issue": {
        "number": 123,
        "title": "Bug in login",
        "author": "alice"
      }
    }
  }'
```

## Template Syntax

Use `${{ data.* }}` for substitution:

### Simple

```markdown
Hello ${{ data.name }}!
```

### Nested

```markdown
Issue: ${{ data.issue.title }}
Author: ${{ data.issue.author }}
```

## Async by Default

Triggers return immediately:

```json
{
  "request_id": "req_abc123",
  "status": "processing"
}
```

Poll for result:

```bash
curl http://localhost:8001/v1/requests/req_abc123
```

### Sync Mode

Wait for completion:

```json
{
  "data": {...},
  "use_async": false
}
```

## Webhook Integration

### GitHub

1. Create trigger template for events
2. Set up GitHub webhook:
   - URL: `https://your-server/v1/triggers/github-issue`
   - Content type: `application/json`
   - Events: Issues

### Middleware Pattern

Transform webhook payloads:

```javascript
// Express middleware
app.post('/github-webhook', (req, res) => {
  const payload = {
    data: {
      repository: req.body.repository.full_name,
      issue: {
        number: req.body.issue.number,
        title: req.body.issue.title
      }
    }
  };
  
  fetch('http://muxi:8001/v1/triggers/github-issue', {
    method: 'POST',
    headers: { 'X-Muxi-Client-Key': KEY },
    body: JSON.stringify(payload)
  });
  
  res.status(200).send('OK');
});
```

## List Triggers

```bash
curl http://localhost:8001/v1/triggers \
  -H "X-Muxi-Client-Key: fmc_..."
```

## Next Steps

- [Triggers Reference](../reference/triggers.md) - Full details
- [Write SOPs](sops.md) - Standard procedures
