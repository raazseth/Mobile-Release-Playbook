# iOS Release Checklist

Use this checklist before submitting an iOS app to the App Store.

This checklist focuses on the **release stage**. It is framework-independent and can be used with Expo, React Native, or native iOS projects.

For framework-specific build and distribution steps, use the relevant guide under `frameworks/`.

> **Important:** Apple changes App Store Connect, signing, privacy, SDK, and submission requirements over time. Verify current requirements against Apple's official documentation before every release.

---

## 1. Release configuration

### App identity

- [ ] The production app name is correct.
- [ ] The production Bundle ID is correct.
- [ ] The Bundle ID matches the intended App Store Connect app.
- [ ] The production configuration does not point to development or staging services.
- [ ] The correct Apple Developer team is selected.
- [ ] The correct App Store Connect app is being used.

Apple identifies a build by the combination of Bundle ID, version number, and build string. The Bundle ID cannot be changed in App Store Connect after a build has been uploaded.

### Versioning

- [ ] The user-facing version number is correct.
- [ ] The iOS build number has been increased for the new build.
- [ ] The build number has not already been used for the intended release.
- [ ] Release notes match the actual changes.
- [ ] The release can be traced to a known source commit.

---

## 2. Apple Developer account

- [ ] The correct Apple Developer account is being used.
- [ ] The Apple Developer Program membership is active.
- [ ] The correct team is selected.
- [ ] Required agreements are accepted.
- [ ] Required team members have appropriate access.
- [ ] The person handling the release has the required permissions.
- [ ] App Store Connect access is available.

Do not share an Apple Account password between developers. Use Apple's team roles and permissions instead.

Apple provides separate account and App Store Connect tooling for team access, roles, certificates, identifiers, profiles, app management, and submission.

---

## 3. App Store Connect app record

- [ ] The app record exists in App Store Connect.
- [ ] The correct Bundle ID is associated with the app.
- [ ] App name is correct.
- [ ] Primary language is correct.
- [ ] SKU/internal reference is documented.
- [ ] Required agreements are complete.
- [ ] Required metadata fields are identified.
- [ ] App Review contact information is correct.
- [ ] App Review notes are prepared where needed.
- [ ] App access instructions are prepared if reviewers need special access.

An app record is required before uploading a build to App Store Connect.

---

## 4. Production configuration

Before building:

- [ ] Production API endpoints are configured.
- [ ] Production environment variables are present.
- [ ] Development endpoints are removed.
- [ ] Staging credentials are not included.
- [ ] Debug-only behavior is disabled.
- [ ] Test data is not unintentionally exposed.
- [ ] Production feature flags are correct.
- [ ] Production analytics configuration is correct.
- [ ] Production crash-reporting configuration is correct.
- [ ] Required third-party services are configured.
- [ ] iOS-specific configuration is reviewed.

### Expo

If using Expo:

- [ ] Production EAS build profile is correct.
- [ ] Resolved Expo app configuration is correct.
- [ ] Bundle identifier is correct.
- [ ] Required native configuration is present.
- [ ] Required entitlements are configured.
- [ ] Production credentials are configured.

Do not verify only the source `app.json` or `app.config.*`. Verify the configuration actually used by the production build.

---

## 5. Signing

iOS distribution depends on Apple signing assets.

- [ ] Distribution signing is configured.
- [ ] The correct Apple Developer team is selected.
- [ ] The Bundle ID/App ID is correct.
- [ ] Required capabilities are enabled.
- [ ] Distribution certificate is valid where manually managed.
- [ ] Distribution provisioning configuration is valid where manually managed.
- [ ] CI signing configuration is valid where CI is used.
- [ ] The final build is signed for App Store distribution.

Apple's Certificates, Identifiers & Profiles system manages certificates, keys, identifiers, capabilities, devices, and provisioning profiles. Xcode can manage many common signing workflows automatically.

### Automatic signing

If using Xcode automatic signing:

- [ ] The correct team is selected.
- [ ] The Bundle ID is correct.
- [ ] Required capabilities are configured.
- [ ] Xcode can resolve the required signing configuration.
- [ ] The resulting archive is signed for the intended distribution workflow.

### Manual signing

If manually managing signing:

