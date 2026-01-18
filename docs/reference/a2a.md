---
title: A2A Services
description: Configure agent-to-agent communication with external services
---

# A2A Services

## Connect to external agent services

A2A (Agent-to-Agent) services let your formation communicate with external agent systems. Define connections to remote services that your agents can delegate tasks to.


## Your First A2A Service

Create `a2a/analytics.afs`:

```yaml
schema: "1.0.0"
id: analytics-engine
name: Analytics Engine
description: External analytics service for data processing

endpoint: "https://analytics.company.com"
auth:
  type: bearer
  token: "${{ secrets.ANALYTICS_TOKEN }}"
```

---

## Configuration Fields

### Required Fields

| Field | Type | Description |
|-------|------|-------------|
| `schema` | string | Schema version (`"1.0.0"`) |
| `id` | string | Unique identifier |
| `description` | string | Service capabilities |
| `endpoint` | string | Service endpoint URL |

### Connection Settings

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `name` | string | - | Human-readable name |
| `protocol` | enum | `https` | `http`, `https`, or `grpc` |
| `version` | string | - | Service API version |
| `timeout_seconds` | int | `30` | Request timeout |

### Retry Configuration

```yaml
retry_attempts: 3
retry_delay_seconds: 1
retry_backoff_multiplier: 2.0
```

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `retry_attempts` | int | `3` | Number of retries (0-10) |
| `retry_delay_seconds` | int | `1` | Initial delay between retries |
| `retry_backoff_multiplier` | float | `2.0` | Exponential backoff multiplier |

---

## Authentication

### Bearer Token

```yaml
auth:
  type: bearer
  token: "${{ secrets.SERVICE_TOKEN }}"
```

### Basic Auth

```yaml
auth:
  type: basic
  username: "${{ secrets.SERVICE_USERNAME }}"
  password: "${{ secrets.SERVICE_PASSWORD }}"
```

### API Key

```yaml
auth:
  type: api_key
  header: "X-API-Key"
  key: "${{ secrets.SERVICE_API_KEY }}"
```

### OAuth2

```yaml
auth:
  type: oauth2
  oauth2:
    client_id: "your-client-id"
    client_secret: "${{ secrets.OAUTH_SECRET }}"
    token_url: "https://auth.example.com/oauth/token"
    scopes: ["read", "write"]
```

---

## Health Checks

Monitor service availability:

```yaml
health_check:
  enabled: true
  endpoint: "/health"
  interval_seconds: 60
  timeout_seconds: 5
  unhealthy_threshold: 3   # Failures before marking unhealthy
  healthy_threshold: 2     # Successes before marking healthy
```

---

## Circuit Breaker

Prevent cascade failures:

```yaml
circuit_breaker:
  enabled: true
  failure_threshold: 5     # Failures before opening circuit
  success_threshold: 2     # Successes before closing circuit
  timeout_seconds: 60      # Time in open state before retry
```

---

## Rate Limiting

Control request rates:

```yaml
rate_limiting:
  requests_per_second: 10
  requests_per_minute: 100
  requests_per_hour: 1000
  requests_per_day: 10000
  burst_limit: 20
  strategy: "sliding_window"  # sliding_window, fixed_window, token_bucket
```

---

## Capabilities

Describe what the service offers:

```yaml
capabilities:
  agents: ["analytics-agent", "reporting-agent"]
  features: ["data-analysis", "visualization"]
  streaming: true
  async: true
  webhooks: true
```

---

## Service Discovery

Routing and load balancing:

```yaml
discovery:
  registry_url: "https://registry.example.com"
  tags: ["analytics", "data", "reporting"]
  priority: 10      # Higher = higher priority
  weight: 100       # 0-100 for load balancing
```

---

## Metadata

Additional service information:

```yaml
metadata:
  owner: "Data Team"
  contact: "data-team@company.com"
  documentation: "https://docs.analytics.company.com"
  environment: "production"  # development, staging, production
  sla:
    uptime_percentage: 99.9
    response_time_ms: 500
```

---

## Full Example

```yaml
# a2a/analytics-engine.afs
schema: "1.0.0"
id: analytics-engine
name: Analytics Engine
description: External analytics for data processing and visualization

endpoint: "https://analytics.company.com"
protocol: https
version: "2.1.0"
timeout_seconds: 60

auth:
  type: bearer
  token: "${{ secrets.ANALYTICS_TOKEN }}"

retry_attempts: 3
retry_delay_seconds: 1
retry_backoff_multiplier: 2.0

health_check:
  enabled: true
  endpoint: "/health"
  interval_seconds: 60

circuit_breaker:
  enabled: true
  failure_threshold: 5
  timeout_seconds: 60

rate_limiting:
  requests_per_minute: 100
  burst_limit: 20

capabilities:
  agents: ["data-analyst", "report-generator"]
  features: ["analysis", "visualization", "export"]
  streaming: true
  async: true

metadata:
  owner: "Data Team"
  documentation: "https://docs.analytics.company.com"
  environment: "production"
```

---

## Learn More

- [Agents Reference](agents.md) - Configure agent delegation
- [Tools Reference](tools.md) - MCP tool integration
- [Secrets Reference](secrets.md) - Managing service credentials
