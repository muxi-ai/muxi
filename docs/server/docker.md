---
title: Docker Quick Start
description: Run MUXI Server in Docker without installing anything locally
---

# Docker Quick Start

## Run MUXI Server with Docker

Run MUXI Server in Docker without installing anything locally. Perfect for quick testing, demos, and CI/CD pipelines.


## Prerequisites

Docker Desktop installed:
- **macOS/Windows:** [Download Docker Desktop](https://docker.com/products/docker-desktop)
- **Linux:** `apt install docker.io` or `yum install docker`


## Quick Start

[[steps]]

[[step Run the container]]

```bash
docker run -d \
  --name muxi-server \
  -p 7890:7890 \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v ~/.muxi/server:/root/.muxi/server \
  ghcr.io/muxi-ai/muxi-server:latest
```

[[/step]]

[[step Verify it's running]]

```bash
curl http://localhost:7890/health
# {"status": "healthy"}
```

[[/step]]

[[/steps]]


## Docker Compose

For easier management, use Docker Compose:

```bash
# Download
curl -O https://raw.githubusercontent.com/muxi-ai/server/main/docker-compose.yml

# Start
docker-compose up -d

# View logs
docker-compose logs -f

# Stop
docker-compose down
```


## Configuration

Customize via environment variables in `docker-compose.yml`:

```yaml
environment:
  MUXI_LOG_LEVEL: debug          # debug, info, warn, error
  MUXI_PORT: 7890                # Server port
  MUXI_RUNTIME_TYPE: docker      # Use Docker for formations
```


## Docker Socket Access

```yaml
volumes:
  - /var/run/docker.sock:/var/run/docker.sock
```

MUXI Server needs access to the host's Docker daemon to spawn formation containers as **sibling containers**:

```plain
Your Machine
├── Docker Daemon
    ├── muxi-server    (container 1)
    ├── formation-a    (container 2) ← spawned by muxi-server
    └── formation-b    (container 3) ← spawned by muxi-server
```

> [!WARNING]
> Docker socket access gives the container full control over Docker. Use only for local development and testing. For production, use the [native installation](../installation/linux.md).


## Common Tasks

| Task | Command |
|------|---------|
| View logs | `docker-compose logs -f` |
| Stop server | `docker-compose stop` |
| Start server | `docker-compose start` |
| Restart | `docker-compose restart` |
| Update image | `docker-compose pull && docker-compose up -d` |
| Full cleanup | `docker-compose down -v` |
| Shell access | `docker exec -it muxi-server sh` |


## Docker vs Native Install

| | Docker | Native |
|-|--------|--------|
| **Setup** | `docker-compose up` | `brew install muxi-ai/tap/muxi-server` |
| **Requirements** | Docker only | System privileges |
| **Performance** | Good | Optimal |
| **Production** | Not recommended | Recommended |
| **Security** | Socket access required | System service |
| **Best for** | Testing, demos, CI/CD | Production servers |


## Troubleshooting

**"Cannot connect to Docker daemon"** - Start Docker Desktop (macOS/Windows) or `sudo systemctl start docker` (Linux).

**"Permission denied: /var/run/docker.sock"** - Add your user to the docker group: `sudo usermod -aG docker $USER` and log out/in.

**"Port 7890 already in use"** - Change the port mapping: `-p 8080:7890`.

**"Server exits immediately"** - Check logs: `docker-compose logs muxi-server`.


## Next Steps

After testing with Docker, install natively for production:

```bash
brew install muxi-ai/tap/muxi-server
```

See [Server Setup](setup.md) for full configuration details.