- [ ] Distribution certificate is available.
- [ ] App Store distribution provisioning profile is valid.
- [ ] Provisioning profile references the correct App ID.
- [ ] Provisioning profile uses the intended distribution certificate.
- [ ] Certificates and profiles are not expired or revoked.

Apple documents that an App Store provisioning profile contains an App ID and distribution certificate and is used when uploading an app to App Store Connect.

---

## 6. Signing security

- [ ] No private keys are committed to Git.
- [ ] No certificate private keys are stored in source code.
- [ ] No provisioning secrets are exposed in documentation.
- [ ] CI signing secrets are stored securely.
- [ ] Access is limited to the people and systems that need it.
- [ ] Credential recovery is understood.
- [ ] Credential rotation/revocation procedures are known.

Never paste:

- private keys
- App Store Connect API keys
- signing secrets
- production credentials

into an AI agent.

If a credential is exposed:

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

## 7. Capabilities and entitlements

Review every iOS capability used by the application.

Examples include:

- [ ] Push Notifications
- [ ] Associated Domains
- [ ] Sign in with Apple
- [ ] App Groups
- [ ] Keychain Sharing
- [ ] Background Modes
- [ ] HealthKit, if applicable
- [ ] Apple Pay, if applicable
- [ ] Other required capabilities

For each capability:

- [ ] It is actually required.
- [ ] The App ID has the capability configured where required.
- [ ] The application configuration includes the required entitlement.
- [ ] Production behavior has been tested.
- [ ] Store declarations are accurate where applicable.

Do not enable capabilities simply because they are available.

---

## 8. Production build

Create the actual production candidate.

- [ ] Release configuration is selected.
- [ ] Production environment is used.
- [ ] Correct version is used.
- [ ] Correct build number is used.
- [ ] Correct Bundle ID is used.
- [ ] Correct signing configuration is used.
- [ ] Required capabilities are included.
- [ ] Build completes successfully.
- [ ] Archive/export succeeds where applicable.
- [ ] The final artifact can be uploaded to App Store Connect.

Apple supports uploading builds through Xcode, Transporter, and supported App Store Connect tooling.

### Do not stop at "build succeeded"

A successful build only proves that the build process completed.

Verify the resulting application.

---

## 9. Production artifact verification

Verify the exact build that will be submitted.

- [ ] App launches.
- [ ] Bundle ID is correct.
- [ ] Version is correct.
- [ ] Build number is correct.
- [ ] Production configuration is active.
- [ ] Development URLs are absent.
- [ ] Debug menus are disabled.
- [ ] Test accounts/data are not accidentally exposed.
- [ ] Required native modules are present.
- [ ] Required entitlements are present.
- [ ] Signing is correct.

Install and test the actual release candidate on a real iPhone or iPad where supported by the app.

---

## 10. Device testing

Test the release candidate on real Apple devices.

- [ ] At least one real device has been tested.
- [ ] Important supported iOS versions have been tested.
- [ ] Important screen sizes have been tested.
- [ ] iPhone behavior is correct.
- [ ] iPad behavior is correct if the app supports iPad.
- [ ] Orientation behavior is correct.
- [ ] Keyboard behavior is correct.
- [ ] Safe-area behavior is correct.
- [ ] Background/foreground behavior is correct.
- [ ] App restart works.
- [ ] Upgrade from the previous version works where applicable.

Do not rely only on the simulator for final release validation.

---

## 11. Core user flows

Test the actual production candidate.

- [ ] App launch.
- [ ] First-launch experience.
- [ ] Onboarding.
- [ ] Signup.
- [ ] Login.
- [ ] Logout.
- [ ] Account recovery where applicable.
- [ ] Primary user journey.
- [ ] Important secondary flows.
- [ ] Error states.
- [ ] Network failure handling.
- [ ] Offline behavior where supported.
- [ ] Background/foreground transitions.
- [ ] App restart.
- [ ] Account switching where applicable.

### Release gate

The critical user journey must work from:

```text
Fresh install
   ↓
Launch
   ↓
Primary action
   ↓
Expected result
```

If the primary journey is broken, do not submit.

---

## 12. Permissions

Review every permission and protected resource used by the app.

