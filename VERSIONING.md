# Versioning Guide

**Using ScalVer for Calendar-Aware, SemVer-Compatible Versioning**

---

## Overview

MUXI uses [ScalVer (Scalable Calendar Versioning)](https://github.com/veiloq/scalver) - a calendar-aware versioning scheme that provides the time-based clarity of CalVer while maintaining full SemVer compatibility and tooling support.

**Format**: `MAJOR.YYYYMMDD.PATCH`

Where:

- **MAJOR**: Breaking changes (like SemVer)
- **YYYYMMDD**: Release date in UTC (8-digit format: 20250115 = January 15, 2025)
- **PATCH**: Backward-compatible updates to that release (like SemVer)

## Why ScalVer?

ScalVer provides **time-based clarity** (knowing when something was released) while maintaining **compatibility guarantees and tooling support** (knowing if updates break things).

### Key Benefits

- **📅 Time Clarity**: `1.20251015.0` tells you it was released on October 15, 2025
- **🔧 Tool Compatibility**: Works with all existing package managers (pip, npm, cargo, etc.)
- **📈 Consistent Format**: Fixed 8-digit date format provides maximum clarity
- **🛡️ Compatibility**: Full SemVer 2.0 compatibility - no tooling changes needed

## Version Format

MUXI uses a **fixed YYYYMMDD format** for maximum clarity:

```bash
# Alpha/experimental releases
0.20250115.0 # Initial release on January 15, 2025
0.20250115.1 # Same-day hotfix (critical bug found)
0.20250130.0 # Second alpha release on January 30, 2025

# Stable releases
1.20250215.0 # First stable release on February 15, 2025
1.20250215.1 # Patch to February 15 release (could be applied weeks later)
1.20250315.0 # March feature release on March 15, 2025
1.20250315.1 # Patch to March 15 release
```

**Key Insight**: If there are multiple releases on the same day, the second one is always a patch/hotfix, never a new feature release. True feature releases need time for development, testing, and proper rollout.

### Core Rules

1. **Fixed Date Format**: Always use YYYYMMDD (8 digits) for precise release dating
2. **Same-Day = Patch**: Multiple releases on the same date are always patches, never new features
3. **MAJOR for Breaking**: Increment MAJOR for breaking changes or API incompatibilities
4. **SemVer Compatibility**: Every version is syntactically valid SemVer 2.0

### Release Cadence Guidelines

**Fixed YYYYMMDD Format Benefits:**

- **Crystal Clear Dating**: Every version shows the exact release date
- **No Format Confusion**: Developers always know what the 8-digit number means
- **Better Support**: "I need the version from March 15th" → `1.20250315.x`
- **Simplified Rules**: No complex format evolution to understand

## Pre-release and Metadata

ScalVer supports SemVer pre-release identifiers and build metadata:

```bash
# Pre-release versions
1.20251015.0-alpha.1  # Alpha release for October 15, 2025
1.20251015.0-beta.2   # Beta release
1.20251015.0-rc.1     # Release candidate
1.20251015.0          # Final release

# Build metadata (ignored in precedence)
1.20251015.0+linux.amd64
1.20251015.0+sha.42ab1ef
```

### Guard Against Legacy Minor Overflows

If your current SemVer minor version is high, ensure the 8-digit date doesn't conflict:

```bash
# Current version: 1.50.3
# Today's date: 20251015 (October 15, 2025)

# Since 20251015 > 50, safe to use:
1.20251015.0

# Current version: 1.30000000.3 (unlikely but possible)
# Since 20251015 < 30000000, increment MAJOR:
2.20251015.0
```

**Note**: This conflict is extremely unlikely with 8-digit dates (20251015 = ~20 million), but the principle remains for safety.

## Version Comparison Examples

ScalVer maintains proper ordering with standard SemVer comparison tools:

```bash
# These are in correct ascending order:
1.20250115.0 < 1.20250115.1 < 1.20250323.0 < 2.20250101.0 < 2.20250125.1
```

## Best Practices

### For Contributors

1. **Use Fixed Format**: Always use YYYYMMDD for consistent, clear dating
2. **Plan Release Dates**: Choose meaningful release dates (avoid holidays, weekends)
3. **Document Changes**: Always explain version meaning in release notes
4. **Test Tooling**: Verify your package managers handle the 8-digit versions correctly

### For Users

1. **Use Standard Ranges**: Caret (`^`) and tilde (`~`) ranges work normally
2. **Pin Major**: Use `^1.20251015.0` to get compatible updates within MAJOR 1
3. **Read Dates**: The 8-digit date tells you exactly when features were released
4. **Check Breaking Changes**: MAJOR bumps indicate breaking changes


## Resources

- **ScalVer Specification**: [github.com/veiloq/scalver](https://github.com/veiloq/scalver)
- **ScalVer Website**: [scalver.org](https://scalver.org)
- **SemVer Compatibility**: All ScalVer versions are valid SemVer 2.0

---

## FAQ

**Q: Do I need to change my tooling?**
A: No. ScalVer is 100% SemVer-compatible, so all existing tools work unchanged.

**Q: What if I want to go back to SemVer?**
A: You can switch back anytime by bumping MAJOR and using traditional numbering.

**Q: How do pre-releases work?**
A: Exactly like SemVer: `1.20251015.0-alpha.1`, `1.20251015.0-beta.2`, etc.

**Q: Why use fixed YYYYMMDD instead of variable precision?**
A: Fixed format provides maximum clarity - developers always know the exact release date without format confusion.

**Q: What about Y10K (year 10000)?**
A: ScalVer continues to work correctly, though the reference grammar uses 4-digit years for maximum tool compatibility.

---

*This document reflects MUXI's adoption of ScalVer with fixed YYYYMMDD format for maximum clarity, providing exact release dating while maintaining full SemVer ecosystem compatibility.*
