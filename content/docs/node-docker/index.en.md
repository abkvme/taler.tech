---
title: "Docker Deployment"
weight: 11
---

This guide covers deploying TALER nodes using Docker and Docker Compose.

## Prerequisites

Before you begin, ensure you have:

- **Docker**: Version 20.10 or higher ([Install Docker](https://docs.docker.com/get-docker/))
- **Docker Compose**: Version 2.0 or higher (included with Docker Desktop)
- **Disk Space**: At least 20GB for full node
- **RAM**: Minimum 2GB available memory (4GB+ recommended)

Verify installation:

```bash
docker --version
docker compose version
```

---

## Quick Start

### Using Docker Compose

The TALER repository includes a production-ready `docker-compose.yml` file.

**docker-compose.yml**:

```yaml
services:
  taler:
    image: ghcr.io/abkvme/taler:latest
    container_name: taler-node
    restart: unless-stopped

    ports:
      - "23153:23153"  # P2P network port
      - "23333:23333"  # RPC port

    volumes:
      - taler-data:/data  # Or use host path: ./data:/data
      # - ./taler.conf:/taler.conf:ro  # Uncomment to use custom config file
      # - ./wallet:/wallet  # Uncomment to enable wallet (also set TALER_WALLETDIR=/wallet in .env)

    # Healthcheck disabled by default (requires RPC to be enabled via custom taler.conf)
    # healthcheck:
    #   test: ["CMD", "taler-cli", "getblockchaininfo"]
    #   interval: 60s
    #   timeout: 30s
    #   retries: 5
    #   start_period: 1800s

    logging:
      driver: "json-file"
      options:
        max-size: "10m"
        max-file: "3"

volumes:
  taler-data:
    driver: local
```

### Start the Node

```bash
# Start in background
docker compose up -d

# View logs
docker compose logs -f

# Check status
docker compose ps

# Stop the node
docker compose down
```

---

## Configuration

### Default Behavior

By default, the TALER Docker container:
- Runs without RPC server enabled (security)
- Disables wallet functionality
- Stores blockchain data in `/data` volume
- Exposes P2P port 23153 and RPC port 23333

### Using Custom Configuration File

To enable RPC or customize node behavior, create a `taler.conf` file:

**taler.conf**:
```ini
# Network settings
listen=1
maxconnections=128

# RPC settings
server=1
rpcuser=yourusername
rpcpassword=your_strong_password_here
rpcport=23333
rpcallowip=0.0.0.0/0
rpcbind=0.0.0.0

# Performance
dbcache=1024
maxmempool=300

# Logging
printtoconsole=1
```

Uncomment the config volume mount in `docker-compose.yml`:

```yaml
volumes:
  - taler-data:/data
  - ./taler.conf:/taler.conf:ro  # Uncomment this line
```

Restart the container:

```bash
docker compose down
docker compose up -d
```

### Enabling Wallet

To enable wallet functionality:

1. Create a wallet directory:
   ```bash
   mkdir wallet
   ```

2. Uncomment the wallet volume in `docker-compose.yml`:
   ```yaml
   volumes:
     - taler-data:/data
     - ./wallet:/wallet  # Uncomment this line
   ```

3. Set the wallet directory environment variable:
   ```yaml
   environment:
     - TALER_WALLETDIR=/wallet
   ```

4. Restart:
   ```bash
   docker compose down
   docker compose up -d
   ```

### Environment Variables

The entrypoint script supports these environment variables:

| Variable | Description | Default |
|----------|-------------|---------|
| `TALER_DATA` | Blockchain data directory | `/data` |
| `TALER_CONF` | Configuration file path | `/taler.conf` |
| `TALER_WALLETDIR` | Wallet directory (enables wallet if set) | unset (wallet disabled) |

---

## Using Docker Run

Instead of Docker Compose, you can run directly with `docker run`:

```bash
docker run -d \
  --name taler-node \
  -p 23153:23153 \
  -p 23333:23333 \
  -v taler-data:/data \
  ghcr.io/abkvme/taler:latest
```

### With Custom Config

```bash
docker run -d \
  --name taler-node \
  -p 23153:23153 \
  -p 23333:23333 \
  -v taler-data:/data \
  -v $(pwd)/taler.conf:/taler.conf:ro \
  ghcr.io/abkvme/taler:latest
```

### With Wallet Enabled

```bash
docker run -d \
  --name taler-node \
  -p 23153:23153 \
  -p 23333:23333 \
  -v taler-data:/data \
  -v $(pwd)/wallet:/wallet \
  -e TALER_WALLETDIR=/wallet \
  ghcr.io/abkvme/taler:latest
```

---

## Monitoring and Management

### View Logs

```bash
# Follow logs
docker compose logs -f

# Last 100 lines
docker compose logs --tail 100

# Logs for specific service
docker logs taler-node
```

### Execute Commands

```bash
# Get node info (requires RPC enabled)
docker exec taler-node taler-cli getinfo

# Get blockchain info
docker exec taler-node taler-cli getblockchaininfo

# Get connection count
docker exec taler-node taler-cli getconnectioncount
```

### Access Container Shell

```bash
docker exec -it taler-node /bin/bash
```

### Container Stats

```bash
# Real-time stats
docker stats taler-node

# One-time snapshot
docker stats --no-stream taler-node
```

---

## Volume Management

### Inspect Volume

```bash
docker volume inspect taler-data
```

### Backup Blockchain Data

```bash
# Create backup
docker run --rm \
  -v taler-data:/data \
  -v $(pwd):/backup \
  ubuntu tar czf /backup/taler-backup-$(date +%Y%m%d).tar.gz /data
```

### Restore Backup

```bash
docker run --rm \
  -v taler-data:/data \
  -v $(pwd):/backup \
  ubuntu tar xzf /backup/taler-backup-YYYYMMDD.tar.gz -C /
```

### Using Host Directory

Instead of Docker volume, use a host directory:

```yaml
volumes:
  - ./data:/data  # Host directory
```

This stores blockchain data in `./data` on your host system.

---

## Building Custom Image

The TALER repository includes a `Dockerfile` for building custom images.

### Build Locally

```bash
# Clone repository
git clone https://github.com/abkvme/taler.git
cd taler

# Build image
docker build -t taler:local .

# Run local build
docker run -d \
  --name taler-node \
  -p 23153:23153 \
  -v taler-data:/data \
  taler:local
```

### Multi-Architecture Build

The Dockerfile supports both `linux/amd64` and `linux/arm64`:

```bash
# Build for multiple platforms
docker buildx build \
  --platform linux/amd64,linux/arm64 \
  -t taler:custom \
  .
```

---

## Healthcheck Configuration

To enable health monitoring, ensure RPC is enabled in your `taler.conf`, then uncomment the healthcheck section in `docker-compose.yml`:

```yaml
healthcheck:
  test: ["CMD", "taler-cli", "getblockchaininfo"]
  interval: 60s
  timeout: 30s
  retries: 5
  start_period: 1800s  # 30 minutes for initial sync
```

Check health status:

```bash
docker inspect --format='{{.State.Health.Status}}' taler-node
```

---

## Security Best Practices

### RPC Port Binding

For security, bind RPC port to localhost only when possible:

```yaml
ports:
  - "23153:23153"
  - "127.0.0.1:23333:23333"  # Localhost only
```

### Strong RPC Credentials

Generate strong passwords:

```bash
openssl rand -base64 32
```

### File Permissions

Protect your configuration file:

```bash
chmod 600 taler.conf
```

### Keep Images Updated

Regularly update to the latest image:

```bash
docker compose pull
docker compose up -d
```

---

## Troubleshooting

### Container Won't Start

```bash
# Check logs for errors
docker compose logs

# Check container status
docker compose ps -a

# Remove and recreate
docker compose down
docker compose up -d
```

### RPC Connection Issues

If `taler-cli` commands fail:

1. Ensure RPC is enabled in `taler.conf` with `server=1`
2. Verify config file is mounted: `docker exec taler-node ls -la /taler.conf`
3. Check if RPC credentials are correct

### Blockchain Sync Issues

```bash
# Check sync progress
docker exec taler-node taler-cli getblockchaininfo

# View debug output in logs
docker compose logs -f
```

### Reset Blockchain Data

```bash
# Stop container
docker compose down

# Remove volume (WARNING: deletes all blockchain data)
docker volume rm taler-data

# Restart
docker compose up -d
```

---

## Production Deployment

### Run as System Service

Create `/etc/systemd/system/taler-docker.service`:

```ini
[Unit]
Description=TALER Docker Node
Requires=docker.service
After=docker.service

[Service]
Type=oneshot
RemainAfterExit=yes
WorkingDirectory=/opt/taler
ExecStart=/usr/bin/docker compose up -d
ExecStop=/usr/bin/docker compose down
TimeoutStartSec=0

[Install]
WantedBy=multi-user.target
```

Enable and start:

```bash
sudo systemctl enable taler-docker
sudo systemctl start taler-docker
```

### Automatic Updates

Use Watchtower to auto-update containers:

```yaml
services:
  taler:
    # ... existing config ...

  watchtower:
    image: containrrr/watchtower
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    command: --interval 86400  # Check daily
```

---

## Next Steps

- **Node CLI**: Learn command-line operations in [Node CLI](/en/docs/node-cli/)
- **Mining & Staking**: Configure mining/staking in [Mining & Staking](/en/docs/node-mining/)
- **RPC API**: Build applications with [Developer Documentation](/en/docs/rpc-reference/)
