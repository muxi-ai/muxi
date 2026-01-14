# C# Style Guide

These are guidelines, not strict rules. Use judgment.

## General Principles

- **Keep methods focused**  –  extract only when reusable or testable
- **Prefer early returns**  –  reduce nesting, avoid `else` when possible
- **Handle errors explicitly**  –  use specific exceptions or Result patterns
- **Use strict typing**  –  avoid `dynamic` and `object` when possible

## Variables

- **Prefer `readonly`** for fields, use mutable only when needed
- **Use `var`** when type is obvious from context
- **Use `camelCase`** for local variables, `PascalCase` for properties

## Null Handling

```csharp
// GOOD: Nullable reference types (C# 8+)
public User? FindUser(string id) { ... }

// GOOD: Null-conditional and coalescing
var length = input?.Length ?? 0;

// GOOD: Early return with null check
public string Process(string? input)
{
    if (string.IsNullOrEmpty(input))
    {
        return "";
    }
    // main logic here
    return result;
}
```

## Error Handling

```csharp
// GOOD: Specific exceptions
try
{
    var result = await FetchDataAsync(url);
}
catch (HttpRequestException e)
{
    _logger.LogError(e, "HTTP error");
    throw new DataFetchException("Failed to fetch data", e);
}

// CONSIDER: Result pattern for expected failures
public Result<Data, Error> FetchData(string url) { ... }
```

## Control Flow

```csharp
// GOOD: Early return
public string? Process(string data)
{
    if (string.IsNullOrEmpty(data))
    {
        return null;
    }
    // main logic here
    return result;
}

// GOOD: Pattern matching (C# 8+)
return result switch
{
    Success s => HandleSuccess(s.Data),
    Error e => HandleError(e.Message),
    _ => throw new InvalidOperationException()
};
```

## Immutability

```csharp
// PREFER: readonly and immutable types
private readonly Config _config;
public IReadOnlyList<string> Items { get; }

// PREFER: Records for data (C# 9+)
public record User(string Id, string Name);
```

## Naming

- **Namespaces/Classes/Interfaces**: `PascalCase`
- **Methods/Properties**: `PascalCase`
- **Local variables/parameters**: `camelCase`
- **Private fields**: `_camelCase`
- **Constants**: `PascalCase`

## Project Conventions

- Follow [Microsoft C# Coding Conventions](https://docs.microsoft.com/en-us/dotnet/csharp/fundamentals/coding-style/coding-conventions)
- Enable nullable reference types: `<Nullable>enable</Nullable>`
- Use .NET analyzers for linting
- Minimum .NET version: 6.0+
