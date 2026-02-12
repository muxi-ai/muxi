---
title: muxi registry
description: Pull and publish formations from the MUXI Registry
---
# muxi registry

## Share formations with the community

Pull pre-built formations to use as templates, publish your own for others to use, and search the registry for solutions to common problems.

## Usage

```bash
muxi pull <name>           # Pull formation
muxi push                  # Publish formation
muxi search <query>        # Search registry
```

## Pull Formation

```bash
muxi pull @muxi/hello-world
```

Downloads to current directory:

```
Pulling @muxi/hello-world...
✓ Downloaded v1.0.0
✓ Extracted to ./starter

cd starter-assistant
muxi secrets setup
muxi dev
```

### Pull Specific Version

```bash
muxi pull @muxi/hello-world@1.0.0
```

### Pull to Custom Path

```bash
muxi pull @muxi/hello-world --output /path/to/dir
```

## Search Registry

```bash
muxi search "customer support"
```

Output:

```
NAME                      DESCRIPTION                 DOWNLOADS
@muxi/support-bot         Customer support agent      1,234
@acme/helpdesk           Helpdesk assistant          567
@company/zendesk-agent   Zendesk integration         89
```

### Filter by Tag

```bash
muxi search --tag support
muxi search --tag research
```

## Publish Formation

```bash
cd my-formation
muxi push
```

Output:

```
Publishing my-formation...
✓ Validated formation
✓ Bundled (2.3MB)
✓ Published to registry

Available at: https://registry.muxi.org/my-formation
```

### First-Time Setup

```bash
muxi login
```

Authenticates with registry via GitHub OAuth.

### Publish Options

```bash
muxi push --public              # Public visibility
muxi push --private             # Private (default)
muxi push --tag v1.0.0          # Specific version tag
```

## Registry Namespaces

| Namespace | Description |
|-----------|-------------|
| `@muxi/*` | Official MUXI formations |
| `@username/*` | User formations |
| `@org/*` | Organization formations |

## Formation Metadata

Add registry metadata to `formation.afs`:

```yaml
schema: "1.0.0"
id: my-formation
name: My Formation
description: A helpful assistant

# Registry metadata
author: username
license: MIT
tags:
  - assistant
  - support
repository: https://github.com/user/my-formation
```

## Browse Online

Visit [registry.muxi.org](https://registry.muxi.org) to:

- Browse formations
- View documentation
- Check download stats

## Private Registry

Configure private registry:

```yaml
# ~/.muxi/cli/config.yaml
registry:
  url: https://registry.company.com
```

## Examples

```bash
# Pull official starter
muxi pull @muxi/hello-world

# Search for research agents
muxi search research

# Publish your formation
muxi push --public

# Pull specific version
muxi pull @acme/support@2.0.0
```
