# Android Keystore Management & `keytool` Generation Handbook

This handbook details Android Keystore generation using Java `keytool`, RSA 4096-bit key length, PKCS12 format, `storepass` and `keypass` security, and Gradle configuration for **Android Keystores** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it details how to create and maintain release upload keystores safely.

This guide is **not**:

- an authorization mechanism to generate keystores using obsolete 1024-bit RSA keys
- a substitute for securing `gradle.properties` passwords
- a guide to committing raw `.keystore` binary files to git

---

# 1. Generating Release Keystore via `keytool`

Generate a secure release upload keystore using Java's native `keytool` CLI utility:

```bash
# Generate 4096-bit RSA Release Upload Keystore in PKCS12 format
keytool -genkeypair -v \
  -storetype PKCS12 \
  -keystore release-upload-key.keystore \
  -alias release-upload-alias \
  -keyalg RSA \
  -keysize 4096 \
  -validity 10000
```

---

# 2. Gradle Keystore Configuration (`android/app/build.gradle`)

Configure release signing in `android/app/build.gradle` using environment variables injected at build time:

```groovy
android {
    signingConfigs {
        release {
            if (project.hasProperty('MYAPP_RELEASE_STORE_FILE')) {
                storeFile file(MYAPP_RELEASE_STORE_FILE)
                storePassword MYAPP_RELEASE_STORE_PASSWORD
                keyAlias MYAPP_RELEASE_KEY_ALIAS
                keyPassword MYAPP_RELEASE_KEY_PASSWORD
            }
        }
    }
    buildTypes {
        release {
            signingConfig signingConfigs.release
            minifyEnabled true
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }
}
```

---

# 3. Operational Verification Checklist

- [ ] **PKCS12 Format Used**: Keystore created with `-storetype PKCS12`.
- [ ] **RSA 4096-Bit Generated**: Key algorithm set to RSA 4096-bit length.
- [ ] **Passwords Injected via ENV**: Passwords injected via environment variables, not hardcoded in `build.gradle`.

---

# 4. Official Sources

- Android App Signing Guide: https://developer.android.com/studio/publish/app-signing

---

**Last verified:** August 14, 2026

---

# Related documentation

### Android signing

- `signing/android/README.md`
- `signing/android/key-rotation.md`
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
