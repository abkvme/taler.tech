---
title: "RPC Reference"
weight: 1
---

TALER provides a comprehensive JSON-RPC interface for programmatic interaction with the blockchain, wallet, and network. This reference documents all available RPC methods.

<!--more-->

## Getting Started with TALER RPC

### Enabling RPC Access

**1. Configure RPC in taler.conf**

Create or edit your configuration file:

**Linux/macOS**: `~/.taler/taler.conf`
**Windows**: `%APPDATA%\TALER\taler.conf`

```ini
# Enable RPC server
server=1

# RPC credentials (required)
rpcuser=yourusername
rpcpassword=yourpassword

# Optional: bind to specific IP
rpcbind=127.0.0.1

# Optional: allow connections from specific IP
rpcallowip=127.0.0.1

# Optional: custom RPC port (default: 23333)
rpcport=23333
```

**2. Start talerd with RPC enabled**

```bash
talerd -daemon
```

### Making RPC Calls

**Using taler-cli (recommended)**

```bash
taler-cli [options] <command> [params]
```

**Example**:
```bash
taler-cli getblockcount
taler-cli getbalance
taler-cli sendtoaddress "TFNxSh9X75WguDWRj4FbRELK8GUP37xu4E" 10.5
```

**Using curl (HTTP POST)**

```bash
curl --user yourusername:yourpassword \
  --data-binary '{"jsonrpc":"1.0","id":"curltest","method":"getblockcount","params":[]}' \
  -H 'content-type: text/plain;' \
  http://127.0.0.1:23333/
```

**Response format**:
```json
{
  "result": 123456,
  "error": null,
  "id": "curltest"
}
```

---

## RPC Methods by Category

### Blockchain Information

Query blockchain state, blocks, transactions, and chain statistics.

| Method | Description |
|--------|-------------|
| `getblockchaininfo` | Returns comprehensive blockchain information including chain tip, difficulty, and softfork status |
| `getblockcount` | Returns the current block height (number of blocks in the main chain) |
| `getbestblockhash` | Returns the hash of the current best (tip) block |
| `getblock "blockhash" (verbosity)` | Returns information about a specific block given its hash |
| `getblockhash height` | Returns the block hash for a block at the specified height |
| `getblockheader "blockhash" (verbose)` | Returns the header information of a specific block |
| `getchaintips` | Returns information about all known chain tips (valid and invalid forks) |
| `getchaintxstats (nblocks) (blockhash)` | Returns chain transaction statistics (tx count, tx rate, window) |
| `getblockstats hash_or_height (stats)` | Returns per-block statistics (fees, weight, time, tx count) |
| `getdifficulty` | Returns the current proof-of-work and proof-of-stake difficulty |
| `verifychain (checklevel) (nblocks)` | Verifies the blockchain database integrity |
| `preciousblock "blockhash"` | Marks a block as precious, affecting chain selection priority |

### Mempool (Transaction Pool)

Inspect and manage transactions waiting to be confirmed.

| Method | Description |
|--------|-------------|
| `getrawmempool (verbose)` | Returns all transaction IDs in the memory pool |
| `getmempoolancestors "txid" (verbose)` | Returns all ancestor transactions of a given mempool transaction |
| `getmempooldescendants "txid" (verbose)` | Returns all descendant transactions of a given mempool transaction |
| `getmempoolentry "txid"` | Returns detailed information about a specific mempool transaction |
| `getmempoolinfo` | Returns mempool size, memory usage, and transaction count |
| `savemempool` | Saves the current memory pool to disk |

### UTXO Set

Query unspent transaction outputs and proof verification.

| Method | Description |
|--------|-------------|
| `gettxout "txid" n (include_mempool)` | Returns details about an unspent transaction output (UTXO) |
| `gettxoutsetinfo` | Returns statistics about the unspent transaction output set |
| `gettxoutproof ["txid",...] (blockhash)` | Returns a hex-encoded proof that transactions were included in a block |
| `verifytxoutproof "proof"` | Verifies a proof and returns the transactions it commits to |
| `scantxoutset "action" [scanobjects,...]` | Scans the UTXO set for specific descriptor patterns |

### Advanced Blockchain Control

Advanced operations for testing and blockchain management.

