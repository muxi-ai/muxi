# C++ Style Guide

These are guidelines, not strict rules. Use judgment.

## General Principles

- **Keep functions focused**  –  extract only when reusable or testable
- **Prefer early returns**  –  reduce nesting, avoid `else` when possible
- **Handle errors explicitly**  –  use return codes, `std::expected`, or exceptions consistently
- **Use modern C++**  –  prefer C++17/20 features over legacy patterns

## Variables

- **Prefer `const`**  –  use mutable variables only when needed
- **Prefer `auto`** when type is obvious or verbose
- **Use `snake_case`** for variables and functions

## Memory Safety

```cpp
// GOOD: Smart pointers
auto user = std::make_unique<User>(id, name);
auto shared = std::make_shared<Config>();

// GOOD: References over pointers when non-null
void process(const Data& data) { ... }

// AVOID: Raw owning pointers
User* user = new User(id, name);  // Who deletes this?
```

## Error Handling

```cpp
// GOOD: std::expected (C++23) or similar
std::expected<Data, Error> fetchData(const std::string& url) {
    if (url.empty()) {
        return std::unexpected(Error::InvalidUrl);
    }
    // fetch logic
    return data;
}

// GOOD: std::optional for "no value" cases
std::optional<User> findUser(const std::string& id) {
    if (/* not found */) {
        return std::nullopt;
    }
    return user;
}
```

## Control Flow

```cpp
// GOOD: Early return
std::string process(const std::string& data) {
    if (data.empty()) {
        return "";
    }
    // main logic here
    return result;
}

// AVOID: Deep nesting
std::string process(const std::string& data) {
    if (!data.empty()) {
        // main logic here
        return result;
    } else {
        return "";
    }
}
```

## Immutability

```cpp
// PREFER: const by default
const auto config = Config{};
const std::vector<std::string> items = {"a", "b"};

// PREFER: const member functions
class Processor {
public:
    std::string getName() const { return name_; }
};
```

## Naming

- **Namespaces**: `snake_case` or `lowercase`
- **Classes/Structs**: `PascalCase`
- **Functions/variables**: `snake_case`
- **Constants**: `kPascalCase` or `SCREAMING_SNAKE_CASE`
- **Private members**: `snake_case_` (trailing underscore)

## Project Conventions

- Use clang-format for formatting
- Use clang-tidy for linting
- Follow [C++ Core Guidelines](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines)
- Minimum standard: C++17
