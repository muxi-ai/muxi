# Troubleshooting

## Step-by-step guide


Common issues and solutions.

## Installation Issues

### Command Not Found

```
muxi: command not found
```

**Solution:**
- Restart terminal
- Check PATH includes install location
- Reinstall: `curl -fsSL https://muxi.org/install | bash`

### Permission Denied

```
Permission denied: /usr/local/bin/muxi
```

**Solution:**
- Use sudo: `curl ... | sudo bash`
- Or install to user directory: `curl ... | bash` (no sudo)

## Server Issues

### Server Won't Start

```
Error: bind: address already in use
```

**Solution:**
```bash
# Find what's using port
lsof -i :7890

# Kill it or change port
muxi-server --port 7891
```

### Authentication Failed

```
Error: 401 Unauthorized
```

**Solution:**
- Check key ID and secret match config
- Verify timestamp is within 5 minutes
- Regenerate keys: `muxi-server init`

## Formation Issues

### Missing Secrets

```
Error: Missing required secret: OPENAI_API_KEY
```

**Solution:**
```bash
muxi secrets setup
# Or
muxi secrets set OPENAI_API_KEY
```

### Formation Won't Start

```
Error: Formation failed to start
```

**Solution:**
1. Check logs: `muxi logs my-formation`
2. Validate config: `muxi deploy --validate`
3. Check port availability

### Invalid Configuration

```
Error: Invalid formation.afs
```

**Solution:**
- Check YAML syntax
- Validate: `muxi deploy --validate`
- Compare with examples

## Memory Issues

### High Memory Usage

Formation using too much RAM.

**Solution:**
```yaml
memory:
  buffer:
    size: 20  # Reduce from 50
```

Or restart:
```bash
muxi formation restart my-assistant
```

### Memory Not Persisting

Conversations not saved.

**Solution:**
```yaml
memory:
  persistent:
    enabled: true
    provider: sqlite
```

Check database connection for PostgreSQL.

## Tool Issues

### MCP Tool Not Found

```
Error: MCP server not found
```

**Solution:**
```bash
# Verify server is installed
npx @anthropic/brave-search --version

# Check server name is correct
```

### Tool Permission Denied

```
Error: Access denied to /etc/passwd
```

**Solution:**
Configure allowed directories:
```yaml
mcps:
  - id: filesystem
    config:
      allowed_directories:
        - /home/user/documents
```

## Network Issues

### Connection Refused

```
Error: Connection refused to localhost:8001
```

**Solution:**
1. Check formation is running: `muxi formation list`
2. Check port is correct
3. Start formation: `muxi formation start my-assistant`

### Timeout

```
Error: Request timeout
```

**Solution:**
- Increase timeout in config
- Check LLM API status
- Simplify request

## Deployment Issues

### Deploy Failed

```
Error: Failed to deploy formation
```

**Solution:**
1. Check server is running
2. Verify profile credentials
3. Validate formation: `muxi deploy --validate`

### Bundle Too Large

```
Error: Formation bundle exceeds limit
```

**Solution:**
- Remove unnecessary files
- Add to `.muxiignore`
- Check knowledge directory size

## Getting Help

1. Check logs: `muxi logs --lines 200`
2. Enable debug: Set `logging.level: debug`
3. Search issues: github.com/muxi-ai/muxi/issues
4. Ask community: community.muxi.org
