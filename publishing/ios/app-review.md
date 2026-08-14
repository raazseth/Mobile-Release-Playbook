# Apple App Store Review Policies & Guidelines

This document details the review policies, App Store Review Guidelines (Guidelines 2.1, 2.3, 3.1.1, 5.1.1), Reviewer Notes requirements, demo credential management, and appeal procedures for **Apple App Store Review**.

Engineered in alignment with **2026 platform specifications**, it specifies how to prepare iOS applications to pass App Review inspection cleanly without rejection delays.

This guide is **not**:

- an authorization mechanism to obfuscate features from Apple reviewers (Guideline 2.3.1 strictly forbids hidden features)
- a substitute for verifying Privacy Manifests (`PrivacyInfo.xcprivacy`)
- a guide to bypassing Guideline 3.1.1 In-App Purchase requirements

---

# 1. App Store Review Core Policy Focus Areas (2026)

Apple App Review enforces rigorous checks across four primary guideline pillars:

```text
┌────────────────────────────────────────────────────────┐
│             APPLE APP REVIEW POLICY PILLARS            │
│                                                        │
│  - Guideline 2.1 (App Completeness):                   │
│    App MUST NOT crash, fail to load, or lack demo auth.│
│                                                        │
│  - Guideline 2.3 (Accurate Metadata):                  │
│    Metadata MUST accurately describe binary features.  │
│                                                        │
│  - Guideline 3.1.1 (In-App Purchase):                  │
│    Digital goods/services MUST use StoreKit 2 IAP.     │
│                                                        │
│  - Guideline 5.1.1 (Data Collection & Storage):        │
│    Privacy Manifests & Privacy Labels MUST align 100%. │
└────────────────────────────────────────────────────────┘
```

---

# 2. Mandatory Demo Credentials & Reviewer Notes

Submitting an app requiring login without functional demo credentials will trigger an immediate rejection under **Guideline 2.1**:

- **Demo Account Setup**: Provide an active demo username and password in App Store Connect under **App Review Information**.
- **2FA Bypass**: Demo accounts MUST bypass SMS or email 2FA prompts so Apple reviewers can log in instantly.
- **Reviewer Notes**: Detail step-by-step navigation instructions to test primary features and StoreKit 2 IAPs.

---

# 3. Export Compliance & Encryption Declarations

To prevent build processing halts when submitting builds:

Include `ITSAppUsesNonExemptEncryption: false` in `Info.plist`:

```xml
<key>ITSAppUsesNonExemptEncryption</key>
<false/>
```

---

# 4. Operational Verification Checklist

- [ ] **Demo Credentials Active**: Demo account credentials verified working on production backend servers.
- [ ] **2FA Bypass Verified**: Reviewer demo account bypasses SMS/email 2FA prompts.
- [ ] **Guideline 3.1.1 Compliant**: Digital purchases use StoreKit 2; zero external payment links for digital goods.
- [ ] **Guideline 5.1.1 Compliant**: `PrivacyInfo.xcprivacy` manifests included for all compiled third-party SDKs.
- [ ] **Reviewer Notes Detailed**: Reviewer notes provide step-by-step instructions for testing features.

---

# 5. Related Documentation

- [Production Release Handbook](production-release.md) - Production releases.
- [Rejection Handling Handbook](../../store-operations/rejection-handling.md) - Rejection triage.
- [App Store Connect Handbook](app-store-connect.md) - Build management.

---

# 6. Official Sources

- Apple App Store Review Guidelines: https://developer.apple.com/app-store/review/guidelines/

---

**Last verified:** August 14, 2026
