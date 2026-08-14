# Privacy & Regulatory Compliance Handbook

This directory contains production handbook guides, regulatory compliance frameworks, privacy manifest configurations (`PrivacyInfo.xcprivacy`), data safety mapping schemas, and user consent architecture for **Privacy & Compliance** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**—specifically Apple Guideline 5.1.1, Required Reason API declarations, Google Play Data Safety rules, EU GDPR, California CCPA/CPRA, COPPA, and South Korea PIPA—it details how to ensure total privacy compliance across mobile platforms.

This guide is **not**:

- an authorization mechanism to track users without explicit consent
- a substitute for consulting legal counsel regarding regional privacy statutes
- a guide to obfuscating data collection practices from store reviewers

---

# 1. Architecture of Mobile Privacy & Regulatory Compliance

Mobile privacy compliance spans client application permission controls, binary privacy manifests (`PrivacyInfo.xcprivacy`), store console disclosures (App Privacy Labels & Data Safety), and legal privacy policy hosting.

```text
┌────────────────────────────────────────────────────────┐
│             MOBILE PRIVACY COMPLIANCE ARCHITECTURE     │
│                                                        │
│  - App Source Code: Just-In-Time Permission Prompts   │
│  - App Binary: `PrivacyInfo.xcprivacy` Manifests       │
│  - App Store Connect: App Privacy Nutrition Labels     │
│  - Google Play Console: Data Safety Form & Web Delete  │
│  - Public Web: Active Privacy Policy (`https://`)      │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ Compliant with EU GDPR, CCPA/CPRA, COPPA, HIPAA, & PIPA ]
```

---

# 2. Subsystem Directory Taxonomy

| Handbook File | Core Privacy Scope | Key Regulatory & Store Rules |
|---|---|---|
| **[README.md](README.md)** | Subsystem index, privacy architecture, and compliance governance. | High-level privacy architecture and 2026 rules. |
| **[privacy-policy.md](privacy-policy.md)** | Privacy Policy URL requirements, hosting, and mandatory clauses. | `https://` HTTP 200 check, data collection & deletion clauses. |
| **[data-collection.md](data-collection.md)** | Data collection taxonomy, minimization, and `PrivacyInfo.xcprivacy`. | `PrivacyInfo.xcprivacy`, Required Reason APIs, Data Safety. |
| **[data-deletion.md](data-deletion.md)** | User account and data deletion requirements (In-app + Web link). | Play Console web deletion URL, in-app deletion button. |
| **[data-retention.md](data-retention.md)** | Data retention policies, storage limits, and data purging. | Data retention disclosures, server-side data purging. |
| **[tracking.md](tracking.md)** | App Tracking Transparency (ATT), IDFA, and GAID rules. | iOS ATT prompt timing, SKAdNetwork 4.0/5.0, `AD_ID`. |
| **[advertising.md](advertising.md)** | Advertising disclosures, AdMob compliance, and ad consent. | Personalized vs Non-Personalized ads, UMP SDK consent. |
| **[analytics.md](analytics.md)** | Privacy-preserving mobile analytics, IP anonymization, and opt-outs. | Anonymous telemetry, opt-out toggles, Firebase/Mixpanel. |
| **[children.md](children.md)** | COPPA, Apple Kids Category, and Google Designed for Families. | Zero tracking in kids apps, neutral age gate, COPPA. |
| **[health-data.md](health-data.md)** | Health & medical data compliance (HealthKit & Health Connect). | Apple HealthKit entitlements, Health Connect, HIPAA disclaimers. |
| **[permissions.md](permissions.md)** | Runtime permission requests, Android 14+ Selected Photos, Just-In-Time. | Android 14+ Selected Photos, Location background prompts. |
| **[regional-requirements.md](regional-requirements.md)** | Regional regulatory compliance (GDPR, CCPA, PIPA, LGPD, GRAC). | EU GDPR, CCPA/CPRA, South Korea PIPA & GRAC overrides. |

---

# 3. Universal Privacy Governance Rules

All privacy compliance implementations in this playbook must adhere to five mandatory governance rules:

```text
1. 100% Binary Manifest Alignment
   The data collection declarations in App Store Connect Privacy Labels and Google Play Data Safety
   MUST align 100% with the compiled binary `PrivacyInfo.xcprivacy` manifest and requested permissions.

2. Mandatory Dual Data Deletion Channels
   Apps allowing account creation MUST provide both an in-app account deletion button and a public
   web-based data deletion URL (`https://example.com/delete-account`).

3. App Tracking Transparency (ATT) First-Prompt Rule
   Access to the iOS Advertising Identifier (IDFA) is FORBIDDEN prior to displaying the native App
   Tracking Transparency (ATT) prompt and receiving explicit user consent (`Authorized`).

4. Zero Tracking in Kids Category Applications
   Applications targeted at children under 13 (COPPA / Kids Category) MUST NOT include third-party
   advertising SDKs, IDFA tracking, or un-moderated user-generated content (UGC).

5. Public Privacy Policy HTTP 200 Requirement
   The Privacy Policy URL configured in App Store Connect and Google Play Console MUST be an active
   `https://` web link returning HTTP 200 OK without broken redirects or authentication gates.
```

---

# 4. Related Repository Documentation

- [Privacy Questionnaire Template](../../templates/privacy-questionnaire.md) - Audit form.
- [Privacy Information Handbook](../../store-operations/privacy-information.md) - Store operations.
- [Privacy Auditor Agent](../../ai/agents/privacy-auditor.md) - Automated privacy audit.

---

# 5. Official Sources

- Apple App Privacy Details: https://developer.apple.com/app-store/app-privacy-details/
- Google Play Data Safety Guidance: https://support.google.com/googleplay/android-developer/answer/10787469

---

**Last verified:** August 14, 2026
