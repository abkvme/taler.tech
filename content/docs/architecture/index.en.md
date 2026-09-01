---
title: "Architecture"
weight: 6
---

This document describes the technical architecture of the TALER Blockchain, covering consensus mechanics, economic model, network protocol, and storage internals. All details are derived from the TALER Blockchain source code.

## Overview

TALER Blockchain is a Bitcoin Core-based blockchain that implements a hybrid Proof-of-Work / Proof-of-Stake consensus model. Both PoW and PoS blocks coexist on the same chain, each governed by independent difficulty targets and block spacing rules.

- **Genesis date:** September 13, 2017
- **Codebase lineage:** Bitcoin Core (UTXO model, script system, peer protocol)
- **Block types:** PoW blocks and PoS blocks interleaved on one chain

## Consensus Mechanism

The chain accepts both PoW-mined and PoS-minted blocks. Each block type has its own difficulty track, spacing target, and reward schedule. A block is valid if it satisfies **either** the PoW or PoS validation rules — the chain does not require alternation or any fixed ratio between the two types.

| Property | PoW | PoS |
|---|---|---|
| Target block spacing | 60 seconds | 140 seconds |
| Difficulty adjustment | Dark Gravity Wave | Independent PoS target |
| Activation | Genesis (block 0) | Block 130,000 |

The chain tracks two height counters:

- **`nHeight`** — total block count (PoW + PoS)
- **`nPowHeight`** — PoW blocks only (used for subsidy halvings and PoW difficulty)

## Proof-of-Work

### Mining Algorithm

- **Blocks 0–9,999:** Scrypt (original launch algorithm)
- **Blocks 10,000+:** Lyra2Z — a memory-hard, ASIC-resistant hashing algorithm

### Difficulty Adjustment: Dark Gravity Wave

PoW difficulty is adjusted using the Dark Gravity Wave (DGW) algorithm, which recalculates difficulty based on a rolling window of recent PoW blocks:

| Version | Block Range | Window Size |
|---|---|---|
| DGW v1 | 10,000–249,999 | 24 PoW blocks |
| DGW v2 | 250,000+ | 120 PoW blocks |

Key properties:

- Adjustment is **clamped to 3x bounds** — difficulty cannot more than triple or drop below one-third per recalculation
- Post-TLRHeight (block 130,000), PoW difficulty retargets every **10 PoW blocks**
- Only PoW blocks are considered in the DGW calculation (PoS blocks are excluded)

## Proof-of-Stake

PoS was activated at **block 130,000** (the TLRHeight fork). Staking allows coin holders to mint new blocks without mining hardware.

### Staking Weight

The probability of minting a PoS block is proportional to a coin's **staking weight**:

```
weight = min(age_in_days, 99) × min(value_in_TLR, 9999)
```

- **Minimum stake age:** 2 days (coins must be unspent for at least 2 days)
- **Maximum weight age:** 90 days (age contribution caps at 90 days, with a hard cap at 99 days in the formula)
- **Maximum value contribution:** 9,999 TLR per UTXO

### Stake Modifier

The stake modifier provides randomness to PoS block selection, preventing predictable staking:

- Computed from a **64-block pseudo-random selection** of prior block hashes
- **Recalculated every 6 hours** (modifier interval)
- Mixes entropy from both PoW and PoS blocks

### PoS Kernel Validation

A PoS block is valid when the **kernel hash** (derived from the stake modifier, coin properties, and timestamp) falls below the PoS difficulty target — conceptually similar to PoW mining, but computed from coin ownership rather than computational work.

### PoS Block Requirements

A valid PoS block must contain:

1. A **coinstake transaction** at transaction index 1 (index 0 is always an empty coinbase)
2. A **block signature** authenticating the block producer's ownership of the staked coins

## Block Structure

### Header Format

Every block uses a standard **84-byte header**:

