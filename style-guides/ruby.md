# Ruby Style Guide

These are guidelines, not strict rules. Use judgment.

## General Principles

- **Keep methods focused** — extract only when reusable or testable
- **Prefer early returns** — use guard clauses liberally
- **Handle errors explicitly** — rescue specific exceptions, not `StandardError`
- **Use duck typing wisely** — but document expected interfaces

## Variables

- **Prefer descriptive names** — clarity over brevity
- **Use `snake_case`** for variables and methods
- **Freeze constants** — `CONSTANT = 'value'.freeze`

## Control Flow

```ruby
# GOOD: Guard clause (early return)
def process(data)
  return '' if data.nil? || data.empty?
  
  # main logic here
  result
end

# AVOID: Unnecessary else
def process(data)
  if data.nil? || data.empty?
    ''
  else
    # main logic here
    result
  end
end
```

## Error Handling

```ruby
# GOOD: Specific exceptions
begin
  result = fetch_data(url)
rescue Net::HTTPError => e
  logger.error("HTTP error: #{e.message}")
  raise DataFetchError, "Failed to fetch data"
end

# AVOID: Rescuing all exceptions
begin
  result = fetch_data(url)
rescue => e
  # Too broad - catches even SyntaxError!
end

# AVOID: Rescuing Exception (catches system signals)
rescue Exception => e
```

## Immutability

```ruby
# PREFER: Frozen strings (Ruby 3.0+ default)
# frozen_string_literal: true

# PREFER: Frozen constants
DEFAULTS = {
  timeout: 30,
  retries: 3
}.freeze

# PREFER: .freeze for arrays and hashes
ALLOWED_TYPES = %w[json xml].freeze
```

## Naming

- **Modules/Classes**: `PascalCase`
- **Methods/variables**: `snake_case`
- **Constants**: `SCREAMING_SNAKE_CASE`
- **Predicates**: end with `?` (`empty?`, `valid?`)
- **Dangerous methods**: end with `!` (`save!`, `destroy!`)

## Style Preferences

```ruby
# PREFER: Implicit returns
def name
  @name
end

# PREFER: Symbols over strings for keys
{ timeout: 30 }  # not { 'timeout' => 30 }

# PREFER: &:method shorthand
items.map(&:to_s)  # not items.map { |i| i.to_s }
```

## Project Conventions

- Follow [Ruby Style Guide](https://rubystyle.guide/)
- Use RuboCop for linting and formatting
- Add `# frozen_string_literal: true` to all files
- Minimum Ruby version: 3.0+
