---
title: "Mining & Staking"
weight: 5
---

This guide covers how to earn TALER rewards through Proof-of-Stake (PoS) staking using the GUI wallet.

## Understanding TALER's Consensus

TALER uses a hybrid Proof-of-Work (PoW) and Proof-of-Stake (PoS) consensus mechanism:

- **Proof-of-Work (PoW)**: Miners generate blocks using computational power
- **Proof-of-Stake (PoS)**: Coin holders stake their coins to generate blocks

**This guide focuses on PoS staking**, which is the recommended method for most users as it:
- Requires no specialized hardware
- Consumes minimal electricity
- Allows you to earn passive rewards on your existing TALER holdings
- Helps secure the network

---

## What is PoS Staking?

### How Staking Works

Proof-of-Stake allows you to earn rewards by holding TALER coins in your wallet. Instead of using computational power like PoW mining, PoS uses:

- **Coin Age**: How long you've held coins
- **Coin Amount**: How many coins you hold
- **Probability**: Older and larger amounts have higher probability of staking

When your coins successfully stake, you create a new block and receive:
- **Block Reward**: Base PoS reward (30% of current PoW block subsidy)
- **Transaction Fees**: All fees from transactions in your block

### Staking Requirements

To stake TALER coins, you need:

1. **Minimum Amount**: At least 10 TLR per coin input
2. **Minimum Age**: Coins must mature for 48 hours after receiving them
3. **Encrypted Wallet**: Wallet must be encrypted for security
4. **Unlocked Wallet**: Wallet must be unlocked for staking
5. **Running Wallet**: taler-qt must be running and synced

**Important**: Coins with less than 10 TLR cannot stake. Coins are eligible to stake 48 hours after they are received and confirmed.

---

## Setting Up PoS Staking

### Step 1: Encrypt Your Wallet

**Why encrypt first?**
- Staking requires an encrypted wallet
- Protects your funds from unauthorized access
- Security best practice before staking

**How to encrypt**:

1. **Open taler-qt wallet**
2. **Go to Settings menu**
3. **Click "Encrypt Wallet"**
4. **Enter a strong passphrase**
   - Minimum 20+ characters recommended
   - Mix of letters, numbers, symbols
   - Write it down securely - **you cannot recover forgotten passphrases**
5. **Confirm passphrase**
6. **Wait for encryption to complete**

The wallet will restart after encryption.

**Already encrypted?** Skip to Step 2.

### Step 2: Fund Your Wallet

To stake, you need TALER coins in your wallet:

1. **Generate receiving address** in the Receive tab
2. **Transfer TALER** to your wallet from an exchange or another wallet
3. **Wait for confirmations** (6+ recommended)
4. **Wait 48 hours** for coins to mature

Your coins must age for 48 hours before they become eligible for staking.

### Step 3: Open Debug Console

The Debug Console allows you to execute commands to control staking.

**To access the Debug Console**:

1. **Open taler-qt wallet**
2. **Go to Help menu**
3. **Click "Debug Window"**
4. **Select the "Console" tab**

You'll see a command-line interface where you can type RPC commands.

### Step 4: Unlock Wallet for Staking

To enable staking, you must unlock your encrypted wallet using the console.

**Command**:
```
walletpassphrase "your-passphrase" timeout
```

**Parameters**:
- `"your-passphrase"`: Your wallet encryption passphrase (in quotes)
- `timeout`: Number of seconds to keep wallet unlocked

**Examples**:

```
walletpassphrase "MySecurePassphrase123!" 2592000
```
This unlocks the wallet for 2,592,000 seconds (30 days).

```
walletpassphrase "MySecurePassphrase123!" 31536000
```
This unlocks the wallet for 31,536,000 seconds (1 year).

**Timeout Guidelines**:
- **Short-term**: 86400 (1 day)
- **Medium-term**: 2592000 (30 days)
- **Long-term**: 31536000 (1 year)
- **Maximum**: 100000000 (~3.17 years)

**After unlocking**:
- Staking begins automatically
- Wallet will automatically re-lock after timeout expires
- You'll need to unlock again to continue staking after timeout

---

## Monitoring Your Staking

### Check Wallet Status

To verify your wallet is unlocked and ready for staking:

**Command**:
```
getwalletinfo
```

**Important Fields**:
- `"unlocked_until"`: Timestamp when wallet re-locks (0 = locked)
- `"balance"`: Your available balance
- `"immature_balance"`: Pending staking rewards (need confirmations)

**Example Output**:
```json
{
  "walletname": "",
  "walletversion": 169900,
  "balance": 1000.00000000,
  "immature_balance": 2.10000000,
  "unlocked_until": 1748362000,
  ...
}
```

If `unlocked_until` is 0, your wallet is locked - unlock it again with `walletpassphrase`.

### Check Available Coins for Staking

To see which coins are eligible for staking:

**Command**:
```
listunspent
```

This shows all unspent coins in your wallet with:
- `amount`: Coin value
- `confirmations`: Number of confirmations
- `spendable`: Whether coin can be spent/staked

