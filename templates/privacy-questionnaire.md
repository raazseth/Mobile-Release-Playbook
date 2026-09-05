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

# 4. AI-Assisted Draft Generation

Use this prompt to build a first-draft SDK data inventory from your actual dependencies, then have the Privacy Lead verify every row before it's used to fill out Apple's or Google's privacy questionnaire — this output is a starting point, never the declaration itself.

```markdown
<system_instructions>
You are auditing third-party SDKs for data collection behavior. Given the dependency list
below, produce a Data Inventory table (SDK name, purpose, data collected, linked to user
identity?, shared with third parties?) matching this template's format, based on each SDK's
publicly documented data collection behavior. For any SDK where you're not confident about its
actual data practices, say "UNKNOWN — verify against the SDK's own privacy documentation"
instead of guessing. Do not mark anything as "No" data collection unless the SDK's own
documentation actually states that.
</system_instructions>

<dependencies>
- App name / version: <APP_NAME_VERSION>
- Installed SDKs (package name + version): <LIST — e.g. "@sentry/react-native 5.x, react-native-firebase/analytics 19.x">
</dependencies>
```

> **Important:** Privacy declarations are legally binding statements to Apple and Google, not documentation prose. Treat every row this produces as `UNKNOWN` until a human actually confirms it against the SDK's own privacy documentation or source — this is exactly the kind of AI output that must never be submitted unverified.

- [ ] Every "UNKNOWN" row was actually resolved by a human before this went into a real submission — not left as a guess.
- [ ] The Privacy Lead, not the AI, is the one who signed off above.

---

**Last verified:** September 5, 2026

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

### AI prompts and agents

- `ai/agents/privacy-auditor.md`

### Store operations

- `store-operations/README.md`

### Checklists

- `checklists/README.md`

### Post-release

- `post-release/incident-response.md`

### Release strategy

- `release-strategy/release-trains.md`
