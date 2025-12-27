# Kotlin Style Guide

These are guidelines, not strict rules. Use judgment.

## General Principles

- **Keep functions focused** — extract only when reusable or testable
- **Prefer early returns** — reduce nesting, avoid `else` when possible
- **Handle errors explicitly** — use `Result`, sealed classes, or exceptions appropriately
- **Use strict typing** — avoid `Any`, prefer specific types

## Variables

- **Prefer `val`** — use `var` only when mutation is needed
- **Prefer concise names** — single words when descriptive enough
- **Use `camelCase`** for variables and functions

## Null Safety

```kotlin
// GOOD: Safe calls and elvis operator
val length = input?.length ?: 0

// GOOD: Early return with null check
fun process(input: String?): String {
    input ?: return ""
    // main logic here
    return result
}

// AVOID: Force unwrapping
val length = input!!.length
```

## Error Handling

```kotlin
// GOOD: Result type
fun fetchData(url: String): Result<Data> {
    return runCatching {
        // fetch logic
    }
}

// GOOD: Sealed class for errors
sealed class FetchResult {
    data class Success(val data: Data) : FetchResult()
    data class Error(val message: String) : FetchResult()
}
```

## Control Flow

```kotlin
// GOOD: Early return
fun process(data: String): String? {
    if (data.isEmpty()) {
        return null
    }
    // main logic here
    return result
}

// GOOD: when expression
when (result) {
    is Success -> handleSuccess(result.data)
    is Error -> handleError(result.message)
}
```

## Immutability

```kotlin
// PREFER: Immutable collections
val items: List<String> = listOf("a", "b")

// Use mutable only when necessary
val buffer = mutableListOf<String>()
buffer.add(item)
```

## Naming

- **Packages**: `lowercase`, no underscores
- **Classes/Interfaces**: `PascalCase`
- **Functions/variables**: `camelCase`
- **Constants**: `SCREAMING_SNAKE_CASE` or `camelCase`

## Project Conventions

- Follow [Kotlin Coding Conventions](https://kotlinlang.org/docs/coding-conventions.html)
- Use ktlint or detekt for linting
- Use kotlinter for formatting
