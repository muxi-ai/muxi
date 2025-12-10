# Development

Set up your local environment to contribute to MUXI.

---

## Prerequisites

### Core Components

| Component | Requirements |
|-----------|--------------|
| **Server** | [Go 1.21+](https://go.dev/doc/install) |
| **Runtime** | [Python 3.11+](https://www.python.org/downloads/), [uv](https://docs.astral.sh/uv/) |
| **CLI** | [Go 1.21+](https://go.dev/doc/install) |

### SDKs

Each SDK has its own language-specific requirements. See the README in the respective SDK repository:

| SDK | Repository |
|-----|------------|
| C++ | [muxi-ai/muxi-cpp](https://github.com/muxi-ai/muxi-cpp) |
| C# | [muxi-ai/muxi-csharp](https://github.com/muxi-ai/muxi-csharp) |
| Dart | [muxi-ai/muxi-dart](https://github.com/muxi-ai/muxi-dart) |
| Go | [muxi-ai/muxi-go](https://github.com/muxi-ai/muxi-go) |
| Java | [muxi-ai/muxi-java](https://github.com/muxi-ai/muxi-java) |
| Kotlin | [muxi-ai/muxi-kotlin](https://github.com/muxi-ai/muxi-kotlin) |
| PHP | [muxi-ai/muxi-php](https://github.com/muxi-ai/muxi-php) |
| Python | [muxi-ai/muxi-python](https://github.com/muxi-ai/muxi-python) |
| Ruby | [muxi-ai/muxi-ruby](https://github.com/muxi-ai/muxi-ruby) |
| Rust | [muxi-ai/muxi-rust](https://github.com/muxi-ai/muxi-rust) |
| Swift | [muxi-ai/muxi-swift](https://github.com/muxi-ai/muxi-swift) |
| TypeScript | [muxi-ai/muxi-typescript](https://github.com/muxi-ai/muxi-typescript) |

---

## Quick Start

### Server

```bash
git clone https://github.com/muxi-ai/server.git
cd server
go build ./... && go test ./...
go run ./cmd/server
```

### Runtime

```bash
git clone https://github.com/muxi-ai/runtime.git
cd runtime
uv sync && uv run pytest
uv run python -m muxi
```

### CLI

```bash
git clone https://github.com/muxi-ai/cli.git
cd cli/src
go build ./... && go test ./...
go run . --help
```

### SDKs

Clone the SDK for your language and follow its README for setup and testing instructions.

---

## Code Style

All code must pass linting and formatting checks before merge.

### Go (Server, CLI)

```bash
go fmt ./...      # Format
go vet ./...      # Lint
go build ./...    # Build
go test ./...     # Test
```

**Guidelines:**
- Place all imports at the top of files
- Use proper goroutine and channel patterns for concurrency
- Follow [Effective Go](https://go.dev/doc/effective_go)

### Python (Runtime)

```bash
uv run ruff format .    # Format
uv run ruff check .     # Lint
uv run mypy .           # Type check
uv run pytest           # Test
```

**Guidelines:**
- Place all imports at the top of files
- Use proper async/await patterns
- Type hints required for public APIs

### SDKs

Each SDK follows its language's conventions. Refer to the SDK's README for specific linting, formatting, and testing commands.

### General

- Write self-documenting code with meaningful names
- Avoid unnecessary comments; let the code speak
- Follow [SOLID](https://en.wikipedia.org/wiki/SOLID) principles
- Only modify code related to your specific issue
- Match existing patterns in the codebase

---

## Git Workflow

All repositories use a **three-branch workflow**:

```
develop (default) → rc (release candidate) → main (production)
```

**All PRs must target `develop`.**

See [GIT-WORKFLOW.md](./GIT-WORKFLOW.md) for details.

---

## Running the Full Stack

To run MUXI locally:

```bash
# Terminal 1: Start server
cd server && go run ./cmd/server

# Terminal 2: Use CLI
cd cli/src && go run . --help
```

The server runs on port **7890** by default.

---

## Getting Help

- **Questions:** [GitHub Discussions](https://github.com/orgs/muxi-ai/discussions)
- **Bugs:** [GitHub Issues](https://github.com/muxi-ai/muxi/issues)