| Method | Description |
|--------|-------------|
| `pruneblockchain height` | Prunes blockchain data up to a specified height (if pruning enabled) |
| `invalidateblock "blockhash"` | Marks a block and all its descendants as invalid (testing only) |
| `reconsiderblock "blockhash"` | Reconsiders a previously invalidated block |
| `waitfornewblock (timeout)` | Waits for a new block to be generated and returns its hash and height |
| `waitforblock "blockhash" (timeout)` | Waits for a specific block to arrive |
| `waitforblockheight height (timeout)` | Waits for the blockchain to reach a specific height |

---

## Mining & Generating

Control block generation for both proof-of-work (PoW) and proof-of-stake (PoS).

| Method | Description |
|--------|-------------|
| `getmininginfo` | Returns mining-related information (hashrate, difficulty, blocks, staking status) |
| `getnetworkhashps (nblocks) (height)` | Returns estimated network hashes per second based on recent blocks |
| `getblocktemplate (template_request)` | Returns a block template for mining (BIP 22/23 compatible) |
| `submitblock "hexdata" (dummy)` | Attempts to submit a new block to the network |
| `prioritisetransaction "txid" (dummy) fee_delta` | Modifies transaction priority for mining (higher fee_delta = higher priority) |
| `generate nblocks (maxtries)` | Mines up to nblocks blocks immediately (requires wallet, regtest only) |
| `generatetoaddress nblocks "address" (maxtries)` | Mines blocks directly to a specified address |
| `getgenerate` | Returns whether the server is set to generate blocks |
| `setgeneratepow generate (genproclimit)` | Enables/disables proof-of-work mining and sets thread count |
| `setgeneratepos generate` | Enables/disables proof-of-stake mining (wallet must be unlocked) |

**Note**: For production staking, use `setgeneratepos true` with an encrypted and unlocked wallet. See [Mining & Staking Guide](/en/docs/node-mining/) for details.

---

## Raw Transactions

Create, decode, sign, and broadcast raw transactions.

### Creating Transactions

| Method | Description |
|--------|-------------|
| `createrawtransaction [{"txid":"id","vout":n},...] [{"address":amount},...] (locktime)` | Creates an unsigned raw transaction |
| `createpsbt [{"txid":"id","vout":n},...] [{"address":amount},...] (locktime)` | Creates an unsigned PSBT (Partially Signed Bitcoin Transaction) |
| `fundrawtransaction "hexstring" (options)` | Adds inputs to a raw transaction using wallet funds |

### Decoding Transactions

| Method | Description |
|--------|-------------|
| `decoderawtransaction "hexstring"` | Decodes a hex-encoded raw transaction and returns its details |
| `decodescript "hexstring"` | Decodes a hex-encoded script (redeem script, witness script) |
| `decodepsbt "psbt"` | Decodes and returns information about a PSBT |

### Signing Transactions

| Method | Description |
|--------|-------------|
| `signrawtransaction "hexstring" ([{"txid":"id","vout":n,"scriptPubKey":"hex","redeemScript":"hex"},...] ["privatekey",...] sighashtype)` | Signs a raw transaction (uses wallet if available) |
| `signrawtransactionwithkey "hexstring" ["privatekey",...] ([{"txid":"id","vout":n,...},...] sighashtype)` | Signs a raw transaction with provided private keys (no wallet needed) |
| `signrawtransactionwithwallet "hexstring" ([{"txid":"id","vout":n,...},...] sighashtype)` | Signs a raw transaction using wallet private keys |

### Broadcasting Transactions

| Method | Description |
|--------|-------------|
| `sendrawtransaction "hexstring" (allowhighfees)` | Broadcasts a signed raw transaction to the network |
| `testmempoolaccept ["rawtx",...] (allowhighfees)` | Tests whether transactions would be accepted by the mempool (does not broadcast) |

### PSBT Operations

| Method | Description |
|--------|-------------|
| `walletcreatefundedpsbt [{"txid":"id","vout":n},...] [{"address":amount},...] (locktime) (options) (bip32derivs)` | Creates and funds a PSBT using wallet UTXOs |
| `walletprocesspsbt "psbt" (sign) (sighashtype) (bip32derivs)` | Processes and optionally signs a PSBT using wallet keys |
| `combinepsbt ["psbt",...]` | Combines multiple partially signed PSBTs into one |
| `finalizepsbt "psbt" (extract)` | Finalizes a PSBT and optionally extracts the complete transaction |
| `converttopsbt "hexstring" (permitsigdata)` | Converts a raw transaction to PSBT format |

### Combining Transactions

