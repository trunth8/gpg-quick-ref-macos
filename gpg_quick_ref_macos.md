# 🛡️ GPG Quick Reference (macOS)

## 1️⃣ Install GnuPG
```bash
brew install gnupg
gpg --version
```

---

## 2️⃣ Generate a New Key
```bash
gpg --full-generate-key
```
**Recommended choices:**
- Type: `(9) ECC (sign only)`
- Curve: `ed25519` (Curve25519)
- Expiration: `2y`
- Protect with a strong passphrase

---

## 3️⃣ Add Encryption Subkey
```bash
gpg --edit-key your@email.com
addkey       # choose ECC → cv25519 (for encryption)
save
```

---

## 4️⃣ List Keys
```bash
gpg --list-keys
gpg --list-secret-keys
```

---

## 5️⃣ Export Keys
Public (safe to share):
```bash
gpg --armor --export your@email.com > publickey.asc
```

Private (keep offline & secure):
```bash
gpg --armor --export-secret-keys your@email.com > secretkey.asc
```

---

## 6️⃣ Create Revocation Certificate
```bash
gpg --output revoke.asc --gen-revoke your@email.com
```
🔑 Store `revoke.asc` safely (e.g., encrypted USB).

---

## 7️⃣ Publish / Share
Send to keyserver:
```bash
gpg --keyserver hkps://keys.openpgp.org --send-keys <KEYID>
```

---

## 8️⃣ Git Integration
```bash
gpg --list-secret-keys --keyid-format LONG
git config --global user.signingkey <LONG_KEYID>
git config --global commit.gpgSign true
```

---

## 9️⃣ Encrypt / Decrypt / Sign / Verify
```bash
# Encrypt
gpg --encrypt --recipient your@email.com file.txt

# Decrypt
gpg --decrypt file.txt.gpg > file.txt

# Sign
gpg --armor --sign file.txt

# Verify
gpg --verify file.txt.asc
```

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
Always generate a **revocation certificate**, **export keys**, and **back them up securely**.

