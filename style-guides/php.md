# PHP Style Guide

These are guidelines, not strict rules. Use judgment.

## General Principles

- **Keep functions focused** — extract only when reusable or testable
- **Prefer early returns** — reduce nesting, avoid `else` when possible
- **Handle errors explicitly** — use exceptions appropriately, avoid `@` suppression
- **Use strict typing** — enable `declare(strict_types=1)` in all files

## Variables

- **Prefer descriptive names** — clarity over brevity
- **Use `camelCase`** for variables and functions
- **Avoid global variables** — use dependency injection

## Type Safety

```php
<?php

declare(strict_types=1);

// GOOD: Type declarations
function processUser(int $userId, ?array $options = null): User
{
    ...
}

// GOOD: Return types
function findUser(string $id): ?User
{
    ...
}

// AVOID: No type information
function processUser($userId, $options = null)
{
    ...
}
```

## Error Handling

```php
// GOOD: Specific exceptions
try {
    $result = $this->fetchData($url);
} catch (HttpException $e) {
    $this->logger->error('HTTP error: ' . $e->getMessage());
    throw new DataFetchException('Failed to fetch data', 0, $e);
}

// AVOID: Catching generic Exception
try {
    $result = $this->fetchData($url);
} catch (Exception $e) {
    // Too broad
}

// AVOID: Error suppression
$result = @file_get_contents($path);
```

## Control Flow

```php
// GOOD: Early return
public function process(string $data): ?string
{
    if (empty($data)) {
        return null;
    }
    // main logic here
    return $result;
}

// AVOID: Unnecessary else
public function process(string $data): ?string
{
    if (empty($data)) {
        return null;
    } else {
        // main logic here
        return $result;
    }
}
```

## Immutability

```php
// PREFER: readonly properties (PHP 8.1+)
class Config
{
    public function __construct(
        public readonly string $apiKey,
        public readonly int $timeout,
    ) {}
}

// PREFER: Constants for fixed values
private const MAX_RETRIES = 3;
```

## Naming

- **Namespaces/Classes**: `PascalCase`
- **Methods/functions**: `camelCase`
- **Variables**: `camelCase`
- **Constants**: `SCREAMING_SNAKE_CASE`

## Project Conventions

- Follow [PSR-12](https://www.php-fig.org/psr/psr-12/) coding standard
- Use PHP CS Fixer or PHP_CodeSniffer for formatting
- Use PHPStan or Psalm for static analysis
- Minimum PHP version: 8.1+
