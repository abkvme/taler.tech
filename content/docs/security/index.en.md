---
title: "Security Best Practices"
weight: 4
---

This guide covers essential security practices for protecting your TALER wallet and assets.

## Understanding wallet.dat - Your Wallet's Source of Truth

### What is wallet.dat?

The `wallet.dat` file is the **only source of truth** for your TALER wallet. This Berkeley DB database file contains everything needed to access and spend your coins:

**Critical Data Stored**:
- **Private Keys**: Cryptographic keys that control your TALER coins
- **HD Wallet Seed**: Master seed for hierarchical deterministic key derivation
- **Transaction History**: Complete record of all wallet transactions
- **Address Book**: Saved addresses with labels
- **Key Metadata**: Creation times, derivation paths, key associations
- **Master Encryption Key**: Encrypted key material (when wallet is encrypted)

**File Location**:
- **Windows**: `%APPDATA%\TALER\wallet.dat`
- **macOS**: `~/Taler/wallet.dat`
- **Linux**: `~/.taler/wallet.dat`

### Why wallet.dat is Critical

**If you lose wallet.dat, you lose your coins permanently.**

- There is no "forgot password" recovery
- No company or authority can restore access
- Blockchain transactions are irreversible
- **Your coins = Your responsibility**

The wallet.dat file is both your greatest asset and your greatest vulnerability.

---

## Default Security State

### Wallet is NOT Encrypted by Default

**Important**: When you first create a TALER wallet, it is **completely unencrypted**.

This means:
- Private keys are stored in **plaintext** inside wallet.dat
- Anyone with access to your computer can copy wallet.dat
- If wallet.dat is stolen, your coins can be spent immediately
- No password is required to send transactions

**Action Required**: You must manually encrypt your wallet to protect it.

---

## Encrypting Your Wallet

### How to Encrypt Your Wallet

**Using taler-qt GUI**:

1. **Open taler-qt wallet**
2. **Go to Settings menu**
3. **Select "Encrypt Wallet"**
4. **Enter a strong passphrase** (see requirements below)
5. **Confirm passphrase**
6. **Wait for encryption process to complete**

The wallet will:
- Generate a random 32-byte master key
- Derive an encryption key from your passphrase using SHA-512
- Encrypt all private keys using AES-256-CBC
- Perform a complete database rewrite to remove unencrypted data
- Generate a new HD seed for future key derivation

**Using command line**:

```bash
taler-cli encryptwallet "your-strong-passphrase-here"
```

### Encryption Technical Details

**Algorithm**: AES-256-CBC (Advanced Encryption Standard, 256-bit key, Cipher Block Chaining mode)

**Key Derivation**:
- Method: SHA-512 based key derivation
- Iterations: Adaptive (minimum 25,000, typically ~100,000+)
- Salt: 8 random bytes per master key
- Performance-tuned to approximately 0.1 seconds per unlock

**Security Features**:
- Master key encrypted with passphrase-derived key
- Each private key encrypted with master key
- Database completely rewritten to eliminate slack space
- Secure memory handling with automatic memory clearing
- Initialization vectors derived from key material

### Choosing a Strong Passphrase

**Passphrase Requirements**:
- **Minimum**: 1 character (technically)
- **Recommended**: 20+ characters with high entropy
- **Best Practice**: Use a passphrase generator or Diceware method

**Passphrase Guidelines**:
- ✓ Use a long, random combination of words
- ✓ Include numbers, symbols, uppercase and lowercase
- ✓ Make it memorable but unpredictable
- ✓ Don't use personal information (names, birthdays)
- ✓ Don't use dictionary words alone
- ✗ Don't reuse passwords from other services
- ✗ Don't use short passwords (< 12 characters)

**Example Strong Passphrases**:
- `Correct-Horse-Battery-Staple-92!Purple`
- `My#Taler$Wallet%Has&8943*Coins`
- `7-Elephant-Dancing-@-Midnight-2025`

**Warning**: If you forget your passphrase, **your funds are permanently lost**. There is no recovery mechanism.

