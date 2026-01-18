---
title: Versioning
description: How formation versions work in the MUXI Registry
---
# Versioning

## How formation versions work in the MUXI Registry


Every push creates a version. Pull specific versions, rollback instantly, manage releases with semantic versioning.


## How Versions Work

### Automatic Increment

Each push auto-increments the patch version:

```bash
muxi push    # v1.0.0 (first push)
muxi push    # v1.0.1
muxi push    # v1.0.2
```

### Manual Versioning

Specify a version explicitly:

```bash
muxi push --tag v1.1.0    # Minor bump
muxi push --tag v2.0.0    # Major bump
```


## Semantic Versioning

MUXI follows [semver](https://semver.org):

| Version | When to Use |
|---------|-------------|
| `v1.0.1` (patch) | Bug fixes, no breaking changes |
| `v1.1.0` (minor) | New features, backward compatible |
| `v2.0.0` (major) | Breaking changes |

### What's a Breaking Change?

- Removing an agent
- Changing required secrets
- Changing API behavior
- Removing triggers or SOPs

### What's Not Breaking?

- Adding new agents
- Adding optional features
- Performance improvements
- Bug fixes


## Pulling Versions

### Latest (Default)

```bash
muxi pull @muxi/starter
# Gets latest version
```

### Exact Version

```bash
muxi pull @muxi/starter@1.0.0
# Gets exactly v1.0.0
```

### Version Ranges

```bash
muxi pull @muxi/starter@^1.0.0    # Latest 1.x.x
muxi pull @muxi/starter@~1.0.0    # Latest 1.0.x
muxi pull @muxi/starter@>=1.0.0   # 1.0.0 or higher
```

| Pattern | Matches |
|---------|---------|
| `^1.2.3` | `>=1.2.3 <2.0.0` |
| `~1.2.3` | `>=1.2.3 <1.3.0` |
| `1.2.x` | `>=1.2.0 <1.3.0` |
| `*` | Any version |


## Version History

### View Versions

```bash
muxi info @alice/my-formation --versions
```

```
@alice/my-formation
  v1.2.0  (latest)  2025-01-08  Added web search
  v1.1.1            2025-01-05  Bug fix
  v1.1.0            2025-01-03  Added memory
  v1.0.0            2025-01-01  Initial release
```

### Compare Versions

```bash
muxi diff @alice/my-formation@1.0.0 @alice/my-formation@1.1.0
```

Shows changes between versions.


## Deployed Versions

### Check Running Version

```bash
muxi formation get my-formation
```

```
Formation: my-formation
Version:   1.1.0
Source:    @alice/my-formation@1.1.0
...
```

### Rollback

```bash
muxi formation rollback my-formation
```

```
Current version: 1.2.0
Available versions:
  1. v1.1.1
  2. v1.1.0
  3. v1.0.0

Select version [1]: 1
Rolling back to v1.1.1...
✓ Rollback complete
```

Or directly:

```bash
muxi formation rollback my-formation --to 1.0.0
```


## Version Pinning

### In Deployment

Pin production to specific versions:

```bash
muxi pull @muxi/starter@1.0.0
muxi deploy
```

The deployed formation stays at 1.0.0 even if newer versions are published.

### Update Strategy

```bash
# Check for updates
muxi outdated

# Update to latest compatible
muxi update @muxi/starter

# Update to specific version
muxi update @muxi/starter@1.1.0
```


## Pre-release Versions

For testing before release:

```bash
muxi push --tag v2.0.0-beta.1
muxi push --tag v2.0.0-rc.1
```

Pre-release versions:
- Not pulled by default (`@latest`)
- Must be explicitly requested
- Show warnings when installed

```bash
muxi pull @alice/my-formation@2.0.0-beta.1
# Warning: This is a pre-release version
```


## Deprecation

Mark old versions as deprecated:

```bash
muxi deprecate @alice/my-formation@1.0.0 --message "Use v2.0.0+"
```

Deprecated versions:
- Still pullable
- Show warning on install
- Listed as deprecated in `muxi info`


## Best Practices

1. **Use semantic versioning** - Communicate changes clearly
2. **Pin production versions** - Avoid surprise updates
3. **Test before major bumps** - Breaking changes need care
4. **Deprecate, don't delete** - Give users migration time
5. **Document changes** - Update README with each version


## Next Steps

- [Publishing](publishing.md) - Publish formations
- [Your Account](account.md) - Account setup
