---
title: Versioning
description: How MUXI handles versions across components
---
# Versioning

## Keep formations stable as MUXI evolves

MUXI uses semantic versioning across schemas, runtime, and server. This page explains version compatibility and upgrade paths.

## Schema Version

Formations specify their schema version:

```yaml
schema: "1.0.0"
```

This ensures compatibility with the runtime.

### Version Compatibility

| Schema | Runtime | Status |
|--------|---------|--------|
| 1.0.x | 1.0.x | Compatible |
| 1.0.x | 1.1.x | Compatible |
| 1.0.x | 2.0.x | May break |

Minor versions are backward compatible. Major versions may require migration.

## Formation Versioning

### Automatic

When publishing to registry:

```bash
muxi push
```

Version auto-increments: 1.0.0 → 1.0.1

### Manual

```bash
muxi push --tag v1.1.0
```

### Semantic Versioning

Follow semver:

- **Major** (2.0.0) - Breaking changes
- **Minor** (1.1.0) - New features, backward compatible
- **Patch** (1.0.1) - Bug fixes

## Pulling Versions

### Latest

```bash
muxi pull @muxi/hello-muxi
```

Gets most recent version.

### Specific Version

```bash
muxi pull @muxi/hello-muxi@1.0.0
```

### Version Ranges

```bash
muxi pull @muxi/hello-muxi@^1.0.0   # 1.x.x
muxi pull @muxi/hello-muxi@~1.0.0   # 1.0.x
```

## Server Versioning

Check server version:

```bash
muxi-server version
```

### Updates

```bash
# Homebrew
brew upgrade muxi

# Linux
curl -fsSL https://muxi.org/install | sudo bash
```

## Runtime Version

Formations run on specific runtime versions:

```yaml
runtime:
  version: "1.0.0"   # Pin version
  version: "latest"  # Use latest
```

### Auto-Download

```yaml
# Server config
runtime:
  auto_download: true
```

Server downloads required runtime versions automatically.

## Migration

When upgrading schema versions:

1. Check changelog for breaking changes
2. Update formation.afs schema version
3. Apply required changes
4. Test locally: `muxi dev`
5. Deploy: `muxi deploy`

### Migration Guides

Major version migrations documented at:

- docs.muxi.org/migrations/v1-to-v2
- docs.muxi.org/migrations/v2-to-v3

## Rollback

Revert to previous version:

```bash
muxi server rollback my-assistant
```

Shows available versions and reverts.

## Best Practices

1. **Pin versions in production** - Avoid unexpected changes
2. **Test updates locally** - Before deploying
3. **Read changelogs** - Understand what changed
4. **Keep current** - Security and feature updates
5. **Version your formations** - Semantic versioning
