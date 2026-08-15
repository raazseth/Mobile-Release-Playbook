# Pre-Release Checklist

Use this checklist immediately before submitting a mobile release to the App Store or Google Play.

This is the final **release-readiness gate**. It is not a replacement for the platform-specific publishing checklists in `checklists/ios.md` and `checklists/android.md`.

The checklist is framework-independent, with first-class support for Expo and React Native.

> **Important:** Apple, Google, Expo, and other release tooling change over time. Current platform requirements must be verified against the official sources linked at the end.

---

## 1. Release candidate

- [ ] The exact release commit is known.
- [ ] The release scope is known.
- [ ] The intended version is known.
- [ ] The release owner is known.
- [ ] The production build configuration is selected.
- [ ] No unrelated changes are included.
- [ ] Release notes match the actual changes.
- [ ] Any required backend/API changes are ready.

A release candidate should be something you can identify later by version, build number, commit, and artifact.

---

## 2. Production configuration

Verify the configuration used to create the final build.

- [ ] Production API endpoints are configured.
- [ ] Production environment variables are present.
- [ ] Development endpoints are absent.
- [ ] Staging credentials are absent.
- [ ] Debug-only behavior is disabled.
- [ ] Test accounts and test data are not unintentionally exposed.
- [ ] Production feature flags are correct.
- [ ] Required third-party services use production configuration.
- [ ] Analytics configuration is correct.
- [ ] Crash reporting configuration is correct.
- [ ] Push notification configuration is correct where applicable.
- [ ] Deep-link configuration is correct where applicable.
- [ ] Payment configuration is correct where applicable.

### Expo

If using Expo:

- [ ] The intended `eas.json` build profile is selected.
- [ ] The resolved production app configuration has been checked.
- [ ] Bundle identifier is correct.
- [ ] Android application ID is correct.
- [ ] Required native configuration is present.
- [ ] Production credentials are configured.

Expo's current documentation recommends using a production build specifically prepared for app-store distribution. EAS Build supports production builds for both Android and iOS.

---

## 3. Application identity

### iOS

- [ ] Bundle ID is correct.
- [ ] Bundle ID matches the intended App Store Connect app.
- [ ] Apple Developer team is correct.

### Android

- [ ] Application ID/package name is correct.
- [ ] Application ID matches the intended Google Play app.
- [ ] Google Play app record is correct.

Do not change production identifiers casually. They are part of the permanent identity of the published app.

---

## 4. Versioning

- [ ] User-facing version is correct.
- [ ] iOS build number is correct.
- [ ] Android version code is correct.
- [ ] Build/version values have not already been used where uniqueness is required.
- [ ] Version values in the actual artifacts match the intended release.
- [ ] Release notes match this version.

Record the release as:

```text
Version:
iOS build:
Android version code:
Commit:
Build profile:
Release date:
```

---

## 5. Dependencies

Review the dependencies included in the release.

- [ ] Lockfile is committed and matches the intended build.
- [ ] No unexpected dependency changes are present.
- [ ] Native dependencies are compatible with the release configuration.
- [ ] Known release-blocking vulnerabilities have been addressed or explicitly accepted.
- [ ] Deprecated dependencies that affect the release have been reviewed.
- [ ] Build tooling versions are known.
- [ ] Native modules/config plugins have been reviewed where applicable.

Do not update dependencies during the final release check unless the change is necessary.

A late dependency change creates a new release candidate and should trigger another round of validation.

---

## 6. Platform requirements

Verify current requirements before submission.

### iOS

- [ ] Current App Store submission requirements have been checked.
- [ ] Current SDK/Xcode requirements have been checked.
- [ ] Required App Store Connect information is complete.
- [ ] Current age-rating requirements are satisfied.
- [ ] Current privacy requirements are satisfied.
- [ ] Current App Review requirements have been reviewed.

Apple currently states that, since April 28, 2026, apps uploaded to App Store Connect must be built with Xcode 26 or later using an SDK for iOS 26, iPadOS 26, tvOS 26, visionOS 26, or watchOS 26, as applicable. Verify this requirement again when releasing because Apple can update it.

### Android

- [ ] Current Google Play target API requirement has been checked.
- [ ] Current Play Console requirements have been checked.
- [ ] Current testing requirements have been checked.
- [ ] Current Data safety requirements have been checked.
- [ ] Current content/target-audience requirements have been checked.
- [ ] Current app-signing requirements have been checked.

Starting August 31, 2026, new apps and app updates submitted to Google Play must target Android 16 (API level 36) or higher, with exceptions for certain form factors.

Do not treat these dates or requirements as permanent. Re-check the official source before each release.

