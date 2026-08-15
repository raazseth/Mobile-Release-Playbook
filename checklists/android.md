# Android Release Checklist

Use this checklist before submitting an Android app to Google Play.

This checklist is written for the **release stage**, not general Android development. It is framework-independent, so it can be used with Expo, React Native, native Android, or another framework.

For framework-specific steps, use the relevant guide under `frameworks/`.

> **Important:** Google Play requirements change. Use the official Google sources linked in this checklist to verify current requirements before every release.

---

## 1. Release configuration

### App identity

- [ ] The production application ID/package name is correct.
- [ ] The application ID is stable and matches the intended Google Play app.
- [ ] The app name shown to users is correct.
- [ ] The production configuration does not point to a development or staging application.

### Versioning

- [ ] The user-facing version name is correct.
- [ ] The Android version code has been increased for this release.
- [ ] The version code has not already been used for the app.
- [ ] The release notes match the actual changes in the build.

### Environment

- [ ] Production API endpoints are configured.
- [ ] Production environment variables are present.
- [ ] Development or staging endpoints are not included accidentally.
- [ ] Debug-only configuration is disabled.
- [ ] Test accounts and test data are not unintentionally exposed to production users.

---

## 2. Google Play account

- [ ] The correct Google Play developer account is being used.
- [ ] The account's developer information is complete and verified where required.
- [ ] The correct account type is being used: Personal or Organization.
- [ ] Required Play Console tasks are completed.
- [ ] The person submitting the release has the required Play Console permissions.
- [ ] Required developer agreements are accepted.

Google Play requires developers to complete applicable Play Console requirements and provide verifiable developer and app information.

### New personal developer accounts

If the Play Console account is a **personal account created after November 13, 2023**:

- [ ] Confirm the account is subject to the new personal-account testing requirements.
- [ ] Complete the required closed test.
- [ ] Have at least **12 testers** opted in continuously for at least **14 days** before applying for production access.
- [ ] Confirm the testing requirement is satisfied in Play Console.
- [ ] Complete the production-access application when eligible.

Google's current guidance states that this requirement applies to new personal developer accounts created after November 13, 2023.

### Device verification

For applicable new personal developer accounts:

- [ ] Complete the required Android device verification in Play Console.
- [ ] Confirm the account owner has completed the verification flow.

Google currently requires applicable new personal accounts to verify access to a real Android mobile device through the Play Console mobile app before making an app available on Google Play.

---

## 3. Android signing

- [ ] The release build is signed for production.
- [ ] The correct signing configuration is being used.
- [ ] The upload key is available to the release process.
- [ ] The signing key is stored securely.
- [ ] No signing secrets are committed to Git.
- [ ] CI/CD uses protected secrets where applicable.
- [ ] The team knows how to recover or rotate the relevant credentials.
- [ ] Play App Signing is configured where applicable.
- [ ] The upload key and app-signing key are not confused.

### Security check

- [ ] No keystore passwords are present in source code.
- [ ] No private keys are present in the repository.
- [ ] No service-account credentials are committed.
- [ ] No credentials are pasted into AI tools.
- [ ] Release credentials are limited to people and systems that need them.

---

## 4. Target Android API level

- [ ] The app's `targetSdkVersion` / target API level satisfies the current Google Play requirement.
- [ ] The build configuration and generated artifact use the intended target API level.
- [ ] The app has been tested against the Android version associated with the target API.
- [ ] Any required migration work has been completed.

### Current 2026 requirement

Google states that, starting **August 31, 2026**, new apps and app updates submitted to Google Play must target **Android 16 (API level 36) or higher**, with exceptions for specific form factors.

For this repository, do not hard-code this as a permanent rule. Re-check the official target API requirement before each release because Google updates this policy over time.

Official source:

- Google Play target API requirements: https://developer.android.com/google/play/requirements/target-sdk

---

## 5. Production build

- [ ] A release build has been generated using the intended production configuration.
- [ ] The output is the correct artifact for Google Play, normally an Android App Bundle (`.aab`) for a standard Play release.
- [ ] The artifact is signed correctly.
- [ ] The build completes without warnings that affect release behavior.
- [ ] The production build has been installed and tested.
- [ ] The artifact is from the expected commit/version.
- [ ] The release artifact has been stored or archived according to the team's release process.

### Build verification

Check the generated artifact for:

- [ ] Correct package/application ID.
- [ ] Correct version name.
- [ ] Correct version code.
- [ ] Correct target API level.
- [ ] Release signing.
- [ ] Production configuration.
- [ ] No development endpoints.
- [ ] No debug-only behavior.

---

## 6. Production behavior

Install and test the actual release build.

### Core flows

- [ ] App launches successfully.
- [ ] First launch works.
- [ ] Existing-user login works.
- [ ] New-user signup works, if applicable.
- [ ] Logout works.
- [ ] The primary user journey works end to end.
- [ ] Important error states work.
- [ ] Network failures are handled.
- [ ] Offline behavior is correct where supported.
- [ ] App restart works.
- [ ] App background/foreground transitions work.
- [ ] App upgrade from the previous production version works.