- [ ] Camera access works where applicable.
- [ ] Microphone access works where applicable.
- [ ] Location access works where applicable.
- [ ] Photos/media access works where applicable.
- [ ] Notifications permission works where applicable.
- [ ] Bluetooth/accessory permissions work where applicable.
- [ ] Permission prompts appear at the correct time.
- [ ] Permission denial is handled.
- [ ] Permission re-enablement is handled.
- [ ] The app does not assume permission was granted.
- [ ] Only required permissions are requested.
- [ ] Privacy declarations match actual behavior.

---

## 13. Push notifications

If the app uses push notifications:

- [ ] Production push configuration is correct.
- [ ] APNs configuration is correct.
- [ ] Production credentials are protected.
- [ ] Notification permission flow works.
- [ ] Notifications arrive on a real device.
- [ ] Foreground behavior is correct.
- [ ] Background behavior is correct.
- [ ] Terminated-app behavior is correct.
- [ ] Notification tap opens the correct destination.
- [ ] Token registration/update works.
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

## 14. Deep links and Universal Links

If the app uses deep links:

- [ ] URL scheme is configured.
- [ ] Production domains are correct.
- [ ] Associated Domains capability is configured where required.
- [ ] Universal Links are tested.
- [ ] Links work when the app is open.
- [ ] Links work when the app is backgrounded.
- [ ] Links work when the app is terminated.
- [ ] Authentication-required links behave correctly.
- [ ] Invalid links are handled.
- [ ] Links open the correct screen.

Test the real production domain, not only a local or staging domain.

---

## 15. Sign in with Apple

If the app supports Sign in with Apple:

- [ ] Production configuration is correct.
- [ ] Sign in works on a real device.
- [ ] New-account flow works.
- [ ] Existing-account flow works.
- [ ] Cancelled authentication is handled.
- [ ] Error states are handled.
- [ ] Account linking behavior is correct.
- [ ] Server-side identity validation is implemented where required.
- [ ] Production redirect/configuration values are correct.

Do not assume a successful client-side authentication response alone is sufficient for account security.

---

## 16. Payments and subscriptions

If the app uses Apple's in-app purchase system:

- [ ] Products are configured in App Store Connect.
- [ ] Product identifiers match the application.
- [ ] Pricing is correct.
- [ ] Subscription configuration is correct.
- [ ] Sandbox/test purchases work.
- [ ] Successful purchases grant the correct entitlement.
- [ ] Failed purchases do not incorrectly grant access.
- [ ] Canceled subscriptions are handled.
- [ ] Expired subscriptions are handled.
- [ ] Restore/recovery behavior is handled where applicable.
- [ ] Server-side transaction/entitlement validation is used where required by the product.
- [ ] The client is not treated as the sole authority for valuable entitlements.

---

## 17. Privacy

Review the actual application behavior.

- [ ] Privacy policy is available.
- [ ] Data collected by the app is documented.
- [ ] Third-party SDKs have been reviewed.
- [ ] Analytics behavior is understood.
- [ ] Crash-reporting behavior is understood.
- [ ] Advertising/tracking behavior is understood.
- [ ] Location collection is understood where applicable.
- [ ] Camera/microphone collection is understood where applicable.
- [ ] Sensitive data handling is understood.
- [ ] Data retention is understood.
- [ ] Data deletion behavior is understood where applicable.
- [ ] App Store privacy information is accurate.

Do not guess privacy declarations.

Base them on the actual production app and the current Apple requirements.

---

## 18. Age rating and content information

- [ ] Age rating questionnaire is complete.
- [ ] Answers match the actual app behavior and content.
- [ ] User-generated content is represented accurately.
- [ ] Messaging/chat functionality is represented accurately.
- [ ] Advertising is represented accurately.
- [ ] Web access is represented accurately.
- [ ] Mature or sensitive content is represented accurately.
- [ ] Region-specific requirements have been considered where applicable.

Apple currently requires an age rating for App Store distribution and generates ratings from the information provided in the age-rating questionnaire.

Do not hard-code rating categories into a generic workflow without checking the current App Store Connect questionnaire.

---

## 19. App Store metadata

- [ ] App name is correct.
- [ ] Subtitle is correct.
- [ ] Description is accurate.
- [ ] Keywords are appropriate.
- [ ] Promotional text is accurate where used.
- [ ] Support URL works.
- [ ] Marketing URL works where used.
- [ ] Privacy policy URL works.
- [ ] Copyright information is correct.
- [ ] Category is correct.
- [ ] Localization is reviewed.
- [ ] Release notes are accurate.

