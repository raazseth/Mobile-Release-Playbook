# Cryptographic Secret Storage & Security Infrastructure Handbook

This directory contains production handbook guides, enterprise secret vault integration (HashiCorp Vault, AWS Secrets Manager, GCP Secret Manager), CI/CD secret injection, Role-Based Access Control (RBAC), and emergency key recovery plans for **Signing Security** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it details how to protect mobile signing credentials across development, build, and distribution environments.

This guide is **not**:

- an authorization mechanism to store plain-text secrets in repository files
- a substitute for using enterprise hardware security modules or secret managers
- a guide to sharing un-encrypted API keys via chat channels

---

# 1. Cryptographic Secret Security Architecture

Mobile code signing security enforces strict isolation between developer environments, secret storage vaults, and automated build runners.

```text
┌────────────────────────────────────────────────────────┐
│             CRYPTOGRAPHIC SECRET VAULT PIPELINE        │
│                                                        │
│  [ Enterprise Secret Vault (Vault / AWS / GCP) ]       │
│                         │                              │
│                         ▼ (Scoped Temporary JWT Token) │
│  [ CI/CD Build Runner (EAS Build / GitHub Actions) ]   │
│                         │                              │
│                         ▼ (Ephemeral In-Memory Injection)│
│  [ Xcode Archiver / Gradle Compiler Build Process ]    │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ Zero Credentials Persisted to Disk or Saved in Build Logs ]
```

---

# 2. Subsystem Directory Taxonomy

| Handbook File | Core Security Scope | Key Vault Tools & Rules |
|---|---|---|
| **[README.md](README.md)** | Subsystem index and secret security architecture. | High-level security pipeline. |
| **[secret-storage.md](secret-storage.md)** | Enterprise secret storage vaults, encryption at rest. | HashiCorp Vault, AWS Secrets, GCP Secret Manager. |
| **[ci-secrets.md](ci-secrets.md)** | CI/CD secret injection, base64 keystore encoding. | GitHub Secrets, EAS Secrets, environment scope. |
| **[access-control.md](access-control.md)** | Role-Based Access Control (RBAC) & Hardware Security Keys. | Admin vs Developer roles, YubiKey FIDO2 2FA. |
| **[recovery.md](recovery.md)** | Emergency key recovery and compromise containment plan. | Key revocation, incident containment protocols. |

---

# 3. Operational Verification Checklist

- [ ] **Secret Manager Active**: Production signing keys stored in enterprise secret manager.
- [ ] **Ephemeral Injection Used**: Secrets injected into CI runners as in-memory environment variables.
- [ ] **Log Masking Active**: CI build output masks secret strings automatically (`***`).

---

# 4. Official Sources

- AWS Secrets Manager Guidance: https://aws.amazon.com/secrets-manager/
- HashiCorp Vault Overview: https://www.vaultproject.io/

---

**Last verified:** August 14, 2026

---

# Related documentation

### Signing security

- `signing/security/access-control.md`
- `signing/security/ci-secrets.md`
- `signing/security/recovery.md`
- `signing/security/secret-storage.md`

### iOS signing

- `signing/ios/certificates.md`
- `signing/ios/key-rotation.md`

### Android signing

- `signing/android/keystore.md`
- `signing/android/key-rotation.md`

### AI security

- `ai/security/secret-protection.md`
- `ai/security/agent-permissions.md`
