# Apple App Store Review

App Store Review is the human (and automated) check Apple runs on every submission. This covers the guidelines that most often trigger rejections, what reviewers need from you, and how to avoid the common trip-ups.

This guide is **not**:

- an authorization mechanism to hide features from Apple reviewers — Guideline 2.3.1 strictly forbids this
- a substitute for verifying your Privacy Manifest (`PrivacyInfo.xcprivacy`)
- a guide to working around Guideline 3.1.1's In-App Purchase requirements

---

## 1. The guidelines that trip people up most

Four areas account for the bulk of rejections:

```text
Guideline 2.1 (App Completeness)       → app must not crash, fail to load, or lack working demo auth
Guideline 2.3 (Accurate Metadata)      → metadata must actually describe what the binary does
Guideline 3.1.1 (In-App Purchase)      → digital goods/services must use StoreKit In-App Purchase
Guideline 5.1.1 (Data Collection)      → your Privacy Manifest and Privacy Nutrition Label must match reality
```

## 2. Demo credentials and reviewer notes

If your app requires a login and you don't provide working demo credentials, expect an immediate rejection under **Guideline 2.1**.

- Provide an active demo username and password in App Store Connect's **App Review Information**.
- Make sure the demo account skips SMS or email 2FA — reviewers need to log in without waiting on a code.
- Write reviewer notes that walk through how to reach your main features and any StoreKit 2 purchases.

## 3. Export compliance

To avoid a build processing halt at submission, declare export compliance in `Info.plist`:

```xml
<key>ITSAppUsesNonExemptEncryption</key>
<false/>
```

## 4. Before you submit

- [ ] Demo credentials work against production, not a staging environment reviewers can't reach.
- [ ] The demo account skips 2FA.
- [ ] Digital purchases go through StoreKit 2 — no external payment links for digital goods.
- [ ] `PrivacyInfo.xcprivacy` is included for every third-party SDK compiled into the binary.
- [ ] Reviewer notes actually explain how to test the app's main features, step by step.

---

## Official sources

- Apple App Store Review Guidelines: https://developer.apple.com/app-store/review/guidelines/

**Last verified:** August 14, 2026

---

## Related documentation

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
