# 🛡️ GPG Quick Reference (macOS)

## 1️⃣ Install GnuPG
```bash
brew install gnupg
gpg --version
```

---

## 2️⃣ Generate a New Key (Signing + Encryption)
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

## 3️⃣ Add or Modify Subkeys (Manual Option)
```bash
gpg --edit-key your@email.com
addkey       # choose ECC → cv25519 (for encryption)
addkey       # choose ECC → ed25519 (for signing, optional if not already created)
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
🗝️ Store `revoke.asc` safely (e.g., encrypted USB).

---

## 7️⃣ Remove a Key
Public key:
```bash
gpg --delete-key <KEYID>
```
Secret (private) key:
```bash
gpg --delete-secret-key <KEYID>
```

---

## 8️⃣ Publish / Share
Send to keyserver:
```bash
gpg --keyserver hkps://keys.openpgp.org --send-keys <KEYID>
```

---

## 9️⃣ Git Integration
```bash
gpg --list-secret-keys --keyid-format LONG
git config --global user.signingkey <LONG_KEYID>
git config --global commit.gpgSign true
```

---

## 🔟 Encrypt / Decrypt / Sign / Verify
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

## 1️⃣1️⃣ Find Your Key ID
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
Always generate a **revocation certificate**, **export keys**, **backup securely**, and **remove old keys** when rotating.