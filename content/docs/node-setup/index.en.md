---
title: "Running a Node"
weight: 9
---

This guide covers how to set up and run a full TALER blockchain node.

## What is a TALER Node?

A TALER node is a full participant in the blockchain network that:

- **Validates Transactions**: Verifies all transactions according to consensus rules
- **Maintains Blockchain**: Stores the complete transaction history
- **Relays Data**: Forwards valid transactions and blocks to other nodes
- **Supports Network**: Contributes to network decentralization and security
- **Enables Mining/Staking**: Allows you to participate in block generation

Running a node helps secure the TALER network and gives you full control over your blockchain interactions.

---

## Ways to Run a TALER Node

You can run a TALER node using several methods depending on your needs:

### 1. GUI Wallet (taler-qt) - For Desktop Users

**Best for**: Personal use, wallet management, and casual staking

Run the graphical wallet application `taler-qt` on your desktop computer:
- Full GUI interface for wallet management
- Built-in node that syncs with the blockchain
- Easy setup for beginners
- Suitable for personal computers

**Keep wallet running 24/7** for continuous node operation and staking.

**Learn more**: [Wallet Setup Guide](/en/docs/wallet-setup/)

---

### 2. Command Line (talerd) - For Server Deployment

**Best for**: Dedicated servers, VPS, headless systems

Run the daemon `talerd` on a server without GUI:
- Configure via `taler.conf` configuration file
- Run on local computer or VPS
- Lower resource usage (no GUI)
- Remote management via RPC

**Learn more**: [Command Line (talerd) Documentation](/en/docs/node-cli/)

---

### 3. Systemd Service - For Linux Servers (Recommended for VPS)

**Best for**: Production VPS deployment, automatic startup

Run `talerd` as a system service:
- Automatic startup on boot
- Process management by systemd
- Service supervision and restart on failure
- Log management
- Professional server setup