| Field | Size | Description |
|---|---|---|
| `nVersion` | 4 bytes | Block version and feature flags |
| `hashPrevBlock` | 32 bytes | Hash of the previous block |
| `hashMerkleRoot` | 32 bytes | Merkle root of all transactions |
| `nTime` | 4 bytes | Block timestamp (Unix epoch) |
| `nBits` | 4 bytes | Compact difficulty target |
| `nNonce` | 4 bytes | Mining nonce (PoW) or 0 (PoS) |

### PoS Block Flags

PoS blocks carry additional metadata flags:

- **`BLOCK_PROOF_OF_STAKE`** — identifies the block as PoS
- **`BLOCK_STAKE_ENTROPY`** — indicates entropy contribution to stake modifier
- **`BLOCK_STAKE_MODIFIER`** — indicates a new stake modifier was generated

### Block Signature

PoS blocks include a cryptographic signature appended after the transaction data, proving that the block producer controls the private key of the staked coins. A **new format flag** is used for post-fork serialization to distinguish the extended block format.

## Economic Model

### Supply Parameters

| Parameter | Value |
|---|---|
| Maximum supply | 23,333,333 TLR |
| Smallest unit | 1 satoshi = 0.00000001 TLR (10⁻⁸) |
| Genesis block premine | 2,333,283 TLR |
| Halving interval | Every 1,050,000 PoW blocks (~2.5 years) |

### Block Rewards

| Period | PoW Reward | PoS Reward | Notes |
|---|---|---|---|
| Pre-PoS (blocks 0–129,999) | 10 TLR | — | PoW only, original reward |
| Post-PoS activation | 7 TLR | 3 TLR | PoW reduced to 70%, PoS gets 30% |
| After 1st halving | 3.5 TLR | 1.5 TLR | |
| After 2nd halving | 1.75 TLR | 0.75 TLR | |
| After 3rd halving | 0.875 TLR | 0.375 TLR | |
| After 4th halving (current) | 0.4375 TLR | 0.1875 TLR | As of March 2026 |
| After 5th halving | 0.21875 TLR | 0.09375 TLR | Expected December 2027 |

The PoS subsidy is always calculated as: `PoS_reward = PoW_reward × 30 / 70`

### Current State (March 2026)

- **Halvings completed:** 4
- **Current PoW subsidy:** 0.4375 TLR
- **Current PoS subsidy:** 0.1875 TLR
- **Circulating supply:** ~19,491,107 TLR

## Transaction Model

TALER Blockchain uses the **UTXO (Unspent Transaction Output)** model inherited from Bitcoin:

- **SegWit support:** Activated at height 32,256 (BIP141/143/144)
- **Max block weight:** 4,000,000 weight units
- **Coinbase maturity:** 20 blocks (newly mined coins cannot be spent for 20 blocks)
- **Transaction format:** Standard Bitcoin transaction structure with witness data

## Address Types

| Type | Format | Identifier |
|---|---|---|
| P2PKH | Base58Check | Prefix byte 65 (addresses start with **T**) |
| P2SH | Base58Check | Prefix byte 50 |
| P2WPKH / P2WSH | Bech32 | HRP: **tlr** (e.g., `tlr1q...`) |
| Extended public keys | Base58 (BIP32) | Standard HD key derivation |

## Network Protocol

| Parameter | Value |
|---|---|
| Protocol version | 70020 |
| Default P2P port | 23153 |
| Message start bytes | `0x64 0xb1 0x73 0xd8` |
| DNS seed nodes | 6 |

### Service Flags

Nodes advertise capabilities via service flags:

- **`NODE_NETWORK`** — full block history available
- **`NODE_WITNESS`** — SegWit support
- **`NODE_BLOOM`** — bloom filter support (BIP37)

### Compact Block Relay

TALER Blockchain supports **BIP152 compact block relay** for efficient block propagation, reducing bandwidth by transmitting short transaction IDs instead of full transaction data.

## Fork History

