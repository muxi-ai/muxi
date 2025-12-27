# Go Style Guide

These are guidelines, not strict rules. Use judgment.

## General Principles

- **Keep functions focused** — extract only when reusable or testable
- **Prefer early returns** — reduce nesting, avoid `else` when possible
- **Handle errors explicitly** — check errors immediately, don't ignore them
- **Use strict typing** — avoid `interface{}` and `any` unless necessary

## Variables

- **Prefer short names** in small scopes (`i`, `n`, `err`)
- **Use descriptive names** for package-level and longer-lived variables
- **Avoid shadowing** — don't reuse variable names in nested scopes

## Error Handling

```go
// GOOD: Handle errors immediately
result, err := doSomething()
if err != nil {
    return fmt.Errorf("failed to do something: %w", err)
}

// AVOID: Ignoring errors
result, _ := doSomething()
```

## Control Flow

```go
// GOOD: Early return
func process(input string) error {
    if input == "" {
        return errors.New("empty input")
    }
    // main logic here
    return nil
}

// AVOID: Unnecessary else
func process(input string) error {
    if input == "" {
        return errors.New("empty input")
    } else {
        // main logic here
        return nil
    }
}
```

## Naming

- **Packages**: short, lowercase, no underscores (`http`, `json`, `user`)
- **Interfaces**: verb or `-er` suffix (`Reader`, `Stringer`, `Handler`)
- **Exported names**: `PascalCase`
- **Unexported names**: `camelCase`

## Project Conventions

- Use `gofmt` / `goimports` for formatting
- Follow [Effective Go](https://go.dev/doc/effective_go)
- Follow [Go Code Review Comments](https://github.com/golang/go/wiki/CodeReviewComments)
