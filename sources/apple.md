# Apple Developer Documentation Registry

This document details the primary documentation portals, App Store Review Guidelines, StoreKit 2 APIs, Privacy Manifest requirements, TestFlight specifications, and Human Interface Guidelines for **Apple Developer & iOS Publishing**.

Engineered in alignment with **2026 platform specifications**, it provides an exhaustive, verified citation registry for Apple iOS development and release engineering.

This guide is **not**:

- an un-verified secondary tutorial list
- an authorization mechanism to cite outdated iOS SDK documentation
- a substitute for inspecting active App Store Connect Help portals

---

# 1. Primary Apple Documentation Portals

| Documentation Domain | Primary Official URL | Key Scope & Governance Rules |
|---|---|---|
| **App Store Review Guidelines** | `https://developer.apple.com/app-store/review/guidelines/` | Canonical rules for App Review approval (Guidelines 2.1, 2.3, 3.1.1, 5.1.1). |
| **App Store Connect Help** | `https://developer.apple.com/help/app-store-connect/` | Portal administration, build uploads, metadata, TestFlight, and releases. |
| **App Store Connect API** | `https://developer.apple.com/documentation/appstoreconnectapi` | REST API for automated build management, `.p8` API keys, and Fastlane Deliver. |
| **StoreKit 2 Documentation** | `https://developer.apple.com/documentation/storekit` | In-App Purchases, Auto-Renewable Subscriptions, and Signed Transaction JWS. |
| **App Privacy Details** | `https://developer.apple.com/app-store/app-privacy-details/` | App Privacy Nutrition Labels, Data Collection, and Tracking disclosures. |
| **Required Reason APIs** | `https://developer.apple.com/documentation/bundleresources/describing_use_of_required_reason_api` | `PrivacyInfo.xcprivacy` manifest declarations for Disk Stat, User Defaults, etc. |
| **TestFlight Documentation** | `https://developer.apple.com/testflight/` | Internal and External beta testing, Beta App Review, and public links. |
| **Apple Developer Program Terms** | `https://developer.apple.com/terms/` | Apple Developer Program License Agreement (DPLA) legal compliance. |

---

# 2. Key Apple Guidelines Citation Index

```text
┌────────────────────────────────────────────────────────┐
│             APPLE APP REVIEW GUIDELINE CITATIONS       │
│                                                        │
│  - Guideline 2.1 (App Completeness):                   │
│    App MUST NOT crash, fail to load, or lack demo auth.│
│                                                        │
│  - Guideline 2.3 (Accurate Metadata):                  │
│    Metadata MUST accurately describe binary features.  │
│                                                        │
│  - Guideline 2.3.7 (Competitor Trademarks):            │
│    Metadata MUST NOT contain competitor app names.     │
│                                                        │
│  - Guideline 3.1.1 (In-App Purchase):                  │
│    Digital goods/services MUST use StoreKit 2 IAP.     │
│                                                        │
│  - Guideline 3.1.2 (Subscriptions):                    │
│    Paywall MUST disclose price, duration, & EULA links.│
│                                                        │
│  - Guideline 5.1.1 (Data Collection & Storage):        │
│    Privacy Manifests & Privacy Labels MUST align 100%. │
└────────────────────────────────────────────────────────┘
```

---

# 3. Operational Verification Checklist

- [ ] **All URLs Active**: Primary Apple documentation URLs verified returning HTTP 200 OK.
- [ ] **Deprecations Flagged**: Legacy `altool` and deprecated receipt validation APIs marked as deprecated.
- [ ] **2026 Guidelines Aligned**: Citations reflect current App Store Review Guidelines.
- [ ] **`PrivacyInfo.xcprivacy` Referenced**: Required Reason API documentation linked for privacy audits.

---

# 4. Related Documentation

- [Google Documentation Registry](google.md) - Android sources.
- [Expo Documentation Registry](expo.md) - Expo sources.
- [iOS Publishing Handbook](../../publishing/ios/README.md) - iOS publishing.

---

# 5. Official Sources

- Apple Developer Documentation Portal: https://developer.apple.com/documentation/

---

**Last verified:** August 14, 2026
