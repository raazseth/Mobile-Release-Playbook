# Android Code Signing & Keystore Infrastructure Handbook

This directory contains production handbook guides, Android Keystore generation, Google Play App Signing architecture, Upload Key management, and key rotation protocols for **Android Code Signing** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it details how to manage Android signing keys securely for local builds and automated Play Store distribution.

This guide is **not**:

- an authorization mechanism to store un-encrypted `.keystore` files in public git repositories
- a substitute for using Google Play App Signing
- a guide to using weak 1024-bit RSA signing keys

---

# 1. Android Code Signing Architecture

Android code signing uses a dual-key architecture: an **Upload Key** managed by developers to authenticate uploads to Google Play, and an **App Signing Key** managed by Google Cloud HSM to sign production `.aab` binaries distributed to end-user devices.

```text
┌────────────────────────────────────────────────────────┐
│             ANDROID DUAL-KEY SIGNING ARCHITECTURE      │
│                                                        │
│  [ Release Upload Keystore (.keystore / .jks) ]        │
│    - Managed by Developer / CI secret vault            │
│    - Signs `.aab` package submitted to Play Console    │
│                         │                              │
│                         ▼                              │
│  [ Google Play App Signing (Google Cloud HSM / KMS) ] │
│    - Master App Signing Key securely stored by Google  │
│    - Signs production APKs generated from `.aab`       │
└────────────────────────────────────────────────────────┘
```

---

# 2. Subsystem Directory Taxonomy

| Handbook File | Core Android Signing Scope | Key Commands & Rules |
|---|---|---|
| **[README.md](README.md)** | Subsystem index and Android signing architecture. | Dual-key architecture overview. |
| **[keystore.md](keystore.md)** | Android Keystore generation, PKCS12 format, RSA 4096-bit keys. | `keytool -genkeypair`, `storepass`, `keypass`. |
| **[upload-key.md](upload-key.md)** | Google Play Upload Key registration and reset procedures. | Upload key registration, Play Console reset. |
| **[play-app-signing.md](play-app-signing.md)** | Google Play App Signing, KMS key management, V2/V3/V4 schemes. | Google Cloud HSM, APK Signature Schemes. |
| **[key-rotation.md](key-rotation.md)** | Upload Key reset & APK Signature Scheme v3 key rotation. | PEPK tool key export, Play reset requests. |

---

# 3. Operational Verification Checklist

- [ ] **Google Play App Signing Enrolled**: App enrolled in Google Play App Signing.
- [ ] **Upload Keystore Encrypted**: Upload keystore stored in base64 format inside CI secret Manager.
- [ ] **RSA 4096-Bit Key Used**: Keystore generated with RSA 4096-bit key length and 10,000-day validity.

---

# 4. Official Sources

- Android App Signing Overview: https://developer.android.com/studio/publish/app-signing
- Google Play App Signing Help: https://support.google.com/googleplay/android-developer/answer/9842756

---

**Last verified:** August 14, 2026

---

# Related documentation

### Android signing

- `signing/android/key-rotation.md`
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
