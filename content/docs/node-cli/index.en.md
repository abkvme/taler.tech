---
title: "Command Line (talerd)"
weight: 10
---

This guide provides comprehensive documentation for `talerd` (the TALER daemon) and `taler-cli` (command-line interface) tools.

## Overview

TALER provides two primary command-line tools:

- **`talerd`**: The blockchain daemon that runs the full node
- **`taler-cli`**: Command-line tool to interact with the running daemon

---

## talerd - TALER Daemon

The `talerd` daemon is the core TALER blockchain node software.

### Basic Usage

```bash
talerd [options]
```

### Essential Options

#### Network Configuration

| Option | Description | Default |
|--------|-------------|---------|
| `-listen` | Accept incoming connections | 1 (enabled) |
| `-port=<port>` | Listen on specified port | 23153 (mainnet) |
| `-maxconnections=<n>` | Maximum peer connections | 128 |
| `-addnode=<ip>` | Add a node to connect to | - |
| `-connect=<ip>` | Connect only to specified node | - |
| `-seednode=<ip>` | Connect to node for initial peers | - |
| `-proxy=<ip:port>` | Connect through SOCKS5 proxy | - |
| `-bind=<addr>` | Bind to specific address | All interfaces |

#### Data Management

| Option | Description | Default |
|--------|-------------|---------|
| `-datadir=<dir>` | Specify data directory | See below |
| `-prune=<n>` | Enable pruning (reduce disk usage to ~n MB) | 0 (disabled) |
| `-dbcache=<n>` | Database cache size in MB | 450 |
| `-maxmempool=<n>` | Maximum mempool size in MB | 300 |
| `-blocksdir=<dir>` | Specify directory for block storage | datadir/blocks |

**Default Data Directories**:
- Windows: `%APPDATA%\TALER\`
- macOS: `~/Taler/`
- Linux: `~/.taler/`

#### RPC Server

| Option | Description | Default |
|--------|-------------|---------|
| `-server` | Accept JSON-RPC commands | 0 (disabled) |
| `-rpcuser=<user>` | RPC username | - |
| `-rpcpassword=<pw>` | RPC password | - |
| `-rpcport=<port>` | Listen for RPC on port | 23152 (mainnet) |
| `-rpcbind=<addr>` | Bind RPC to address | 127.0.0.1 |
| `-rpcallowip=<ip>` | Allow RPC from IP (can use multiple times) | 127.0.0.1 |

#### Mining & Staking

| Option | Description | Default |
|--------|-------------|---------|
| `-gen` | Generate/mine coins | 0 (disabled) |
| `-genproclimit=<n>` | Set mining threads (-1 = all cores) | -1 |
| `-stakegen` | Enable stake generation (PoS) | 1 (enabled) |
| `-reservebalance=<amt>` | Reserve amount not used for staking | 0 |

#### Blockchain & Sync

| Option | Description | Default |
|--------|-------------|---------|
| `-testnet` | Use test network | 0 (mainnet) |
| `-reindex` | Rebuild blockchain index from blk*.dat files | 0 |
| `-reindex-chainstate` | Rebuild chainstate from current blocks | 0 |
| `-txindex` | Maintain full transaction index | 0 |
| `-addressindex` | Maintain address-based transaction index | 0 |
| `-timestampindex` | Maintain timestamp-based block index | 0 |
| `-spentindex` | Maintain full spent index | 0 |

#### Wallet

| Option | Description | Default |
|--------|-------------|---------|
| `-disablewallet` | Do not load wallet | 0 (wallet enabled) |
| `-wallet=<file>` | Specify wallet file | wallet.dat |
| `-zapwallettxes=<mode>` | Delete all wallet transactions (1=keep metadata, 2=drop) | 0 |
| `-upgradewallet` | Upgrade wallet to latest format | 0 |
| `-salvagewallet` | Attempt to recover private keys from corrupt wallet | 0 |

#### Logging & Debugging

| Option | Description | Default |
|--------|-------------|---------|
| `-debug=<category>` | Enable debug logging (net, tor, mempool, rpc, bench, db) | 0 |
| `-debuglogfile=<file>` | Specify debug log file location | debug.log |
| `-printtoconsole` | Send trace/debug info to console | 0 |
| `-logtimestamps` | Prepend timestamps to debug output | 1 |
| `-shrinkdebugfile` | Shrink debug.log on startup | 1 |

#### Performance

| Option | Description | Default |
|--------|-------------|---------|
| `-par=<n>` | Set script verification threads (-1 = auto) | -1 |
| `-checkblocks=<n>` | Number of blocks to check at startup | 6 |
| `-checklevel=<n>` | Thoroughness of block verification (0-4) | 3 |

#### Daemon Control

| Option | Description |
|--------|-------------|
| `-daemon` | Run in background as daemon (Unix only) |
| `-pid=<file>` | Specify PID file location |
| `-conf=<file>` | Specify configuration file |
| `-version` | Print version information |
| `-help` | Show help message |

---

## Common talerd Usage Examples

### Run Full Node with RPC Enabled

```bash
talerd -server -rpcuser=myuser -rpcpassword=mypassword
```

### Run Pruned Node (Saves Disk Space)

```bash
talerd -prune=2000
```

Keeps approximately 2GB of blockchain data.

### Run on Testnet

```bash
talerd -testnet
```

### Enable PoW Mining

```bash
talerd -gen -genproclimit=4
```

Mines using 4 CPU cores.

### Run as Background Daemon (Linux/macOS)

```bash
talerd -daemon
```

### Run as Systemd Service (Linux)

For production server deployments, running `talerd` as a systemd service provides automatic startup, supervision, and logging.

**Step 1: Create systemd service file**

Create `/etc/systemd/system/talerd.service`:

```ini
[Unit]
Description=TALER Daemon
After=network.target

