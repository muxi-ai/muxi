# Swift Style Guide

These are guidelines, not strict rules. Use judgment.

## General Principles

- **Keep functions focused** — extract only when reusable or testable
- **Prefer early returns** — use `guard` statements liberally
- **Handle errors explicitly** — use `throws`, `Result`, or optionals appropriately
- **Use strict typing** — avoid `Any` and force unwrapping

## Variables

- **Prefer `let`** — use `var` only when mutation is needed
- **Prefer concise names** — single words when descriptive enough
- **Use `camelCase`** for variables and functions

## Optionals

```swift
// GOOD: guard let for early exit
func process(input: String?) -> String {
    guard let input = input, !input.isEmpty else {
        return ""
    }
    // main logic here
    return result
}

// AVOID: Force unwrapping
let value = optionalValue!
```

## Error Handling

```swift
// GOOD: throws and do-catch
func fetchData(from url: URL) throws -> Data {
    let data = try Data(contentsOf: url)
    return data
}

// GOOD: Result type
func fetchData(from url: URL) -> Result<Data, Error> {
    ...
}

// AVOID: Ignoring errors
let data = try? Data(contentsOf: url)  // Only when nil is acceptable
```

## Control Flow

```swift
// GOOD: guard for preconditions
func process(data: String) -> String? {
    guard !data.isEmpty else {
        return nil
    }
    // main logic here
    return result
}

// AVOID: Nested if-else
func process(data: String) -> String? {
    if !data.isEmpty {
        // main logic here
        return result
    } else {
        return nil
    }
}
```

## Naming

- **Types/Protocols**: `PascalCase`
- **Functions/variables**: `camelCase`
- **Constants**: `camelCase` (or `PascalCase` for type-level)

## Project Conventions

- Follow [Swift API Design Guidelines](https://swift.org/documentation/api-design-guidelines/)
- Use SwiftLint for linting
- Use SwiftFormat for formatting
