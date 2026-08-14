# Google Play & Android Developer Documentation Registry

This document details the primary documentation portals, Google Play Developer Policies, Target API Level requirements, Play Billing Library specifications, Android App Bundle rules, and Android Vitals metrics for **Google Play & Android Publishing**.

Engineered in alignment with **2026 platform specifications**, it provides an authoritative citation registry for Android app development and release engineering.

This guide is **not**:

- an un-verified secondary tutorial list
- an authorization mechanism to cite outdated Android SDK requirements
- a substitute for inspecting active Google Play Console Help portals

---

# 1. Primary Google Documentation Portals

| Documentation Domain | Primary Official URL | Key Scope & Governance Rules |
|---|---|---|
| **Google Play Policy Center** | `https://play.google.com/about/developer-content-policy/` | Developer Content Policy, Privacy, Security, and Commercial guidelines. |
| **Play Console Help** | `https://support.google.com/googleplay/android-developer/` | Account verification, release management, store listings, and tracks. |
| **Android Developer Docs** | `https://developer.android.com/docs` | Core Android SDK, architecture guides, security, and API references. |
| **Target SDK Requirements** | `https://developer.android.com/google/play/requirements/target-sdk` | Annual Target API Level requirements (**Android 16 / API Level 36**). |
| **Google Play Billing** | `https://developer.android.com/google/play/billing` | Play Billing Library (PBL 8.0/9.0+), Base Plans, and Billing Choice. |
| **Android App Bundle (.aab)** | `https://developer.android.com/guide/app-bundle` | `.aab` compilation, Dynamic Delivery, Split APKs, and Play App Signing. |
| **Google Play Data Safety** | `https://support.google.com/googleplay/android-developer/answer/10787469` | Data Safety questionnaire, data collection types, and encryption status. |
| **Account Verification** | `https://support.google.com/googleplay/android-developer/answer/10788668` | D-U-N-S Number requirements for Organization accounts. |

---

# 2. Key Google Play Policy Citation Index

```text
┌────────────────────────────────────────────────────────┐
│             GOOGLE PLAY POLICY CITATIONS               │
│                                                        │
│  - Target API Level 36 (Android 16):                   │
│    Mandatory for all new apps & app updates in 2026.   │
│                                                        │
│  - Android App Bundle (.aab) Mandate:                  │
│    APKs strictly rejected for Play Store distribution. │
│                                                        │
│  - Play Billing Library (PBL 8.0 / 9.0+):              │
│    PBL 7 deprecated; older versions rejected.          │
│                                                        │
│  - Closed Testing Gate (12 Testers / 14 Days):         │
│    Mandatory for new Personal developer accounts.      │
│                                                        │
│  - Data Safety & Account Deletion:                     │
│    Public web deletion URL mandatory for account apps. │
└────────────────────────────────────────────────────────┘
```

---

# 3. Operational Verification Checklist

- [ ] **All URLs Active**: Primary Google documentation URLs verified returning HTTP 200 OK.
- [ ] **Target API 36 Cited**: Target SDK requirements updated to Android 16 (API 36).
- [ ] **PBL 8.0/9.0+ Cited**: Play Billing Library references reflect active submission gates.
- [ ] **Account Verification Linked**: D-U-N-S lookup and account verification docs cited.

---

# 4. Related Documentation

- [Apple Documentation Registry](apple.md) - iOS sources.
- [Expo Documentation Registry](expo.md) - Expo sources.
- [Android Publishing Handbook](../../publishing/android/README.md) - Android publishing.

---

# 5. Official Sources

- Android Developer Documentation Portal: https://developer.android.com/docs

---

**Last verified:** August 14, 2026