[Service]
Type=forking
User=taler
Group=taler
ExecStart=/usr/local/bin/talerd -daemon
ExecStop=/usr/local/bin/taler-cli stop
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

**Step 2: Create taler user** (if not exists):

```bash
sudo useradd -r -m -s /bin/bash taler
```

**Step 3: Set permissions**:

```bash
sudo chown -R taler:taler /home/taler/.taler
```

**Step 4: Enable and start service**:

```bash
# Reload systemd to recognize new service
sudo systemctl daemon-reload

# Enable service to start on boot
sudo systemctl enable talerd

# Start the service
sudo systemctl start talerd

# Check status
sudo systemctl status talerd
```

**Useful systemd commands**:

```bash
# Stop the service
sudo systemctl stop talerd

# Restart the service
sudo systemctl restart talerd

# View logs
sudo journalctl -u talerd -f

# Disable auto-start
sudo systemctl disable talerd
```

### Custom Data Directory

```bash
talerd -datadir=/mnt/blockchain/taler
```

### Connect to Specific Nodes Only

```bash
talerd -connect=192.168.1.100:23153 -connect=192.168.1.101:23153
```

### Enable Transaction Indexing

```bash
talerd -txindex
```

Required for full block explorer functionality.

### Reindex Blockchain

```bash
talerd -reindex
```

Useful if blockchain database is corrupted.

---

## taler-cli - Command Line Interface

`taler-cli` is used to send JSON-RPC commands to a running `talerd` instance.

### Basic Usage

```bash
taler-cli [options] <command> [params]
```

### Connection Options

| Option | Description | Default |
|--------|-------------|---------|
| `-rpcuser=<user>` | RPC username | - |
| `-rpcpassword=<pw>` | RPC password | - |
| `-rpcport=<port>` | RPC port | 23152 |
| `-rpcconnect=<ip>` | RPC server address | 127.0.0.1 |
| `-datadir=<dir>` | Data directory (for reading conf) | See above |
| `-testnet` | Use testnet | 0 |

### Essential Commands

#### Node Information

```bash
# Get general node information
taler-cli getinfo

# Get blockchain information
taler-cli getblockchaininfo

# Get network information
taler-cli getnetworkinfo

# Get peer connection count
taler-cli getconnectioncount

# Get detailed peer info
taler-cli getpeerinfo
```

#### Blockchain Queries

```bash
# Get current block count
taler-cli getblockcount

# Get block hash by height
taler-cli getblockhash 12345

# Get block details by hash
taler-cli getblock <blockhash>

# Get transaction details
taler-cli gettransaction <txid>

# Get raw transaction (hex)
taler-cli getrawtransaction <txid>

# Get mempool info
taler-cli getmempoolinfo

# Get transactions in mempool
taler-cli getrawmempool
```

#### Wallet Operations

```bash
# Get wallet balance
taler-cli getbalance

# Get new receiving address
taler-cli getnewaddress

# List recent transactions
taler-cli listtransactions

# List unspent outputs
taler-cli listunspent

# Send coins
taler-cli sendtoaddress <address> <amount>

# Create raw transaction
taler-cli createrawtransaction '[{"txid":"...","vout":0}]' '{"address":amount}'

# Sign raw transaction
taler-cli signrawtransaction <hex>

# Send raw transaction
taler-cli sendrawtransaction <hex>
```

#### Staking Commands

```bash
# Get staking status
taler-cli getstakingstatus

# Get staking info
taler-cli getstakinginfo
```

#### Node Control

```bash
# Stop the daemon
taler-cli stop

# Get memory pool statistics
taler-cli getmempoolinfo

# Estimate fee for transaction
taler-cli estimatefee <blocks>
```

---

## Configuration File

Instead of passing command-line options every time, create a configuration file.

### Location

- **Windows**: `%APPDATA%\TALER\taler.conf`
- **macOS**: `~/Taler/taler.conf`
- **Linux**: `~/.taler/taler.conf`

