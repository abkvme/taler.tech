---
title: "Sending & Receiving"
weight: 3
---

This guide covers how to send and receive TALER coins using the GUI wallet application.

## Prerequisites

Before sending or receiving TALER:

- **Wallet Installed**: Complete the [Wallet Setup](/en/docs/wallet-setup/) guide
- **Blockchain Synced**: Wait for initial synchronization to complete
- **Wallet Created**: Your wallet should be created automatically on first launch

---

## Receiving TALER

### Generating a Receive Address

To receive TALER coins, you need to provide a payment address to the sender.

1. **Open the Receive Tab**:
   - Launch `taler-qt`
   - Click the **Receive** tab in the top navigation

2. **Generate New Address**:
   - Enter a label for this address (optional but recommended)
     - Example: "Payment from Alice", "Mining rewards", "Exchange deposit"
   - Click **Request payment** button
   - A new TALER address will be generated

3. **Copy the Address**:
   - The address appears in the format: `T...` (34 characters)
   - Click **Copy Address** to copy to clipboard
   - Or use the QR code for mobile scanning

### Understanding TALER Addresses

TALER addresses:
- Start with capital `T`
- Are exactly 34 characters long
- Are case-sensitive
- Each address can be reused, but generating new addresses improves privacy

**Example address format**:
```
TFNxSh9X75WguDWRj4FbRELK8GUP37xu4E
```

### Sharing Your Address

To receive payment, share your TALER address with the sender via:
- Copy and paste in messaging apps
- QR code scanning (for mobile wallets)
- Email or other communication channels

**Security Note**: TALER addresses are public information. Sharing them does not compromise your wallet security.

---

## Checking Received Transactions

### View Recent Transactions

1. **Open Overview Tab**:
   - The main dashboard shows recent transactions
   - Incoming transactions appear with a **green arrow** icon

2. **Open Transactions Tab**:
   - Click **Transactions** in the top navigation
   - View complete transaction history
   - Filter by type: All, Received, Sent, Mined

### Transaction Confirmations

When you receive TALER:
- **0 confirmations**: Transaction is pending (shown in gray)
- **1+ confirmations**: Transaction is being confirmed (shown in yellow)
- **6+ confirmations**: Transaction is fully confirmed (shown in green)

Each confirmation takes approximately 1 minute (one block).

**Recommended confirmations**:
- Small amounts: 1-3 confirmations
- Large amounts: 6+ confirmations
- Exchanges: 10+ confirmations (varies by exchange)

### Transaction Details

Click on any transaction to view details:
- **Status**: Confirmed or Pending
- **Date**: Transaction timestamp
- **Amount**: TLR received
- **Confirmations**: Number of blocks confirming the transaction
- **Transaction ID**: Unique identifier (txid)
- **Address**: The address that received funds

---

## Sending TALER

### Basic Send Transaction

1. **Open Send Tab**:
   - Launch `taler-qt`
   - Click the **Send** tab in the top navigation

2. **Enter Payment Details**:
   - **Pay To**: Enter recipient's TALER address
     - Address starts with `T`
     - Must be exact (case-sensitive)
   - **Label**: Optional description for your records
     - Example: "Payment to Bob", "Exchange withdrawal"
   - **Amount**: Enter amount to send in TLR
     - You can use decimal values (e.g., 0.5, 10.25)

3. **Transaction Fee**:
   - Fee is calculated automatically
   - Recommended fee shown by default
   - Higher fee = faster confirmation
   - Typical fee: 0.0001 - 0.001 TLR

4. **Send the Transaction**:
   - Review all details carefully
   - Click **Send** button
   - Confirm the transaction in the popup dialog
   - Enter wallet passphrase if wallet is encrypted

### Verifying Before Sending

**Always verify**:
- ✓ Recipient address is correct (double-check first and last characters)
- ✓ Amount is correct
- ✓ You have sufficient balance
- ✓ Transaction fee is reasonable

**Warning**: Cryptocurrency transactions are irreversible. Once sent, funds cannot be recovered if sent to wrong address.

---

## Advanced Send Options

### Coin Control

Coin Control allows you to manually select which coins (UTXOs) to spend in a transaction.

**When to use**:
- Consolidating small inputs
- Improving privacy by selecting specific coins
- Managing transaction fees

**How to enable**:
1. Go to **Settings** → **Options** → **Wallet**
2. Enable "Enable coin control features"
3. Return to **Send** tab
4. Click **Inputs** button to manually select coins

### Custom Transaction Fees

1. In the **Send** tab, look for fee options
2. Select fee priority:
   - **Low**: Slower confirmation (cheapest)
   - **Medium**: Standard confirmation (recommended)
   - **High**: Fast confirmation (higher fee)
3. Or enter custom fee manually (advanced users)

### Subtract Fee From Amount

When enabled, the transaction fee is deducted from the send amount rather than added on top.

**Example**:
- Send amount: 10 TLR
- Fee: 0.001 TLR
- **Without subtract**: Recipient gets 10 TLR, you pay 10.001 TLR
- **With subtract**: Recipient gets 9.999 TLR, you pay 10 TLR

**When to use**: Sending your entire balance or when recipient should receive exact amount minus fees.

---

## Transaction Status

### Pending Transactions

