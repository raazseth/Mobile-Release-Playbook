# Google Play App Review Policies & Pre-Launch Reports

This document covers the Pre-Launch Report (Firebase Test Lab), the policy areas Google Play checks most often, and appeal procedures for **Google Play App Review** — how to navigate Google Play's review scans and resolve policy warnings before publishing.

This guide is **not**:

- an authorization mechanism to ignore Google Play Developer Policy Center notices
- a substitute for inspecting automated Firebase Test Lab crash reports
- a guide to bypassing policy violation strikes

---

# 1. Google Play Review & Automated Inspection Pipeline

Every App Bundle (`.aab`) submitted to Google Play Console undergoes dual inspection: automated static/dynamic security analysis (Firebase Test Lab) followed by policy review.

```text
Upload .aab to Google Play Console
        ↓
Automated pre-launch report scan
  - runs the app on physical Android devices
  - scans for startup crashes, ANRs, rendering bugs
  - checks accessibility and security vulnerabilities
        ↓
Google Play policy inspection
  - Data Safety form checked against requested permissions
  - target API level compliance checked
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

# Related documentation

### Publishing (Android)

- `publishing/android/README.md`
- `publishing/android/app-bundle.md`
- `publishing/android/closed-testing.md`
- `publishing/android/internal-testing.md`
- `publishing/android/metadata.md`
- `publishing/android/play-console.md`
- `publishing/android/production-release.md`
- `publishing/android/screenshots.md`

### Store accounts

- `store-accounts/google-play-console.md`

### Android signing

- `signing/android/README.md`
- `signing/android/play-app-signing.md`

### Store operations

- `store-operations/app-review.md`
- `store-operations/rejection-handling.md`

### Checklists

- `checklists/android.md`

### Publishing (cross-platform)

- `publishing/cross-platform/README.md`

---

# Official sources

- Google Play Policy Center: https://play.google.com/about/developer-content-policy/
- Google Play Pre-Launch Report Guide: https://support.google.com/googleplay/android-developer/answer/9842757

---

**Last verified:** August 14, 2026