Do not make claims in metadata that the released application does not support.

---

## 20. Screenshots and app previews

- [ ] Screenshots represent the current production app.
- [ ] Screenshots are generated from the intended app version.
- [ ] Screenshots do not contain test data.
- [ ] Screenshots do not contain development URLs.
- [ ] Screenshots do not show unsupported features.
- [ ] Required device sizes are covered.
- [ ] Screenshots are localized where needed.
- [ ] App previews are accurate where used.
- [ ] Promotional assets are current.

Use Apple's current App Store Connect screenshot requirements rather than relying on old image-size lists.

---

## 21. App Review information

Prepare information that helps Apple review the app.

- [ ] Review contact information is correct.
- [ ] Demo/test account is available where required.
- [ ] Login instructions are clear.
- [ ] Special setup instructions are provided.
- [ ] Hardware/device requirements are explained where relevant.
- [ ] Restricted functionality is explained.
- [ ] Subscription/purchase flows are explained where useful.
- [ ] Review notes describe anything that may not be obvious to the reviewer.
- [ ] The reviewer can reach the core functionality without unnecessary barriers.

If reviewers cannot access important functionality, explain how they should access it.

---

## 22. TestFlight

Before App Review, test the release candidate through TestFlight where practical.

- [ ] Build uploaded to App Store Connect.
- [ ] Build processing completed.
- [ ] Correct build is available.
- [ ] Internal testing works.
- [ ] Critical flows work through TestFlight.
- [ ] Push notifications work where applicable.
- [ ] Deep links work where applicable.
- [ ] Payments work through the appropriate testing environment.
- [ ] Feedback has been reviewed.
- [ ] Release-blocking issues are fixed.

Apple provides TestFlight for distributing beta versions and App Store Connect for managing the submission process.

---

## 23. Upload verification

After uploading:

- [ ] The expected build appears in App Store Connect.
- [ ] Build processing has completed.
- [ ] No processing errors remain.
- [ ] Correct version/build number is shown.
- [ ] Correct Bundle ID is shown.
- [ ] Required capabilities/entitlements are present.
- [ ] The intended build is selected for the app version.

Do not assume that a successful upload means the build is ready for review.

---

## 24. Submission readiness

Before submitting:

- [ ] Required metadata is complete.
- [ ] Correct build is selected.
- [ ] Age rating is complete.
- [ ] Privacy information is complete.
- [ ] Screenshots are current.
- [ ] App Review information is complete.
- [ ] Required agreements are complete.
- [ ] Release notes are ready.
- [ ] TestFlight validation is complete where applicable.
- [ ] Known release-blocking issues are resolved.
- [ ] A human has reviewed the final submission.

Apple's current submission workflow requires the appropriate metadata and a selected build before submitting an app version for review.

---

## 25. Submit for App Review

The final submission should be deliberate.

```text
Build uploaded
   ↓
Build processed
   ↓
Metadata complete
   ↓
Build selected
   ↓
Review submission
   ↓
Human approval
   ↓
Submit for Review
```

Apple's current App Store Connect flow separates adding an app version to a review submission from actually submitting it for review.

Do not allow an AI agent to independently submit a production app for review without an explicit, reviewed workflow.

---

## 26. Production release

After App Review approval:

- [ ] Confirm the approved version is the intended version.
- [ ] Confirm the intended build is attached.
- [ ] Confirm release configuration is correct.
- [ ] Confirm production backend is healthy.
- [ ] Confirm monitoring is active.
- [ ] Confirm crash reporting is active.
- [ ] Confirm support channels are ready.
- [ ] Confirm rollout/release settings are correct.
- [ ] Human approves the production release.

Apple's App Store Connect status system distinguishes states such as Ready for Review, In Review, Accepted, Rejected, and Ready for Distribution.

---

## 27. Post-release verification

Install the public release from the App Store.

- [ ] App installs successfully.
- [ ] App launches.
- [ ] Primary user journey works.
- [ ] Login/signup works.
- [ ] Push notifications work where applicable.
- [ ] Deep links work where applicable.
- [ ] Payments work where applicable.
- [ ] Analytics events are working where expected.
- [ ] Crash reporting is working.
- [ ] Backend/API health is normal.
- [ ] No unexpected production errors are visible.

Monitor the first release closely.

---

## 28. Incident readiness

Before release, know what happens if the app is broken.

