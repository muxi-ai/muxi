---
title: CLI Setup
description: First-time configuration for the MUXI CLI
---
# CLI Setup

## First-time configuration for the MUXI CLI


Connect the CLI to your server and deploy your first formation.


<!-- Video placeholder -->
<div class="video-placeholder">
  <p>🎬 Video: CLI Setup Walkthrough</p>
</div>


## What You'll Do

[[steps]]

### Step 1: Verify Installation

Check the CLI is installed:

```bash
muxi --version
```

```
muxi version 1.0.0
```

If you don't see this, [install the CLI first](installation/README.md).

---

### Step 2: Connect to Your Server

Add a server profile. You'll need credentials from your server admin (or from `muxi-server init` if you set up the server yourself):

```bash
muxi profile add
```

```
Profile name [local]: local
Server URL: http://localhost:7890
Key ID: MUXI_e8f3a9b2
Secret Key: ••••••••••••••••

✓ Profile 'local' created
✓ Connection verified
✓ Set as default profile
```

> [!TIP]
> For remote servers, use the full URL: `https://muxi.example.com:7890`

---

### Step 3: Test the Connection

```bash
muxi formation list
```

```
No formations deployed yet.
```

If you see this (even empty), you're connected!

---

### Step 4: Create Your First Formation

```bash
muxi new formation my-assistant
cd my-assistant
```

This creates a formation directory:

```
my-assistant/
├── formation.afs       # Main configuration
├── agents/             # Agent definitions
├── knowledge/          # Documents for RAG
└── secrets             # Required secrets template
```

---

### Step 5: Add Your API Keys

The formation needs LLM credentials. Run the setup wizard:

```bash
muxi secrets setup
```

```
This formation requires the following secrets:

OPENAI_API_KEY (required)
  Your OpenAI API key for LLM access
  Enter value: sk-••••••••••••••••

✓ Secrets encrypted and saved
```

Your keys are encrypted locally - never sent to the server unencrypted.

---

### Step 6: Run Locally

Test your formation before deploying:

```bash
muxi dev
```

```
Starting development server...
✓ Formation loaded
✓ Knowledge indexed (0 documents)
✓ Server running at http://localhost:8001

Chat: http://localhost:8001/chat
API:  http://localhost:8001/v1

Press Ctrl+C to stop.
```

Try chatting:

```bash
muxi chat "Hello!"
```

```
assistant: Hello! How can I help you today?
```

---

### Step 7: Deploy to Server

When you're ready, deploy to your MUXI server:

```bash
muxi deploy
```

```
Deploying my-assistant to local...
✓ Formation validated
✓ Secrets verified
✓ Deployed successfully

Formation: my-assistant
Status:    running
URL:       http://localhost:7890/v1/formations/my-assistant
```

[[/steps]]


## You're Ready!

Your CLI is configured and you've deployed your first formation. Next steps:

:::: cols=3

(/guides/add-tools.md)[[card]]
### Add Tools

Give your agent web search, file access, and more
[[/card]]

(/guides/add-memory.md)[[card]]
### Add Memory

Let your agent remember conversations
[[/card]]

(/guides/add-knowledge.md)[[card]]
### Add Knowledge

Teach your agent from your documents
[[/card]]

::::


## Managing Profiles

### Multiple Servers

Add profiles for different environments:

```bash
muxi profile add staging
muxi profile add production
```

### Switch Default

```bash
muxi profile use production
```

### List Profiles

```bash
muxi profile list
```

```
  local       http://localhost:7890 (default)
  staging     https://staging.example.com:7890
  production  https://muxi.example.com:7890
```

### Deploy to Specific Profile

```bash
muxi deploy --profile production
```


## Troubleshooting

[[toggle Connection refused]]

1. Check server is running: contact your server admin or check `muxi-server status`
2. Verify the URL: `muxi profile list`
3. Check firewall/network allows connection

[[/toggle]]

[[toggle Authentication failed]]

1. Verify credentials with your server admin
2. Re-add the profile: `muxi profile add local`
3. Check for typos in key ID or secret

[[/toggle]]

[[toggle Secrets setup failed]]

1. Make sure you're in a formation directory (has `formation.afs`)
2. Check you have the required API keys
3. Try `muxi secrets list` to see what's needed

[[/toggle]]


## Need Help?

- [CLI Cheatsheet](cheatsheet.md) - Quick command reference
- [Configuration Reference](configuration.md) - All CLI settings
- [Troubleshooting](guides/troubleshooting.md) - Common issues
