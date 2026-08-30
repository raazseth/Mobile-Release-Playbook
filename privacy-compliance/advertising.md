# Advertising Disclosures & Ad Network Compliance Handbook

This handbook details advertising disclosures, User Messaging Platform (UMP) consent SDK setup, personalized vs non-personalized ad delivery, and store policy compliance for **Mobile Advertising** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it specifies how to integrate mobile ad networks (AdMob, AppLovin) while maintaining strict privacy compliance.

This guide is **not**:

- an authorization mechanism to serve targeted ads to children (COPPA violation)
- a substitute for integrating Google UMP Consent SDK for EEA/UK users
- a guide to obfuscating sponsored ad placements

---

# 1. Mobile Ad Consent Architecture (Google UMP SDK)

For users in the European Economic Area (EEA) and the UK, Google Play and Apple mandate integrating the **User Messaging Platform (UMP) SDK** to gather IAB TCF v2.2 consent before serving ads.

```text
┌────────────────────────────────────────────────────────┐
│             GOOGLE UMP CONSENT WORKFLOW                │
│                                                        │
│  - App Launch in EEA / UK Region                      │
│  - Request Consent Information Update via UMP SDK      │
│  - Display IAB TCF Consent Form if required            │
│  - Pass Consent Signals to AdMob / AppLovin           │
└──────────────────────────┬─────────────────────────────┘
                           │
       ┌───────────────────┴───────────────────┐
       ▼                                       ▼
[ Full Consent Granted ]               [ Consent Denied / Limited ]
Serve Personalized Ads                 Serve Non-Personalized Ads Only
```

---

# 2. Operational Verification Checklist

- [ ] **Google UMP Active**: UMP SDK integrated to collect TCF v2.2 consent in EEA/UK regions.
- [ ] **Non-Personalized Ad Fallback**: Ad network configured to request non-personalized ads when consent is denied.
- [ ] **No Ads in Kids Apps**: Zero advertising SDKs integrated if app targets children under 13.

---

# 3. Official Sources

- Google UMP SDK Guide: https://developers.google.com/admob/ump/android/quick-start

---

**Last verified:** August 14, 2026

---

# Related documentation

### Privacy compliance

- `privacy-compliance/README.md`
- `privacy-compliance/analytics.md`
- `privacy-compliance/children.md`
- `privacy-compliance/data-collection.md`
- `privacy-compliance/data-deletion.md`
- `privacy-compliance/data-retention.md`
- `privacy-compliance/health-data.md`
- `privacy-compliance/permissions.md`
- `privacy-compliance/privacy-policy.md`
- `privacy-compliance/regional-requirements.md`
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
