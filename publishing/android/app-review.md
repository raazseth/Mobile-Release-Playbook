# Google Play App Review Policies & Pre-Launch Reports

This document details the evaluation mechanics, automated Pre-Launch Report analysis (Firebase Test Lab), policy enforcement rules, and appeal procedures for **Google Play App Review**.

Engineered in alignment with **2026 platform specifications**, it specifies how to navigate Google Play review scans and resolve policy warnings before publishing.

This guide is **not**:

- an authorization mechanism to ignore Google Play Developer Policy Center notices
- a substitute for inspecting automated Firebase Test Lab crash reports
- a guide to bypassing policy violation strikes

---

# 1. Google Play Review & Automated Inspection Pipeline

Every App Bundle (`.aab`) submitted to Google Play Console undergoes dual inspection: automated static/dynamic security analysis (Firebase Test Lab) followed by policy review.

```text
[ Upload App Bundle (.aab) to Google Play Console ]
                         │
                         ▼
┌────────────────────────────────────────────────────────┐
│             AUTOMATED PRE-LAUNCH REPORT SCAN           │
│  - Executes app on physical Android devices            │
│  - Scans for startup crashes, ANRs, & rendering bugs   │
│  - Checks accessibility & security vulnerabilities     │
└────────────────────────┬───────────────────────────────┘
                         │
                         ▼
┌────────────────────────────────────────────────────────┐
│             GOOGLE PLAY POLICY INSPECTION              │
│  - Evaluates Data Safety form against app permissions  │
│  - Checks Target API Level 36 compliance               │
└────────────────────────────────────────────────────────┘
```

---

# 2. Key Google Play Review Policy Focus Areas (2026)

Google Play Review enforces strict checks across several core policy areas:

1. **Target API Level 36 Enforcement**: All submissions MUST target Android 16 (API 36) or higher.
2. **Data Safety Form Alignment**: Declared data collection MUST match permissions requested in `AndroidManifest.xml` (e.g., location, contacts, `AD_ID`).
3. **Billing Policy Compliance**: Digital goods MUST use Google Play Billing Library 8.0/9.0+.
4. **Account Deletion Link**: Apps allowing account creation MUST provide an in-app and web deletion URL.

---

# 3. Handling Policy Violation Notices & Appeals

If Google Play issues a **Rejection**, **Warning**, or **Removal Notice**:

1. **Identify Specific Violation**: Read the Play Console inbox notification detailing the exact policy clause (e.g., *Impersonation*, *Deceptive Behavior*, *Unapproved Permissions*).
2. **Fix Code or Store Listing**: Update `AndroidManifest.xml`, strip un-needed permissions, or update store text.
3. **Submit Appeal**: If the violation was issued in error, submit a formal appeal via the **Google Play Policy Appeal Form** with technical evidence.

---

# 4. Operational Verification Checklist

- [ ] **Pre-Launch Report Clean**: Pre-launch report inspected; zero startup crashes or ANRs.
- [ ] **Data Safety Aligned**: Data Safety declarations match `AndroidManifest.xml` permissions.
- [ ] **Account Deletion Link Active**: Web-based account deletion URL verified live.
- [ ] **Target API 36 Set**: `targetSdkVersion 36` confirmed in build configuration.
- [ ] **No High-Risk Permissions**: High-risk permissions (`SMS`, `CALL_LOG`, `QUERY_ALL_PACKAGES`) justified or removed.

---

# 5. Related Documentation

- [App Bundle Handbook](app-bundle.md) - `.aab` compilation.
- [Data Safety Handbook](../../store-operations/data-safety.md) - Data Safety questionnaire.
- [Google Play Store Accounts](../../store-accounts/google-play-console.md) - Play Console accounts.

---

# 6. Official Sources

- Google Play Policy Center: https://play.google.com/about/developer-content-policy/
- Google Play Pre-Launch Report Guide: https://support.google.com/googleplay/android-developer/answer/9842757

---

**Last verified:** August 14, 2026
