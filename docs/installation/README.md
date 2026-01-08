---
title: Installation
description: Install MUXI Server and CLI on any platform
---

# Installation

## Get MUXI running on your system

One command installs both the **MUXI Server** (orchestration) and **CLI** (management tool).


## Quick Install

[[tabs]]

[[tab macOS]]
```bash
# Homebrew handles everything - binaries, PATH, and updates.
brew install muxi-ai/tap/muxi
```
[[/tab]]

[[tab Linux]]
```bash
# System-wide (recommended for servers)
curl -fsSL https://muxi.org/install | sudo bash

# User-level (no sudo required)
curl -fsSL https://muxi.org/install | bash
```
[[/tab]]

[[tab Windows]]
```powershell
# Run PowerShell as Administrator for system-wide install.
powershell -c "irm https://muxi.org/install | iex"
```
[[/tab]]

[[tab Docker]]
```bash
docker run -d \
  --name muxi-server \
  -p 7890:7890 \
  -v muxi-data:/data \
  ghcr.io/muxi-ai/server:latest
```
[[/tab]]

[[/tabs]]

---

### Verify Installation

```bash
muxi --version
muxi-server version
```

You should see version numbers for both tools.

---

## What Gets Installed

| Binary | Description | Default Port |
|--------|-------------|--------------|
| `muxi-server` | Orchestration server | 7890 |
| `muxi` | Command-line tool | - |

### Install Locations

| Method | Binaries | Config |
|--------|----------|--------|
| Homebrew | `/opt/homebrew/bin` | `~/.muxi/` |
| Linux (sudo) | `/usr/local/bin` | `/etc/muxi/` |
| Linux (user) | `~/.local/bin` | `~/.muxi/` |
| Windows | `%LOCALAPPDATA%\MUXI\bin` | `%USERPROFILE%\.muxi\` |

---

## After Installation

[[steps]]

[[step Initialize the server]]

```bash
muxi-server init
```

This generates authentication credentials. **Save them** - you'll need them for CLI access.

[[/step]]

[[step Start the server]]

```bash
muxi-server start
```

Server runs on port 7890.

[[/step]]

[[step Create your first formation]]

```bash
muxi new formation my-assistant
cd my-assistant
muxi secrets setup
muxi dev
```

[[/step]]

[[/steps]]

> [!TIP]
> See the [Quickstart](../quickstart.md) for a complete walkthrough.

---

## Installation Options

| Flag | Description |
|------|-------------|
| `--non-interactive` | Skip prompts, use defaults |
| `--cli-only` | Install CLI only (no server) |

```bash
curl -fsSL https://muxi.org/install | bash -s -- --cli-only
```

---

## System Requirements

| Component | Minimum | Recommended |
|-----------|---------|-------------|
| CPU | 2 cores | 4+ cores |
| RAM | 2 GB | 8 GB |
| Disk | 10 GB | 50 GB SSD |

---

## Platform-Specific Guides

:::: cols=2

(macos.md)[[card]]
#### macOS
Homebrew, manual install, Apple Silicon notes.
[[/card]]

(linux.md)[[card]]
#### Linux
Ubuntu, Debian, RHEL, systemd service setup.
[[/card]]

(windows.md)[[card]]
#### Windows
PowerShell, WSL options, service configuration.
[[/card]]

(docker.md)[[card]]
#### Docker
Container deployment and Docker Compose.
[[/card]]

::::
