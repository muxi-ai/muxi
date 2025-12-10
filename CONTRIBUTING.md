# Contributing to MUXI

Thank you for your interest in contributing to MUXI! This document provides guidelines and information for contributing to our open-source project.

> [!NOTE]
> These guidelines apply to **[all MUXI repositories](REPOSITORIES.md)** including the runtime, CLI, SDKs, and documentation.

## 🚨 Important: Issue Assignment Required

**Before opening a Pull Request**, unless it's a **minor fix** (see definition below), **an issue must exist and you must be assigned to it**. This ensures that:

- The proposed change aligns with our project goals
- Only one person is working on the same issue
- We can provide guidance and feedback before implementation begins
- We avoid frustrations from rejected contributions

### How to get started:

1. **Browse existing issues** or **create a new issue** describing the bug fix, feature, or improvement
2. **Comment on the issue** asking to be assigned to it
3. **Wait for a maintainer** to assign you before starting work
4. **Only then** begin implementation and open a PR

### What qualifies as a "minor fix"?

Minor fixes are small, self-evident changes that don't require discussion or planning:

✅ **Allowed without an issue**:

- Fixing typos in documentation, comments, or error messages
- Correcting broken links in README or documentation
- Fixing obvious formatting issues (indentation, whitespace)
- Updating outdated version numbers in documentation
- Small grammar or spelling corrections

❌ **Requires an issue**:

- Any code logic changes, no matter how small
- Adding new dependencies or packages
- Changing API behavior or responses
- Modifying database schemas or migrations
- UI/UX changes or new components
- Performance optimizations
- Security-related changes
- Configuration changes

**When in doubt, create an issue first.** It's better to over-communicate than to have your PR rejected.

## 🤖 AI/LLM Usage Policy

We, of course, welcome the use of AI tools and Large Language Models to assist with development. However, **all code must be tested and executed in your local environment** before submission.

### Requirements:

- ✅ Test your changes locally using our development environment
- ✅ Run existing tests to ensure nothing breaks
- ✅ Add new tests for new functionality
- ✅ Verify the application runs correctly with your changes
- ❌ **Do not submit "vibe-coded" contributions** that haven't been executed

> [!WARNING]
> Pull requests that show evidence of being AI-generated without proper local testing will be immediately closed.

## 🏗️ Development Setup

Before contributing, set up your local development environment following the instructions in [DEVELOPMENT.md](./DEVELOPMENT.md) and in each repository's README.

## 🎨 Code Style and Standards

### General Guidelines

- Write self-documenting code with meaningful variable and function names
- Avoid unnecessary comments; let the code speak for itself
- Follow [SOLID](https://grokipedia.com/page/SOLID) principles
- Only modify code related to your specific issue
- Maintain consistency with existing codebase patterns

### Python (Runtime)

- **Linting**: Run `uv run task lint && uv run task lint_types`
- **Testing**: Run `uv run task test`
- **Structure**: Follow the modular structure in `server/MUXI/`
- **Imports**: Place all imports at the top of files
- **Async**: Use proper async/await patterns

### Go (CLI)

- **Formatting**: Run `go fmt ./...`
- **Linting**: Run `go vet ./...`
- **Building**: Run `go build ./...`
- **Testing**: Run `go test ./...`
- **Imports**: Place all imports at the top of files
- **Concurrency**: Use proper goroutine and channel patterns

### Other Languages (SDKs)

- Follow each SDK's README for setup and testing instructions
- Use language-appropriate linting and formatting tools
- Maintain consistency with existing codebase patterns


## 🔍 Code Review Process

1. **Automated checks** must pass (linting, tests, type checking)
2. **Manual review** by maintainers focuses on:
    - Code quality and adherence to standards
    - Security considerations
    - Performance implications
    - Architectural consistency
3. **Address feedback** promptly and thoroughly
4. **Squash commits** before merge if requested

## 🚫 What We Don't Accept

- Pull requests without associated issues (except minor fixes as defined above)
- Code that hasn't been tested locally
- Changes that break existing functionality
- Code that doesn't follow our style guidelines
- Contributions that significantly deviate from project goals
- Large refactoring without prior discussion

## 🎯 Types of Contributions

We welcome various types of contributions:

- **🐛 Bug fixes** - Fix existing issues
- **✨ Features** - Add new functionality (discuss first)
- **📝 Documentation** - Improve guides, API docs, or code comments
- **🧪 Tests** - Improve test coverage
- **🔧 Developer Experience** - Improve tooling, setup, or workflows

## 🌿 Branch Naming

Use descriptive branch names with prefixes:

- `feature/` - New features (e.g., `feature/add-memory-command`)
- `fix/` - Bug fixes (e.g., `fix/session-timeout`)
- `docs/` - Documentation changes (e.g., `docs/update-api-guide`)
- `refactor/` - Code refactoring (e.g., `refactor/simplify-auth`)

## 💬 Commit Messages

Write clear, concise commit messages:

- Use present tense ("Add feature" not "Added feature")
- Keep the first line under 72 characters
- Reference issue numbers when applicable (e.g., `Fix timeout bug (#123)`)

## 🔒 Security

If you discover a security vulnerability, **do not open a public issue**. Instead, please report it privately by emailing [security@muxi.org](mailto:security@muxi.org). We take security seriously and will respond promptly.

## ❓ Getting Help

- **Questions about contributing?** Open a [Discussion](https://github.com/orgs/muxi-ai/discussions)
- **Found a bug?** Check existing issues first, then create a new one if needed
- **Feature ideas?** Start a discussion before creating an issue

## 📄 License

By contributing to MUXI, you agree that your contributions will be licensed under the same license as the project. Please review our [Contributor License Agreement](./CONTRIBUTOR_LICENSE_AGREEMENT.md) before submitting.

---

**Remember**: Quality over quantity. We prefer well-tested, thoughtful contributions over quick fixes that might introduce issues.
