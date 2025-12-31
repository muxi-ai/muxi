# Telemetry

MUXI includes optional, anonymous telemetry that helps us understand how the software is used and where to focus improvements. **No personally identifiable information is ever collected.**

## Your Privacy Matters

We designed our telemetry with privacy as the top priority:

| We Collect | We Never Collect |
|------------|------------------|
| ✓ MUXI version | ✗ IP addresses or location |
| ✓ OS type (macOS, Linux, Windows) | ✗ Your name, email, or identity |
| ✓ Feature usage patterns | ✗ Your prompts or AI responses |
| ✓ Performance metrics | ✗ Your code or file contents |
| ✓ LLM provider choice (e.g., "OpenAI") | ✗ API keys or credentials |
| ✓ Aggregate stats (agent count, etc.) | ✗ URLs or domain names |

## How It Works

- **Default: Enabled** — Telemetry is on unless you disable it
- **Fully anonymous** — No way to trace data back to you
- **Secure transmission** — All data sent over HTTPS
- **Open source** — Review exactly what we collect: [github.com/muxi-ai/telemetry](https://github.com/muxi-ai/telemetry)

## Disable Telemetry

You can opt out at any time. Choose whichever method you prefer:

**Option 1: CLI command**

```bash
muxi config telemetry off
```

**Option 2: Environment variable**

```bash
export MUXI_TELEMETRY=0
```

**Option 3: Configuration file**

Add to `~/.muxi/config.yaml`:

```yaml
telemetry: false
```

## Re-Enable Telemetry

```bash
muxi config telemetry on
```

Or set `telemetry: true` in your config file.

---

Questions? Open an issue on [GitHub](https://github.com/muxi-ai/muxi) or email privacy@muxi.org.
