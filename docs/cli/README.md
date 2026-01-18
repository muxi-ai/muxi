---
title: CLI
description: Command-line tool for managing MUXI formations
---

# MUXI CLI

## Manage formations from the command line

The `muxi` CLI handles everything: creating formations, managing secrets, deploying to servers, and chatting with your agents.


## Installation

```bash
brew install muxi-ai/tap/muxi
```

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
muxi dev
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
muxi formation list
muxi formation restart <id>
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
muxi formation get <id>
```
[[/card]]

::::


## Common Workflows

### Create and Run Locally

```bash
muxi new formation my-assistant
cd my-assistant
muxi secrets setup
muxi dev
```

### Deploy to Server

```bash
muxi deploy --profile production
muxi formation list
muxi logs my-assistant --follow
```

### Pull from Registry

```bash
muxi pull @acme/research-agent
cd research-agent
muxi secrets setup
muxi dev
```


## Quick Reference

| Command | Description |
|---------|-------------|
| `muxi new` | Create formations, agents, MCPs, SOPs, triggers |
| `muxi dev` | Run formation locally |
| `muxi deploy` | Deploy to server |
| `muxi formation` | Manage deployed formations |
| `muxi secrets` | Manage encrypted secrets |
| `muxi logs` | View formation logs |
| `muxi chat` | Interactive chat with formation |
| `muxi pull` | Download from registry |
| `muxi push` | Publish to registry |
| `muxi profile` | Manage server profiles |
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

(deploy.md)[[card]]
#### muxi deploy
Deploy formations to servers.
[[/card]]

(secrets.md)[[card]]
#### muxi secrets
Manage encrypted secrets.
[[/card]]

(formation.md)[[card]]
#### muxi formation
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