**Learn more**: [Systemd Configuration](/en/docs/node-cli/#run-as-background-service) in Node CLI guide

---

### 4. Docker Compose - For Containerized Deployment (Recommended for VPS)

**Best for**: Easy upgrades, reproducible deployments, VPS hosting

Run TALER node in Docker container:
- Easy updates: `docker compose pull && docker compose up -d`
- Isolated environment
- Reproducible configuration
- Simplified backup and migration
- No dependency conflicts

**Learn more**: [Docker Deployment Guide](/en/docs/node-docker/)

---

## Choosing the Right Method

| Method | Best Use Case | Difficulty | Updates |
|--------|--------------|------------|---------|
| **taler-qt (GUI)** | Personal desktop, wallet + node | Easy | Manual download |
| **talerd (CLI)** | Server, VPS, headless | Medium | Manual download |
| **Systemd Service** | Production VPS | Medium | Manual download |
| **Docker Compose** | Production VPS, easy updates | Easy | `docker compose pull` |

**Recommendation**:
- **Desktop users**: Use taler-qt GUI wallet
- **VPS/Server users**: Use Docker Compose for easiest management
- **Advanced users**: Use systemd service for full control

---

## Prerequisites

Before running a TALER node, ensure your system meets these requirements:

### Hardware Requirements

- **CPU**: 2+ cores recommended (4+ cores for mining)
- **RAM**: Minimum 4GB (8GB+ recommended)
- **Disk Space**:
  - Full node: 20GB+ (blockchain grows over time)
  - Pruned node: 2GB+ (with `-prune` option)
- **Network**: Stable internet connection with at least 1 Mbps upload/download

### Software Requirements

- **Operating System**: Windows 10/11, macOS (Apple Silicon), or Linux (x64/ARM64)
- **Port Access**: Port 23153 (mainnet) must be accessible for incoming connections

---

## Download TALER Node Software

### Step 1: Visit the Official Release Page

Download the latest TALER node software from the official GitHub repository:

**Latest Release**: [v0.18.44.7](https://github.com/abkvme/taler/releases/tag/v0.18.44.7)

### Step 2: Choose Your Platform

Select the appropriate package for your operating system:

| Platform | File | Description |
|----------|------|-------------|
| **Windows (64-bit)** | `taler-v0.18.44.7-win64.zip` | Windows 10/11 binaries |
| **macOS (M1/M2/M3+)** | `taler-v0.18.44.7-macos-arm64.tar.gz` | Apple Silicon native build |
| **Linux (64-bit)** | `taler-v0.18.44.7-linux-x64.tar.gz` | Linux x86_64 binaries |
| **Linux (ARM64)** | `taler-v0.18.44.7-linux-arm64.tar.gz` | ARM64/aarch64 binaries |

---

## Installation

### Windows Installation

1. **Extract the archive**:
   - Right-click `taler-v0.18.44.7-win64.zip`
   - Select "Extract All..."
   - Choose a destination folder (e.g., `C:\TALER`)

2. **Locate the daemon**:
   - Navigate to the extracted folder
   - Find `talerd.exe` (the node daemon)

### macOS Installation

1. **Extract the archive**:
   ```bash
   tar -xzvf taler-v0.18.44.7-macos-arm64.tar.gz
   cd taler-v0.18.44.7
   ```

2. **Install to system** (optional):
   ```bash
   sudo mv bin/* /usr/local/bin/
   sudo chmod +x /usr/local/bin/talerd
   ```

3. **Or run from extracted directory**:
   ```bash
   chmod +x bin/talerd
   ./bin/talerd
   ```

### Linux Installation

1. **Extract the archive**:
   ```bash
   tar -xzvf taler-v0.18.44.7-linux-x64.tar.gz
   cd taler-v0.18.44.7
   ```

2. **Install to system** (optional):
   ```bash
   sudo install -m 0755 -o root -g root -t /usr/local/bin bin/*
   ```

3. **Or run from extracted directory**:
   ```bash
   chmod +x bin/talerd
   ./bin/talerd
   ```

---

## Starting Your Node

### Basic Node Start

**Windows**:
```cmd
talerd.exe
```

**macOS / Linux**:
```bash
talerd
```

On first launch, `talerd` will:
1. Create the data directory (if it doesn't exist)
2. Connect to the TALER network
3. Begin downloading the blockchain
4. Start validating and relaying transactions

### Data Directory Locations

By default, TALER stores blockchain data in:

- **Windows**: `%APPDATA%\TALER\`
- **macOS**: `~/Taler/`
- **Linux**: `~/.taler/`

---

## Common Node Configurations

### Custom Data Directory

Specify a custom location for blockchain data:

```bash
talerd -datadir=/path/to/custom/directory
```

### Pruned Node (Reduced Disk Usage)

Run a pruned node to save disk space (stores only recent blocks):

```bash
talerd -prune=2000
```

This keeps approximately 2GB of blockchain data instead of the full chain.

### Testnet Node

Run a node on the test network for development:

```bash
talerd -testnet
```

### Allow Incoming Connections

To maximize network support, allow incoming connections:

```bash
talerd -listen=1 -maxconnections=128
```

Make sure port 23153 is open in your firewall.

---

## Monitoring Your Node

### Check Node Status

Use `taler-cli` to check your node status:

```bash
taler-cli getinfo
```

This displays:
- Current block height
- Number of connections
- Network hash rate
- Wallet balance (if wallet loaded)

### View Connection Count

```bash
taler-cli getconnectioncount
```

### Check Blockchain Sync Progress

```bash
taler-cli getblockchaininfo
```

Shows:
- Current block vs. network height
- Verification progress percentage
- Chain size on disk

### Monitor Logs

**Linux/macOS**:
```bash
tail -f ~/.taler/debug.log
```

**Windows**:
```cmd
type %APPDATA%\TALER\debug.log
```

---

## Network Ports

### Mainnet

- **P2P Network**: 23153 (TCP)
- **RPC Port**: 23152 (localhost only by default)

### Testnet

- **P2P Network**: 33153 (TCP)
- **RPC Port**: 33152 (localhost only by default)

To accept incoming connections, ensure port 23153 (mainnet) or 33153 (testnet) is accessible through your firewall.

---

## Stopping Your Node

### Graceful Shutdown

Use `taler-cli` for clean shutdown:

```bash
taler-cli stop
```

This ensures:
- Database is properly closed
- Connections are cleanly terminated
- No blockchain corruption

### Force Stop (Not Recommended)

If `taler-cli stop` doesn't work:

**Linux/macOS**:
```bash
killall talerd
```

**Windows**:
```cmd
taskkill /IM talerd.exe
```

**Note**: Force stopping may require reindexing on next start.

---

## Configuration File

Create `taler.conf` in your data directory for persistent settings:

**Example taler.conf**:
```ini
# Network settings
listen=1
maxconnections=128
port=23153

# RPC settings
server=1
rpcuser=yourusername
rpcpassword=yourpassword
rpcport=23152
rpcallowip=127.0.0.1

# Performance
dbcache=1024
maxmempool=300

# Pruning (optional)
prune=2000
```

**Location**:
- **Windows**: `%APPDATA%\TALER\taler.conf`
- **macOS**: `~/Taler/taler.conf`
- **Linux**: `~/.taler/taler.conf`

---

## Next Steps

- **Command Line**: Learn advanced `talerd` options in the [Node CLI](/en/docs/node-cli/) guide
- **Docker Deployment**: Run TALER node in containers with [Docker](/en/docs/node-docker/)
- **Mining & Staking**: Participate in block generation in [Mining & Staking](/en/docs/node-mining/)