### After Encryption

Once encrypted, your wallet will:
- **Require passphrase** for sending transactions
- **Auto-lock** after a timeout period (default: never, but configurable)
- **Protect private keys** even if wallet.dat is stolen
- **Prevent unauthorized spending** of your coins

---

## Wallet Backup Strategies

### Why Backup is Essential

**Backup protects against**:
- Hard drive failure
- Computer loss or theft
- Accidental deletion
- File corruption
- Ransomware attacks

**Remember**: No backup = No recovery

### How to Backup wallet.dat

**Method 1: Manual File Backup** (Recommended)

1. **Close taler-qt wallet** (important!)
2. **Locate wallet.dat file**:
   - Windows: `%APPDATA%\TALER\wallet.dat`
   - macOS: `~/Taler/wallet.dat`
   - Linux: `~/.taler/wallet.dat`
3. **Copy wallet.dat to secure location**:
   - External USB drive (encrypted)
   - Encrypted cloud storage (VeraCrypt container, etc.)
   - Multiple physical locations for redundancy
4. **Verify backup** by checking file size and date

**Method 2: Using dumpwallet Command**

For unencrypted or unlocked wallets:

```bash
taler-cli dumpwallet "/path/to/backup/wallet-backup.txt"
```

This creates a text file with all private keys in WIF format.

**Method 3: HD Seed Backup**

If using HD wallet (default for new wallets):
- Backup the HD seed (displayed when wallet is created)
- All future keys can be derived from this seed
- Store seed securely offline (paper, metal backup)

### Backup Best Practices

**Frequency**:
- Backup **immediately** after encrypting wallet
- Backup after generating many new addresses
- Backup before major wallet operations
- Regular backups (weekly/monthly for active wallets)

**Storage Locations**:
- ✓ **Multiple copies** in different physical locations
- ✓ **Encrypted external drives** (USB, external SSD)
- ✓ **Offline storage** (air-gapped computer, safe deposit box)
- ✓ **Encrypted cloud storage** (encrypt first, then upload)
- ✗ **Never** store unencrypted backups in cloud services
- ✗ **Never** email wallet backups to yourself
- ✗ **Never** store on network-accessible drives

**Encryption for Backups**:
- Encrypt backup files before storing externally
- Use tools like VeraCrypt, GPG, or 7-Zip with AES-256
- Store encryption password separately from backup
- Consider hardware encrypted USB drives

### Testing Backups

**Verify your backup works**:

1. Create a test restore on a separate system
2. Copy backup to test location
3. Start wallet with test data directory
4. Verify addresses and balance match
5. **Do not send transactions from test restore**

Never trust a backup you haven't tested.

---

## Wallet Lock and Unlock

### Unlocking Your Wallet

After encryption, you'll need to unlock your wallet to send transactions.

**Using taler-qt GUI**:
- When sending transaction, passphrase prompt appears automatically
- Enter passphrase to sign transaction
- Wallet re-locks after timeout expires (if timeout set)

**Using command line**:

```bash
# Unlock wallet for 300 seconds (5 minutes)
taler-cli walletpassphrase "your-passphrase" 300

# Unlock wallet indefinitely (until manual lock or restart)
taler-cli walletpassphrase "your-passphrase" 0
```

**Timeout parameter**:
- Specifies seconds wallet stays unlocked
- Maximum: 100,000,000 seconds (~3.17 years)
- 0 = unlocked until daemon restart
- Wallet automatically re-locks after timeout expires

### Changing Your Passphrase

If you need to change your wallet passphrase:

**Using command line**:

```bash
taler-cli walletpassphrasechange "old-passphrase" "new-passphrase"
```

**Process**:
- Verifies old passphrase
- Re-encrypts all keys with new passphrase
- Updates master key store
- **Backup wallet after changing passphrase**

**When to change passphrase**:
- Suspicion of compromise
- Passphrase was weak
- Shared with someone previously
- Regular security practice (annually)

---

## File System Security

### Protecting wallet.dat on Disk