| Method | Description |
|--------|-------------|
| `combinerawtransaction ["hexstring",...]` | Combines multiple partial raw transactions into one |

---

## Wallet Management

Manage wallet files, encryption, backups, and key pools.

### Wallet Operations

| Method | Description |
|--------|-------------|
| `getwalletinfo` | Returns general wallet information (balance, tx count, key pool, encryption status) |
| `listwallets` | Lists all currently loaded wallets |
| `createwallet "wallet_name" (disable_private_keys) (blank) (passphrase) (avoid_reuse)` | Creates a new wallet |
| `loadwallet "filename"` | Loads an existing wallet file into memory |
| `unloadwallet ("wallet_name")` | Unloads a wallet from memory (does not delete) |
| `backupwallet "destination"` | Backs up the wallet.dat file to the specified location |
| `rescanblockchain (start_height) (stop_height)` | Rescans the blockchain for wallet transactions |
| `abortrescan` | Aborts an ongoing blockchain rescan |

### Wallet Encryption & Security

| Method | Description |
|--------|-------------|
| `encryptwallet "passphrase"` | Encrypts the wallet with a passphrase (one-time operation, requires restart) |
| `walletpassphrase "passphrase" timeout` | Unlocks the wallet for a specified time (in seconds) |
| `walletpassphrasechange "oldpassphrase" "newpassphrase"` | Changes the wallet encryption passphrase |
| `walletlock` | Immediately locks an unlocked wallet |

**Important**: Always encrypt your wallet before staking. See [Security Best Practices](/en/docs/security/) for details.

### Key Pool Management

| Method | Description |
|--------|-------------|
| `keypoolrefill (newsize)` | Refills the key pool with new keys |
| `sethdseed (newkeypool) ("seed")` | Sets or generates a new HD wallet seed |

---

## Addresses & Labels

Generate and manage wallet addresses.

### Address Generation

| Method | Description |
|--------|-------------|
| `getnewaddress ("label") ("address_type")` | Generates a new receiving address with optional label |
| `getrawchangeaddress ("address_type")` | Returns a new address for receiving change |
| `validateaddress "address"` | Validates a TALER address and returns information about it |
| `getaddressinfo "address"` | Returns detailed information about an address in the wallet |

**Address Types**: `"legacy"`, `"p2sh-segwit"`, `"bech32"` (default depends on wallet configuration)

### Labels & Organization

| Method | Description |
|--------|-------------|
| `setlabel "address" "label"` | Sets or updates a label for an address |
| `getaddressesbylabel "label"` | Returns all addresses associated with a specific label |
| `listlabels ("purpose")` | Lists all address labels in the wallet |
| `listaddressgroupings` | Lists address groupings (addresses that are likely owned together) |

### Multi-Signature Addresses

| Method | Description |
|--------|-------------|
| `addmultisigaddress nrequired ["key",...] ("label") ("address_type")` | Creates a multi-signature address and adds it to the wallet |
| `createmultisig nrequired ["key",...]` | Creates a multi-signature redemption script (does not add to wallet) |

---

## Balances & Transaction History

Query wallet balances and transaction information.

### Balance Information

| Method | Description |
|--------|-------------|
| `getbalance ("dummy") (minconf) (include_watchonly)` | Returns the wallet's total available balance |
| `getunconfirmedbalance` | Returns the balance of unconfirmed transactions |
| `getreceivedbyaddress "address" (minconf)` | Returns the total amount received by a specific address |
| `getreceivedbylabel "label" (minconf)` | Returns the total amount received by addresses with a specific label |
| `listreceivedbyaddress (minconf) (include_empty) (include_watchonly) ("address_filter")` | Lists addresses with their received amounts |
| `listreceivedbylabel (minconf) (include_empty) (include_watchonly)` | Lists labels with their received amounts |

### Transaction History

| Method | Description |
|--------|-------------|
| `listtransactions ("label") (count) (skip) (include_watchonly)` | Lists wallet transactions with optional filtering |
| `gettransaction "txid" (include_watchonly)` | Returns detailed information about a wallet transaction |
| `listsinceblock ("blockhash") (target_confirmations) (include_watchonly) (include_removed)` | Lists transactions since a specified block |

---

## Sending Transactions

Send TALER coins from the wallet.

### Simple Sending

| Method | Description |
|--------|-------------|
| `sendtoaddress "address" amount ("comment") ("comment_to") (subtractfeefromamount)` | Sends an amount to a given address |
| `sendmany "" {"address":amount,...} (minconf) ("comment") ["address",...] (subtractfeefromamount)` | Sends to multiple addresses in one transaction |

