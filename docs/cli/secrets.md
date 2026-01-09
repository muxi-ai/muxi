---
title: muxi secrets
description: Manage encrypted secrets for formations
---
# muxi secrets

## Secure credential management from the CLI

Add, update, and remove encrypted secrets for your formations. Secrets are stored in `secrets.enc` and never exposed in logs or environment variables.

## Usage

```bash
muxi secrets <command> [options]
```

## Commands

| Command | Description |
|---------|-------------|
| `setup` | Interactive secrets wizard |
| `set` | Set a secret |
| `get` | Get a secret value |
| `list` | List all secrets |
| `delete` | Delete a secret |

## Setup Wizard

```bash
muxi secrets setup
```

Scans formation for required secrets and prompts for each:

```
Setting up secrets for my-assistant...

Required secrets:
  OPENAI_API_KEY (from llm.api_keys)
  BRAVE_API_KEY (from mcps.web-search)

Enter OPENAI_API_KEY: ****
Enter BRAVE_API_KEY: ****

✓ Secrets encrypted and saved
```

## Set Secret

```bash
muxi secrets set OPENAI_API_KEY
```

Prompts for value:

```
Enter OPENAI_API_KEY: ****
✓ Secret saved
```

Or from environment:

```bash
echo $OPENAI_API_KEY | muxi secrets set OPENAI_API_KEY --stdin
```

## Get Secret

```bash
muxi secrets get OPENAI_API_KEY
```

Displays the value (use carefully).

## List Secrets

```bash
muxi secrets list
```

Output (keys only):

```
OPENAI_API_KEY
BRAVE_API_KEY
DATABASE_URL
```

## Delete Secret

```bash
muxi secrets delete OLD_KEY
```

## Options

| Flag | Description |
|------|-------------|
| `--path <dir>` | Formation directory |
| `--stdin` | Read value from stdin |
| `--force` | Overwrite without confirm |

## Files

Secrets are stored encrypted:

```
my-formation/
├── secrets.enc      # Encrypted secrets
├── secrets  # Template (commit this)
└── .key             # Encryption key (never commit!)
```

## Security

- **Never commit `.key`** - Add to `.gitignore`
- **Safe to commit `secrets.enc`** - Encrypted
- **Keep `secrets` updated** - Shows required secrets

## Examples

```bash
# Run setup wizard
muxi secrets setup

# Set individual secret
muxi secrets set GITHUB_TOKEN

# List all secrets
muxi secrets list

# Delete old secret
muxi secrets delete OLD_API_KEY
```