**File Permissions** (Linux/macOS):

```bash
# Set restrictive permissions on wallet.dat
chmod 600 ~/.taler/wallet.dat

# Ensure wallet directory is private
chmod 700 ~/.taler
```

This ensures only your user account can read/write the wallet.

**Windows**:
- Right-click wallet.dat → Properties → Security
- Remove all users except your account
- Set permissions to Read & Write only for your user

### Secure Deletion

If you need to delete old wallet backups:

**Linux**:
```bash
shred -vfz -n 10 old-wallet-backup.dat
```

**macOS**:
```bash
srm -v old-wallet-backup.dat
```

**Windows**:
Use tools like SDelete or Eraser to securely wipe files.

**Never** use standard delete for wallet files - they can be recovered.

---

## Additional Security Measures

### Antivirus and Anti-Malware

**Risks**:
- Keyloggers can capture your passphrase
- Screen capture malware can steal wallet contents
- Clipboard hijackers can replace addresses
- Ransomware can encrypt your wallet.dat

**Protection**:
- Keep antivirus software updated
- Use reputable security software
- Scan system regularly
- Be cautious of unknown downloads
- Don't click suspicious email attachments

### Network Security

**RPC Interface Security**:

If running `talerd` with RPC enabled:

```ini
# In taler.conf
rpcuser=your_username
rpcpassword=your_strong_password
rpcallowip=127.0.0.1  # Only localhost
```

**Never expose RPC to public internet**:
- Bind RPC to localhost only (`rpcbind=127.0.0.1`)
- Use firewall to block external RPC access
- Use strong RPC credentials
- Don't use default passwords

### Wallet Encryption vs Disk Encryption

**Defense in Depth**:
- Wallet encryption protects if wallet.dat is stolen
- Disk encryption (BitLocker, FileVault, LUKS) protects entire system
- **Use both** for maximum security

**Disk Encryption Benefits**:
- Protects against physical theft
- Encrypts all files, not just wallet
- Protects debug logs and temporary files
- Required if laptop travels with you

### Cold Storage for Large Holdings

For significant amounts of TALER:

1. **Create offline wallet** on air-gapped computer
2. **Generate receiving address** offline
3. **Transfer coins to cold wallet** address
4. **Backup wallet.dat and HD seed** to multiple secure locations
5. **Store backup in physical safe** or safe deposit box
6. **Never connect cold wallet computer to internet**

**Spending from cold storage**:
- Create unsigned transaction on online computer
- Transfer to offline computer via USB
- Sign transaction offline
- Transfer signed transaction back to online computer
- Broadcast to network

---

## Database Security Considerations

### Berkeley DB Private Mode

TALER wallet uses Berkeley DB with private mode enabled by default (`-privdb` flag).

**What this means**:
- Database files are not accessible via shared memory
- Prevents other processes from reading wallet data
- Improves privacy and security

### Database Slack Space

**Security Feature**: When you encrypt your wallet, TALER performs a complete database rewrite.

**Why this matters**:
- Berkeley DB may leave unencrypted data in "slack space"
- Slack space = unused areas of database file
- Complete rewrite eliminates this risk
- Unencrypted keys are not recoverable after encryption

### Database Lock File

TALER creates `.walletlock` file to prevent simultaneous access:
- Prevents database corruption
- Ensures only one process accesses wallet
- If you see "wallet already in use" error, another process is running

---

## Debug Logging Security

### Log File Considerations

Debug logs may contain sensitive information:
- Transaction details
- Address information
- Connection data
- Timing information

**Log Location**:
- **Windows**: `%APPDATA%\TALER\debug.log`
- **macOS**: `~/Taler/debug.log`
- **Linux**: `~/.taler/debug.log`

**Best Practices**:
- Don't share debug logs publicly without sanitizing
- Restrict file permissions on debug.log
- Disable verbose logging if not needed (`-debug=0`)
- Clear old logs periodically

---

## Security Checklist

### Essential Security Steps