**Staking Eligibility**:
- Amount >= 10 TLR
- Confirmations >= 288 blocks (~48 hours at 60 second blocks for PoW)
- Spendable = true

### Understanding Coin Age

**Coin Age determines staking weight**:

- **Minimum Age**: 48 hours (coins become eligible)
- **Maximum Age**: 90 days (coins reach maximum weight)
- **Age Tick**: 1 day (weight increases daily)

**How it works**:
- Day 0-2: Not eligible to stake
- Day 2-90: Staking weight increases daily
- Day 90+: Maximum weight reached (no further increase)

The longer you hold coins without spending, the higher your staking probability.

---

## Staking Rewards

### Reward Calculation

PoS staking rewards are based on:

1. **Coin Age**: How long you've held the coins (in coin-days)
2. **Coin Amount**: How many coins you're staking
3. **Block Subsidy**: Current PoS block reward (30% of PoW subsidy)
4. **Network Supply**: Total TALER supply

**Formula**:
```
Reward = (Coin Age × Coin-Year Reward Rate) + Transaction Fees
```

The reward is calculated automatically when you successfully stake a block.

### Expected Returns

**Typical Staking Rewards**:
- Base PoS block reward: 2.1 TLR (30% of 7 TLR PoW block)
- Plus transaction fees from the block
- Adjusted for coin age and network conditions

**Staking Frequency**:
- Depends on your coin amount and age
- Larger holdings stake more frequently
- Network difficulty affects all stakers equally

**Example**:
- 1,000 TLR staked
- 90 days old (maximum weight)
- May stake every few days to weeks
- Each stake earns ~2.1 TLR base reward

### When Rewards Appear

**After Staking a Block**:

1. **Immediately**: Reward appears in "Immature Balance"
2. **Maturation**: Requires 288 confirmations (~48 hours)
3. **Available**: After maturation, moves to available balance
4. **New Age**: Newly staked coins reset to 0 age, must mature 48 hours again

Check the **Transactions** tab to see staking rewards marked as "Mined" or "Generated".

---

## Staking Best Practices

### Keep Wallet Running

**Staking requires your wallet to be running**:
- Wallet must be open and synced
- Wallet must remain unlocked
- Computer should remain on (or use dedicated staking node)

**Tips**:
- Disable screen saver/sleep mode
- Consider running on dedicated hardware
- Use VPS or always-on computer for 24/7 staking

### Optimal Staking Setup

**For Best Results**:

1. **Keep Wallet Synced**: Ensure blockchain is fully synchronized
2. **Maintain Connection**: Stable internet connection required
3. **Consolidate Small Amounts**: Combine small coins into larger ones (10+ TLR each)
4. **Long Unlock Timeout**: Use 30+ day timeout to avoid frequent re-unlocking
5. **Regular Backups**: Backup wallet.dat before and after receiving rewards

### Coin Management

**Managing Your Staking Coins**:

- **Don't spend recently staked coins**: Let them age for better rewards
- **Consolidate when needed**: Merge small UTXOs into larger ones
- **Reserve some liquid coins**: Keep some coins for spending, others for staking
- **Monitor maturity**: Check immature balance for pending rewards

### Security While Staking

**Important Security Practices**:

- **Encrypted wallet required**: Never stake with unencrypted wallet
- **Strong passphrase**: Use 20+ character passphrase
- **Secure system**: Ensure computer is malware-free
- **Backup regularly**: Backup wallet.dat after receiving rewards
- **Limit remote access**: Don't expose RPC to public internet
- **Monitor activity**: Check transactions regularly for anomalies

---

## Troubleshooting Staking

### Wallet Not Staking

**Problem**: Coins not staking, no rewards

**Check**:

1. **Is wallet encrypted?**
   ```
   getwalletinfo
   ```
   Look for `"walletversion"` - should be encrypted

2. **Is wallet unlocked?**
   ```
   getwalletinfo
   ```
   Check `"unlocked_until"` - should be future timestamp, not 0

3. **Do coins meet requirements?**
   ```
   listunspent
   ```
   - Each coin >= 10 TLR?
   - Confirmations >= 288?
   - Age >= 48 hours?

4. **Is wallet synced?**
   ```
   getblockchaininfo
   ```
   Check if `"blocks"` equals `"headers"`

**Solutions**:
- Unlock wallet: `walletpassphrase "passphrase" 2592000`
- Wait for coin maturity (48 hours)
- Ensure wallet is fully synced
- Check you have eligible coins (>= 10 TLR each)

### Wallet Locked Unexpectedly

**Problem**: Wallet re-locked, staking stopped

**Cause**: Unlock timeout expired

**Solution**:
```
walletpassphrase "your-passphrase" 2592000
```

Set longer timeout to avoid frequent re-locking.

### Low Staking Frequency

**Problem**: Rarely staking blocks

**Causes**:
- Small coin amounts (< 100 TLR)
- Young coin age (< 7 days)
- High network difficulty

