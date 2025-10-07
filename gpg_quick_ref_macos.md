# 🛡️ GPG Quick Reference (macOS)

## 1️⃣ Install GnuPG
```bash
brew install gnupg
gpg --version
```

---

## 2️⃣ Configure Terminal Pinentry (Recommended)
Use the terminal-based pinentry to avoid macOS GUI glitches when entering passphrases.

```bash
echo 'use-agent' >> ~/.gnupg/gpg.conf
echo 'pinentry-program /usr/local/bin/pinentry-mac' >> ~/.gnupg/gpg-agent.conf
echo 'allow-loopback-pinentry' >> ~/.gnupg/gpg-agent.conf
echo 'pinentry-mode loopback' >> ~/.gnupg/gpg.conf
gpgconf --kill gpg-agent
```

Alternatively, if `/usr/local/bin/pinentry-mac` causes passphrase mismatches, switch to the terminal interface:
```bash
echo 'pinentry-program /usr/local/bin/pinentry-curses' >> ~/.gnupg/gpg-agent.conf
gpgconf --kill gpg-agent
```

You can also run a command directly using the terminal pinentry:
```bash
export GPG_TTY=$(tty)
gpg --pinentry-mode loopback --full-generate-key
```

---

## 3️⃣ Generate a New Key (Signing + Encryption)
```bash
gpg --full-generate-key
```
**Recommended choices:**
- Type: `(9) ECC (sign and encrypt)`
- Curve: `ed25519` for signing / `cv25519` for encryption
- Expiration: `2y`
- Protect with a strong passphrase

This setup creates both signing and encryption subkeys automatically.

---

## 4️⃣ Add or Modify Subkeys (Manual Option)
```bash
gpg --edit-key your@email.com
addkey       # choose ECC → cv25519 (for encryption)
addkey       # choose ECC → ed25519 (for signing, optional if not already created)
save
```

---

## 5️⃣ List Keys
```bash
gpg --list-keys
gpg --list-secret-keys
```

---

## 6️⃣ Export Public Key (for Web Forms or Integration)
To share your public key safely (e.g., for encryption systems like HashiCorp Vault or similar tools):

```bash
gpg --armor --export your@email.com > mypublickey.asc
```

Or to display it directly in your terminal:
```bash
gpg --armor --export your@email.com
```

Copy **everything**, including the header and footer lines:
```
-----BEGIN PGP PUBLIC KEY BLOCK-----
... (many lines) ...
-----END PGP PUBLIC KEY BLOCK-----
```

Then paste this text block into the web form field labeled **PGP KEY 1**, as shown below.

![Screenshot showing where to paste public key](Screenshot%202025-10-07%20at%2011.20.31%E2%80%AFAM.png)

🧩 Only your **public key** should be pasted — never your private key.

---

## 7️⃣ Export Private Key (for backup only)
```bash
gpg --armor --export-secret-keys your@email.com > secretkey.asc
```
🔒 Store securely and never upload it anywhere.

---

## 8️⃣ Create Revocation Certificate
```bash
gpg --output revoke.asc --gen-revoke your@email.com
```
🗝️ Store `revoke.asc` safely (e.g., encrypted USB).

---

## 9️⃣ Remove a Key
Public key:
```bash
gpg --delete-key <KEYID>
```
Secret (private) key:
```bash
gpg --delete-secret-key <KEYID>
```

---

## 🔟 Publish / Share
Send to keyserver:
```bash
gpg --keyserver hkps://keys.openpgp.org --send-keys <KEYID>
```

---

## 1️⃣1️⃣ Encrypt / Decrypt / Sign / Verify
```bash
# Encrypt (creates file.txt.gpg)
gpg --encrypt --recipient your@email.com file.txt

# Decrypt (restores original file)
gpg --decrypt file.txt.gpg > file.txt

# Sign
gpg --armor --sign file.txt

# Verify
gpg --verify file.txt.asc
```

🗂️ Encrypted files will have a `.gpg` extension by default.

---

## 1️⃣2️⃣ Find Your Key ID
To view all keys and find your Key ID:
```bash
gpg --list-keys --keyid-format LONG
```
Example output:
```
pub   ed25519/3F4A7C2E9D123456 2025-10-07 [SC]
      1A2B3C4D5E6F7A8B9C0D1E2F3F4A7C2E9D123456
uid           [ultimate] John Doe <john@example.com>
sub   cv25519/7A1B2C3D4E5F6A7B 2025-10-07 [E]
```
- Short Key ID → `3F4A7C2E9D123456`
- Full fingerprint → `1A2B3C4D5E6F7A8B9C0D1E2F3F4A7C2E9D123456`

Get fingerprint (safer):
```bash
gpg --fingerprint
```
You can use either ID, but the **long fingerprint** is safest for editing, exporting, or deleting.

---

## 🔤 Key Capability Meanings

| Symbol | Meaning | What it lets you do | Typical Key Type |
|:-------:|:---------|:--------------------|:------------------|
| **S** | **Sign** | Sign files, commits, or messages to prove they came from you | Primary or signing subkey |
| **C** | **Certify** | Certify (authorize) your subkeys or other people’s keys | Primary key only |
| **E** | **Encrypt** | Encrypt data so only you can decrypt it | Encryption subkey |
| **A** | **Authenticate** | Used for login or SSH authentication | Authentication subkey (optional) |

### 💡 Quick Summary
- `[SC]` → Your **main key** can **Sign** and **Certify**.
- `[E]` → A **subkey** that handles **encryption**.
- `[A]` → Optional, used for **authentication**.

### 🧩 Analogy
Think of your GPG setup like a digital passport with specialized stamps:

| Role | Purpose |
|------|----------|
| **S** = Signature stamp | “Yes, I signed this document myself.” |
| **C** = Certification seal | “Yes, I vouch for this subkey’s authenticity.” |
| **E** = Envelope key | “Use me to lock (encrypt) the message so only I can open it.” |
| **A** = Access key | “Use me to prove my identity when logging in.” |

---

## 🔒 Recommended Curve Choices

| Curve | Description | Recommended Use |
|-------|--------------|-----------------|
| **Curve25519** | Modern, fast, trusted (default) | ✅ Best for most users |
| **NIST P-384** | Government-approved, slower | For compliance needs |
| **Brainpool P-256** | European, transparent | For privacy-conscious users |

---

## 🧩 Tips
- Backup `~/.gnupg/` and key exports securely.
- Renew key before expiry:
  ```bash
  gpg --edit-key <KEYID>
  expire
  save
  ```

---

**✅ Summary:**  
Use **Curve25519** (Ed25519/X25519).  
Always generate a **revocation certificate**, **export keys**, **backup securely**, and **use terminal pinentry** to avoid passphrase mismatches.  
Encrypted files will be saved as **filename.gpg** by default.

