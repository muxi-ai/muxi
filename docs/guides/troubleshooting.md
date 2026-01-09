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

## Common Error Messages

### `ECONNREFUSED` - Connection Refused

**Full error:**
```
Error: connect ECONNREFUSED 127.0.0.1:7890
    at TCPConnectWrap.afterConnect [as oncomplete]
```

**Cause:** MUXI Server isn't running

**Solution:**
```bash
# Start the server
muxi-server start

# Verify it's running
curl http://localhost:7890/health
```

---

### `EADDRINUSE` - Port Already in Use

**Full error:**
```
Error: listen EADDRINUSE: address already in use :::7890
    at Server.setupListenHandle [as _listen2]
```

**Cause:** Another process is using port 7890

**Solution:**
```bash
# Find what's using the port
lsof -i :7890

# Kill it (if safe)
kill -9 <PID>

# Or use different port
muxi-server --port 7891
```

---

### `401 Unauthorized` - Auth Failed

**Full error:**
```
{
  "error": {
    "code": "unauthorized",
    "message": "Invalid API key or signature"
  }
}
```

**Cause:** Invalid credentials or timestamp mismatch

**Solution:**
```bash
# Check your credentials
cat ~/.muxi/server/config.yaml

# Regenerate if needed
muxi-server init --reset

# Check system time is correct
date
```

---

### `Missing required secret` - Secrets Not Set

**Full error:**
```
Error: Missing required secret: OPENAI_API_KEY
Required by: llm.api_keys.openai
```

**Cause:** Formation needs secrets that aren't set

**Solution:**
```bash
# Set the secret
muxi secrets set OPENAI_API_KEY sk-...

# Or run interactive setup
muxi secrets setup
```

---

### `Formation validation failed` - Invalid YAML

**Full error:**
```
Error: Formation validation failed
  - agents[0].llm.model: required field missing
  - memory.buffer.size: must be integer, got string
```

**Cause:** Invalid formation.yaml syntax

**Solution:**
1. Check YAML syntax (indentation, colons)
2. Validate: `muxi deploy --validate`
3. Compare with examples: `docs/examples/`

**Common fixes:**
```yaml
# Wrong: String instead of integer
memory:
  buffer:
    size: "50"  # ✗ String

# Right:
memory:
  buffer:
    size: 50    # ✓ Integer

# Wrong: Missing colon
agents
  - name: assistant  # ✗ Missing colon after agents

# Right:
agents:              # ✓ Has colon
  - name: assistant
```

---

### `Cannot find module` - MCP Server Not Installed

**Full error:**
```
Error: spawn npx ENOENT
Cannot find module '@modelcontextprotocol/server-brave-search'
```

**Cause:** MCP server package not installed

**Solution:**
```bash
# Install the package
npm install -g @modelcontextprotocol/server-brave-search

# Verify installation
npx @modelcontextprotocol/server-brave-search --version

# Check node/npm are installed
node --version
npm --version
```

---

### `Tool execution failed` - MCP Tool Error

**Full error:**
```
{
  "error": {
    "type": "tool_error",
    "tool": "brave_web_search",
    "message": "HTTP 403: API key invalid"
  }
}
```

**Cause:** Invalid API key for MCP tool

**Solution:**
```bash
# Check secret is set
muxi secrets get BRAVE_SEARCH_API_KEY

# Set/update the secret
muxi secrets set BRAVE_SEARCH_API_KEY BSA...

# Restart formation
muxi formation restart my-formation
```

---

### `Memory database locked` - SQLite Lock

**Full error:**
```
Error: SQLITE_BUSY: database is locked
```

**Cause:** Another process is accessing the SQLite database

**Solution:**
```bash
# Stop all formations using this database
muxi formation list
muxi formation stop my-formation

# Check for stale lock files
rm data/memory.db-wal
rm data/memory.db-shm

# Restart formation
muxi formation start my-formation
```

---

### `Rate limit exceeded` - OpenAI Rate Limit

**Full error:**
```
{
  "error": {
    "type": "rate_limit_error",
    "message": "Rate limit reached for gpt-4 in organization org-..."
  }
}
```

**Cause:** Hit OpenAI API rate limit

**Solution:**
```yaml
# Reduce parallel tasks
workflow:
  max_parallel_tasks: 2  # Was 10

# Add retry delays
workflow:
  retry_delay: 10        # Wait 10s between retries

# Use cheaper model
llm:
  model: gpt-3.5-turbo   # Less expensive
```

---

### `Webhook delivery failed` - Webhook Error

**Full error:**
```
Error: Failed to deliver webhook to https://your-app.com/callback
  Cause: getaddrinfo ENOTFOUND your-app.com
```

**Cause:** Webhook URL is unreachable

**Solution:**
1. Verify URL is accessible: `curl https://your-app.com/callback`
2. Check firewall/network settings
3. For localhost, use ngrok: `ngrok http 3000`
4. Update webhook URL in formation

---

### `Python execution failed` - Artifact Error

**Full error:**
```
Error: Python artifact execution failed
  ModuleNotFoundError: No module named 'reportlab'
```

**Cause:** Required Python package not installed

**Solution:**
```bash
# Install the package
pip install reportlab

# Or install all recommended packages
pip install reportlab matplotlib pandas

# Check Python version
python --version  # Should be 3.8+
```

---

### `Knowledge indexing failed` - Embedding Error

**Full error:**
```
Error: Failed to index knowledge base
  OpenAI API error: Incorrect API key provided
```

**Cause:** OpenAI key invalid or embedding API issue

**Solution:**
```bash
# Verify OpenAI key works
curl https://api.openai.com/v1/models \
  -H "Authorization: Bearer $OPENAI_API_KEY"

# Check embedding model is accessible
# (text-embedding-3-small is the default)

# Update API key if expired
muxi secrets set OPENAI_API_KEY sk-...
```

---

## Getting Help

Still stuck? Here's how to get help:

1. **Enable debug logging:**
   ```yaml
   logging:
     level: debug
   ```
   Then check: `muxi logs --lines 200`

2. **Search existing issues:**
   https://github.com/muxi-ai/muxi/issues

3. **Ask the community:**
   https://community.muxi.org

4. **Report a bug:**
   https://github.com/muxi-ai/muxi/issues/new

**Include in your report:**
- MUXI version: `muxi --version`
- OS and version: `uname -a`
- Formation config (remove secrets!)
- Full error message and stack trace
- Steps to reproduce
