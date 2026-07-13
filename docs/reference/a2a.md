---
title: A2A Services
description: Configure agent-to-agent communication with external services
---

# A2A Services

## Connect to external agent services

A2A (Agent-to-Agent) services let a formation communicate with external agent
systems. Put reusable service definitions in `a2a/*.afs`; configure inbound
server auth and extended outbound auth in the top-level `a2a:` block.

## Service files

Create `a2a/analytics.afs`:

```yaml
schema: "1.0.0"
id: analytics-engine
name: Analytics Engine
description: External analytics service
url: "https://analytics.company.com"
active: true
timeout_seconds: 30
retry_attempts: 3

auth:
  type: bearer
  token: "${{ secrets.ANALYTICS_TOKEN }}"
```

Required fields are `schema`, `id`, `name`, `description`, and an HTTP(S)
`url`. Optional top-level fields include `active`, `timeout_seconds`,
`retry_attempts`, `author`, `version`, `documentation`, and `support_contact`.
Service-file auth supports `none`, `api_key`, `bearer`, `basic`, and `custom`.

## Top-level configuration

```yaml
# formation.yaml
a2a:
  enabled: true
  inbound:
    enabled: true
    host: 0.0.0.0
    port: 8181
    auth:
      type: hmac
      secret: "${{ secrets.A2A_HMAC_SECRET }}"
      timestamp_tolerance: 300
  outbound:
    enabled: true
    default_timeout_seconds: 30
    default_retry_attempts: 3
    services:
      - id: analytics
        url: "https://analytics.example.com"
        auth:
          type: oauth2
          client_id: "analytics-client"
          client_secret: "${{ secrets.OAUTH_SECRET }}"
          token_url: "https://auth.example.com/oauth/token"
          scope: "read write"
```

`id` is the canonical identifier for credential registration. The runtime
matches outbound requests through the service's `url`; when several URL paths
match, the most specific path wins. The legacy `service_id` field remains
accepted for compatibility but is deprecated.

## Authentication

### API key

```yaml
auth:
  type: api_key
  header: X-API-Key
  key: "${{ secrets.SERVICE_API_KEY }}"
```

### Bearer token

```yaml
auth:
  type: bearer
  token: "${{ secrets.SERVICE_TOKEN }}"
```

### Basic auth

```yaml
auth:
  type: basic
  username: "${{ secrets.SERVICE_USERNAME }}"
  password: "${{ secrets.SERVICE_PASSWORD }}"
```

### Custom headers

```yaml
auth:
  type: custom
  headers:
    X-Tenant: acme
    X-Service-Key: "${{ secrets.SERVICE_KEY }}"
```

### OAuth2 client credentials (outbound only)

```yaml
a2a:
  outbound:
    services:
      - id: analytics
        url: "https://analytics.example.com"
        auth:
          type: oauth2
          client_id: "your-client-id"
          client_secret: "${{ secrets.OAUTH_SECRET }}"
          token_url: "https://auth.example.com/oauth/token"
          scope: "read write"
```

Tokens are cached until expiry. `scope` is an optional space-delimited string.

### HMAC (inbound and outbound)

HMAC uses SHA-256 over the timestamp. Inbound verification uses constant-time
comparison, rejects stale timestamps, and prevents replay. Defaults are
`X-Signature`, `X-Timestamp`, and a 300-second inbound tolerance.

```yaml
a2a:
  inbound:
    enabled: true
    auth:
      type: hmac
      secret: "${{ secrets.HMAC_SECRET }}"
      timestamp_tolerance: 300
  outbound:
    services:
      - id: analytics
        url: "https://analytics.example.com"
        auth:
          type: hmac
          secret: "${{ secrets.HMAC_SECRET }}"
          signature_header: X-Signature
          timestamp_header: X-Timestamp
```

### OpenID Connect (inbound only)

OpenID verifies bearer JWTs against the issuer's JWKS and identifies callers as
`openid:<sub>`. When `jwks_url` is omitted, the runtime uses
`<issuer>/.well-known/jwks.json`.

```yaml
a2a:
  inbound:
    enabled: true
    auth:
      type: openid
      issuer: "https://auth.example.com"
      audience: "your-client-id"
      jwks_url: "https://auth.example.com/.well-known/jwks.json"
      allowed_algorithms: ["RS256"]
      clock_skew_seconds: 30
```

`oauth2` is outbound-only and `openid` is inbound-only. `api_key`, `bearer`,
`basic`, `custom`, and `hmac` are valid in both directions.

## Learn More

- [Agents Reference](../concepts/agents-and-orchestration.md) - Configure agent delegation
- [Tools Reference](../concepts/tools-and-mcp.md) - MCP tool integration
- [Secrets Reference](../concepts/secrets-and-security.md) - Managing service credentials
