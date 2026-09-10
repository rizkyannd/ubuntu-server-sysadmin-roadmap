# Setup & Manage SSH Keys

## 🧭 Context
I performed this exercise to transition from traditional password-based SSH authentication to a more secure key-pair configuration (public/private key). The primary objective was to enforce public key authentication, secure distant management channels, and establish best practices for managing server access control between a Windows client host and an Ubuntu Server environment.

## 🛠️ Environment
- **Algorithm:** ED25519
- **Client Host:** Windows 11
- **Server:** Ubuntu Server (VirtualBox VM)
- **Protocols & Tools:** SSH, `ssh-keygen`, OpenSSH

## 📋 Hands-On Practice

**1. Generated ED25519 SSH key pair on client host:**
```bash
ssh-keygen -t ed25519 -C "label_nama"
```
Opted for ED25519 over RSA for superior cryptographic performance and shorter key length.

**2. Retrieved public key contents:**
```cmd
type id_ed25519.pub
```

**3. Established initial connection to target server:**
```bash
ssh username@ipaddress
```

**4. Configured target server authorized identities:**
```bash
nano ~/.ssh/authorized_keys
```
Appended the generated public key string into `authorized_keys` and ensured proper file/directory permission boundaries (`700` for `~/.ssh` and `600` for `authorized_keys`).

## ⚙️ Verification
Attempted session initiation via SSH key pair: **Success**. Authenticated seamlessly without requiring user account password input, confirming effective identity delegation via private key.

## 🧩 Key Takeaways

**Cryptographic Selection & Hardening**

Choosing ED25519 ensures resilience against modern key cracking techniques while keeping connection handshakes efficient. While key-based login was successfully implemented, hardening the SSH daemon (`/etc/ssh/sshd_config`) by explicitly disabling password authentication (`PasswordAuthentication no`) and disabling root login (`PermitRootLogin no`) is a critical next step to completely eliminate brute-force attack vectors.

## 📸 Screenshots

**1. Public key deployment & SSH key authentication verification:**
<img width="960" height="891" alt="image" src="https://github.com/user-attachments/assets/25ab69c1-3df0-46de-af26-4205c09ed22e" />

*Execution log demonstrating key generation on Windows host, placement in `authorized_keys`, and passwordless authentication upon server login.*
