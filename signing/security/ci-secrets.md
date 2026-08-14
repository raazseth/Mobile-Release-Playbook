# CI/CD Pipeline Secret Injection Handbook

This handbook details CI/CD secret injection, GitHub Repository Secrets, EAS Secrets (`eas secret:create`), base64 keystore decoding in CI runners, and environment variable scoping for **CI Secrets** in Expo and React Native.

Engineered in alignment with **2026 platform specifications**, it details how to inject secrets into CI runners safely.

This guide is **not**:

- an authorization mechanism to print signing passwords in CI build logs
- a substitute for scoping secrets to specific environments (e.g. `production` vs `staging`)
- a guide to persisting decoded certificates on CI runner disk after build completion

---

# 1. CI Secret Injection & Decoding Pipeline

CI pipelines fetch base64-encoded secrets from repository environment variables, decode them ephemerally to temporary runner directories, execute the build, and purge temporary files upon step completion.

```text
┌────────────────────────────────────────────────────────┐
│             CI SECRET INJECTION PIPELINE               │
│                                                        │
│  [ GitHub Secrets / EAS Secret Vault ]                 │
│                         │                              │
│                         ▼ (Inject Base64 String)       │
│  [ CI Runner Environment: `$ANDROID_KEYSTORE_BASE64` ] │
│                         │                              │
│                         ▼ (Decode to Ephemeral Disk)   │
│  [ `echo "$ANDROID_KEYSTORE_BASE64" | base64 -d > /tmp/key.jks` ]│
│                         │                              │
│                         ▼ (Build & Archive Binary)     │
│  [ `./gradlew bundleRelease` ]                         │
│                         │                              │
│                         ▼ (Purge Temporary Secrets)    │
│  [ `rm -f /tmp/key.jks` ]                              │
└────────────────────────────────────────────────────────┘
```

---

# 2. Key EAS Secrets Commands (`eas secret`)

```bash
# Create encrypted secret in Expo EAS Build environment
eas secret:create --name ANDROID_KEYSTORE_PASSWORD --type string --value "SecurePass2026!" --scope project

# List active project secrets
eas secret:list
```

---

# 3. Operational Verification Checklist

- [ ] **Secrets Masked**: All signing secrets masked (`***`) in CI build logs.
- [ ] **Ephemeral Deletion**: Temporary decoded keystore files deleted upon build completion.
- [ ] **Environment Scope Applied**: Production secrets scoped strictly to `main` branch pipelines.

---

# 4. Official Sources

- Expo EAS Secrets Guide: https://docs.expo.dev/eas-build/variables/
- GitHub Actions Encrypted Secrets: https://docs.github.com/en/actions/security-for-github-actions/security-guides/using-secrets-in-github-actions

---

**Last verified:** August 14, 2026
