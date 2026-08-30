# Google Play Data Safety Form Questionnaire Handbook

This handbook details the questionnaire requirements, data collection taxonomy, encryption status disclosures, SDK data usage mapping, and compliance rules for the **Google Play Data Safety Form** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it provides exhaustive technical and operational guidance for completing the mandatory Data Safety declaration in Google Play Console without facing policy warnings or store takedowns.

This guide is **not**:

- an authorization mechanism to omit third-party SDK data collection disclosures
- a generic privacy policy text generator
- a substitute for auditing actual network traffic emitted by installed SDKs

---

# 1. Data Safety Architecture & Compliance Mandate

Google Play requires all Android apps to declare their data collection and data sharing practices in the Play Console **Data Safety Form**.

```text
┌────────────────────────────────────────────────────────┐
│             GOOGLE PLAY DATA SAFETY FORM               │
│                                                        │
│  - What data types are collected?                      │
│    (Location, Personal Info, Financial, Crash Logs)    │
│  - Is data encrypted in transit? (TLS / HTTPS)         │
│  - Is data shared with third parties? (Analytics, Ads) │
│  - Can users request data deletion? (Account Deletion) │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ Published on Public Google Play Store Listing Data Safety Section ]
```

---

# 2. Key Data Collection Categories

When filling out the questionnaire, declare data collected by both your application code and **all integrated third-party SDKs** (e.g., Sentry, Firebase, Mixpanel, AppsFlyer):

| Data Category | Data Types | Common SDK Sources | Data Purpose |
|---|---|---|---|
| **Location** | Approximate / Precise Location | Maps SDK, Location APIs | App Functionality, Analytics |
| **Personal Info** | Name, Email Address, User IDs | OAuth, Account Registration | App Functionality, Account Management |
| **Financial Info** | Purchase History, Transaction IDs | Play Billing, RevenueCat | In-App Purchases, Billing |
| **App Activity** | App Interactions, In-App Search | Segment, Mixpanel, Amplitude | Analytics, Personalization |
| **App Info & Perf** | Crash Logs, Diagnostics | Sentry, Crashlytics | Developer Troubleshooting |
| **Device IDs** | Advertising ID (GAID), Device Tokens | FCM Push, MMP Attribution | Push Notifications, Attribution |

---

# 3. User Data Deletion Requirement

Google Play mandates that any app allowing account creation MUST provide a clear path for users to request account and data deletion:

1. **In-App Deletion Option**: Provide an easily accessible "Delete Account" button within app settings.
2. **Web-Based Deletion URL**: Provide a public web page URL where users can request data deletion without re-installing the app.

---

# 4. Operational Verification Checklist

- [ ] **SDK Data Audited**: Data collection audited for all third-party SDKs in app binary.
- [ ] **Encryption in Transit**: Data declared encrypted in transit (`https://` active).
- [ ] **Account Deletion URL Provided**: Public web link for account/data deletion declared in Play Console.
- [ ] **Advertising ID Disclosed**: `AD_ID` permission declared if MMP/Ads SDKs are present.
- [ ] **Listing Data Matches**: Data Safety section on Play Store listing accurately reflects actual app behavior.

---

# 5. Related Documentation

- [Privacy Information Handbook](privacy-information.md) - Apple Privacy Labels.
- [App Listing Handbook](app-listing.md) - Store listing setup.
- [Privacy Auditor Agent](../ai/agents/privacy-auditor.md) - Privacy auditing.

---

# 6. Official Sources

- Google Play Data Safety Guidance: https://support.google.com/googleplay/android-developer/answer/10787469
- Google Play Account Deletion Requirement: https://support.google.com/googleplay/android-developer/answer/13327111

---

**Last verified:** August 14, 2026