### Permissions

- [ ] Only required permissions are requested.
- [ ] Permission prompts appear at the correct time.
- [ ] Denied permissions are handled correctly.
- [ ] Re-enabled permissions work correctly.
- [ ] Permission-related functionality is tested on a real device.

### Device behavior

- [ ] The app works on supported Android versions.
- [ ] Important screen sizes have been tested.
- [ ] Important manufacturer/device differences have been considered.
- [ ] Orientation behavior is correct.
- [ ] Keyboard behavior is correct.
- [ ] System back behavior is correct.
- [ ] Status/navigation bar behavior is correct.

---

## 7. Deep links and app links

If the app uses deep links:

- [ ] App links are configured correctly.
- [ ] Links open the expected screen.
- [ ] Links work when the app is installed.
- [ ] Links behave correctly when the app is not running.
- [ ] Invalid or expired links are handled.
- [ ] Authentication-required links behave correctly.
- [ ] Domain verification has been tested.
- [ ] Production domains are used.

---

## 8. Push notifications

If the app uses push notifications:

- [ ] Production Firebase/project configuration is correct.
- [ ] Production notification credentials are configured securely.
- [ ] Permission behavior is correct.
- [ ] Notifications arrive on a real device.
- [ ] Tapping a notification opens the correct destination.
- [ ] Notifications work when the app is foregrounded.
- [ ] Notifications work when the app is backgrounded.
- [ ] Notifications behave correctly after app restart.
- [ ] Notification tokens are handled correctly.
- [ ] Logout/account switching does not cause notification leakage.

---

## 9. Payments and subscriptions

If the app uses Google Play billing:

- [ ] Products are configured in Play Console.
- [ ] Product IDs match the application configuration.
- [ ] Pricing is correct.
- [ ] Subscription configuration is correct.
- [ ] Test purchases have been verified.
- [ ] Successful purchases unlock the correct entitlement.
- [ ] Failed purchases do not unlock access incorrectly.
- [ ] Canceled subscriptions are handled.
- [ ] Expired subscriptions are handled.
- [ ] Restored purchases are handled.
- [ ] Server-side entitlement validation is used where required by the product.
- [ ] The client is not treated as the sole source of truth for valuable entitlements.

---

## 10. Privacy and data safety

Before submitting:

- [ ] The privacy policy is available where required.
- [ ] Data collected by the app has been reviewed.
- [ ] Data shared with third parties has been reviewed.
- [ ] Analytics and crash-reporting SDKs have been included in the review.
- [ ] Advertising SDKs have been reviewed.
- [ ] Location collection has been reviewed.
- [ ] Personal information handling has been reviewed.
- [ ] The Play Console Data safety information accurately represents the released app.
- [ ] Permission declarations match actual app behavior.
- [ ] Third-party SDK behavior has been considered.

Do not guess answers in Play Console declarations. Verify them against the actual application and the current Google Play requirements.

---

## 11. Store listing

### App information

- [ ] App name is correct.
- [ ] Short description is correct.
- [ ] Full description is correct.
- [ ] Category is correct.
- [ ] Tags or equivalent metadata are appropriate.
- [ ] Contact information is correct.
- [ ] Support information is correct.
- [ ] Website URL is correct, if provided.

### Graphics and screenshots

- [ ] App icon is correct.
- [ ] Feature graphic is correct where required.
- [ ] Screenshots show the current production UI.
- [ ] Screenshots do not contain development data.
- [ ] Screenshots do not make unsupported claims.
- [ ] Screenshots are appropriate for the supported devices and store requirements.
- [ ] Promotional text matches the actual app.

### Content declarations

- [ ] Content rating questionnaire is complete.
- [ ] Target audience information is correct.
- [ ] Ads declaration is correct.
- [ ] Data safety information is complete.
- [ ] App access information is provided if reviewers need credentials or special access.
- [ ] Any required declarations are complete.

---

## 12. Testing tracks

Use Play Console testing before production where appropriate.

### Internal testing

- [ ] Internal testing has been used for fast build validation where useful.
- [ ] Testers can install the build.
- [ ] Critical flows have been checked.

### Closed testing

- [ ] Closed testing has been configured where required or useful.
- [ ] Testers represent the intended users where possible.
- [ ] Testers know what to test.
- [ ] Feedback can be collected.
- [ ] Important bugs found during testing are fixed or explicitly accepted.
- [ ] The final production candidate has been tested.

### New personal developer accounts

For applicable new personal developer accounts:

- [ ] At least 12 testers are opted in.
- [ ] The testers remain opted in continuously for at least 14 days.
- [ ] The production-access requirement is satisfied.
- [ ] The production-access application has been submitted when eligible.

Google recommends using closed testing to identify issues and improve the app before production.

---

## 13. Final release audit

Before submitting, verify the exact artifact that will be released.