| Height | Fork | Change |
|---|---|---|
| 0 | Genesis | Scrypt PoW, 50 TLR block reward |
| 10,000 | Fork 1 | Lyra2Z algorithm, Dark Gravity Wave v1 (24-block window) |
| 32,256 | — | BIP65, BIP66, CSV, SegWit activation |
| 130,000 | Fork 2 (TLRHeight) | PoS activation, PoW reward reduced to 7 TLR, 60s target blocks |
| 250,000 | Fork 3 | DGW v2 (120-block window), independent PoW/PoS difficulty tracking |

## Storage Layer

TALER Blockchain uses **LevelDB** for persistent storage:

| Database | Purpose |
|---|---|
| Block index | Maps block hashes to on-disk locations |
| Chainstate (UTXO set) | Current set of unspent transaction outputs |
| Address index (optional) | Maps addresses to transactions (for explorer functionality) |

### Block Data Files

- **`blk?????.dat`** — raw block data, up to 128 MB per file
- **`rev?????.dat`** — undo/revert data for block disconnection during reorgs

## BIP Support

TALER Blockchain implements the following Bitcoin Improvement Proposals:

| BIP | Description |
|---|---|
| BIP34 | Block height in coinbase |
| BIP65 | OP_CHECKLOCKTIMEVERIFY |
| BIP66 | Strict DER signature encoding |
| BIP68 | Relative lock-time using sequence numbers |
| BIP112 | OP_CHECKSEQUENCEVERIFY |
| BIP113 | Median time-past for lock-time calculations |
| BIP141 | Segregated Witness (consensus layer) |
| BIP143 | Transaction signature verification for SegWit |
| BIP144 | Segregated Witness (peer services) |
| BIP147 | Dealing with dummy stack element malleability |
| BIP152 | Compact block relay |
| BIP173 | Bech32 address encoding |
| BIP32 | Hierarchical deterministic wallets |
| BIP39 | Mnemonic recovery phrases (from wallet 0.20.0) |
| BIP44 | Multi-account hierarchy — registered coin type **1524** |

### Registered coin type (SLIP-44)

TALER Blockchain holds a permanently registered BIP-44 coin type:

| | |
|---|---|
| Coin type | **1524** |
| Hardened path component | `0x800005F4` |
| Derivation path | `m/44'/1524'/<account>'/<change>/<index>` |
| Registered | 18 December 2017 |
| Registry | [SLIP-0044 — Registered coin types for BIP-0044](https://github.com/satoshilabs/slips/blob/master/slip-0044.md) |

SLIP-0044 is the public registry that BIP-44 defers to for coin type numbers, maintained by
SatoshiLabs. An entry there is what stops two projects from deriving keys at the same path,
and it is the number wallets and recovery tools use to find a coin's accounts.

TALER's entry was added three months after the 13 September 2017 genesis block, so every
TALER wallet that follows BIP-44 — desktop or mobile — derives from `m/44'/1524'/…` and
recovers the same addresses from the same recovery phrase, on any implementation.

Note that GNU Taler, an unrelated payment system, has no SLIP-0044 entry; coin type 1524 is
this project's.

## Coin Specifications Quick Reference

| Specification | Value |
|---|---|
| Name | TALER Coin (TLR) |
| Type | Hybrid PoW/PoS |
| Algorithm | Lyra2Z (memory-hard) |
| Genesis date | September 13, 2017 |
| Max supply | 23,333,333 TLR |
| Genesis premine | 2,333,283 TLR |
| Current PoW reward | 0.4375 TLR |
| Current PoS reward | 0.1875 TLR |
| PoW block target | 60 seconds |
| PoS block target | 140 seconds |
| Halving interval | 1,050,000 PoW blocks |
| Coinbase maturity | 20 blocks |
| Min stake age | 2 days |
| Max block weight | 4,000,000 |
| Default port | 23153 |
| Address prefix | T (Base58), tlr (Bech32) |
| BIP44 coin type | 1524 (`0x800005F4`) |
| SegWit | Active (since height 32,256) |
