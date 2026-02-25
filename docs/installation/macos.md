---
title: macOS Installation
description: Install MUXI on macOS with Homebrew or direct download
---
# macOS Installation

## Get MUXI running on your Mac

Install MUXI Server and CLI using Homebrew (recommended) or download binaries directly. Works on both Intel and Apple Silicon Macs.

## Homebrew (Recommended)

```bash
brew install muxi-ai/tap/muxi
```

This installs both `muxi-server` and `muxi` CLI.

### Verify

```bash
muxi --version
muxi-server version
```

### Update

```bash
brew upgrade muxi
```

### Uninstall

```bash
brew uninstall muxi
```

## Manual Install

Download from GitHub releases:

```bash
# Download latest release
curl -LO https://github.com/muxi-ai/server/releases/latest/download/muxi-server-darwin-arm64.tar.gz

# Extract
tar -xzf muxi-server-darwin-arm64.tar.gz

# Move to PATH
sudo mv muxi-server /usr/local/bin/
sudo mv muxi /usr/local/bin/

# Verify
muxi --version
```

For Intel Macs, use `darwin-amd64` instead of `darwin-arm64`.

## Apple Silicon Notes

MUXI runs natively on Apple Silicon (M1/M2/M3). No Rosetta required.

If you encounter code signing issues:

```bash
xattr -d com.apple.quarantine /usr/local/bin/muxi-server
xattr -d com.apple.quarantine /usr/local/bin/muxi
```

## Configuration

Config files are stored in `~/.muxi/`:

```
~/.muxi/
├── server/
│   └── config.yaml    # Server configuration
└── cli/
    ├── config.yaml    # CLI settings
    └── servers.afs   # Server profiles
```

## Running as a Service

Use `launchd` to run the server on boot:

```bash
# Create launch agent
cat > ~/Library/LaunchAgents/ai.muxi.server.plist << 'EOF'
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>ai.muxi.server</string>
    <key>ProgramArguments</key>
    <array>
        <string>/opt/homebrew/bin/muxi-server</string>
        <string>serve</string>
    </array>
    <key>RunAtLoad</key>
    <true/>
    <key>KeepAlive</key>
    <true/>
</dict>
</plist>
EOF

# Load the service
launchctl load ~/Library/LaunchAgents/ai.muxi.server.plist
```

## Next Steps

- [Quickstart](../quickstart.md) - Create your first formation
- [Server Configuration](../server/configuration.md) - Customize settings