**Example**:
```bash
taler-cli sendtoaddress "TFNxSh9X75WguDWRj4FbRELK8GUP37xu4E" 10.5
taler-cli sendmany "" '{"TAddr1":10,"TAddr2":5.5}'
```

### Transaction Management

| Method | Description |
|--------|-------------|
| `abandontransaction "txid"` | Marks a transaction as abandoned (allows coins to be respent) |
| `bumpfee "txid" (options)` | Increases the fee of a transaction using RBF (Replace-By-Fee) |
| `resendwallettransactions` | Immediately re-broadcasts unconfirmed wallet transactions |

---

## UTXO Management

Control which unspent outputs are available for spending.

| Method | Description |
|--------|-------------|
| `listunspent (minconf) (maxconf) (["address",...]) (include_unsafe) (query_options)` | Lists unspent transaction outputs (UTXOs) with optional filters |
| `lockunspent unlock ([{"txid":"id","vout":n},...])` | Locks or unlocks specific UTXOs to prevent spending |
| `listlockunspent` | Returns a list of all currently locked UTXOs |

**Example - Lock a UTXO**:
```bash
taler-cli lockunspent false '[{"txid":"abc123...","vout":0}]'
```

**Example - Unlock all UTXOs**:
```bash
taler-cli lockunspent true
```

---

## Importing & Exporting

Import and export keys, addresses, and wallet data.

### Importing

| Method | Description |
|--------|-------------|
| `importprivkey "privkey" ("label") (rescan)` | Imports a private key into the wallet |
| `importaddress "address" ("label") (rescan) (p2sh)` | Imports an address or script for watch-only monitoring |
| `importpubkey "pubkey" ("label") (rescan)` | Imports a public key for watch-only monitoring |
| `importwallet "filename"` | Imports keys from a wallet dump file |
| `importmulti '[{"scriptPubKey":{"address":"addr"},"timestamp":now,...},...]' (options)` | Imports multiple keys/addresses with metadata in one call |
| `importprunedfunds "rawtransaction" "txoutproof"` | Imports a transaction and proof (for pruned nodes) |
| `removeprunedfunds "txid"` | Removes a transaction imported via importprunedfunds |

### Exporting

| Method | Description |
|--------|-------------|
| `dumpprivkey "address"` | Exports the private key for an address in WIF format |
| `dumpwallet "filename"` | Exports all wallet keys and metadata to a human-readable file |

**Security Warning**: Never share exported private keys or wallet dumps. Anyone with access to private keys can spend your coins.

---

## Signing & Verifying Messages

Sign messages with wallet addresses and verify signatures.

| Method | Description |
|--------|-------------|
| `signmessage "address" "message"` | Signs a message with the private key of an address in the wallet |
| `signmessagewithprivkey "privkey" "message"` | Signs a message with a private key (no wallet needed) |
| `verifymessage "address" "signature" "message"` | Verifies a signed message |

**Example**:
```bash
# Sign a message
taler-cli signmessage "TFNxSh9X75WguDWRj4FbRELK8GUP37xu4E" "Hello TALER"

# Verify the signature
taler-cli verifymessage "TFNxSh9X75WguDWRj4FbRELK8GUP37xu4E" "H1234..." "Hello TALER"
```

---

## Staking (Proof-of-Stake)

Monitor and manage proof-of-stake minting/staking.

| Method | Description |
|--------|-------------|
| `listminting` | Lists minting/staking information including expected time to next stake |
| `setgeneratepos generate` | Enables/disables proof-of-stake staking (requires unlocked encrypted wallet) |
| `getmininginfo` | Returns mining and staking information (includes staking status) |

**For detailed staking setup instructions**, see [Mining & Staking Guide](/en/docs/node-mining/).

---

## Network Information

Query peer connections and network statistics.

### Peer Management

| Method | Description |
|--------|-------------|
| `getconnectioncount` | Returns the number of connections to other nodes |
| `getpeerinfo` | Returns detailed information about each connected peer |
| `addnode "node" "command"` | Adds, removes, or tries connecting to a peer (`"add"`, `"remove"`, `"onetry"`) |
| `disconnectnode "[address]" (nodeid)` | Disconnects from a specific peer by address or node ID |
| `getaddednodeinfo (node)` | Returns information about manually added nodes |
| `ping` | Sends a ping to all connected peers (measures latency) |

