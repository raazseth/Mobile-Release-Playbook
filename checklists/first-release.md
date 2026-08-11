# First Release Checklist

Use this checklist when publishing a mobile app to the App Store and Google Play for the first time.

This checklist focuses on the **first production release**. It covers the shared work that must be completed before the first public launch and links the platform-specific work to the appropriate areas of the playbook.

It is framework-independent, with **Expo and React Native as first-class implementations**.

> **Important:** Apple and Google requirements change. Verify current platform requirements against official documentation before submitting the app.

---

## 1. Release planning

- [ ] The app has a clearly identified first-release version.
- [ ] The production release scope is defined.
- [ ] The release owner is identified.
- [ ] The exact source commit for the release is known.
- [ ] Production and development environments are separated.
- [ ] Required backend/API services are production-ready.
- [ ] Required third-party services are configured for production.
- [ ] A post-release monitoring plan exists.
- [ ] A plan exists for handling serious production issues.

Recommended flow:

```text
App ready
   ↓
Production configuration
   ↓
Accounts
   ↓
Signing
   ↓
Testing
   ↓
Store preparation
   ↓
Production builds
   ↓
Store testing
   ↓
Submission
   ↓
Approval
   ↓
Production release
   ↓
Monitoring
```

---

## 2. Application identity

Before creating store records, decide and document the production identity.

### Shared

- [ ] Production app name is final.
- [ ] Production package/application identifiers are final.
- [ ] Production domain is known where applicable.
- [ ] Support email is available.
- [ ] Support website is available where required.
- [ ] Privacy policy URL is available where required.

### iOS

- [ ] Bundle identifier is final.
- [ ] Bundle identifier is registered with the Apple Developer account.
- [ ] The App Store Connect app will use the intended bundle identifier.

### Android

- [ ] Application ID/package name is final.
- [ ] The Play Console app will use the intended application ID.

Application identifiers are important production identifiers. Avoid changing them after publishing unless you understand the platform-specific consequences.

---

## 3. Apple Developer account

If publishing to iOS:

- [ ] Apple Developer account exists.
- [ ] The correct individual or organization account is being used.
- [ ] Required agreements are accepted.
- [ ] Required team members have appropriate access.
- [ ] The developer account is active.
- [ ] Required certificates/signing configuration is available.
- [ ] App Store Connect access is available.

Do not share the Apple account password between developers.

Use the appropriate roles and access controls instead.

---

## 4. Google Play developer account

If publishing to Android:

- [ ] Google Play developer account exists.
- [ ] The correct Personal or Organization account is being used.
- [ ] Required account information is complete.
- [ ] Required agreements are accepted.
- [ ] Required team permissions are configured.
- [ ] The developer account is active.

### New personal developer accounts

If this is a personal Google Play developer account created after **November 13, 2023**:

- [ ] Check whether the account is subject to the current closed-testing requirement.
- [ ] Complete the required closed test if applicable.
- [ ] Confirm at least 12 testers are opted in continuously for at least 14 days where the requirement applies.
- [ ] Complete the production-access application when eligible.

Google's current guidance states that this requirement applies to new personal developer accounts created after November 13, 2023. citeturn0search1

### Device verification

For applicable new personal developer accounts:

- [ ] Complete the required Android device verification.
- [ ] Confirm the account owner has completed the required verification flow.

Verify the current requirements directly in Google Play Console and the official Google documentation before release.

---

## 5. Create store app records

Create the application record on each store.

### App Store Connect

- [ ] App record is created.
- [ ] App name is correct.
- [ ] Primary language is correct.
- [ ] Bundle ID is correct.
- [ ] SKU/reference information is documented.
- [ ] Required agreements are complete.
- [ ] Store metadata fields are understood.
- [ ] App access information is prepared if reviewers need access.

Apple requires an app record to exist in App Store Connect before a build can be uploaded. citeturn0search10

### Google Play Console

- [ ] App is created in Play Console.
- [ ] App name is correct.
- [ ] Default language is correct.
- [ ] Application ID is correct.
- [ ] Required app declarations are identified.
- [ ] Store listing requirements are understood.
- [ ] Testing track is configured where appropriate.