### Code

- [ ] Release commit is known.
- [ ] No unintended changes are included.
- [ ] Production configuration is correct.
- [ ] Debug functionality is disabled.
- [ ] Development URLs are removed.

### Dependencies

- [ ] Dependencies have been reviewed.
- [ ] No known release-blocking dependency issue remains.
- [ ] Native dependencies are compatible with the production build.
- [ ] Build tooling versions are known.

### Security

- [ ] No secrets are included in the artifact or repository.
- [ ] Production credentials are protected.
- [ ] Authentication and authorization flows work.
- [ ] Sensitive data is handled correctly.
- [ ] Debug logging does not expose sensitive information.

### Quality

- [ ] No release-blocking crash is known.
- [ ] Core flows pass.
- [ ] Important devices/OS versions have been tested.
- [ ] Performance is acceptable.
- [ ] Accessibility issues that block important flows are resolved.

### Store

- [ ] Store listing is accurate.
- [ ] Declarations are accurate.
- [ ] Screenshots match the current app.
- [ ] App access instructions are correct.
- [ ] Release notes are ready.

---

## 14. Submit to Google Play

Before selecting production:

- [ ] The correct application is selected in Play Console.
- [ ] The correct release artifact is uploaded.
- [ ] The release version is correct.
- [ ] Store listing information is complete.
- [ ] Required declarations are complete.
- [ ] Testing requirements are satisfied where applicable.
- [ ] Play Console shows no blocking errors.
- [ ] The release has been reviewed by a human.

Then:

```text
Upload
  ↓
Validate
  ↓
Review release
  ↓
Confirm store information
  ↓
Submit / roll out
  ↓
Monitor
```

Do not automate the final production submission blindly.

A human should confirm that the intended artifact, listing, rollout configuration, and release notes are correct before production release.

---

## 15. Production rollout

After approval:

- [ ] Confirm the release is actually available.
- [ ] Confirm the intended version is visible.
- [ ] Verify installation from Google Play.
- [ ] Test the production-installed version.
- [ ] Monitor crashes.
- [ ] Monitor important user flows.
- [ ] Monitor performance.
- [ ] Monitor reviews and ratings.
- [ ] Watch for unusual support reports.
- [ ] Be ready to stop, limit, or roll back the rollout where the platform and release strategy allow it.

---

## 16. Post-release verification

Within the initial release window:

- [ ] Install the production version from Google Play.
- [ ] Complete the primary user journey.
- [ ] Verify login/signup.
- [ ] Verify push notifications if applicable.
- [ ] Verify deep links if applicable.
- [ ] Verify payments if applicable.
- [ ] Check crash reporting.
- [ ] Check backend/API health.
- [ ] Check analytics events.
- [ ] Check user feedback.
- [ ] Confirm the release is behaving as expected.

If a serious issue appears:

```text
Detect
  ↓
Assess impact
  ↓
Stop or limit rollout if possible
  ↓
Identify root cause
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

# AI-assisted Android release audit

AI can help review the release, but it should not replace the final human check.

A useful workflow is:

```text
Repository
   ↓
AI Release Audit
   ↓
Find blockers
   ↓
Developer verifies findings
   ↓
Fix
   ↓
Build
   ↓
Test
   ↓
Human release approval
```

Ask an AI coding agent to inspect:

- Android configuration
- application ID
- versioning
- target API level
- release build configuration
- signing configuration
- production environment
- permissions
- deep links
- notifications
- payments
- privacy-sensitive SDKs
- store metadata
- known release blockers

The agent should have access only to what it needs.

Never provide signing secrets, production credentials, or private keys to an AI agent.

Treat every AI finding as a suggestion until it is verified.

---

# Final verification

Before production release, the answer to all of these should be **yes**:

- [ ] Do I know exactly which build I am releasing?
- [ ] Is it the production configuration?
- [ ] Is it correctly signed?
- [ ] Does it meet the current Google Play target API requirement?
- [ ] Have the critical user flows been tested on real devices?
- [ ] Are Play Console declarations accurate?
- [ ] Is the store listing accurate?
- [ ] Are testing requirements satisfied for this account?
- [ ] Are credentials and secrets protected?
- [ ] Has a human reviewed the final release?
- [ ] Do I know how I will detect and respond to a production problem?

If any critical answer is **no**, stop and resolve it before release.

---

# Official sources

Use these sources to verify current requirements before release:

- Google Play Console Help: https://support.google.com/googleplay/android-developer/
- Google Play target API requirements: https://developer.android.com/google/play/requirements/target-sdk
- Google Play testing requirements for new personal developer accounts: https://support.google.com/googleplay/android-developer/answer/14151465
- Google Play developer account setup: https://support.google.com/googleplay/android-developer/answer/6112435
- Google Play device verification: https://support.google.com/googleplay/android-developer/answer/14316361

**Last verified:** August 11, 2026

Platform requirements are volatile. Re-check the official sources before every production release.
