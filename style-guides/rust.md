# Rust Style Guide

These are guidelines, not strict rules. Use judgment.

## General Principles

- **Keep functions focused** — extract only when reusable or testable
- **Prefer early returns** — use `?` operator and guard clauses
- **Handle errors explicitly** — use `Result<T, E>`, avoid `.unwrap()` in library code
- **Leverage the type system** — use enums, newtypes, and strict typing

## Variables

- **Prefer immutability** — use `let`, add `mut` only when needed
- **Use `snake_case`** for variables and functions
- **Prefer concise names** — single words when descriptive enough

## Error Handling

```rust
// GOOD: Use Result and ? operator
fn process_file(path: &Path) -> Result<Data, Error> {
    let content = fs::read_to_string(path)?;
    let data = parse(&content)?;
    Ok(data)
}

// AVOID: unwrap in library code
fn process_file(path: &Path) -> Data {
    let content = fs::read_to_string(path).unwrap();
    parse(&content).unwrap()
}
```

## Control Flow

```rust
// GOOD: Early return with ?
fn process(input: &str) -> Result<Output, Error> {
    if input.is_empty() {
        return Err(Error::EmptyInput);
    }
    // main logic here
    Ok(result)
}

// GOOD: Pattern matching
match result {
    Ok(value) => handle_success(value),
    Err(e) => handle_error(e),
}
```

## Immutability

```rust
// PREFER: Immutable by default
let config = Config::new();
let items: Vec<String> = vec!["a".into(), "b".into()];

// Use mut only when necessary
let mut buffer = Vec::new();
buffer.push(item);
```

## Naming

- **Crates/modules**: `snake_case`
- **Types/Traits**: `PascalCase`
- **Functions/variables**: `snake_case`
- **Constants**: `SCREAMING_SNAKE_CASE`
- **Lifetimes**: short, lowercase (`'a`, `'de`)

## Project Conventions

- Use `rustfmt` for formatting
- Use `clippy` for linting
- Follow [Rust API Guidelines](https://rust-lang.github.io/api-guidelines/)
- Document public APIs with `///` doc comments