After sending, your transaction appears as pending until confirmed.

**Check status**:
1. Open **Transactions** tab
2. Pending transactions show 0 confirmations
3. Hover over status icon for details

**If transaction stays pending**:
- Transaction may be in mempool awaiting confirmation
- Network may be congested (wait longer)
- Fee may be too low (wait or use Replace-By-Fee if enabled)

### Transaction ID (TXID)

Every transaction has a unique Transaction ID (TXID).

**Uses**:
- Tracking transaction on block explorer
- Providing proof of payment
- Customer support inquiries

**How to find TXID**:
1. Open **Transactions** tab
2. Right-click on transaction
3. Select **Copy transaction ID**

**Block Explorer**:
Paste TXID in block explorer to view:
- [explorer.talercoin.org](https://explorer.talercoin.org/)
- [explorer.talercrypto.com](https://explorer.talercrypto.com/)

---

## Address Book

### Saving Addresses

Save frequently used addresses for quick access:

1. **From Send Tab**:
   - Enter address in "Pay To" field
   - Click **Add to address book** (+ icon)
   - Enter label for the address
   - Click **OK**

2. **From Address Book**:
   - Go to **File** → **Address Book** (or **Window** → **Address Book**)
   - Click **New** button
   - Enter label and address
   - Click **OK**

### Using Saved Addresses

1. In **Send** tab, click **Address Book** button
2. Select saved address from list
3. Address auto-fills in "Pay To" field

### Managing Address Book

**Edit address**:
- Open Address Book
- Right-click on address
- Select **Edit**

**Delete address**:
- Open Address Book
- Right-click on address
- Select **Delete**

---

## Multiple Recipients

Send TALER to multiple recipients in a single transaction:

1. In **Send** tab, enter first recipient details
2. Click **Add Recipient** button (+ icon)
3. Enter second recipient address and amount
4. Repeat for additional recipients
5. Review total amount and fee
6. Click **Send**

**Benefits**:
- Save on transaction fees (one fee for multiple payments)
- Atomic transaction (all recipients receive funds or none do)

---

## Security Best Practices

### Verify Addresses

- **Always double-check** recipient addresses
- Compare first 6 and last 6 characters at minimum
- Use QR codes when possible to avoid typos
- Test with small amount first for new recipients

### Transaction Privacy

- **Generate new addresses** for each transaction (improves privacy)
- Avoid address reuse when possible
- Use coin control to avoid linking addresses
- Consider transaction timing to protect privacy

### Wallet Encryption

If your wallet contains significant funds:

1. Go to **Settings** → **Encrypt Wallet**
2. Choose strong passphrase
3. **Backup passphrase** securely
4. You'll need passphrase for every outgoing transaction

**Warning**: If you forget your passphrase, you cannot access your funds. There is no password recovery.

### Backup Before Large Transactions

Before sending large amounts:
1. **Backup wallet.dat** file
2. Verify backup is accessible
3. Store backup securely offline

---

## Common Issues

### Insufficient Funds

**Error**: "Insufficient funds"

**Causes**:
- Balance not fully confirmed
- Transaction fee not included in calculation
- Pending outgoing transactions

**Solution**:
- Wait for confirmations on recent deposits
- Reduce send amount to account for fee
- Wait for pending transactions to confirm

### Invalid Address

**Error**: "Invalid TALER address"

**Causes**:
- Typo in address
- Wrong cryptocurrency address (Bitcoin, etc.)
- Extra spaces or characters

**Solution**:
- Copy address directly (don't type manually)
- Verify address format starts with `T`
- Check for extra spaces or line breaks

### Transaction Not Confirming

**Problem**: Transaction stuck with 0 confirmations for hours

**Causes**:
- Network congestion
- Fee too low
- Node not connected to network

**Solutions**:
1. **Wait longer**: Most transactions confirm within 10 minutes
2. **Check connections**: Ensure node has active peer connections
3. **Increase fee**: Use Replace-By-Fee (RBF) if enabled
4. **Contact support**: After 24+ hours, seek help in [community channels](/en/links/)

### Transaction Appears Twice

**Problem**: Same transaction shown multiple times

**Causes**:
- Display bug (rare)
- Transaction has multiple outputs (change)
- Transaction was replaced (RBF)

**Solution**:
- Check actual balance in **Overview** tab
- Verify on block explorer using TXID
- Restart wallet if display issue persists

---

## Transaction Fees Explained

### How Fees Work

- Fees are paid to miners/stakers who confirm transactions
- Fee based on transaction size (in bytes), not amount sent
- More inputs/outputs = larger transaction = higher fee

### Fee Estimation

The wallet automatically estimates appropriate fees based on:
- Current network congestion
- Recent block fees
- Desired confirmation speed

### When to Use Higher Fees

- Network is congested
- Need fast confirmation (exchange deposit, time-sensitive payment)
- Large transaction with many inputs

### When Lower Fees Are OK

- Not time-sensitive
- Network is quiet
- Small transaction value

---

## Next Steps

- **Mining & Staking**: Earn TALER rewards in [Mining & Staking](/en/docs/node-mining/)
- **Security**: Learn security best practices in [Security Guide](/en/docs/security/)
- **Troubleshooting**: Get help in [Community Channels](/en/links/)
