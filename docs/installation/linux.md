---
title: Linux Installation
description: Install MUXI on Ubuntu, Debian, Fedora, and other Linux distributions
---
# Linux Installation

## One command to install on any Linux distro

Install MUXI Server and CLI with a single command. Works on Ubuntu, Debian, Fedora, CentOS, Arch, and most other distributions.

## Quick Install

**System-wide (recommended for production):**

```bash
curl -fsSL https://muxi.org/install | sudo bash
```

**User-level (no sudo):**

```bash
curl -fsSL https://muxi.org/install | bash
```

## Distribution-Specific

### Ubuntu / Debian

```bash
# Install dependencies
sudo apt update
sudo apt install -y curl

# Install MUXI
curl -fsSL https://muxi.org/install | sudo bash
```

### RHEL / CentOS / Fedora

```bash
# Install dependencies
sudo dnf install -y curl

# Install MUXI
curl -fsSL https://muxi.org/install | sudo bash
```

### Arch Linux

```bash
# Install dependencies
sudo pacman -S curl

# Install MUXI
curl -fsSL https://muxi.org/install | sudo bash
```

## Manual Install

```bash
# Download latest release
curl -LO https://github.com/muxi-ai/server/releases/latest/download/muxi-server-linux-amd64.tar.gz

# Extract
tar -xzf muxi-server-linux-amd64.tar.gz

# Move to PATH
sudo mv muxi-server /usr/local/bin/
sudo mv muxi /usr/local/bin/

# Verify
muxi --version
```

For ARM64, use `linux-arm64` instead of `linux-amd64`.

## Running as a Service

### systemd (Recommended)

Create `/etc/systemd/system/muxi-server.service`:

```ini
[Unit]
Description=MUXI Server
After=network.target

[Service]
Type=simple
User=muxi
Group=muxi
ExecStart=/usr/local/bin/muxi-server serve
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
```

Enable and start:

```bash
# Create service user
sudo useradd -r -s /bin/false muxi

# Enable and start
sudo systemctl daemon-reload
sudo systemctl enable muxi-server
sudo systemctl start muxi-server

# Check status
sudo systemctl status muxi-server
```

### View Logs

```bash
sudo journalctl -u muxi-server -f
```

## Configuration

| Install Type | Config Location |
|--------------|-----------------|
| System (sudo) | `/etc/muxi/` |
| User | `~/.muxi/` |

## Production Setup

1. **Create dedicated user:**

   ```bash
   sudo useradd -r -s /bin/false muxi
   ```

2. **Set permissions:**

   ```bash
   sudo chown -R muxi:muxi /etc/muxi
   ```

3. **Configure firewall:**

   ```bash
   sudo ufw allow 7890/tcp
   ```

4. **Enable service:**

   ```bash
   sudo systemctl enable muxi-server
   ```

## Next Steps

- [Quickstart](../quickstart.md) - Create your first formation
- [Production Deployment](../server/production.md) - Production best practices
