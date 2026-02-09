---
title: CLI
description: Command-line tool for managing MUXI formations
---

# MUXI CLI

## Manage formations from the command line

The `muxi` CLI handles everything: creating formations, managing secrets, deploying to servers, and chatting with your agents.


## Installation

[[tabs]]

[[tab macOS]]
```bash
brew install muxi-ai/tap/muxi
```
[[/tab]]

[[tab Linux]]
```bash
curl -fsSL https://muxi.org/install | sudo bash
```
[[/tab]]

[[tab Windows]]
```powershell
powershell -c "irm https://muxi.org/install | iex"
```
[[/tab]]

[[/tabs]]

Verify:

```bash
muxi --version
```


## Command Overview

:::: cols=2

[[card]]
#### Create
```bash
muxi new formation <name>
muxi new agent <name>
muxi new mcp <name>
```
[[/card]]

[[card]]
#### Develop
```bash
muxi up
muxi down
muxi validate
muxi chat
```
[[/card]]

[[card]]
#### Secrets
```bash
muxi secrets setup
muxi secrets set <key>
muxi secrets list
```
[[/card]]

[[card]]
#### Deploy
```bash
muxi deploy
muxi server list
muxi server restart <id>
```
[[/card]]

[[card]]
#### Registry
```bash
muxi pull @muxi/starter
muxi push
muxi search <query>
```
[[/card]]

[[card]]
#### Monitor
```bash
muxi logs <formation>
muxi server get <id>
```
[[/card]]

::::


## Common Workflows

### Create and Run Locally

```bash
muxi new formation my-assistant
cd my-assistant
muxi secrets setup
muxi up              # Like docker compose up
# ... develop ...
muxi down            # Like docker compose down
```

### Deploy to Server

```bash
muxi deploy --profile production
muxi server list
muxi logs my-assistant --follow
```

### Pull from Registry

```bash
muxi pull @acme/research-agent
cd research-agent
muxi secrets setup
muxi up
```


## Quick Reference

| Command | Description |
|---------|-------------|
| `muxi new` | Create formations, agents, MCPs, SOPs, triggers |
| `muxi up` | Start formation locally (like docker compose up) |
| `muxi down` | Stop local formation (like docker compose down) |
| `muxi deploy` | Deploy to server |
| `muxi server` | Manage deployed formations |
| `muxi secrets` | Manage encrypted secrets |
| `muxi logs` | View formation logs |
| `muxi chat` | Interactive chat with formation |
| `muxi pull` | Download from registry |
| `muxi push` | Publish to registry |
| `muxi profiles` | Manage server profiles |
| `muxi validate` | Validate formation config |


## Configuration

CLI config: `~/.muxi/cli/config.afs`

```yaml
default_profile: local
editor: vim
output:
  format: table    # table, json
```


## Command Reference

:::: cols=2

(new.md)[[card]]
#### muxi new
Create formations, agents, MCPs, triggers, SOPs.
[[/card]]

(local-dev.md)[[card]]
#### muxi up/down
Local development - like docker compose.
[[/card]]

(deploy.md)[[card]]
#### muxi deploy
Deploy formations to servers.
[[/card]]

(secrets.md)[[card]]
#### muxi secrets
Manage encrypted secrets.
[[/card]]

(server.md)[[card]]
#### muxi server
Manage deployed formations.
[[/card]]

(registry.md)[[card]]
#### muxi pull/push
Pull and publish formations.
[[/card]]

(chat.md)[[card]]
#### muxi chat
Interactive chat sessions.
[[/card]]

::::
