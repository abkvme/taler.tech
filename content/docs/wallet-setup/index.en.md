---
title: "Wallet Setup"
weight: 2
---

This guide will walk you through installing and setting up the TALER wallet on your system.

## Installation

### Windows Installation

1. **Extract the archive**:
   - Right-click the downloaded `taler-*-win64.zip` file
   - Select "Extract All..."
   - Choose a destination folder (e.g., `C:\Program Files\TALER`)

2. **Locate the executable**:
   - Navigate to the extracted folder
   - Find `taler-qt.exe` (the GUI wallet application)

3. **Optional - Create a desktop shortcut**:
   - Right-click `taler-qt.exe`
   - Select "Send to" → "Desktop (create shortcut)"

### macOS Installation

1. **Extract the archive**:
   ```bash
   tar -xzvf taler-*-macos-arm64.tar.gz
   ```

2. **Move to Applications** (optional):
   ```bash
   sudo mv taler-*/bin/* /usr/local/bin/
   ```

3. **Grant execution permissions**:
   ```bash
   chmod +x /usr/local/bin/taler-qt
   ```

### Linux Installation

1. **Extract the archive**:
   ```bash
   tar -xzvf taler-*-linux-x64.tar.gz
   cd taler-*
   ```

2. **Install to system** (optional):
   ```bash
   sudo install -m 0755 -o root -g root -t /usr/local/bin bin/*
   ```

3. **Or run from extracted directory**:
   ```bash
   ./bin/taler-qt
   ```

---

## Launching the TALER Wallet

### Starting via Command Line

The TALER GUI wallet can be started from the command line with various options.

#### Basic Launch

**Windows**:
```cmd
taler-qt.exe
```

**macOS / Linux**:
```bash
taler-qt
```

#### Launch with Custom Data Directory

Specify a custom location for blockchain data:

```bash
taler-qt -datadir=/path/to/custom/directory
```

#### Launch on Testnet

For testing purposes, launch the wallet on the test network:

```bash
taler-qt -testnet
```

#### Common Command-Line Options

| Option | Description |
|--------|-------------|
| `-datadir=<dir>` | Specify data directory location |
| `-conf=<file>` | Specify configuration file |
| `-testnet` | Use the test network |
| `-prune=<n>` | Enable blockchain pruning (reduce disk usage) |
| `-dbcache=<n>` | Set database cache size in MB (default: 450) |
| `-maxconnections=<n>` | Maximum peer connections (default: 128) |
| `-listen` | Accept incoming connections (default: 1) |
| `-server` | Enable RPC server |

### Starting via Desktop (GUI)

#### Windows
- Double-click the `taler-qt.exe` file or use the desktop shortcut

#### macOS
- Navigate to the extracted directory
- Double-click `taler-qt` (if installed to `/usr/local/bin`, run from terminal: `taler-qt`)
- Or run from extracted directory: `./bin/taler-qt`

#### Linux
- Use your application launcher to find "TALER"
- Or run from terminal: `taler-qt`
- Or from extracted directory: `./bin/taler-qt`

---

## First-Time Setup

### Initial Blockchain Synchronization

When you first launch TALER wallet, it will begin synchronizing with the blockchain:

1. **Splash Screen**: The application will display the TALER logo while loading
2. **Synchronization Notice**: A modal overlay will appear showing sync progress
3. **Progress Bar**: Shows percentage of blockchain downloaded
4. **Time Estimate**: Displays estimated time remaining

**Note**: Initial synchronization can take several hours depending on your internet connection speed. The wallet must download the entire blockchain history.

### Understanding the Synchronization Process

- **Block Height**: Current block vs. latest network block
- **Download Speed**: Network throughput in KB/s or MB/s
- **Headers First**: Downloads block headers before full blocks for faster initial sync
- **Background Sync**: You can minimize the wallet while syncing continues

**Tip**: For faster synchronization, ensure your system allows TALER through the firewall and has sufficient peer connections (check status bar).

---

## Wallet Interface Overview

Once synchronized, the TALER wallet interface consists of several main sections:

### Top Navigation Tabs

1. **Overview**: Dashboard showing balances and recent transactions
2. **Send**: Interface for sending TALER coins
3. **Receive**: Generate addresses to receive coins
4. **Transactions**: Complete transaction history
5. **Mining**: Enable/configure PoW mining
6. **Staking**: Configure PoS staking (available after sync)

### Status Bar (Bottom)

- **Connection Status**: Number of active peer connections
- **Block Count**: Current synchronized block height
- **Network Status**: Network hash rate and difficulty
- **Wallet Status**: Lock/unlock status (encrypted wallets)

### Main Toolbar

- **File**: Backup wallet, export keys, exit
- **Settings**: Configure wallet preferences, network settings
- **Tools**: Debug console, sign/verify messages, peer info
- **Help**: About dialog, command-line options

---

## Creating Your First Wallet

The TALER application creates a wallet automatically on first launch. The wallet file is stored in your data directory:

### Default Wallet Locations

- **Windows**: `%APPDATA%\TALER\wallet.dat`
- **macOS**: `~/Taler/wallet.dat`
- **Linux**: `~/.taler/wallet.dat`
