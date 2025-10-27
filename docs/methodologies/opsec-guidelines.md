# Operational Security (OPSEC) Guidelines

> Protecting yourself, your team, and your operations in the offensive security world.

## 🛡️ Overview

OPSEC is critical for Red Team operators, security researchers, and anyone working with offensive security tools. This guide provides comprehensive OPSEC practices for S1BGr0up contributors and users.

---

## 📋 Table of Contents

1. [Core OPSEC Principles](#core-opsec-principles)
2. [Digital Identity Management](#digital-identity-management)
3. [Infrastructure Security](#infrastructure-security)
4. [Tool Usage Security](#tool-usage-security)
5. [Communication Security](#communication-security)
6. [Data Protection](#data-protection)
7. [Incident Response](#incident-response)

---

## 🎯 Core OPSEC Principles

### The Five Steps of OPSEC

1. **Identification of Critical Information**
   - What data must be protected?
   - Who are you protecting it from?
   - What could adversaries do with this information?

2. **Analysis of Threats**
   - Who wants your information?
   - What are their capabilities?
   - What are their intentions?

3. **Analysis of Vulnerabilities**
   - What weaknesses can adversaries exploit?
   - How are you exposing critical information?
   - What gaps exist in your defenses?

4. **Assessment of Risks**
   - What is the likelihood of compromise?
   - What is the potential impact?
   - Is the risk acceptable?

5. **Application of Countermeasures**
   - What controls will mitigate risks?
   - Are they cost-effective?
   - Do they address the actual threats?

---

## 🎭 Digital Identity Management

### Separate Identities

**Never mix personal and professional security work identities.**

#### Personal Identity
- Real name and information
- Personal social media
- Personal email and accounts
- Personal projects unrelated to security

#### Professional Security Identity
- Professional name (can be real or pseudonym)
- Security-focused social media
- Professional email
- Security research and projects

#### Anonymous/Operational Identity
- Pseudonym only
- No personal information
- Dedicated accounts
- Operational work only

### Account Security

```bash
# Good practices for operational accounts:
- Use unique, strong passwords (20+ characters)
- Enable 2FA/MFA on all accounts
- Use password manager (KeePass, Bitwarden)
- Never reuse passwords across identities
- Use hardware security keys when possible
```

### Email Security

**Recommended Providers for OPSEC**:
- ProtonMail (encrypted, privacy-focused)
- Tutanota (encrypted, based in Germany)
- Mailfence (encrypted, based in Belgium)

**Avoid**:
- Gmail, Outlook, Yahoo (metadata collection)
- Free providers requiring phone verification
- Email tied to real identity for operational work

---

## 🏗️ Infrastructure Security

### Operational Infrastructure

#### VPN/Proxy Chain

```
Your Machine → VPN 1 → VPN 2 → Proxy → Target
              (No logs)  (No logs)  (Rotating)
```

**Recommended VPN Providers** (No logs policy):
- Mullvad (accepts cash, no account required)
- IVPN (privacy-focused, no logs)
- ProtonVPN (secure, Swiss jurisdiction)

**Avoid**:
- Free VPNs (sell your data)
- VPNs in Five Eyes countries without court orders
- VPNs requiring extensive personal information

#### Dedicated Hardware

```
┌─────────────────────────────────┐
│  Personal Life                  │
│  └─ Personal Laptop             │
│     └─ Personal accounts        │
└─────────────────────────────────┘

┌─────────────────────────────────┐
│  Security Work                  │
│  └─ Dedicated Security Laptop   │
│     └─ Security accounts        │
│     └─ Encrypted disk           │
│     └─ Secure boot              │
└─────────────────────────────────┘

┌─────────────────────────────────┐
│  Operations (High Risk)         │
│  └─ Burner device               │
│     └─ Anonymous accounts       │
│     └─ Full disk encryption     │
│     └─ Tails/Whonix OS          │
└─────────────────────────────────┘
```

#### Operating System Security

**Recommended for Operations**:
- **Tails**: Amnesic OS (live USB, leaves no trace)
- **Whonix**: Tor-based OS (all traffic through Tor)
- **Qubes OS**: Security by compartmentalization
- **Hardened Linux**: Arch, Debian with security hardening

**Security Measures**:
```bash
# Full disk encryption
sudo cryptsetup luksFormat /dev/sdX

# Secure boot configuration
# MAC address randomization
# Firewall rules (default deny)
# Disable unnecessary services
# Regular updates
```

### Cloud Infrastructure

**For Red Team Operations**:
- Use cloud VPS with cryptocurrency payment
- Register with anonymous email
- No reuse across operations
- Destroy after engagement
- Use infrastructure-as-code for reproducibility

**Providers Accepting Crypto**:
- Njalla (privacy-focused)
- 1984 Hosting (Iceland, privacy-focused)
- Various bulletproof hosting (research carefully)

---

## 🔧 Tool Usage Security

### Development OPSEC

#### Code Repositories

```bash
# Remove metadata before commits
exiftool -all= file.jpg

# Sanitize Git history
git filter-branch --tree-filter 'git ls-files -z "*.jpg" | xargs -0 exiftool -all=' HEAD

# Check for secrets before commit
gitleaks detect --source . --verbose

# Use signed commits
git config --global user.signingkey YOUR_GPG_KEY
git config --global commit.gpgsign true
```

#### Build Security

```bash
# Verify tool signatures
gpg --verify tool.sig tool.tar.gz

# Use reproducible builds
# Check hashes
sha256sum tool.tar.gz

# Sandbox testing
# Use Docker/VMs for unknown tools
docker run --rm -it --network none alpine sh
```

### Tool Fingerprinting

**Problem**: Tools leave fingerprints that can identify you.

**Mitigations**:
- Modify default User-Agent strings
- Change default ports
- Customize tool behavior
- Use polymorphic/obfuscated payloads
- Randomize timing and patterns

```python
# Example: Randomize HTTP headers
headers = {
    'User-Agent': random.choice(user_agent_list),
    'Accept-Language': random.choice(languages),
    'Accept-Encoding': 'gzip, deflate'
}
```

### Network OPSEC

```bash
# Check for DNS leaks
dig @8.8.8.8 whoami.akamai.net

# Verify VPN connection
curl https://am.i.mullvad.net/json

# Test for WebRTC leaks
# (Use browser extension to disable WebRTC)

# Monitor network connections
netstat -tunapl | grep ESTABLISHED
```

---

## 💬 Communication Security

### Secure Messaging

**Recommended Platforms**:
- **Signal**: End-to-end encrypted, open source
- **Wire**: End-to-end encrypted, European
- **Element (Matrix)**: Federated, encrypted

**Configuration**:
```
- Enable disappearing messages
- Disable message preview
- Use registration lock
- Verify safety numbers
- Disable cloud backups (they're not encrypted)
```

**Avoid**:
- WhatsApp (owned by Meta)
- Telegram (not E2E by default)
- Discord (no E2E encryption)
- Slack (no E2E encryption)

### Email Encryption

```bash
# Generate GPG key pair
gpg --full-generate-key

# Export public key
gpg --armor --export your-email@protonmail.com

# Encrypt message
gpg --encrypt --armor --recipient recipient@email.com message.txt

# Decrypt message
gpg --decrypt message.txt.asc
```

### Metadata Minimization

**Remember**: Metadata tells a story.

**What metadata reveals**:
- Who you communicate with
- When you communicate
- Where you are located
- What devices you use
- Your activity patterns

**Mitigation**:
- Use Tor for anonymous browsing
- Use VPN before Tor for additional protection
- Avoid posting at predictable times
- Vary your communication patterns
- Use anonymous remailers for email

---

## 🗄️ Data Protection

### Encryption at Rest

```bash
# File encryption with GPG
gpg --symmetric --cipher-algo AES256 sensitive_file.txt

# Directory encryption with EncFS
encfs ~/.encrypted ~/encrypted

# Full disk encryption
# Use LUKS during Linux installation
```

### Secure Deletion

```bash
# Secure file deletion
shred -vfz -n 10 sensitive_file.txt

# Wipe free space
sfill -v /mount/point

# Memory wiping
sdmem -v
```

### Data Handling During Engagements

**Critical Rules**:
1. Never store real credentials in plain text
2. Encrypt all engagement data
3. Use secure note-taking (CherryTree, Obsidian with encryption)
4. Hash or redact sensitive information in reports
5. Securely delete temporary files after engagement

```bash
# Example: Secure note-taking setup
# Use CherryTree with password protection
cherrytree --export-to-html-dir /encrypted/notes /encrypted/notes.ctb
```

---

## 🚨 Incident Response

### If You're Compromised

#### Immediate Actions

1. **Disconnect from network** (physical disconnect if possible)
2. **Document everything** you remember
3. **Don't delete evidence** (for analysis)
4. **Notify affected parties** if applicable
5. **Assess damage** and information exposed

#### Recovery Steps

```bash
# 1. Change all passwords (from clean device)
# 2. Revoke and regenerate API keys
# 3. Review account activity
# 4. Enable additional 2FA
# 5. Check for unauthorized access

# 6. Forensic analysis
# Create disk image
dd if=/dev/sda of=~/compromised-disk.img bs=4M status=progress

# 7. Rebuild from clean OS
# Don't restore from potentially compromised backups
```

### Indicators of Compromise (IOCs)

**Watch for**:
- Unexpected network connections
- Unknown processes running
- Configuration changes
- New user accounts
- Unauthorized access logs
- Slow system performance
- Antivirus alerts

```bash
# Monitor processes
ps aux | grep -v "^USER"

# Check network connections
netstat -tunapl

# Review auth logs
sudo tail -f /var/log/auth.log

# Check user accounts
cat /etc/passwd
```

---

## 📊 OPSEC Checklist

### Daily Operations

- [ ] VPN/Proxy active before any security work
- [ ] Using correct identity for current activity
- [ ] No personal information in operational work
- [ ] Encrypted storage for sensitive data
- [ ] Secure communications only
- [ ] Browser in private mode or Tor Browser
- [ ] MAC address randomized (if needed)
- [ ] Location services disabled

### Before Engagement

- [ ] Dedicated infrastructure set up
- [ ] All tools tested in isolated environment
- [ ] Communication channels established
- [ ] Data handling procedures in place
- [ ] Emergency procedures defined
- [ ] Clean-up checklist prepared

### After Engagement

- [ ] All tools removed from target
- [ ] Persistence mechanisms removed
- [ ] Accounts deleted
- [ ] Logs cleaned (if authorized)
- [ ] Engagement data encrypted
- [ ] Infrastructure destroyed
- [ ] Tools securely deleted
- [ ] Report sanitized of sensitive data

---

## 🎓 Training Resources

### Courses
- "OPSEC Fundamentals" by SANS
- "Advanced OPSEC" by Offensive Security
- "Privacy and OPSEC" by Michael Bazzell

### Books
- "Extreme Privacy" by Michael Bazzell
- "The Art of Invisibility" by Kevin Mitnick
- "Digital Assassination" by Richard Loren

### Online Resources
- PrivacyTools.io
- PRISM Break
- EFF Surveillance Self-Defense

---

## ⚠️ Final Reminders

**OPSEC Failures Have Consequences**:
- Legal prosecution
- Loss of employment
- Reputation damage
- Team exposure
- Client data breach

**Stay Paranoid**:
- Trust no one completely
- Verify everything
- Assume compromise
- Plan for worst case
- Practice good hygiene daily

---

## 🤝 Contributing

Found OPSEC improvements or best practices to share? See [CONTRIBUTING.md](../../CONTRIBUTING.md).

---

```
[*] Your security is only as strong as your weakest OPSEC practice
[*] Stay paranoid. Stay private. Stay protected.
[*] S1BGr0up - Operational Security First
```

**Last Updated**: October 27, 2025
