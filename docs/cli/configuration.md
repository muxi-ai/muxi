---
title: CLI Configuration
description: Configure the MUXI CLI settings and server profiles
---
# CLI Configuration

## Configure the MUXI CLI settings and server profiles


The CLI stores configuration in `~/.muxi/cli/`: server profiles, default settings, and preferences.


## Configuration Files

```
~/.muxi/cli/
├── config.yaml      # CLI settings
└── servers.yaml     # Server profiles
```


## CLI Settings

`~/.muxi/cli/config.yaml`:

```yaml
# Default server profile
default_profile: local

# Editor for secrets and configs
editor: vim

# Output format
output:
  format: table      # table, json, yaml
  color: true

# Development settings
dev:
  port: 8001
  auto_reload: true
```

### Settings Reference

| Setting | Default | Description |
|---------|---------|-------------|
| `default_profile` | `local` | Server profile for commands |
| `editor` | `$EDITOR` or `vim` | Editor for interactive editing |
| `output.format` | `table` | Output format (table/json/yaml) |
| `output.color` | `true` | Enable colored output |
| `dev.port` | `8001` | Port for `muxi dev` |
| `dev.auto_reload` | `true` | Auto-reload on file changes |


## Server Profiles

`~/.muxi/cli/servers.yaml`:

```yaml
profiles:
  local:
    url: http://localhost:7890
    key_id: MUXI_local
    secret_key: sk_...

  staging:
    url: https://staging.example.com:7890
    key_id: MUXI_staging
    secret_key: sk_...

  production:
    url: https://muxi.example.com:7890
    key_id: MUXI_production
    secret_key: sk_...
```

### Managing Profiles

```bash
# Add a profile
muxi profiles add production

# List profiles
muxi profiles list

# Switch default
muxi set default profile production

# Remove profile
muxi profiles remove old-server
```

### Using Profiles

```bash
# Use default profile
muxi deploy

# Use specific profile
muxi deploy --profile production

# Override for one command
muxi server list --profile staging
```


## Environment Variables

Override config with environment variables:

| Variable | Overrides |
|----------|-----------|
| `MUXI_PROFILE` | `default_profile` |
| `MUXI_SERVER_URL` | Profile URL |
| `MUXI_KEY_ID` | Profile key_id |
| `MUXI_SECRET_KEY` | Profile secret_key |
| `MUXI_OUTPUT_FORMAT` | `output.format` |

```bash
# One-off override
MUXI_PROFILE=production muxi server list

# CI/CD usage
export MUXI_SERVER_URL=https://muxi.example.com:7890
export MUXI_KEY_ID=$CI_MUXI_KEY_ID
export MUXI_SECRET_KEY=$CI_MUXI_SECRET
muxi deploy
```


## Multiple Servers

Deploy to multiple servers from one profile:

```yaml
profiles:
  production:
    servers:
      - id: us-east
        url: https://east.example.com:7890
        key_id: MUXI_east
        secret_key: sk_...
      - id: us-west
        url: https://west.example.com:7890
        key_id: MUXI_west
        secret_key: sk_...
```

```bash
muxi deploy --profile production
# Deploys to both us-east and us-west
```


## Reset Configuration

```bash
# Reset CLI config
rm ~/.muxi/cli/config.yaml

# Reset all CLI data
rm -rf ~/.muxi/cli/

# Next command regenerates defaults
muxi --version
```


## Next Steps

- [CLI Overview](README.md) - All commands
- [CLI Cheatsheet](cheatsheet.md) - Quick reference
