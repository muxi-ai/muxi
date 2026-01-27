# Contributing to MUXI

Thank you for your interest in contributing to MUXI!

> [!NOTE]
> These guidelines apply to **all MUXI repositories** including the server, runtime, CLI, SDKs, and documentation.


## Quick Links

- **[DEVELOPMENT.md](https://github.com/muxi-ai/muxi/blob/main/contributing/DEVELOPMENT.md)** - Set up your dev environment
- **[GIT-WORKFLOW.md](https://github.com/muxi-ai/muxi/blob/main/contributing/GIT-WORKFLOW.md)** - Branching and release process
- **[ARCHITECTURE.md](https://github.com/muxi-ai/muxi/blob/main/ARCHITECTURE.md)** - Understand the system
- **[REPOSITORIES.md](https://github.com/muxi-ai/muxi/blob/main/REPOSITORIES.md)** - All MUXI repositories
- **[Style Guides](https://github.com/muxi-ai/muxi/blob/main/contributing/style-guides/)** - Language-specific coding conventions
- **[AGENTS.md](https://github.com/muxi-ai/muxi/blob/main/contributing/AGENTS.md)** - AI assistant context (template for all repos)


## Find Something to Work On

Looking for ways to contribute? Check issues with these labels:

- **Help Wanted**: [server](https://github.com/muxi-ai/server/issues?q=is%3Aissue+state%3Aopen+label%3A%22help+wanted%22) · [runtime](https://github.com/muxi-ai/runtime/issues?q=is%3Aissue+state%3Aopen+label%3A%22help+wanted%22) · [cli](https://github.com/muxi-ai/cli/issues?q=is%3Aissue+state%3Aopen+label%3A%22help+wanted%22) · [sdks](https://github.com/muxi-ai/sdks/issues?q=is%3Aissue+state%3Aopen+label%3A%22help+wanted%22) · [onellm](https://github.com/muxi-ai/onellm/issues?q=is%3Aissue+state%3Aopen+label%3A%22help+wanted%22) · [faissx](https://github.com/muxi-ai/faissx/issues?q=is%3Aissue+state%3Aopen+label%3A%22help+wanted%22)
- **Good First Issue**: [server](https://github.com/muxi-ai/server/issues?q=is%3Aissue+state%3Aopen+label%3A%22good+first+issue%22) · [runtime](https://github.com/muxi-ai/runtime/issues?q=is%3Aissue+state%3Aopen+label%3A%22good+first+issue%22) · [cli](https://github.com/muxi-ai/cli/issues?q=is%3Aissue+state%3Aopen+label%3A%22good+first+issue%22) · [sdks](https://github.com/muxi-ai/sdks/issues?q=is%3Aissue+state%3Aopen+label%3A%22help+wanted%22) · [onellm](https://github.com/muxi-ai/onellm/issues?q=is%3Aissue+state%3Aopen+label%3A%22good+first+issue%22) · [faissx](https://github.com/muxi-ai/faissx/issues?q=is%3Aissue+state%3Aopen+label%3A%22good+first+issue%22)
- **Bug**: [server](https://github.com/muxi-ai/server/issues?q=is%3Aissue+state%3Aopen+label%3Abug) · [runtime](https://github.com/muxi-ai/runtime/issues?q=is%3Aissue+state%3Aopen+label%3Abug) · [cli](https://github.com/muxi-ai/cli/issues?q=is%3Aissue+state%3Aopen+label%3Abug) · [sdks](https://github.com/muxi-ai/sdks/issues?q=is%3Aissue+state%3Aopen+label%3A%22help+wanted%22) · [onellm](https://github.com/muxi-ai/onellm/issues?q=is%3Aissue+state%3Aopen+label%3Abug) · [faissx](https://github.com/muxi-ai/faissx/issues?q=is%3Aissue+state%3Aopen+label%3Abug)
- **Performance**: [server](https://github.com/muxi-ai/server/issues?q=is%3Aissue+state%3Aopen+label%3Aperformance) · [runtime](https://github.com/muxi-ai/runtime/issues?q=is%3Aissue+state%3Aopen+label%3Aperformance) · [cli](https://github.com/muxi-ai/cli/issues?q=is%3Aissue+state%3Aopen+label%3Aperformance) · [sdks](https://github.com/muxi-ai/sdks/issues?q=is%3Aissue+state%3Aopen+label%3A%22help+wanted%22) · [onellm](https://github.com/muxi-ai/onellm/issues?q=is%3Aissue+state%3Aopen+label%3Aperformance) · [faissx](https://github.com/muxi-ai/faissx/issues?q=is%3Aissue+state%3Aopen+label%3Aperformance)

Want to take on an issue? Leave a comment and a maintainer may assign it to you.


## The Basics

### PRs Target `develop`

All pull requests **must target the `develop` branch**, not `main`. We use a three-branch workflow:

```
develop → rc → main
```

See [GIT-WORKFLOW.md](https://github.com/muxi-ai/muxi/blob/main/contributing/GIT-WORKFLOW.md) for details.

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


## AI-Assisted Development

We welcome AI-assisted contributions that meet our quality standards. MUXI is built with significant AI assistance.

**Quick rules:**
- ✅ Test your changes locally
- ✅ Understand and be able to explain your code
- ❌ No drive-by AI PRs (work on accepted issues only)
- ❌ No committing agent dotfiles (`.claude/`, `.factory/`, etc.)

> [!WARNING]
> Low-effort AI-generated contributions will be closed and may result in a ban.

**Read the full policy:** [AI-POLICY.md](https://github.com/muxi-ai/muxi/blob/main/AI-POLICY.md)


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


## Pull Request Expectations

- **Keep PRs small and focused**  –  easier to review, faster to merge
- **Link relevant issue(s)** in the description
- **Explain the problem** and why your change fixes it
- **Before adding new functions**, ensure similar behavior doesn't already exist elsewhere in the codebase
- **Test locally** before submitting


## Feature Requests

For net-new functionality, **start with a design conversation**. Open an issue describing:

1. The problem you're trying to solve
2. Your proposed approach (optional)
3. Why it belongs in MUXI

Wait for maintainer approval before opening a feature PR. This helps ensure alignment with project direction and avoids wasted effort.


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


## Code Review Process

1. **Automated checks** must pass (lint, tests, types)
2. **Manual review** by maintainers
3. **Address feedback** promptly
4. **Squash commits** if requested


## Security

Found a vulnerability? **Do not open a public issue.**

Email [security@muxi.org](mailto:security@muxi.org) instead.


## Getting Help

- **Questions?** [GitHub Discussions](https://github.com/muxi-ai/community/discussions)
- **Bugs?** [GitHub Issues](https://github.com/muxi-ai/muxi/issues)
- **Community:** [GitHub Discussions](https://github.com/orgs/muxi-ai/discussions)


## License

By contributing, you agree to license your contributions under the same license as the project. Please review our [Contributor License Agreement](https://github.com/muxi-ai/muxi/blob/main/CONTRIBUTOR_LICENSE_AGREEMENT.md).

---

**Quality over quantity.** We prefer well-tested, thoughtful contributions over quick fixes that might introduce issues.
