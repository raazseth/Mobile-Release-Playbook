# Store Privacy Questionnaire & Audit Form Template

Use this operational form template to audit and document data collection practices across your mobile application and integrated third-party SDKs before completing **Apple App Privacy Labels** and **Google Play Data Safety Forms**.

---

# Privacy Audit Meta Information

- **App Name**: `[App Name]`
- **Target Release Version**: `v[1.2.0]`
- **Audit Date**: `[YYYY-MM-DD]`
- **Privacy Lead Auditor**: `[Name / Title]`

---

# 1. Third-Party SDK Data Inventory

Identify all embedded SDKs in the application binary and document their data collection behavior:

| Integrated SDK Name | SDK Purpose | Data Collected | Linked to User Identity? | Shared with 3rd Parties? |
|---|---|---|---|---|
| **Sentry React Native** | Crash Reporting | Crash Logs, Device Hardware Specs | No (Anonymous telemetry) | No |
| **Google Analytics for Firebase** | Analytics | App Interactions, Screen Views | Yes (User ID / Device ID) | Yes (Google Services) |
| **AppsFlyer SDK** | MMP Attribution | Advertising ID (IDFA / GAID) | Yes (Ad Attribution) | Yes (Ad Networks) |
| **RevenueCat SDK** | App Commerce | Purchase History, Customer User ID | Yes (Entitlement mapping) | No |

---

# 2. Apple App Privacy Nutrition Label Declarations

Map data collection practices to App Store Connect privacy questionnaire categories:

```text
┌────────────────────────────────────────────────────────┐
│             APPLE PRIVACY LABEL MAPPING                │
│                                                        │
│  - Data Used to Track You:                             │
│    Advertising ID (IDFA) ──→ AppsFlyer MMP Attribution  │
│                                                        │
│  - Data Linked to You:                                 │
│    Email Address, Name, User ID, Purchase History      │
│                                                        │
│  - Data Not Linked to You:                             │
│    Crash Data, Performance Diagnostics                │
└────────────────────────────────────────────────────────┘
```

---

# 3. Google Play Data Safety Form Declarations

Map data collection practices to Google Play Console Data Safety questionnaire categories:

- **Location**: Approximate Location / Precise Location (`[YES / NO]`).
- **Personal Info**: Name, Email Address, User IDs (`[YES / NO]`).
- **Financial Info**: Purchase History (`[YES / NO]`).
- **App Info & Performance**: Crash logs, Diagnostics (`[YES / NO]`).
- **Device or Other IDs**: Advertising ID (`AD_ID`) (`[YES / NO]`).
- **Data Encryption**: Data encrypted in transit using HTTPS/TLS (`[YES - MANDATORY]`).
- **Account Deletion Link**: Active web page for requesting account deletion (`[https://example.com/delete-account]`).

---

# Sign-Off Verification

- **Privacy Auditor Signature**: `[ Signature / Date ]`

---

**Last verified:** August 14, 2026

---

# Related documentation

### Templates

- `templates/README.md`
- `templates/app-store-description.md`
- `templates/changelog.md`
- `templates/incident-report.md`
- `templates/play-store-description.md`
- `templates/rejection-response.md`
- `templates/release-checklist.md`
- `templates/release-notes.md`
- `templates/release-plan.md`

### Store operations

- `store-operations/README.md`

### Checklists

- `checklists/README.md`

### Post-release

- `post-release/incident-response.md`

### Release strategy

- `release-strategy/release-trains.md`
