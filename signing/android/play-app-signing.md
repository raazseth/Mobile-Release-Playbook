# Google Play App Signing Architecture Handbook

This handbook details Google Play App Signing enrollment, Google Cloud HSM / KMS master key security, APK Signature Schemes v2, v3, and v4, and `.aab` bundle re-signing for **Google Play App Signing** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it details how Google Play App Signing protects application binaries.

This guide is **not**:

- an authorization mechanism to opt out of Google Play App Signing for new apps (mandatory since 2021)
- a substitute for uploading compiled Android App Bundles (`.aab`)
- a guide to signing production APKs manually

---

# 1. Google Play App Signing Architecture

Google Play App Signing stores the master App Signing Key inside Google's enterprise Cloud Key Management Service (KMS) and Hardware Security Modules (HSM).

```text
┌────────────────────────────────────────────────────────┐
│             GOOGLE PLAY APP SIGNING FLOW               │
│                                                        │
│  [ Developer Uploads `.aab` Signed with Upload Key ]   │
│                         │                              │
│                         ▼                              │
│  [ Google Play Verifies Upload Key Signature ]         │
│                         │                              │
│                         ▼                              │
│  [ Google Cloud HSM Signs Device APKs with App Key ]   │
│  (Applies APK Signature Schemes v2, v3, and v4)        │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ Optimized & Signed Device-Specific APKs Delivered to Users ]
```

---

# 2. Key APK Signature Schemes (2026)

- **Scheme v2 (APK Signature Scheme v2)**: Full-file signature scheme verifying entire APK binary against tampering.
- **Scheme v3 (APK Signature Scheme v3)**: Adds support for key rotation history in APK signature block.
- **Scheme v4 (APK Signature Scheme v4)**: Streaming signature scheme supporting incremental APK installation on Android 11+.

---

# 3. Operational Verification Checklist

- [ ] **Enrolled in Play App Signing**: Account verified enrolled in Google Play App Signing.
- [ ] **`.aab` Format Submitted**: Production builds submitted as Android App Bundles (`.aab`).
- [ ] **KMS Protection Active**: Master App Signing Key protected inside Google Cloud HSM.

---

# 4. Official Sources

- Google Play App Signing Architecture: https://developer.android.com/studio/publish/app-signing#google-play-app-signing

---

**Last verified:** August 14, 2026

---

# Related documentation

### Android signing

- `signing/android/README.md`
- `signing/android/key-rotation.md`
- `signing/android/keystore.md`
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
