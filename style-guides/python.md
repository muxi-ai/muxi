# Python Style Guide

These are guidelines, not strict rules. Use judgment.

## General Principles

- **Keep functions focused** — extract only when reusable or testable
- **Prefer early returns** — reduce nesting, avoid `else` when possible
- **Handle errors explicitly** — use specific exceptions, not bare `except:`
- **Use type hints** — annotate function signatures and complex variables

## Variables

- **Prefer descriptive names** — clarity over brevity
- **Use `snake_case`** for variables and functions
- **Avoid single letters** except in comprehensions and short lambdas

## Typing

```python
# GOOD: Type hints
def process_user(user_id: int, options: dict[str, Any] | None = None) -> User:
    ...

# AVOID: No type information
def process_user(user_id, options=None):
    ...
```

## Error Handling

```python
# GOOD: Specific exceptions
try:
    result = fetch_data(url)
except HTTPError as e:
    logger.error(f"HTTP error: {e}")
    raise

# AVOID: Bare except
try:
    result = fetch_data(url)
except:
    pass
```

## Control Flow

```python
# GOOD: Early return
def process(data: str) -> str | None:
    if not data:
        return None
    # main logic here
    return result

# AVOID: Unnecessary else
def process(data: str) -> str | None:
    if not data:
        return None
    else:
        # main logic here
        return result
```

## Immutability

```python
# PREFER: Immutable defaults
def process(items: list[str] | None = None) -> list[str]:
    items = items or []
    ...

# AVOID: Mutable defaults
def process(items: list[str] = []) -> list[str]:
    ...
```

## Naming

- **Modules/packages**: `snake_case`
- **Classes**: `PascalCase`
- **Functions/variables**: `snake_case`
- **Constants**: `SCREAMING_SNAKE_CASE`

## Project Conventions

- Follow [PEP 8](https://peps.python.org/pep-0008/)
- Use `ruff` or `black` for formatting
- Use `mypy` or `pyright` for type checking
- Minimum Python version: 3.10+
