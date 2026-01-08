# Publishing to Registry

Share your formations with the community.

## Prerequisites

1. MUXI account
2. Formation ready for distribution
3. CLI authenticated

## Authenticate

```bash
muxi auth login
```

Opens browser for authentication.

## Prepare Formation

### Required Metadata

Add to `formation.afs`:

```yaml
schema: "1.0.0"
id: my-formation
name: My Formation
description: A helpful AI assistant

# Registry metadata
author: your-username
license: MIT
tags:
  - assistant
  - chat
repository: https://github.com/user/my-formation
```

### Create secrets.example

Document required secrets:

```
# Required secrets
OPENAI_API_KEY=
```

### Add README

Create `README.md` in formation directory:

```markdown
# My Formation

Description of what this formation does.

## Quick Start

1. Pull the formation
2. Set up secrets
3. Run

## Configuration

Describe any configuration options.
```

## Publish

```bash
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

## Visibility

### Public

```bash
muxi push --public
```

Anyone can find and pull.

### Private (Default)

```bash
muxi push --private
```

Only you can access.

### Organization

```bash
muxi push --org my-org
```

Published under organization namespace.

## Versioning

### Automatic

Versions auto-increment on push.

### Manual

```bash
muxi push --tag v1.0.0
```

### Semantic Versioning

```
v1.0.0  # Initial release
v1.1.0  # New features
v1.1.1  # Bug fixes
v2.0.0  # Breaking changes
```

## Update Formation

Edit and push again:

```bash
# Make changes
muxi push
```

Previous versions remain available.

## Unpublish

```bash
muxi unpublish my-formation
```

Removes from registry.

## Best Practices

1. **Clear description** - Explain what it does
2. **Document secrets** - List required credentials
3. **Include README** - Usage instructions
4. **Add tags** - Help discovery
5. **Version properly** - Follow semantic versioning
6. **Test before publish** - Run `muxi validate`

## Validation

Check before publishing:

```bash
muxi validate
```

Checks:

- Schema validity
- Required fields
- secrets.example exists
- README exists (warning if missing)
