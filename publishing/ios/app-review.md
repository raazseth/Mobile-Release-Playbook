# Apple App Store Review Policies & Guidelines

This document covers the App Store Review Guidelines that most often trigger rejections (2.1, 2.3, 3.1.1, 5.1.1), Reviewer Notes, demo credential requirements, and export compliance for **Apple App Store Review** — how to prepare an iOS submission to pass review cleanly.

This guide is **not**:

- an authorization mechanism to obfuscate features from Apple reviewers (Guideline 2.3.1 strictly forbids hidden features)
- a substitute for verifying Privacy Manifests (`PrivacyInfo.xcprivacy`)
- a guide to bypassing Guideline 3.1.1 In-App Purchase requirements

---

# 1. App Store Review Core Policy Focus Areas (2026)

Apple App Review enforces rigorous checks across four primary guideline pillars:

```text
Guideline 2.1 (App Completeness)       → app must not crash, fail to load, or lack working demo auth
Guideline 2.3 (Accurate Metadata)      → metadata must accurately describe what the binary does
Guideline 3.1.1 (In-App Purchase)      → digital goods/services must use StoreKit In-App Purchase
Guideline 5.1.1 (Data Collection)      → Privacy Manifests and Privacy Nutrition Labels must match
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

# Related documentation

### Publishing (iOS)

- `publishing/ios/README.md`
- `publishing/ios/app-store-connect.md`
- `publishing/ios/build-upload.md`
- `publishing/ios/metadata.md`
- `publishing/ios/production-release.md`
- `publishing/ios/screenshots.md`
- `publishing/ios/testflight.md`

### Store accounts

- `store-accounts/app-store-connect.md`

### iOS signing

- `signing/ios/README.md`
- `signing/ios/distribution.md`

### Store operations

- `store-operations/app-review.md`
- `store-operations/rejection-handling.md`

### Checklists

- `checklists/ios.md`

### Publishing (cross-platform)

- `publishing/cross-platform/README.md`

---

# Official sources

- Apple App Store Review Guidelines: https://developer.apple.com/app-store/review/guidelines/

---

**Last verified:** August 14, 2026