---

## 6. Production configuration

Before building the first release:

- [ ] Production API URL is configured.
- [ ] Production database/backend is ready.
- [ ] Production authentication configuration is ready.
- [ ] Production environment variables are configured.
- [ ] Development API URLs are removed.
- [ ] Staging credentials are removed.
- [ ] Debug flags are disabled.
- [ ] Test accounts are not unintentionally exposed.
- [ ] Production feature flags are correct.
- [ ] Required third-party services are configured.
- [ ] Analytics configuration is correct.
- [ ] Crash reporting is configured.
- [ ] Push notification configuration is ready where applicable.
- [ ] Deep-link configuration is ready where applicable.

For Expo, verify the **resolved production app configuration and EAS build profile**, not only the source configuration file.

---

## 7. Versioning

Define the first public version.

- [ ] User-facing version name is set.
- [ ] iOS build number is set.
- [ ] Android version code is set.
- [ ] Version values are documented.
- [ ] The production build uses the intended values.
- [ ] Future version increments are understood.

Do not reuse a platform build number/version code that has already been submitted where the platform requires it to be unique.

---

## 8. Signing

Production signing must be configured before release.

### iOS

- [ ] Distribution signing is configured.
- [ ] Required certificates are available.
- [ ] Provisioning/signing configuration is valid.
- [ ] App Store distribution configuration is correct.
- [ ] CI credentials are protected if CI is used.

### Android

- [ ] Release signing is configured.
- [ ] Upload key is configured.
- [ ] Play App Signing is configured where applicable.
- [ ] Keystore is stored securely.
- [ ] Keystore passwords are protected.
- [ ] Recovery procedures are documented.

### Security

- [ ] No signing keys are committed to Git.
- [ ] No private keys are included in documentation.
- [ ] No production credentials are stored in source code.
- [ ] CI secrets are protected.
- [ ] Access is limited to people and systems that need it.
- [ ] Credential recovery/rotation is understood.

Never provide signing credentials or private keys to an AI agent.

---

## 9. Build the production candidates

Create production builds for each platform.

### Expo

If using Expo:

- [ ] Production EAS profile exists.
- [ ] EAS project is correctly configured.
- [ ] Production credentials are configured.
- [ ] iOS production build succeeds.
- [ ] Android production build succeeds.
- [ ] Generated artifacts are the intended production builds.

Relevant Expo documentation:

- https://docs.expo.dev/distribution/introduction/
- https://docs.expo.dev/build/setup/
- https://docs.expo.dev/build-reference/variants/

### React Native

If using React Native without a fully managed Expo workflow:

- [ ] iOS Release configuration is valid.
- [ ] Android Release configuration is valid.
- [ ] iOS archive/build succeeds.
- [ ] Android release bundle succeeds.
- [ ] Native signing is correct.
- [ ] Production configuration is included.

---

## 10. Verify production artifacts

Do not assume a successful build means the release is correct.

Verify the actual artifacts.

### Both platforms

- [ ] Correct application identity.
- [ ] Correct version.
- [ ] Correct build number/version code.
- [ ] Production environment.
- [ ] Correct signing.
- [ ] No development endpoints.
- [ ] No debug-only behavior.
- [ ] Expected native modules are included.
- [ ] Expected assets are included.

### Install the actual release builds

- [ ] iOS production build installs.
- [ ] Android production build installs.
- [ ] App launches successfully.
- [ ] No first-launch crash occurs.

---

## 11. Functional testing

Test the actual production candidate.

### Core flows

- [ ] App launch.
- [ ] Onboarding.
- [ ] Signup.
- [ ] Login.
- [ ] Logout.
- [ ] Password/account recovery where applicable.
- [ ] Primary user journey.
- [ ] Important error states.
- [ ] Network failure behavior.
- [ ] Offline behavior where supported.
- [ ] Background/foreground behavior.
- [ ] App restart.
- [ ] Account switching where applicable.

### Real-device testing

