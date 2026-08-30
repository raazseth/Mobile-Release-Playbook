# Android Upload Key & App Signing Key Rotation Protocol

This handbook details Android Upload Key rotation procedures, Google Play Console key reset requests using the Play Encrypted Private Key (PEPK) tool, and APK Signature Scheme v3 key rotation history for **Android Key Rotation**.

Engineered in alignment with **2026 platform specifications**, it details how to rotate Android signing keys safely.

This guide is **not**:

- an authorization mechanism to break update compatibility for existing installed users
- a substitute for using the Play Encrypted Private Key (PEPK) tool when migrating keys
- a guide to losing track of keystore backup credentials

---

# 1. PEPK Tool Export Pipeline for Key Migration

When registering a new app or upgrading an existing keystore to Google Play App Signing, developers use the **Play Encrypted Private Key (PEPK)** tool to encrypt the private key locally before uploading it to Google.

```text
┌────────────────────────────────────────────────────────┐
│             PEPK TOOL EXPORT PIPELINE                  │
│                                                        │
│  [ Local Keystore File (.keystore) ]                   │
│                         │                              │
│                         ▼ (PEPK Java CLI Tool)         │
│  [ Encrypted Output File (output.zip) ]                │
│    (Encrypted using Google Play Public Encryption Key) │
│                         │                              │
│                         ▼                              │
│  [ Upload `output.zip` to Google Play Console ]        │
└────────────────────────────────────────────────────────┘
```

---

# 2. Key PEPK CLI Execution Command

```bash
# Encrypt local app signing key using PEPK tool for Play App Signing
java -jar pepk.jar \
  --keystore=release-upload-key.keystore \
  --alias=release-upload-alias \
  --output=output.zip \
  --encryptionkey=[GOOGLE_PLAY_ENCRYPTION_KEY] \
  --include-cert
```

---

# 3. Operational Verification Checklist

- [ ] **PEPK Tool Executed**: Private key encrypted using official PEPK tool before upload.
- [ ] **Upload Key Reset Tested**: New Upload Key registered in Play Console verified functional.
- [ ] **Rotation History Preserved**: APK Signature Scheme v3 rotation history preserved.

---

# 4. Official Sources

- PEPK Tool Usage Guidance: https://support.google.com/googleplay/android-developer/answer/9842756

---

**Last verified:** August 14, 2026

---

# Related documentation

### Android signing

- `signing/android/README.md`
- `signing/android/keystore.md`
- `signing/android/play-app-signing.md`
- `signing/android/upload-key.md`

### iOS signing

- `signing/ios/README.md`

### Signing security

- `signing/security/secret-storage.md`
- `signing/security/recovery.md`

### Store accounts

- `store-accounts/google-play-console.md`

### Troubleshooting

- `troubleshooting/signing-fails.md`