---

## 7. Signing and credentials

### iOS

- [ ] Production distribution signing is valid.
- [ ] Correct Apple Developer team is used.
- [ ] Required capabilities are configured.
- [ ] Required provisioning/signing configuration is valid.
- [ ] CI signing configuration is valid if CI is used.

### Android

- [ ] Production release signing is valid.
- [ ] Upload key is available to the release process.
- [ ] Play App Signing is configured where applicable.
- [ ] Required Play Console credentials are available securely.

### Security

- [ ] No secrets are committed to Git.
- [ ] No private keys are included in the repository.
- [ ] No credentials are embedded in documentation.
- [ ] CI secrets are protected.
- [ ] Access is limited to systems and people that need it.
- [ ] Credential recovery/rotation is understood.

Never paste signing keys, store credentials, or production secrets into an AI tool.

If a credential has been exposed:

```text
Revoke
  ↓
Rotate
  ↓
Investigate
  ↓
Verify
```

---

## 8. Build the final artifacts

Create the exact production artifacts that will be submitted.

- [ ] iOS production build succeeds.
- [ ] Android production build succeeds.
- [ ] Both builds use the intended release commit.
- [ ] Both builds use production configuration.
- [ ] Both builds use the intended version values.
- [ ] Both builds are correctly signed.
- [ ] Build artifacts are retained according to the team's release process.

For Expo, the current production-build workflow supports:

```bash
eas build --platform ios
eas build --platform android
```

or a combined build:

```bash
eas build --platform all
```

These commands are current Expo documentation examples as of July 2026.

---

## 9. Verify the actual artifacts

Do not rely only on build success.

Verify:

### iOS

- [ ] Bundle ID is correct.
- [ ] Version is correct.
- [ ] Build number is correct.
- [ ] Production configuration is active.
- [ ] Required entitlements are present.
- [ ] Correct signing is used.
- [ ] No development endpoints are present.

### Android

- [ ] Application ID is correct.
- [ ] Version is correct.
- [ ] Version code is correct.
- [ ] Target API level is correct.
- [ ] Production configuration is active.
- [ ] Correct signing is used.
- [ ] No development endpoints are present.

---

## 10. Real-device smoke test

Test the actual release candidate.

- [ ] iOS build tested on a real device.
- [ ] Android build tested on a real device.
- [ ] App launches.
- [ ] App does not crash on first launch.
- [ ] Production API is reachable.
- [ ] Authentication works.
- [ ] Primary user journey works.
- [ ] Important error states work.
- [ ] App restart works.
- [ ] Background/foreground behavior works.
- [ ] Logout works.
- [ ] Account switching works where applicable.

A successful simulator/emulator test is not enough for final release approval.

---

## 11. Permissions

Review every permission requested by the app.

- [ ] Only required permissions are requested.
- [ ] Permission prompts appear at the right time.
- [ ] Permission denial is handled.
- [ ] Permission re-enablement is handled.
- [ ] Camera works where applicable.
- [ ] Microphone works where applicable.
- [ ] Location works where applicable.
- [ ] Photos/media access works where applicable.
- [ ] Notification permission works where applicable.
- [ ] Store declarations match actual behavior.

---

## 12. Deep links

If deep links are supported:

- [ ] Production URL scheme is correct.
- [ ] Production domain is correct.
- [ ] iOS Universal Links work where used.
- [ ] Android App Links work where used.
- [ ] Link works when the app is open.
- [ ] Link works when the app is backgrounded.
- [ ] Link works when the app is closed.
- [ ] Authentication-required links work.
- [ ] Invalid links fail safely.
- [ ] Correct screen opens.

---

## 13. Push notifications

If push notifications are supported:

- [ ] Production push configuration is correct.
- [ ] Permission flow works.
- [ ] Notification arrives on a real device.
- [ ] Foreground behavior works.
- [ ] Background behavior works.
- [ ] Terminated-app behavior works.
- [ ] Notification tap opens the correct destination.
- [ ] Token registration/update works.
- [ ] Logout/account switching does not leak notifications.

Test:

```text
App open
App backgrounded
App terminated
User logged out
User logged in
```

---

## 14. Payments and subscriptions

If the app uses platform billing:

- [ ] Products are configured.
- [ ] Product identifiers match the app.
- [ ] Pricing is correct.
- [ ] Test purchase has been completed.
- [ ] Successful purchase grants the correct entitlement.
- [ ] Failed purchase does not grant access incorrectly.
- [ ] Cancellation is handled.
- [ ] Expiration is handled.
- [ ] Restore/recovery works where applicable.
- [ ] Server-side entitlement validation is used where required.

