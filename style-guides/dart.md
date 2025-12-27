# Dart Style Guide

These are guidelines, not strict rules. Use judgment.

## General Principles

- **Keep functions focused** — extract only when reusable or testable
- **Prefer early returns** — reduce nesting, avoid `else` when possible
- **Handle errors explicitly** — use try/catch or Result patterns appropriately
- **Use strict typing** — avoid `dynamic`, enable strict analysis

## Variables

- **Prefer `final`** — use `var` only when reassignment is needed
- **Prefer concise names** — single words when descriptive enough
- **Use `camelCase`** for variables and functions

## Null Safety

```dart
// GOOD: Sound null safety
String? nullableValue;
String nonNullable = 'value';

// GOOD: Null-aware operators
final length = input?.length ?? 0;

// GOOD: Early return with null check
String process(String? input) {
  if (input == null || input.isEmpty) {
    return '';
  }
  // main logic here
  return result;
}

// AVOID: Force unwrapping without check
final length = input!.length;  // Might throw
```

## Error Handling

```dart
// GOOD: Specific exception handling
try {
  final result = await fetchData(url);
} on HttpException catch (e) {
  logger.error('HTTP error: $e');
  rethrow;
}

// CONSIDER: Result pattern for expected failures
Result<Data, Error> fetchData(String url) { ... }
```

## Control Flow

```dart
// GOOD: Early return
String? process(String data) {
  if (data.isEmpty) {
    return null;
  }
  // main logic here
  return result;
}

// AVOID: Unnecessary else
String? process(String data) {
  if (data.isEmpty) {
    return null;
  } else {
    // main logic here
    return result;
  }
}
```

## Immutability

```dart
// PREFER: final and const
final config = Config();
const items = ['a', 'b'];

// PREFER: Immutable collections
final items = List.unmodifiable(['a', 'b']);
```

## Naming

- **Packages/directories**: `snake_case`
- **Classes/Types**: `PascalCase`
- **Functions/variables**: `camelCase`
- **Constants**: `camelCase` or `SCREAMING_SNAKE_CASE`
- **Private members**: `_camelCase`

## Project Conventions

- Follow [Effective Dart](https://dart.dev/effective-dart)
- Use `dart format` for formatting
- Use `dart analyze` with strict settings
- Enable all recommended lints in `analysis_options.yaml`