**Solutions**:
- **Accumulate more coins**: More coins = higher probability
- **Wait for age**: Coins gain weight over 90 days
- **Consolidate inputs**: Combine small amounts into larger ones
- **Be patient**: Staking is probabilistic, may take time

### Immature Balance Not Confirming

**Problem**: Immature balance stuck

**Cause**: PoS rewards require 288 confirmations (~48 hours)

**Solution**: Wait for confirmations. Check:
```
getblockchaininfo
```

If blockchain is synced and moving, immature balance will eventually mature.

---

## Advanced Staking

### Command-Line Staking

For advanced users running `talerd` (daemon without GUI):

**Start talerd with staking enabled**:
```bash
talerd -stakegen=1
```

**Unlock wallet**:
```bash
taler-cli walletpassphrase "your-passphrase" 2592000
```

**Monitor staking**:
```bash
taler-cli getwalletinfo
taler-cli listunspent
```

### Configuration File

Create `taler.conf` in your data directory:

**Windows**: `%APPDATA%\TALER\taler.conf`
**macOS**: `~/Taler/taler.conf`
**Linux**: `~/.taler/taler.conf`

**Example staking configuration**:
```ini
# Enable PoS staking (default: 1)
stakegen=1

# RPC for remote management (optional)
server=1
rpcuser=stakinguser
rpcpassword=your_secure_rpc_password
rpcallowip=127.0.0.1

# Network
listen=1
maxconnections=128
```

### Dedicated Staking Node

For serious stakers, run dedicated staking node:

1. **Use dedicated hardware**: VPS, Raspberry Pi, or always-on PC
2. **Install TALER**: Follow [Node Setup](/en/docs/node-setup/) guide
3. **Sync blockchain**: Wait for full synchronization
4. **Transfer staking coins**: Send coins to node wallet
5. **Unlock wallet**: Use long timeout (months/years)
6. **Monitor remotely**: Use RPC to check status

**Benefits**:
- 24/7 staking without keeping personal computer on
- Higher uptime = more staking opportunities
- Reduced electricity costs (vs mining)
- More reliable staking

---

## Understanding PoS vs PoW

### Comparison

| Feature | Proof-of-Stake (PoS) | Proof-of-Work (PoW) |
|---------|----------------------|---------------------|
| **Hardware** | Regular computer | Specialized mining hardware |
| **Energy** | Very low | Very high |
| **Entry Barrier** | 10 TLR minimum | Expensive ASIC miners |
| **Rewards** | 2.1 TLR per block | 4.9 TLR per block |
| **Block Time** | ~140 seconds | ~60 seconds |
| **Recommended For** | Most users | Professional miners |

### Why PoS is Recommended

**For Individual Users**:
- No expensive hardware required
- Earn passive rewards on holdings
- Helps secure network
- Low energy consumption
- Can run on personal computer

**Environmental Benefits**:
- Minimal electricity usage
- No electronic waste from obsolete ASICs
- Sustainable long-term

---

## Staking Economics

### Network Distribution

TALER block rewards are split:
- **70% to PoW miners**: 4.9 TLR per block
- **30% to PoS stakers**: 2.1 TLR per block

### Long-Term Strategy

**Staking as Investment**:
1. **Acquire TALER**: Buy from exchanges or earn through work
2. **Hold in wallet**: Transfer to personal wallet
3. **Enable staking**: Encrypt and unlock wallet
4. **Compound rewards**: Stake earned rewards for compounding
5. **Long-term growth**: Benefit from coin age and compounding

**Compounding Effect**:
- Staking rewards are automatically stakeable after 48 hours
- Larger balance = more frequent staking
- Rewards add to balance, increasing future rewards
- Compounding grows holdings over time

---

## Frequently Asked Questions

### Does staking lock my coins?

**No**. Coins are not locked during staking. You can spend them anytime, but:
- Spending resets coin age to 0
- You lose accumulated staking weight
- Coins need another 48 hours to re-stake

### Can I stake from multiple wallets?

**Yes**. Each wallet stakes independently. You can:
- Run multiple wallet instances on different computers
- Split coins across wallets for redundancy
- Each wallet competes for blocks independently

### Do I need to keep wallet open 24/7?

**For best results, yes**. Staking requires:
- Wallet running and synced
- Wallet unlocked
- Internet connection active

More uptime = more staking opportunities.

### What happens if I miss a staking opportunity?

**Nothing bad**. Staking is probabilistic:
- You'll have another chance next block
- Missed opportunities don't penalize you
- Coin age continues accumulating
- Just keep wallet running and unlocked

### Can I lose money staking?

**No risk of losing coins** through staking itself. However:
- Computer security risks (malware, theft)
- Forgotten passphrase = lost access
- No guarantees of rewards (probabilistic)

---

## Next Steps

- **Learn Security**: Review [Security Best Practices](/en/docs/security/)
- **Monitor Transactions**: Track rewards in [Sending & Receiving](/en/docs/sending-receiving/)
- **Community Support**: Get help in [Community Channels](/en/links/)

**Start staking today and help secure the TALER network while earning rewards!**