Do not treat the client as the sole source of truth for valuable entitlements.

---

## 15. Privacy and data

Review the actual release candidate.

- [ ] Privacy policy is available.
- [ ] Data collection is understood.
- [ ] Third-party SDKs have been reviewed.
- [ ] Analytics collection is understood.
- [ ] Crash-reporting collection is understood.
- [ ] Advertising/tracking behavior is understood where applicable.
- [ ] Location collection is understood where applicable.
- [ ] Sensitive data is not unnecessarily collected.
- [ ] Sensitive data is not written to logs.
- [ ] Store privacy/data declarations are accurate.
- [ ] Required platform permission declarations are accurate.

Do not guess privacy declarations.

If the behavior is unclear, inspect the application and the relevant SDK documentation before submitting.

---

## 16. Security audit

Perform a final release-focused security check.

- [ ] No secrets are committed.
- [ ] No API keys are unintentionally exposed.
- [ ] No production credentials are included in the app configuration.
- [ ] Debug endpoints are disabled.
- [ ] Debug logging is disabled or appropriately restricted.
- [ ] Authentication works.
- [ ] Authorization is enforced by trusted backend systems where applicable.
- [ ] Sensitive local data is stored appropriately.
- [ ] Network communication uses secure transport.
- [ ] Third-party SDKs are expected.
- [ ] New native capabilities have been reviewed.

Focus on realistic release risks rather than theoretical hardening.

---

## 17. Store metadata

### iOS

- [ ] App name is correct.
- [ ] Subtitle is correct.
- [ ] Description is accurate.
- [ ] Keywords are appropriate.
- [ ] Screenshots are current.
- [ ] App preview is current where used.
- [ ] Privacy information is complete.
- [ ] Age rating information is complete.
- [ ] App Review information is complete.
- [ ] Support URL works.
- [ ] Privacy policy URL works.
- [ ] Release notes are accurate.

### Android

- [ ] App name is correct.
- [ ] Short description is accurate.
- [ ] Full description is accurate.
- [ ] Screenshots are current.
- [ ] Required graphics are current.
- [ ] Content rating is complete.
- [ ] Target audience information is correct.
- [ ] Ads declaration is correct.
- [ ] Data safety information is accurate.
- [ ] App access information is complete where required.
- [ ] Release notes are accurate.

Do not make store claims that the production build cannot demonstrate.

---

## 18. App Review access

If reviewers need access:

- [ ] Test/demo account exists.
- [ ] Credentials are valid.
- [ ] Login instructions are clear.
- [ ] Required setup steps are documented.
- [ ] Restricted functionality is explained.
- [ ] Reviewers can reach the important functionality.
- [ ] Any region/device/account limitations are explained.

Apple explicitly asks developers to provide required settings, user account information, or special instructions in App Review Information when the app needs them. Missing information can delay review or prevent the app from passing review.

---

## 19. Testing platform checks

### iOS

- [ ] TestFlight build is available.
- [ ] Build processing completed.
- [ ] Critical flows work in TestFlight.
- [ ] Real-device testing is complete.
- [ ] Known TestFlight issues are resolved.

### Android

- [ ] Appropriate Play testing track has been used.
- [ ] Critical flows work from the test build.
- [ ] Required closed testing requirements are satisfied where applicable.
- [ ] Play Console pre-launch report has been reviewed where available.

Google Play can automatically generate pre-launch reports after app bundles/APKs are uploaded, checking areas including stability, compatibility, performance, and accessibility.

For new personal developer accounts created after November 13, 2023, Google currently requires a closed test with at least 12 testers opted in continuously for at least 14 days before production access can be requested.

---

## 20. Final store submission check

### iOS

Before submission:

- [ ] Required metadata is complete.
- [ ] Correct build is selected.
- [ ] Build processing is complete.
- [ ] App Review information is complete.
- [ ] Required declarations are complete.
- [ ] Submission has been reviewed by a human.

Apple's current workflow requires required metadata and the correct build to be selected before adding the app version for review. Adding it for review and actually submitting it are separate steps.

### Android

Before submission:

- [ ] Correct App Bundle is selected.
- [ ] Release notes are complete.
- [ ] Required declarations are complete.
- [ ] Testing requirements are satisfied.
- [ ] Store listing is complete.
- [ ] Release configuration has been reviewed by a human.

Google's current release workflow requires the release artifact and release information to be prepared before moving the release forward.

---

## 21. Backend compatibility

Before releasing the mobile build:

- [ ] Backend supports the new mobile version.
- [ ] API changes are backward compatible where required.
- [ ] Database migrations are complete and safe.
- [ ] Feature flags are compatible with the released app.
- [ ] Older supported app versions will not be broken unintentionally.
- [ ] Required third-party services are available.
- [ ] Rollback/mitigation steps are understood.

Mobile clients do not update at the same time.

Do not deploy a backend change that assumes every user has the newest mobile version unless the product explicitly controls that behavior.

---

## 22. Observability

Before production release:

- [ ] Crash reporting is active.
- [ ] Error monitoring is active.
- [ ] Important backend metrics are available.
- [ ] Critical API errors are visible.
- [ ] Important analytics events are available.
- [ ] Alerts exist for meaningful user-impacting failures.
- [ ] Release/version can be identified in monitoring.
- [ ] The team knows where to investigate failures.

Do not add monitoring that nobody will use.

The important question is:

> Can we detect a serious release problem quickly enough to act?

---

## 23. Recovery readiness

Before release, know what happens if the build is broken.

- [ ] Rollout controls are understood.
- [ ] Feature flags can disable risky functionality where applicable.
- [ ] Backend mitigation is understood.
- [ ] Hotfix build process is known.
- [ ] Store resubmission process is known.
- [ ] Production incident owner is known.
- [ ] Required production logs are accessible.
- [ ] Signing credentials can be recovered safely.

Do not assume a mobile release can always be rolled back like a server deployment.

Existing users may already have the affected version installed.

---

## 24. AI-assisted final audit

AI can help perform a final repository audit.

Useful tasks:

- inspect release configuration
- find development URLs
- check version configuration
- review Expo/EAS configuration
- review permissions
- review deep-link configuration
- review notification configuration
- inspect dependency changes
- draft release notes
- compare store metadata with the product
- identify obvious release blockers

Use:

```text
AI suggestion
    ↓
Developer inspects
    ↓
Verify against source/code
    ↓
Test
    ↓
Human approval
```

Do not use:

```text
AI says "ready"
    ↓
Ship
```

AI output is untrusted until verified.

Never give an AI agent unrestricted access to:

- signing credentials
- store accounts
- production secrets
- production databases
- destructive production tools

Store submission and production release remain human-controlled actions.

---

# 25. Final release gate

The release is ready only when all critical areas pass:

```text
Release candidate
       ↓
Production config
       ↓
Versioning
       ↓
Dependencies
       ↓
Signing
       ↓
Build
       ↓
Artifact verification
       ↓
Real-device testing
       ↓
Privacy
       ↓
Security
       ↓
Store metadata
       ↓
Platform testing
       ↓
Backend compatibility
       ↓
Observability
       ↓
Recovery readiness
       ↓
Human approval
       ↓
SUBMIT
```

### Stop the release if:

- [ ] A critical security issue exists.
- [ ] Production configuration is uncertain.
- [ ] The release artifact cannot be identified.
- [ ] Signing is uncertain.
- [ ] The primary user journey is broken.
- [ ] Required store information is inaccurate or incomplete.
- [ ] A required platform condition has not been verified.
- [ ] A production dependency is unavailable.
- [ ] A serious regression has no mitigation.

If the evidence is incomplete:

> **Not enough evidence yet.**

Investigate before submitting.

---

# Official sources

Use the most specific official source available when verifying current requirements.

### Apple

- App Store Connect Help: https://developer.apple.com/help/app-store-connect/
- App Review: https://developer.apple.com/app-store/review/
- Submit an app: https://developer.apple.com/help/app-store-connect/manage-submissions-to-app-review/submit-an-app
- Upcoming requirements: https://developer.apple.com/news/upcoming-requirements/
- App Store Connect workflow: https://developer.apple.com/help/app-store-connect/get-started/app-store-connect-workflow

### Google

- Play Console Help: https://support.google.com/googleplay/android-developer/
- Prepare and roll out a release: https://support.google.com/googleplay/android-developer/answer/9859348
- New personal developer account testing: https://support.google.com/googleplay/android-developer/answer/14151465
- Target API requirements: https://developer.android.com/google/play/requirements/target-sdk
- Pre-launch reports: https://support.google.com/googleplay/android-developer/answer/9842757

### Expo

- Distribution overview: https://docs.expo.dev/distribution/introduction/
- Build for app stores: https://docs.expo.dev/deploy/build-project/
- EAS Build: https://docs.expo.dev/build/setup/
- EAS Submit: https://docs.expo.dev/submit/introduction/

### React Native

- React Native documentation: https://reactnative.dev/docs/getting-started

**Last verified:** August 11, 2026

Platform requirements and tooling behavior are volatile. Re-check the official sources before every production submission.