### Example Full Node Configuration

```ini
# Network settings
listen=1
port=23153
maxconnections=128

# RPC server
server=1
rpcuser=yourusername
rpcpassword=your_strong_password_here
rpcport=23152
rpcallowip=127.0.0.1

# Performance
dbcache=1024
maxmempool=300
par=-1

# Indexing (optional, required for full block explorer)
txindex=1

# Logging
printtoconsole=0
logtimestamps=1
```

### Example Mining Node Configuration

```ini
# Network
listen=1
maxconnections=64

# RPC
server=1
rpcuser=miner
rpcpassword=mining_password

# Mining
gen=1
genproclimit=8

# Performance
dbcache=2048
```

### Example Pruned Node Configuration

```ini
# Network
listen=1
maxconnections=64

# Pruning
prune=2000

# RPC
server=1
rpcuser=pruned
rpcpassword=password

# Performance
dbcache=512
```

### Example Staking Configuration

```ini
# Network
listen=1
maxconnections=128

# RPC
server=1
rpcuser=staker
rpcpassword=staking_password

# Staking
stakegen=1
reservebalance=10

# Performance
dbcache=1024
```

---

## Environment Variables

You can also configure `talerd` and `taler-cli` using environment variables:

```bash
export TALER_DATA_DIR=/custom/path
export TALER_RPC_USER=myuser
export TALER_RPC_PASSWORD=mypassword
```

---

## Monitoring & Maintenance

### Check Sync Progress

```bash
taler-cli getblockchaininfo | grep "verificationprogress"
```

### View Debug Log

**Linux/macOS**:
```bash
tail -f ~/.taler/debug.log
```

**Windows**:
```cmd
type %APPDATA%\TALER\debug.log
```

### Monitor Network Connections

```bash
watch -n 5 'taler-cli getpeerinfo | grep addr'
```

### Check Mempool Size

```bash
taler-cli getmempoolinfo
```

### Estimate Network Hash Rate

```bash
taler-cli getmininginfo
```

---

## Troubleshooting

### RPC Connection Refused

**Problem**: `taler-cli` cannot connect to `talerd`

**Solutions**:
1. Ensure `talerd` is running: `ps aux | grep talerd`
2. Check RPC is enabled in `taler.conf`: `server=1`
3. Verify RPC credentials match in both `talerd` and `taler-cli`
4. Check firewall isn't blocking port 23152

### Blockchain Sync Stuck

**Problem**: Node stops syncing at certain block height

**Solutions**:
```bash
# Stop daemon
taler-cli stop

# Reindex blockchain
talerd -reindex

# Or rebuild chainstate only (faster)
talerd -reindex-chainstate
```

### High Memory Usage

**Problem**: `talerd` consuming too much RAM

**Solutions**:
```bash
# Reduce database cache
talerd -dbcache=256

# Reduce mempool size
talerd -maxmempool=100
```

### Wallet Won't Load

**Problem**: Wallet fails to load on startup

**Solutions**:
```bash
# Attempt to salvage wallet
talerd -salvagewallet

# Or upgrade wallet format
talerd -upgradewallet
```

---

## Advanced Usage

### Running Multiple Nodes

Run separate mainnet and testnet nodes:

**Mainnet**:
```bash
talerd -datadir=/data/taler-mainnet -port=23153 -rpcport=23152
```

**Testnet**:
```bash
talerd -testnet -datadir=/data/taler-testnet -port=33153 -rpcport=33152
```

### Custom Network Seeds

Add specific seed nodes for initial peer discovery:

```bash
talerd -seednode=seed1.talercoin.org -seednode=seed2.talercoin.org
```

### Blockchain Analysis

Enable full indexing for analysis:

```bash
talerd -txindex -addressindex -timestampindex -spentindex
```

**Note**: Requires full blockchain reindex and significantly increases disk usage.

---

## Security Best Practices

### RPC Security

1. **Use Strong Passwords**:
   ```ini
   rpcpassword=$(openssl rand -base64 32)
   ```

2. **Restrict RPC Access**:
   ```ini
   rpcallowip=127.0.0.1
   rpcbind=127.0.0.1
   ```

3. **Never expose RPC to public internet** without proper authentication and encryption

### File Permissions

Protect configuration file:

```bash
chmod 600 ~/.taler/taler.conf
```

### Firewall Configuration

Only expose P2P port (23153), not RPC port (23152):

```bash
# Allow P2P
sudo ufw allow 23153/tcp

# Block RPC from external
sudo ufw deny 23152/tcp
```

---

## Next Steps

- **Docker Deployment**: Run TALER node in containers with [Docker](/en/docs/node-docker/)
- **Mining & Staking**: Learn about block generation in [Mining & Staking](/en/docs/node-mining/)
- **RPC Reference**: Full RPC API documentation in [Developer Documentation](/en/docs/rpc-reference/)
