---
title: "Community Seed Servers"
weight: 10
---

Seed servers help TALER Blockchain nodes discover peers when joining the network. TALER Blockchain uses a combination of hardcoded seeds and a community-maintained seed list to ensure reliable connectivity and decentralization.

## How Peer Discovery Works

When a TALER Blockchain node starts, it needs to find other nodes to connect to. This happens in several stages:

1. **Hardcoded DNS Seeds** - Built into the node binary, these are DNS names that resolve to known TALER Blockchain nodes
2. **Hardcoded Fixed IP Seeds** - A fallback list of IP addresses compiled into the binary
3. **Community Seed List** - A remote `bootstrap.json` file fetched from GitHub on startup, maintained by the community
4. **Peer Exchange** - Once connected, nodes share peer addresses with each other

The community seed list supplements hardcoded seeds, improving network resilience and allowing new nodes to be added without a software update.

---

## Community Seed List

The community-maintained seed list lives on GitHub:

**Repository**: [github.com/abkvme/taler-seeds](https://github.com/abkvme/taler-seeds)

The node fetches `bootstrap.json` from this repository on startup. The file contains a list of community-contributed nodes:

```json
{
  "nodes": [
    {"host": "talerseed01.taler.tech", "port": 23153},
    {"host": "talerseed02.taler.tech", "port": 23153}
  ]
}
```

Each entry has:
- **host** - DNS name or IP address of the node
- **port** - (optional) P2P port, defaults to 23153

---

## Viewing Seed Servers in the GUI

The TALER Coin Qt wallet includes an **Info** tab that displays all seed servers and their connectivity status:

- **Hardcoded Seeds** - DNS and fixed IP seeds built into the binary
- **Community Seeds** - Nodes from the GitHub `bootstrap.json` list
- **Discovered Peers** - Currently connected peers with version info

Click **Refresh** to run connectivity checks against all seed servers. Green dots indicate reachable nodes, red dots indicate unreachable ones.

---

## Adding Your Node to the Seed List

If you run a TALER Blockchain node with reliable uptime on a static IP or DNS name, you can contribute it to the community seed list. This helps other nodes discover peers and strengthens the network.

### Requirements

- Your node must be running TALER (default port 23153, custom ports supported)
- Port must be open and reachable from the internet
- Your node should have reliable uptime

### Steps

1. **Fork** the repository: [github.com/abkvme/taler-seeds](https://github.com/abkvme/taler-seeds)
2. **Clone** your fork:
   ```bash
   git clone https://github.com/YOUR_USERNAME/taler-seeds.git
   cd taler-seeds
   ```
3. **Create a branch**:
   ```bash
   git checkout -b add-my-node
   ```
4. **Edit** `bootstrap.json` - add your node entry to the `nodes` array
5. **Commit and push**:
   ```bash
   git add bootstrap.json
   git commit -m "Add my node to bootstrap list"
   git push origin add-my-node
   ```
6. **Create a Pull Request** at [github.com/abkvme/taler-seeds](https://github.com/abkvme/taler-seeds) with a brief description of your node (location, uptime, etc.)

Submissions are validated automatically by CI. Once merged, all new TALER Blockchain nodes starting up will discover your node.

---

## Official Seed Infrastructure

The TALER Blockchain project maintains seed servers on the `taler.tech` domain:

| Seed Server | Port | Type |
|-------------|------|------|
| `talerseed01.taler.tech` | 23153 | Community list |
| `talerseed02.taler.tech` | 23153 | Community list |

These are included in the community seed list and serve as baseline infrastructure for the network.

---

## Network Ports

For your node to be discoverable as a seed, ensure the following port is accessible:

- **Mainnet**: Port 23153 (TCP)
- **Testnet**: Port 33153 (TCP)

Configure your firewall to allow incoming connections on the appropriate port.

---

## Related

- [Running a Node](/en/docs/node-setup/) - Full guide to setting up a TALER Blockchain node
- [Node CLI](/en/docs/node-cli/) - Command-line daemon configuration
- [Docker Deployment](/en/docs/node-docker/) - Running a node in Docker
