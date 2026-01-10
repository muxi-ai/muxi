---
title: Server Setup
description: First-time configuration for MUXI Server
---
# Server Setup

## First-time configuration for MUXI Server


Configure MUXI Server and start deploying formations.


<!-- Video placeholder -->
<div class="video-placeholder">
  <p>🎬 Video: Server Setup Walkthrough</p>
</div>

---

## What You'll Do

[[steps]]

### Step 1: Initialize Configuration

Run the init command to generate your config and authentication keys:

```bash
muxi-server init
```

This creates:
- `~/.muxi/server/config.afs` - Server configuration
- Authentication keys for secure API access

```
✓ Created config at ~/.muxi/server/config.yaml
✓ Generated authentication key: MUXI_e8f3a9b2

Your API credentials:
  Key ID:     MUXI_e8f3a9b2
  Secret Key: sk_9f2e8d7c6b5a4e3f2d1c0b9a8e7f6d5c

Save these credentials - you'll need them to connect from the CLI.
```

> [!WARNING]
> Save your secret key now - it's only shown once. You'll need it to connect from the CLI.

---

### Step 2: Review Your Configuration

Open the generated config:

```bash
cat ~/.muxi/server/config.yaml
```

The defaults work for most setups:

```yaml
server:
  port: 7890              # MUXI's default port
  host: 0.0.0.0           # Accept connections from anywhere

auth:
  enabled: true           # Always enabled for security
  keys:
    - id: MUXI_e8f3a9b2
      secret: sk_...

formations:
  port_range: [8000, 9000]  # Ports for your formations
  auto_restart: true        # Restart crashed formations

logging:
  level: info
  format: json
```

**Common adjustments:**

| Setting | When to Change |
|---------|---------------|
| `server.host: 127.0.0.1` | Localhost only (dev) |
| `logging.level: debug` | Troubleshooting |
| `formations.port_range` | If ports conflict |

---

### Step 3: Start the Server

```bash
muxi-server start
```

```
MUXI Server v1.0.0
━━━━━━━━━━━━━━━━━━━━
Listening on :7890
Auth: enabled
Formations: ready

Server is running. Press Ctrl+C to stop.
```

**Run in background (production):**

```bash
# macOS/Linux with systemd
sudo systemctl enable muxi-server
sudo systemctl start muxi-server

# Or with the built-in daemon mode
muxi-server start --daemon
```

---

### Step 4: Verify It's Running

```bash
curl http://localhost:7890/health
```

```json
{"status": "healthy", "version": "1.0.0"}
```

Or check the status:

```bash
muxi-server status
```

```
MUXI Server Status
━━━━━━━━━━━━━━━━━━
Status:     running
Port:       7890
Uptime:     2m 34s
Formations: 0 running
```

---

### Step 5: Connect Your CLI

Now configure the CLI to talk to your server. You'll need the credentials from Step 1:

```bash
muxi profile add local
```

```
Server URL: http://localhost:7890
Key ID: MUXI_e8f3a9b2
Secret Key: sk_9f2e8d7c6b5a4e3f2d1c0b9a8e7f6d5c

✓ Profile 'local' saved
✓ Set as default profile
```

Test the connection:

```bash
muxi formation list
```

```
No formations deployed yet. Deploy your first with: muxi deploy
```

[[/steps]]

---

## You're Ready!

Your server is configured and the CLI is connected. Next steps:

<div class="cards">
  <a href="../quickstart" class="card">
    <h3>Deploy Your First Formation</h3>
    <p>Create and deploy an agent in 5 minutes</p>
  </a>
  <a href="configuration" class="card">
    <h3>Configuration Reference</h3>
    <p>All server configuration options</p>
  </a>
  <a href="production" class="card">
    <h3>Production Setup</h3>
    <p>SSL, firewalls, and scaling</p>
  </a>
</div>

---

## Troubleshooting

<details>
<summary>Port 7890 is already in use</summary>

Change the port in your config:

```yaml
server:
  port: 7891  # Different port
```

Then restart and update your CLI profile.
</details>

<details>
<summary>Connection refused from CLI</summary>

1. Check the server is running: `muxi-server status`
2. Verify the URL in your profile: `muxi profile list`
3. Check firewall allows port 7890
</details>

<details>
<summary>Authentication failed</summary>

1. Verify key ID and secret match between server and CLI
2. Check `auth.enabled: true` in server config
3. Re-run `muxi-server init` to generate new keys if needed
</details>

---

## Need Help?

- [Full Configuration Reference](configuration.md)
- [Authentication Details](authentication.md)
- [Troubleshooting Guide](../guides/troubleshooting.md)