- [ ] At least one real iOS device tested.
- [ ] At least one real Android device tested.
- [ ] Important supported OS versions tested.
- [ ] Important screen sizes tested.
- [ ] Keyboard behavior checked.
- [ ] System navigation/back behavior checked.
- [ ] Orientation behavior checked where applicable.

Do not treat simulator/emulator testing as a replacement for real-device testing.

---

## 12. Permissions

Review every permission used by the app.

- [ ] Camera permission works where applicable.
- [ ] Microphone permission works where applicable.
- [ ] Location permission works where applicable.
- [ ] Notification permission works where applicable.
- [ ] Photo/media permissions work where applicable.
- [ ] Permission denial is handled.
- [ ] Permission re-enablement is handled.
- [ ] The app does not assume permissions are granted.
- [ ] Only necessary permissions are requested.
- [ ] Store declarations match actual app behavior.

---

## 13. Deep links

If the app uses deep links:

- [ ] URL scheme is configured.
- [ ] Production domains are configured.
- [ ] iOS Universal Links work where applicable.
- [ ] Android App Links work where applicable.
- [ ] Links work when the app is open.
- [ ] Links work when the app is backgrounded.
- [ ] Links work when the app is closed.
- [ ] Authentication-required links behave correctly.
- [ ] Invalid links are handled.
- [ ] Deep-link destinations are correct.

---

## 14. Push notifications

If push notifications are used:

- [ ] Production notification project/configuration is correct.
- [ ] iOS notification configuration is correct.
- [ ] Android notification configuration is correct.
- [ ] Permission flow works.
- [ ] Notifications arrive on real devices.
- [ ] Foreground behavior works.
- [ ] Background behavior works.
- [ ] Notification tap navigation works.
- [ ] Token registration works.
- [ ] Logout/account switching does not cause notification leakage.

Test:

```text
App open
App backgrounded
App terminated
User logged out
User logged in
```

---

## 15. Payments and subscriptions

If the app uses in-app purchases or subscriptions:

- [ ] Store products are configured.
- [ ] Product IDs match the application.
- [ ] Pricing is correct.
- [ ] Test purchase works.
- [ ] Successful purchase grants the correct entitlement.
- [ ] Failed purchase does not incorrectly grant access.
- [ ] Restore purchase works where applicable.
- [ ] Cancellation is handled.
- [ ] Expiration is handled.
- [ ] Subscription state is handled.
- [ ] Backend entitlement validation exists where required.

Do not treat the mobile client as the sole authority for valuable entitlements.

---

## 16. Privacy and compliance

Review the actual production behavior.

- [ ] Privacy policy is available.
- [ ] Data collection has been documented.
- [ ] Third-party SDKs have been reviewed.
- [ ] Analytics behavior is understood.
- [ ] Crash reporting behavior is understood.
- [ ] Advertising/tracking behavior is understood where applicable.
- [ ] Location collection is understood where applicable.
- [ ] Camera/microphone access is understood where applicable.
- [ ] Data retention is understood.
- [ ] Data deletion behavior is understood where applicable.
- [ ] Store privacy declarations are accurate.
- [ ] Required platform permissions/declarations are accurate.

Do not guess store privacy declarations. Base them on the actual application behavior and current platform guidance.

---

## 17. Store listing

### Shared

- [ ] App name is correct.
- [ ] Description is accurate.
- [ ] Support information is correct.
- [ ] Website is correct.
- [ ] Privacy policy URL works.
- [ ] Marketing claims are accurate.
- [ ] Release notes are ready.
- [ ] Localization is reviewed where used.

### iOS

- [ ] App Store screenshots are ready.
- [ ] App preview is ready where applicable.
- [ ] App icon is correct.
- [ ] Required metadata is complete.
- [ ] Age/content rating information is complete.
- [ ] App Review information is complete.

### Android

- [ ] Google Play screenshots are ready.
- [ ] Feature graphic is ready where required.
- [ ] App icon is correct.
- [ ] Store listing is complete.
- [ ] Content rating is complete.
- [ ] Target audience information is correct.
- [ ] Ads declaration is correct.
- [ ] Data safety information is complete.
- [ ] App access instructions are complete where required.

---

## 18. Store testing

### iOS

