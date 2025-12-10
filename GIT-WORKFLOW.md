# Git Workflow

All MUXI repositories use a consistent **three-branch workflow** for development, testing, and releases.

---

## Overview

```
develop (default) → rc (release candidate) → main (production)
                                                  ↓
                                          (auto-merge back to develop)
```

| Branch | Purpose | Stability |
|--------|---------|-----------|
| `develop` | Active development | Can break |
| `rc` | Release candidate testing | Should work |
| `main` | Production releases | Must work |

---

## The Workflow

### 1. Feature Development

```bash
# Start from develop
git checkout develop
git pull

# Create feature branch
git checkout -b feature/awesome-thing

# Work, commit, push
git push -u origin feature/awesome-thing

# Create PR targeting develop
gh pr create --base develop
```

**What happens:**
- CI runs automatically
- Tests must pass
- Requires 1 approval
- Merges when green + approved

### 2. Release Candidate

When `develop` is ready for release, maintainers merge it to `rc`:

```bash
git checkout rc && git merge develop && git push
```

**What happens:**
- RC workflow builds all platforms
- All tests run
- Binaries are validated

### 3. Production Release

After RC passes, maintainers merge to `main`:

```bash
git checkout main && git merge rc && git push
```

**What happens:**
- Version is auto-calculated (ScalVer)
- Git tag created
- GitHub release published
- Auto-merges back to `develop`

---

## ScalVer Versioning

We use **ScalVer** (date-based versioning): `0.YYYYMMDD.N`

```
0.20251029.0  ← First release on Oct 29, 2025
0.20251029.1  ← Second release same day (hotfix)
0.20251030.0  ← First release on Oct 30, 2025
```

**Why?**
- Date shows recency clearly
- Supports multiple releases per day
- No semantic meaning to debate

> [!IMPORTANT]
> Don't bump versions manually. CI/CD handles it automatically when `rc` merges to `main`.

---

## Hotfix Process

For urgent production fixes:

```bash
# 1. Create hotfix branch from develop
git checkout develop
git checkout -b hotfix/critical-bug
# Fix and commit

# 2. Create PR with [HOTFIX] tag
gh pr create --base develop --title "[HOTFIX] Critical bug fix"

# 3. After merge, maintainers fast-track to rc → main
```

Result: Hotfix deployed in ~15-30 minutes.

---

## Branch Naming

```
feature/descriptive-name
fix/bug-description
hotfix/critical-issue
docs/update-readme
test/add-integration-tests
refactor/clean-up-code
```

---

## Commit Messages

Follow [Conventional Commits](https://www.conventionalcommits.org/):

```
feat: add new feature
fix: resolve bug
docs: update documentation
chore: maintenance task
test: add tests
refactor: code refactoring
perf: performance improvement
```

---

## Best Practices

### Before Merging to develop
- ✅ All tests pass
- ✅ Code review approved
- ✅ No merge conflicts

### Before Merging to rc
- ✅ All tests pass on develop
- ✅ Manual smoke testing
- ✅ No critical bugs outstanding

### Before Merging to main
- ✅ RC builds pass on all platforms
- ✅ Binaries tested
- ✅ Release notes ready

---

## Common Issues

### Can't push to `rc` or `main`

These branches are protected. The flow is:
1. PR to `develop`
2. Maintainers merge `develop` → `rc`
3. Maintainers merge `rc` → `main`

### CI workflow not showing

Wait for the first CI run to complete, then the status check appears in settings.

---

## Summary

```
1. Create feature branch from develop
2. Open PR targeting develop
3. Get review + merge
4. Maintainers: develop → rc → main
5. Auto-release + auto-merge back to develop
```

**Benefits:**
- Automated releases (no manual steps)
- Fast hotfixes (15-30 minutes)
- Clear process for everyone
- Safe production (tested on rc first)
