# AI-Assisted Development Policy

> **This is not an anti-AI stance. This is an anti-slop stance.**
>
> MUXI is built with significant AI assistance, and many of our maintainers use AI tools daily. We welcome AI-assisted contributions that meet our quality standards.

## The Problem

The rise of agentic programming has eliminated the natural effort-based backpressure that previously limited low-effort contributions. It is now trivially easy to generate large amounts of low-quality code with minimal effort.

Open source projects have always had quality issues with drive-by contributions. Unfortunately, the ease and carelessness by which these are now created has increased dramatically. This policy exists to protect maintainer time and project quality.

## The Policy

### 1. Quality is the Bar

You are responsible for what you submit, regardless of how it was created. If you use AI, you must:

- Understand the code you're submitting
- Test it locally before opening a PR
- Be able to explain your changes during review
- Fix issues identified in review (not just re-prompt)

### 2. No Drive-By AI PRs

AI-assisted contributions are only accepted for:

- **Accepted issues** - Issues that have been triaged and accepted by maintainers
- **Assigned work** - You've been assigned to the issue
- **Maintainer contributions** - Established contributors with commit history

Drive-by PRs with AI-generated content will be immediately closed.

### 3. Zero Tolerance for Slop

Contributors who submit low-effort AI-generated content will be banned from future contributions. This includes:

- Code that clearly hasn't been tested
- Verbose, LLM-style PR descriptions or comments
- Changes that don't address the actual issue
- "Vibe-coded" solutions that sort of work but miss the point

### 4. Don't Commit Agent Configuration Files

Do not commit AI assistant configuration files to MUXI repositories:

- `.claude/`, `CLAUDE.md`
- `.factory/`
- `.cursor/`
- `.aider/`
- Any similar tool-specific directories

These are personal or team preferences, not project standards. Add them to your global gitignore.

**Want to share your setup?** Post in [GitHub Discussions](https://github.com/orgs/muxi-ai/discussions) under the "AI Tooling" category. We welcome sharing what works, just not in the codebase.

## For Junior Developers

If you're learning and genuinely trying to improve: put aside the AI, do your best, and we will help you. We want to help developers grow. But we expect effort and organic thinking in return.

Using AI as a learning accelerator (explaining concepts, reviewing your code) is fine. Using AI as a replacement for understanding is not.

**Related reading:** [From Junior to 10x Dev](https://aroussi.com/post/from-junior-to-10x-dev)

## Scope and Authority

MUXI has a clear technical vision and architecture. The project maintainer ([@ranaroussi](https://github.com/ranaroussi)) has final authority on:

- What features are in scope
- Architectural decisions
- API design
- Which issues get accepted

Before investing significant effort, ensure your proposed work aligns with project direction by discussing in an issue first.

## Summary

| Allowed | Not Allowed |
|---------|-------------|
| AI-assisted code that you understand and tested | Untested AI-generated code dumps |
| AI help on accepted/assigned issues | Drive-by AI PRs on random ideas |
| Sharing AI configs in Discussions | Committing agent dotfiles to repos |
| Using AI to learn and accelerate | Using AI as a replacement for thinking |

---

*This policy is inspired by [Ghostty's approach](https://github.com/ghostty-org/ghostty) to AI contributions.*
