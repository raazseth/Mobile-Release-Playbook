# Application Security & Hardening Audit Handbook

This handbook details application security audits, OWASP Mobile Top 10 mitigations, App Transport Security (ATS), R8/ProGuard obfuscation, API token vaults, and Hermes bytecode security for **Security Audits** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it specifies how to audit mobile binaries for security vulnerabilities.

This guide is **not**:

- an authorization mechanism to embed plain-text private keys in JS source code
- a substitute for performing static/dynamic application security testing (SAST/DAST)
- a guide to disabling SSL certificate validation in production builds

---

# 1. OWASP Mobile Top 10 Security Architecture

Mobile application security auditing evaluates binaries against the OWASP Mobile Top 10 security risks:

```text
┌────────────────────────────────────────────────────────┐
│             OWASP MOBILE TOP 10 AUDIT CHECKPOINTS      │
│                                                        │
│  - M1: Improper Credential Usage ──→ Expo SecureStore  │
│  - M2: Inadequate Supply Chain   ──→ `npm audit`       │
│  - M3: Insecure Authentication   ──→ OAuth2 + PKCE     │
│  - M4: Insufficient Input Valid. ──→ Input Sanitization│
│  - M5: Insecure Communication   ──→ HTTPS / ATS / TLS │
│  - M9: Insecure Data Storage     ──→ Encrypted Storage │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ Verified Secure Binary Ready for Store Distribution ]
```

---

# 2. Hardening Verification Rules

- **Zero Plain-Text API Keys**: Private API keys and secret tokens MUST NOT be present in client-side JavaScript source code. Use backend proxy endpoints or secure secret vaults (`expo-secure-store`).
- **iOS App Transport Security (ATS)**: `NSAppTransportSecurity` MUST enforce HTTPS (`NSAllowsArbitraryLoads: false`).
- **Hermes Bytecode Engine**: Hermes engine MUST be enabled (`jsEngine: "hermes"`) to compile JavaScript to binary bytecode, preventing easy source code inspection.

---

# 3. Operational Verification Checklist

- [ ] **ATS Enforced**: `NSAllowsArbitraryLoads` is set to `false` in `Info.plist`.
- [ ] **Zero Hardcoded Secrets**: Client code audited for hardcoded AWS keys, database passwords, or private API tokens.
- [ ] **Hermes Engine Active**: Production build compiles JS to Hermes bytecode (`.bundle`).
- [ ] **R8 Obfuscation Enabled**: Android release build compiled with `minifyEnabled true`.

---

# 4. Official Sources

- OWASP Mobile Top 10 Security Project: https://owasp.org/www-project-mobile-top-10/
- Apple App Transport Security Guidance: https://developer.apple.com/documentation/security/preventing_insecure_network_connections_with_ats

---

**Last verified:** August 14, 2026
