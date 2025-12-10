# Contributing to MUXI

Thank you for your interest in contributing to MUXI!

> [!NOTE]
> These guidelines apply to **all MUXI repositories** including the server, runtime, CLI, SDKs, and documentation.

---

## Quick Links

- **[DEVELOPMENT.md](./DEVELOPMENT.md)** - Set up your dev environment
- **[GIT-WORKFLOW.md](./GIT-WORKFLOW.md)** - Branching and release process
- **[ARCHITECTURE.md](./ARCHITECTURE.md)** - Understand the system
- **[REPOSITORIES.md](./REPOSITORIES.md)** - All MUXI repositories

---

## The Basics

### PRs Target `develop`

All pull requests **must target the `develop` branch**, not `main`. We use a three-branch workflow:

```
develop → rc → main
```

See [GIT-WORKFLOW.md](./GIT-WORKFLOW.md) for details.

### Issue First

**Before opening a PR**, an issue must exist and you must be assigned to it (except for minor fixes). This ensures:

- The change aligns with project goals
- No duplicate work
- You get guidance before implementation

**How to start:**
1. Browse existing issues or create a new one
2. Comment asking to be assigned
3. Wait for assignment
4. Then start coding

### What's a "Minor Fix"?

These don't need an issue:

- ✅ Typo fixes in docs, comments, or error messages
- ✅ Broken link corrections
- ✅ Formatting fixes (indentation, whitespace)
- ✅ Small grammar corrections

These **do** need an issue:

- ❌ Any code logic changes
- ❌ New dependencies
- ❌ API changes
- ❌ UI/UX changes
- ❌ Performance optimizations
- ❌ Security-related changes

**When in doubt, create an issue.**

---

## AI/LLM Policy

We welcome AI-assisted development. However:

- ✅ Test your changes locally
- ✅ Run existing tests
- ✅ Add tests for new functionality
- ❌ **No "vibe-coded" contributions** that haven't been executed

> [!WARNING]
> PRs that show evidence of being AI-generated without local testing will be closed.

---

## What We Accept

- 🐛 **Bug fixes** - Fix existing issues
- ✨ **Features** - New functionality (discuss first)
- 📝 **Documentation** - Improve guides and docs
- 🧪 **Tests** - Improve coverage
- 🔧 **DX improvements** - Better tooling and workflows

## What We Don't Accept

- PRs without associated issues (except minor fixes)
- Code that hasn't been tested locally
- Changes that break existing functionality
- Code that doesn't follow our style guidelines
- Large refactoring without prior discussion
- **Manual version bumps** - CI/CD handles versioning automatically

---

## Branch Naming

```
feature/descriptive-name
fix/bug-description
docs/what-changed
refactor/what-improved
hotfix/critical-issue
```

## Commit Messages

Follow [Conventional Commits](https://www.conventionalcommits.org/):

```
feat: add memory persistence option
fix: resolve session timeout bug
docs: update API reference
chore: update dependencies
test: add integration tests for chat endpoint
```

- Use present tense ("Add feature" not "Added feature")
- Keep first line under 72 characters
- Reference issues when applicable: `Fix timeout bug (#123)`

---

## Code Review Process

1. **Automated checks** must pass (lint, tests, types)
2. **Manual review** by maintainers
3. **Address feedback** promptly
4. **Squash commits** if requested

---

## Security

Found a vulnerability? **Do not open a public issue.**

Email [security@muxi.org](mailto:security@muxi.org) instead.

---

## Getting Help

- **Questions?** [GitHub Discussions](https://github.com/orgs/muxi-ai/discussions)
- **Bugs?** [GitHub Issues](https://github.com/muxi-ai/muxi/issues)
- **Community:** [GitHub Discussions](https://github.com/orgs/muxi-ai/discussions)

---

## License

By contributing, you agree to license your contributions under the same license as the project. Please review our [Contributor License Agreement](./CONTRIBUTOR_LICENSE_AGREEMENT.md).

---

**Quality over quantity.** We prefer well-tested, thoughtful contributions over quick fixes that might introduce issues.