- [ ] Production candidate uploaded to App Store Connect.
- [ ] Build processing completed.
- [ ] TestFlight installation works.
- [ ] Critical flows work through TestFlight.
- [ ] Required external testing is complete where applicable.
- [ ] Final build is selected for review.

### Android

- [ ] Production candidate uploaded to Play Console.
- [ ] Internal testing completed where useful.
- [ ] Closed testing completed where required.
- [ ] Testers can install the app.
- [ ] Critical flows work through Google Play testing.
- [ ] Required testing requirements are satisfied.

For applicable new personal Google Play developer accounts created after November 13, 2023, Google currently requires a closed test with at least 12 opted-in testers for 14 continuous days before production access can be requested. citeturn0search1

---

## 19. Review preparation

Before submission:

- [ ] App behaves as described in the store listing.
- [ ] Reviewers can access the app where access is required.
- [ ] Demo/test credentials are available where appropriate.
- [ ] Review instructions are clear.
- [ ] Restricted functionality is explained.
- [ ] Subscription/purchase behavior is clear.
- [ ] Privacy behavior is consistent with declarations.
- [ ] No obvious crashes exist.
- [ ] No placeholder content remains.
- [ ] No development tools or debug menus are exposed.
- [ ] Store metadata does not make unsupported claims.

---

## 20. Final security review

Before uploading the first production build:

- [ ] No secrets are committed.
- [ ] No API keys are unintentionally embedded.
- [ ] Production credentials are protected.
- [ ] Signing credentials are protected.
- [ ] Debug logging does not expose sensitive data.
- [ ] Authentication is configured correctly.
- [ ] Authorization is enforced server-side where applicable.
- [ ] Sensitive local storage is appropriate.
- [ ] Network communication uses secure transport.
- [ ] Third-party SDKs have been reviewed.
- [ ] Production endpoints are correct.

---

## 21. Final build verification

Verify the exact artifacts that will be submitted.

### iOS

- [ ] Correct bundle identifier.
- [ ] Correct version.
- [ ] Correct build number.
- [ ] Correct signing.
- [ ] Production configuration.
- [ ] Correct app icon/assets.
- [ ] Tested on a real device.
- [ ] Tested through TestFlight.

### Android

- [ ] Correct application ID.
- [ ] Correct version.
- [ ] Correct version code.
- [ ] Correct signing.
- [ ] Production configuration.
- [ ] Correct app icon/assets.
- [ ] Tested on a real device.
- [ ] Tested through Google Play testing.

---

## 22. Submission

### iOS

- [ ] Final build uploaded.
- [ ] Build processed successfully.
- [ ] Metadata complete.
- [ ] Required declarations complete.
- [ ] App Review information complete.
- [ ] Build selected.
- [ ] Submission reviewed by a human.
- [ ] App submitted for review.

### Android

- [ ] Final App Bundle uploaded.
- [ ] Required declarations complete.
- [ ] Store listing complete.
- [ ] Testing requirements satisfied.
- [ ] Release reviewed by a human.
- [ ] Production rollout configuration checked.
- [ ] Release submitted/published according to the intended rollout.

Uploading a build and releasing it to production are separate steps. Do not assume that a successful upload means the app is already live.

---

## 23. Production release

Before making the app publicly available:

- [ ] Exact release version is confirmed.
- [ ] Exact iOS/Android artifacts are confirmed.
- [ ] Store metadata is confirmed.
- [ ] Production backend is healthy.
- [ ] Monitoring is active.
- [ ] Crash reporting is active.
- [ ] Analytics are active where required.
- [ ] Support contact is ready.
- [ ] Incident response path is known.
- [ ] Rollout strategy is understood.

If the platform provides a staged or phased rollout option appropriate for the release, consider using it for the first production launch.

---

## 24. First-release monitoring

The first release needs closer monitoring than a routine update.

Immediately after release:

- [ ] Install the public version from each store.
- [ ] Complete the primary user journey.
- [ ] Verify login/signup.
- [ ] Verify payments where applicable.
- [ ] Verify push notifications where applicable.
- [ ] Verify deep links where applicable.
- [ ] Check crash reporting.
- [ ] Check backend/API health.
- [ ] Check critical analytics.
- [ ] Monitor reviews and ratings.
- [ ] Monitor support reports.
- [ ] Watch for abnormal error rates.

