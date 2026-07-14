---
title: Versioning
description: How formation versions work in the MUXI Registry
---
# Versioning

## How formation versions work in the MUXI Registry


Every push publishes the version declared in the formation manifest. Pull
specific versions, roll deployments back, and manage releases with semantic
versioning.


## How Versions Work

### Publish the Declared Version

```bash
muxi push
```

### Bump Before Publishing

Update the manifest version, then publish it:

```bash
muxi bump minor
muxi push

muxi bump --set 2.0.0
muxi push
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
muxi pull @muxi/hello-muxi
# Gets latest version
```

### Exact Version

```bash
muxi pull @muxi/hello-muxi@1.0.0
# Gets exactly v1.0.0
```

### Version Ranges

```bash
muxi pull @muxi/hello-muxi@^1.0.0    # Latest 1.x.x
muxi pull @muxi/hello-muxi@~1.0.0    # Latest 1.0.x
muxi pull @muxi/hello-muxi@>=1.0.0   # 1.0.0 or higher
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
muxi show @alice/my-formation --versions
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
muxi remote get my-formation
```

```
Formation: my-formation
Version:   1.1.0
Source:    @alice/my-formation@1.1.0
...
```

### Rollback

```bash
muxi remote rollback my-formation
```

Rollback restores the immediately previous deployed version using the Server's
blue-green rollback path.


## Version Pinning

### In Deployment

Pin production to specific versions:

```bash
muxi pull @muxi/hello-muxi@1.0.0
muxi deploy
```

The deployed formation stays at 1.0.0 even if newer versions are published.

### Update Strategy

```bash
# Inspect published versions
muxi show @muxi/hello-muxi --versions

# Pull a specific version into a new directory
muxi pull @muxi/hello-muxi@1.1.0 --output hello-muxi-1.1.0
```


## Pre-release Versions

For testing before release:

```bash
muxi bump --set 2.0.0-beta.1
muxi push
```

Pre-release versions:
- Not pulled by default (`@latest`)
- Must be explicitly requested
- Show warnings when installed

```bash
muxi pull @alice/my-formation@2.0.0-beta.1
# Warning: This is a pre-release version
```


## Best Practices

1. **Use semantic versioning** - Communicate changes clearly
2. **Pin production versions** - Avoid surprise updates
3. **Test before major bumps** - Breaking changes need care
4. **Document changes** - Update release notes with each version


## Next Steps

- [Publishing](../registry/publish-formations.md) - Publish formations
- [Your Account](account.md) - Account setup
