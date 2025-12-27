# Java Style Guide

These are guidelines, not strict rules. Use judgment.

## General Principles

- **Keep methods focused** — extract only when reusable or testable
- **Prefer early returns** — reduce nesting, avoid `else` when possible
- **Handle errors explicitly** — use specific exceptions, avoid catching `Exception`
- **Use strict typing** — avoid raw types and `Object` when possible

## Variables

- **Prefer `final`** — use mutable variables only when needed
- **Use descriptive names** — clarity over brevity
- **Use `camelCase`** for variables and methods

## Null Handling

```java
// GOOD: Optional for nullable returns
public Optional<User> findUser(String id) {
    ...
}

// GOOD: Null checks with early return
public String process(String input) {
    if (input == null || input.isEmpty()) {
        return "";
    }
    // main logic here
    return result;
}

// AVOID: Returning null without Optional
public User findUser(String id) {
    return null;  // Caller might forget to check
}
```

## Error Handling

```java
// GOOD: Specific exceptions
try {
    var result = fetchData(url);
} catch (IOException e) {
    logger.error("IO error: {}", e.getMessage());
    throw new DataFetchException("Failed to fetch data", e);
}

// AVOID: Catching generic Exception
try {
    var result = fetchData(url);
} catch (Exception e) {
    // Too broad
}
```

## Control Flow

```java
// GOOD: Early return
public String process(String data) {
    if (data == null || data.isEmpty()) {
        return null;
    }
    // main logic here
    return result;
}

// AVOID: Unnecessary else
public String process(String data) {
    if (data == null || data.isEmpty()) {
        return null;
    } else {
        // main logic here
        return result;
    }
}
```

## Immutability

```java
// PREFER: final variables
final var config = new Config();
final List<String> items = List.of("a", "b");

// PREFER: Immutable collections (Java 9+)
var items = List.of("a", "b");
var map = Map.of("key", "value");
```

## Naming

- **Packages**: `lowercase`, dot-separated
- **Classes/Interfaces**: `PascalCase`
- **Methods/variables**: `camelCase`
- **Constants**: `SCREAMING_SNAKE_CASE`

## Project Conventions

- Follow [Google Java Style Guide](https://google.github.io/styleguide/javaguide.html)
- Use `google-java-format` or IDE formatter
- Use Checkstyle or SpotBugs for linting
- Minimum Java version: 17+