- [ ] Feature flags can disable risky features where applicable.
- [ ] Backend mitigation options are known.
- [ ] Hotfix build process is known.
- [ ] App Review resubmission process is understood.
- [ ] Store release controls are understood.
- [ ] Support/incident communication path is ready.
- [ ] Production logs and crash reports are accessible.
- [ ] The team knows who can approve an emergency release.

Do not assume that a broken iOS app can always be instantly rolled back to a previous App Store version.

---

# AI-assisted iOS release audit

AI can help inspect the repository and identify obvious release blockers.

Suggested workflow:

```text
Repository
   ↓
AI Release Auditor
   ↓
Inspect configuration
   ↓
Inspect signing configuration
   ↓
Inspect capabilities
   ↓
Inspect permissions
   ↓
Inspect deep links
   ↓
Inspect notification configuration
   ↓
Inspect versioning
   ↓
Inspect release metadata
   ↓
Report findings
   ↓
Developer verifies findings
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

- checking Bundle ID configuration
- checking version/build configuration
- reviewing Expo/EAS configuration
- finding development URLs
- reviewing entitlements
- reviewing permissions
- checking deep-link configuration
- checking notification configuration
- reviewing dependency configuration
- drafting App Store metadata
- drafting release notes
- preparing a release-readiness report
- analyzing build errors

AI should not be treated as the authority for Apple requirements.

For current App Store Connect and Apple Developer requirements, verify the official Apple documentation.

Never provide an AI agent with:

- signing private keys
- certificates containing private keys
- App Store Connect secrets
- production credentials
- account passwords

Keep App Store submission, production release, credential changes, and other irreversible actions behind explicit human approval.

Treat AI findings as untrusted until verified.

---

# Final iOS release gate

Do not submit until these are confirmed:

```text
App identity
        ✓
App Store Connect record
        ✓
Production configuration
        ✓
Signing
        ✓
Capabilities
        ✓
Production build
        ✓
Real-device testing
        ✓
Core flows
        ✓
Permissions
        ✓
Privacy
        ✓
Store metadata
        ✓
TestFlight
        ✓
App Review information
        ✓
Security
        ✓
Monitoring
        ✓
Human approval
        ↓
   APP REVIEW
```

If a critical item cannot be verified, stop and investigate rather than assuming it is correct.


---

# Related documentation

### Checklists

- `checklists/README.md`
- `checklists/android.md`
- `checklists/cross-platform.md`
- `checklists/first-release.md`
- `checklists/post-release.md`
- `checklists/pre-release.md`
- `checklists/production.md`

### Pre-release

- `pre-release/final-release-checklist.md`
- `pre-release/release-readiness.md`

### Publishing (iOS)

- `publishing/ios/README.md`

### Publishing (Android)

- `publishing/android/README.md`

### Store operations

- `store-operations/README.md`

### Templates

- `templates/release-checklist.md`

---

# Official sources

Use Apple's current documentation to verify requirements before every release.

### Apple Developer

- Apple Developer Account Help: https://developer.apple.com/help/account/
- Certificates, Identifiers & Profiles: https://developer.apple.com/help/account/
- Certificates overview: https://developer.apple.com/help/account/certificates/certificates-overview
- Provisioning profiles: https://developer.apple.com/help/account/provisioning-profiles/

### App Store Connect

- App Store Connect Help: https://developer.apple.com/help/app-store-connect/
- App information: https://developer.apple.com/help/app-store-connect/reference/app-information/app-information
- Age ratings: https://developer.apple.com/help/app-store-connect/reference/app-information/age-ratings-values-and-definitions/
- Submit an app: https://developer.apple.com/help/app-store-connect/manage-submissions-to-app-review/submit-an-app
- TestFlight: https://developer.apple.com/testflight/

### Expo

- Distribution overview: https://docs.expo.dev/distribution/introduction/
- EAS Build: https://docs.expo.dev/build/introduction/
- EAS Submit: https://docs.expo.dev/submit/introduction/
- Submit to the Apple App Store: https://docs.expo.dev/submit/ios/

### React Native

- Publishing to App Store: https://reactnative.dev/docs/publishing-to-app-store

**Last verified:** August 11, 2026

Apple requirements, App Store Connect behavior, signing systems, SDK requirements, and release tooling can change. Re-check the official sources before each production submission.