### Network Statistics

| Method | Description |
|--------|-------------|
| `getnetworkinfo` | Returns network information (version, connections, protocol, networks) |
| `getnettotals` | Returns network traffic statistics (total bytes sent/received) |

### Ban Management

| Method | Description |
|--------|-------------|
| `setban "subnet" "command" (bantime) (absolute)` | Bans or unbans an IP/subnet (`"add"` or `"remove"`) |
| `listbanned` | Lists all banned IPs/subnets with ban duration |
| `clearbanned` | Clears all banned IPs/subnets |

**Example - Ban an IP**:
```bash
taler-cli setban "192.168.1.100" "add" 86400
```

### Network Control

| Method | Description |
|--------|-------------|
| `setnetworkactive state` | Enables/disables all P2P network activity |

---

## Server Control & Information

Control the TALER daemon and query system information.

| Method | Description |
|--------|-------------|
| `help ("command")` | Lists all commands or returns help for a specific command |
| `stop` | Stops the TALER server gracefully |
| `uptime` | Returns the total server uptime in seconds |
| `getmemoryinfo ("mode")` | Returns memory usage statistics |
| `logging ("[include,...] <exclude>")` | Gets or sets logging categories for debugging |

**Example**:
```bash
# Get help for a command
taler-cli help getblockcount

# Check server uptime
taler-cli uptime

# Stop the daemon
taler-cli stop
```

---

## Deprecated Methods

The following methods are deprecated and maintained for backward compatibility only. They may be removed in future versions.

| Method | Replacement | Description |
|--------|-------------|-------------|
| `getinfo` | Use specific methods | Returns aggregated node information (use `getblockchaininfo`, `getnetworkinfo`, `getwalletinfo` instead) |
| `getaccount "address"` | `getaddressinfo` | Returns the account for an address |
| `setaccount "address" "account"` | `setlabel` | Sets the account for an address |
| `getaccountaddress "account"` | `getnewaddress` | Gets an address for an account |
| `getaddressesbyaccount "account"` | `getaddressesbylabel` | Lists addresses for an account |
| `listaccounts` | `listlabels` | Lists accounts and their balances |
| `getreceivedbyaccount "account"` | `getreceivedbylabel` | Returns total received by an account |
| `listreceivedbyaccount` | `listreceivedbylabel` | Lists accounts with received amounts |
| `sendfrom "account" "address" amount` | `sendtoaddress` | Sends from a specific account |
| `move "fromaccount" "toaccount" amount` | N/A | Moves funds between accounts (removed) |
| `addwitnessaddress "address"` | N/A | Adds a witness address (SegWit related) |

---

## Error Handling

RPC calls return errors in the following format:

```json
{
  "result": null,
  "error": {
    "code": -5,
    "message": "Invalid address"
  },
  "id": "curltest"
}
```

### Common Error Codes

| Code | Description |
|------|-------------|
| `-1` | General error |
| `-3` | Invalid parameter |
| `-5` | Invalid address or key |
| `-6` | Insufficient funds |
| `-8` | Insufficient priority |
| `-13` | Wallet unlock required |
| `-14` | Wallet passphrase incorrect |
| `-15` | Wallet encryption failure |
| `-17` | Database error |
| `-22` | Invalid parameter (too many) |
| `-32600` | Invalid JSON-RPC request |
| `-32601` | Method not found |

---

## RPC Authentication

TALER RPC uses HTTP Basic Authentication. Configure credentials in `taler.conf`:

```ini
rpcuser=yourusername
rpcpassword=yourpassword
```

**Security Best Practices**:
- Use a strong, randomly generated password
- Never expose RPC to the public internet
- Use `rpcallowip` to restrict access to specific IPs
- Consider using SSH tunneling for remote access
- Use HTTPS/TLS if exposing RPC remotely (requires reverse proxy)

---

## Additional Resources

- **Node Setup Guide**: [Running a TALER Node](/en/docs/node-setup/)
- **Command Line Reference**: [talerd & taler-cli](/en/docs/node-cli/)
- **Security Best Practices**: [Wallet Security](/en/docs/security/)
- **Staking Guide**: [Mining & Staking](/en/docs/node-mining/)
- **Docker Deployment**: [Docker Setup](/en/docs/node-docker/)

For questions or support, visit the [TALER Community](https://t.me/talercommunity) on Telegram.
