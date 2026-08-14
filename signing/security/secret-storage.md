# Enterprise Secret Vault Storage Handbook

This handbook details HashiCorp Vault integration, AWS Secrets Manager, GCP Secret Manager, AES-256 encryption at rest, and local `.env` protection for **Secret Storage** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it details how to store signing credentials securely.

This guide is **not**:

- an authorization mechanism to store secrets in version control repositories
- a substitute for using enterprise KMS encryption
- a guide to storing private keys in plain-text `.env` files

---

# 1. Secret Manager Vault Architecture

Enterprise secret managers store sensitive certificates, passwords, and private keys encrypted at rest using KMS envelope encryption with Hardware Security Modules (HSM).

```text
┌────────────────────────────────────────────────────────┐
│             SECRET MANAGER VAULT ARCHITECTURE          │
│                                                        │
│  - Storage Engine: AWS Secrets Manager / GCP KMS       │
│  - Encryption: AES-256 KMS Envelope Encryption         │
│  - Access Control: IAM Role Policies & IP Allow-Lists  │
│  - Audit Logging: CloudTrail / GCP Audit Logs          │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ Encrypted Secrets Fetched Ephemerally via IAM Auth ]
```

---

# 2. Key Storage Best Practices

- **Base64 Encoding Binary Files**: Convert binary files (`.p12` certificates, `.keystore` files, `.p8` API keys) to base64-encoded strings before storing in secret managers:
  ```bash
  # Convert Android release keystore to base64 string
  base64 -i release-upload-key.keystore -o keystore_base64.txt
  ```
- **Audit Logging**: Enable CloudTrail / GCP Audit Logging to track every read request for production signing secrets.

---

# 3. Operational Verification Checklist

- [ ] **KMS Envelope Encryption Active**: Secrets stored in AWS Secrets Manager / GCP Secret Manager.
- [ ] **Binary Base64 Encoded**: Keystores and `.p12` files stored as base64-encoded strings.
- [ ] **Audit Logging Enabled**: Access logs tracked and audited weekly.

---

# 4. Official Sources

- AWS Secrets Manager Guidance: https://aws.amazon.com/secrets-manager/

---

**Last verified:** August 14, 2026
