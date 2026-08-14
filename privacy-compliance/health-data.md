# Health & Medical Data Compliance Handbook

This handbook details Apple HealthKit entitlements, Health Connect on Android, HIPAA compliance disclaimers, data encryption, and advertising restrictions for **Health & Medical Data** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it specifies how to handle sensitive health and biometric data legally and securely.

This guide is **not**:

- an authorization mechanism to use HealthKit / Health Connect data for advertising or marketing
- a substitute for providing medical disclaimers
- a guide to storing un-encrypted health records on public servers

---

# 1. Health & Medical Data Architecture

Health data collected via Apple HealthKit or Android Health Connect is subject to strict store guidelines prohibiting advertising usage.

```text
┌────────────────────────────────────────────────────────┐
│             HEALTH DATA COMPLIANCE RULES               │
│                                                        │
│  - Mandatory Entitlement: `com.apple.developer.healthkit`│
│  - ZERO Advertising Use: Health data CANNOT be used for│
│    advertising or shared with data brokers.            │
│  - HIPAA Disclaimer: Clear medical disclaimer shown    │
│    before presenting clinical health recommendations.  │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ Stored Encrypted on Device & Secure Healthcare Backend ]
```

---

# 2. Operational Verification Checklist

- [ ] **HealthKit Entitlement Configured**: `com.apple.developer.healthkit` entitlement declared.
- [ ] **Zero Ad Usage**: Verified health data is NEVER passed to analytics or advertising SDKs.
- [ ] **Medical Disclaimer Present**: Medical advice disclaimer rendered prominently in app.
- [ ] **Encrypted Storage Active**: Health records encrypted at rest using AES-256 / SecureStore.

---

# 3. Official Sources

- Apple HealthKit Human Interface Guidelines: https://developer.apple.com/healthkit/
- Android Health Connect Guide: https://developer.android.com/health-and-fitness/guides/health-connect

---

**Last verified:** August 14, 2026
