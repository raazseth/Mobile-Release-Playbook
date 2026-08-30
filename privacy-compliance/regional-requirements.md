# Regional Regulatory Compliance Handbook (GDPR, CCPA, PIPA, LGPD, GRAC)

This handbook details regional privacy statutes, EU General Data Protection Regulation (GDPR), California CCPA/CPRA, South Korea Personal Information Protection Act (PIPA), Brazil LGPD, and South Korea Game Rating & Administration Committee (GRAC) age rating overrides for **Regional Compliance** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**—including South Korea GRAC age rating overrides (effective August 12, 2026) and social media disclosures (effective September 2026)—it specifies how to comply with global regional statutes.

This guide is **not**:

- an authorization mechanism to ignore regional privacy statutes
- a substitute for consulting international legal counsel
- a guide to geo-blocking users to evade privacy compliance

---

# 1. Regional Regulatory Compliance Taxonomy

Mobile apps distributed globally MUST comply with regional privacy laws based on user residency:

```text
┌────────────────────────────────────────────────────────┐
│             REGIONAL PRIVACY STATUTES MATRIX           │
│                                                        │
│  - EU / EEA / UK: General Data Protection Reg (GDPR)   │
│  - California (US): CCPA / CPRA ("Do Not Sell My Info")│
│  - South Korea: PIPA + GRAC Rating Classification (RCN)│
│  - Brazil: Lei Geral de Proteção de Dados (LGPD)       │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ Dynamic Consent & Regional Disclosures Executed in App ]
```

| Jurisdiction | Statute Name | Key Requirement | Application Action |
|---|---|---|---|
| **European Union / UK** | **GDPR** | Explicit Opt-In Consent, Data Portability, Right to be Forgotten | Display IAB TCF Consent Banner via Google UMP SDK. |
| **California (US)** | **CCPA / CPRA** | "Do Not Sell/Share My Personal Info", Opt-Out | Provide explicit "Do Not Sell My Info" link in Settings. |
| **South Korea** | **PIPA / GRAC** | Strict PIPA Data Laws & GRAC Age Rating RCN Overrides | Provide GRAC Rating Classification Number in App Store Connect. |
| **Brazil** | **LGPD** | Legal bases for processing, Data Protection Officer (DPO) contact | Disclose DPO contact info in Privacy Policy. |

---

# 2. South Korea GRAC Age Rating Override Compliance (2026 Update)

Effective **August 12, 2026**, Apple App Store Connect permits developers distributing apps in the Republic of Korea to override default App Store age ratings by entering an official **GRAC Rating Classification Number (RCN)**:

```text
App Store Connect ──→ [ App Information ] ──→ [ Age Rating ] ──→ [ Korea Storefront ]
                                                                        │
                                                                        ▼
                                                   [ Enter GRAC Classification Number ]
```

---

# 3. Operational Verification Checklist

- [ ] **GDPR Consent Form Active**: IAB TCF v2.2 consent banner displayed to EU/UK users.
- [ ] **CCPA Opt-Out Active**: "Do Not Sell My Info" option provided for California users.
- [ ] **South Korea GRAC Configured**: GRAC Rating Classification Number entered for Korea distribution.
- [ ] **Social Media Disclosures Ready**: Social media capability disclosures prepared for September 2026 requirement.

---

# 4. Official Sources

- EU GDPR Portal: https://gdpr.eu/
- California CPRA Text: https://oag.ca.gov/privacy/ccpa
- Apple Korea GRAC Age Rating Guidance: https://developer.apple.com/news/

---

**Last verified:** August 14, 2026

---

# Related documentation

### Privacy compliance

- `privacy-compliance/README.md`
- `privacy-compliance/advertising.md`
- `privacy-compliance/analytics.md`
- `privacy-compliance/children.md`
- `privacy-compliance/data-collection.md`
- `privacy-compliance/data-deletion.md`
- `privacy-compliance/data-retention.md`
- `privacy-compliance/health-data.md`
- `privacy-compliance/permissions.md`
- `privacy-compliance/privacy-policy.md`
- `privacy-compliance/tracking.md`

### Store operations

- `store-operations/data-safety.md`
- `store-operations/privacy-information.md`

### Pre-release

- `pre-release/privacy-audit.md`

### AI agents

- `ai/agents/privacy-auditor.md`

### Monetization

- `monetization/receipt-validation.md`

### Troubleshooting

- `troubleshooting/privacy-rejected.md`
