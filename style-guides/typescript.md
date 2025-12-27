# TypeScript Style Guide

These are guidelines, not strict rules. Use judgment.

## General Principles

- **Keep functions focused** — extract only when reusable or testable
- **Prefer early returns** — reduce nesting, avoid `else` when possible
- **Handle errors explicitly** — use `.catch()`, Result types, or try/catch sparingly
- **Use strict typing** — avoid `any`, prefer `unknown` when type is uncertain

## Variables

- **Prefer `const`** — use `let` only when reassignment is necessary
- **Prefer concise names** — single words when descriptive enough
- **Avoid unnecessary destructuring** — keep access explicit when clearer

## Typing

```typescript
// GOOD: Explicit types
function processUser(userId: string, options?: ProcessOptions): User {
  ...
}

// AVOID: any
function processUser(userId: any, options: any): any {
  ...
}
```

## Error Handling

```typescript
// GOOD: .catch() or Result pattern
const result = await fetchData(url).catch((e) => {
  logger.error("Fetch failed", e);
  return null;
});

// ACCEPTABLE: try/catch when needed
try {
  const result = await fetchData(url);
} catch (e) {
  if (e instanceof HTTPError) {
    logger.error("HTTP error", e);
  }
  throw e;
}
```

## Control Flow

```typescript
// GOOD: Early return
function process(data: string): string | null {
  if (!data) {
    return null;
  }
  // main logic here
  return result;
}

// AVOID: Unnecessary else
function process(data: string): string | null {
  if (!data) {
    return null;
  } else {
    // main logic here
    return result;
  }
}
```

## Immutability

```typescript
// PREFER: const and readonly
const config: Readonly<Config> = { ... };
const items: readonly string[] = ["a", "b"];

// AVOID: let when not needed
let config = { ... };  // never reassigned
```

## Naming

- **Files**: `kebab-case.ts` or `camelCase.ts` (match project convention)
- **Classes/Types/Interfaces**: `PascalCase`
- **Functions/variables**: `camelCase`
- **Constants**: `SCREAMING_SNAKE_CASE` or `camelCase`

## Project Conventions

- Use `prettier` for formatting
- Use `eslint` with strict TypeScript rules
- Enable `strict: true` in `tsconfig.json`