- [ ] **Encrypt your wallet** with a strong passphrase
- [ ] **Backup wallet.dat** to multiple secure locations
- [ ] **Test your backup** by restoring on test system
- [ ] **Write down HD seed** (if using HD wallet)
- [ ] **Set restrictive file permissions** on wallet.dat (600)
- [ ] **Enable disk encryption** (BitLocker/FileVault/LUKS)
- [ ] **Use strong RPC credentials** if running daemon
- [ ] **Keep system and antivirus updated**
- [ ] **Never share passphrase** with anyone

### Regular Security Maintenance

**Monthly**:
- [ ] Create fresh wallet.dat backup
- [ ] Verify existing backups are accessible
- [ ] Check for system updates and patches
- [ ] Review active RPC connections (if applicable)

**Annually**:
- [ ] Consider changing wallet passphrase
- [ ] Audit backup locations and security
- [ ] Test wallet restore procedure
- [ ] Review overall security setup

---

## Common Security Mistakes

### Don't Do This

**❌ Storing wallet on cloud sync folders**:
- Dropbox, Google Drive, iCloud, OneDrive
- Can sync corrupted files during write
- Exposes wallet.dat to cloud service
- Sync conflicts can corrupt wallet

**❌ Using weak passphrases**:
- Short passwords (< 12 characters)
- Dictionary words alone
- Personal information (birthday, pet name)
- Common passwords (Password123, etc.)

**❌ Sharing wallet.dat**:
- Multiple people using same wallet
- Sending wallet.dat via email/chat
- Storing unencrypted on shared drives

**❌ Ignoring backups**:
- "I'll backup later" mentality
- Single backup location
- Never testing backups
- Old backups (missing recent keys)

**❌ Leaving wallet unlocked indefinitely**:
- Unlocking with timeout=0 for convenience
- Long timeout periods on shared/public computers

---

## What to Do if Compromised

### If You Suspect Wallet Compromise

**Immediate Actions**:

1. **Create new secure wallet** on clean system
2. **Transfer all funds** to new wallet immediately
3. **Disconnect compromised computer** from network
4. **Change all passphrases** and passwords
5. **Scan system** for malware thoroughly
6. **Rebuild compromised system** from clean OS install

### If Wallet is Lost or Stolen

**If you have backup**:
1. Restore wallet.dat from backup
2. Create new wallet on secure system
3. Transfer all funds to new wallet
4. Abandon old wallet

**If you don't have backup**:
- Your funds are **permanently lost**
- No recovery is possible
- Learn from this experience
- Always backup future wallets

---

## Advanced Security Topics

### Multi-Signature Wallets

For organizational use or enhanced security:
- Require multiple signatures to authorize transactions
- Protects against single key compromise
- Useful for business/treasury management
- Requires careful setup and key management

### Hardware Wallets

Currently TALER does not have dedicated hardware wallet support, but you can:
- Use air-gapped computer as cold storage
- Store wallet.dat on encrypted hardware device
- Use hardware encrypted USB drives for backups

### Watch-Only Wallets

Create watch-only wallet for monitoring balance without spending ability:
- Import addresses without private keys
- Monitor incoming transactions
- Safe to use on internet-connected systems
- Cannot send transactions

---

## Getting Help

### Security Questions

If you have security concerns:
- **DO NOT** share your passphrase or private keys
- **DO NOT** post wallet.dat publicly
- **DO NOT** share debug logs without sanitizing

For help with security:
- Visit [Community Channels](/en/links/)
- Ask general questions without revealing sensitive details
- Consult cryptocurrency security experts

---

## Summary

**Key Takeaways**:

1. **wallet.dat is everything** - lose it, lose your coins
2. **Not encrypted by default** - you must encrypt manually
3. **Strong passphrase required** - 20+ characters recommended
4. **Backup multiple locations** - test your backups
5. **Use unlock timeouts** - wallet auto-locks after timeout
6. **Defense in depth** - use multiple security layers
7. **Your responsibility** - no company can recover lost wallets

**Security is a process, not a product**. Stay vigilant, stay secure.
