# MUXI CLI Brand Colors

## Primary Brand Color

| Name | Hex | RGB | ANSI Escape |
|------|-----|-----|-------------|
| **Gold** | `#d8893e` | `216, 137, 62` | `\033[38;2;216;137;62m` |

Use for: selected items, arrows, highlights, interactive elements.

## Banner Gradient

The ASCII banner uses a 6-color gradient from light gold to dark bronze:

```
███╗   ███╗██╗   ██╗██╗  ██╗██╗      ← Line 1: #d9aa54
████╗ ████║██║   ██║╚██╗██╔╝██║      ← Line 2: #da9e4b
██╔████╔██║██║   ██║ ╚███╔╝ ██║      ← Line 3: #db9647
██║╚██╔╝██║██║   ██║ ██╔██╗ ██║      ← Line 4: #dc8f42
██║ ╚═╝ ██║╚██████╔╝██╔╝ ██╗██║      ← Line 5: #d8893e
╚═╝     ╚═╝ ╚═════╝ ╚═╝  ╚═╝╚═╝      ← Line 6: #bf7840
```

| Line | Hex | RGB | ANSI Escape |
|------|-----|-----|-------------|
| 1 | `#d9aa54` | `217, 170, 84` | `\033[38;2;217;170;84m` |
| 2 | `#da9e4b` | `218, 158, 75` | `\033[38;2;218;158;75m` |
| 3 | `#db9647` | `219, 150, 71` | `\033[38;2;219;150;71m` |
| 4 | `#dc8f42` | `220, 143, 66` | `\033[38;2;220;143;66m` |
| 5 | `#d8893e` | `216, 137, 62` | `\033[38;2;216;137;62m` |
| 6 | `#bf7840` | `191, 120, 64` | `\033[38;2;191;120;64m` |

## Usage Examples

### Bash
```bash
GOLD='\033[38;2;216;137;62m'
NC='\033[0m'
echo -e "${GOLD}→ Selected item${NC}"
```

### Go
```go
const (
    Gold = "\033[38;2;216;137;62m"
    NC   = "\033[0m"
)
fmt.Printf("%s→ Selected item%s\n", Gold, NC)
```

### Python
```python
GOLD = '\033[38;2;216;137;62m'
NC = '\033[0m'
print(f"{GOLD}→ Selected item{NC}")
```

## Standard UI Colors

| Purpose | Color | Hex | ANSI |
|---------|-------|-----|------|
| Success/Check | Green | - | `\033[0;32m` |
| Error/Cross | Red | - | `\033[0;31m` |
| Highlight/Selected | Gold | `#d8893e` | `\033[38;2;216;137;62m` |
| Bold text | - | - | `\033[1m` |
| Reset | - | - | `\033[0m` |

## Notes

- These colors are optimized for both light and dark terminal themes
- The gradient was tested on macOS Terminal, iTerm2, and VS Code terminal
- Always reset with `\033[0m` (NC) after colored text
