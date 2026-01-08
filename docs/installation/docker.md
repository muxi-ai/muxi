# Docker Installation

## Overview


Run MUXI Server in a container.

## Quick Start

```bash
docker run -d \
  --name muxi-server \
  -p 7890:7890 \
  -v muxi-data:/data \
  ghcr.io/muxi-ai/server:latest
```

## Docker Compose

Create `docker-compose.yml`:

```yaml
version: '3.8'

services:
  muxi-server:
    image: ghcr.io/muxi-ai/server:latest
    ports:
      - "7890:7890"
    volumes:
      - muxi-data:/data
      - ./formations:/formations
    environment:
      - MUXI_AUTH_ENABLED=true
    restart: unless-stopped

volumes:
  muxi-data:
```

Start:

```bash
docker compose up -d
```

## Environment Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `MUXI_PORT` | `7890` | Server port |
| `MUXI_AUTH_ENABLED` | `true` | Enable authentication |
| `MUXI_LOG_LEVEL` | `info` | Log level |

## Volume Mounts

| Path | Purpose |
|------|---------|
| `/data` | Server data and state |
| `/formations` | Formation files |
| `/etc/muxi` | Configuration |

## Initialize Credentials

```bash
docker exec muxi-server muxi-server init
```

Retrieve generated credentials:

```bash
docker exec muxi-server cat /etc/muxi/server/config.yaml
```

## Deploy a Formation

```bash
# Copy formation to container
docker cp my-formation muxi-server:/formations/

# Or mount formations directory in docker-compose.yml
```

## View Logs

```bash
docker logs -f muxi-server
```

## Health Check

```bash
curl http://localhost:7890/health
```

## Production Docker Compose

```yaml
version: '3.8'

services:
  muxi-server:
    image: ghcr.io/muxi-ai/server:latest
    ports:
      - "7890:7890"
    volumes:
      - muxi-data:/data
      - ./config:/etc/muxi:ro
      - ./formations:/formations:ro
    environment:
      - MUXI_AUTH_ENABLED=true
      - MUXI_LOG_LEVEL=info
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:7890/health"]
      interval: 30s
      timeout: 10s
      retries: 3
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 4G

volumes:
  muxi-data:
```

## Next Steps

- [Server Configuration](../server/configuration.md) - Configure the server
- [Production Deployment](../server/production.md) - Production best practices