If a serious issue appears:

```text
Detect
  ↓
Assess impact
  ↓
Limit rollout if possible
  ↓
Identify cause
  ↓
Fix
  ↓
Test
  ↓
Release hotfix
  ↓
Verify
```

---

## 25. First-release rollback and recovery

Before launch, know what you can actually do if the release is broken.

- [ ] Rollout controls are understood.
- [ ] Feature flags can disable risky functionality where applicable.
- [ ] Kill switches exist for genuinely high-risk features where appropriate.
- [ ] Backend rollback/recovery is understood.
- [ ] Database migration recovery is understood.
- [ ] Hotfix build process is understood.
- [ ] Store submission process is understood.
- [ ] Support communication process is ready.

Do not assume a mobile app can always be instantly rolled back to the previous store version. Store distribution and update behavior can limit rollback options.

---

# AI-assisted first-release audit

A first release is a good use case for AI-assisted review.

An AI coding tool can inspect the repository for obvious release blockers before the final human review.

Suggested workflow:

```text
Repository
   ↓
AI Release Auditor
   ↓
Configuration checks
   ↓
Dependency checks
   ↓
Permissions checks
   ↓
Signing/configuration checks
   ↓
Store-readiness checks
   ↓
Potential blockers
   ↓
Developer verification
   ↓
Fix
   ↓
Build
   ↓
Test
   ↓
Human approval
```

Useful AI tasks include:

- finding development URLs
- checking version configuration
- reviewing Expo/EAS configuration
- checking native configuration
- finding obvious debug code
- reviewing permissions
- checking deep-link configuration
- checking notification configuration
- reviewing dependency versions
- preparing store metadata drafts
- preparing release notes
- generating a release-readiness report

AI should not be treated as the authority for Apple or Google requirements.

For current store requirements, verify the official documentation.

Never provide an AI agent with:

- signing private keys
- keystore passwords
- App Store Connect secrets
- Google Play service-account secrets
- production credentials

Keep production submission and other irreversible actions behind explicit human approval.

---

# Final first-release gate

Do not submit until these are all confirmed:

```text
Application identity
        ✓
Store accounts
        ✓
Production configuration
        ✓
Signing
        ✓
Production builds
        ✓
Real-device testing
        ✓
Privacy
        ✓
Store metadata
        ✓
Store testing
        ✓
Review preparation
        ✓
Security
        ✓
Monitoring
        ✓
Recovery plan
        ✓
Human approval
        ↓
     RELEASE
```

The first release should be treated as a controlled production launch, not simply the final step after development.

---

# Official sources

Verify current requirements before the first production release.

### Apple

- App Store Connect Help: https://developer.apple.com/help/app-store-connect/
- Create an app record: https://developer.apple.com/help/app-store-connect/create-an-app-record/add-a-new-app/
- Submit an app for review: https://developer.apple.com/help/app-store-connect/manage-submissions-to-app-review/submit-an-app
- TestFlight: https://developer.apple.com/testflight/

### Google

- Google Play Console Help: https://support.google.com/googleplay/android-developer/
- Create and set up an app: https://support.google.com/googleplay/android-developer/answer/9859152
- Testing requirements: https://support.google.com/googleplay/android-developer/answer/14151465
- Target API requirements: https://developer.android.com/google/play/requirements/target-sdk

### Expo

- Distribution overview: https://docs.expo.dev/distribution/introduction/
- EAS Build: https://docs.expo.dev/build/introduction/
- EAS Submit: https://docs.expo.dev/submit/introduction/
- App stores: https://docs.expo.dev/deploy/build-project/

### React Native

- Publishing to Google Play: https://reactnative.dev/docs/signed-apk-android
- Publishing to Apple App Store: https://reactnative.dev/docs/publishing-to-app-store

**Last verified:** August 11, 2026

Platform requirements, store policies, and tooling behavior are volatile. Re-check the official sources immediately before submission.
