# Expo Store Submission

This guide explains how to move a **verified Expo/React Native release artifact** into Apple App Store Connect or Google Play Console using EAS Submit.

The important distinction is:

```text
Build
 ↓
Verify
 ↓
Test
 ↓
Approve
 ↓
Submit
 ↓
Store processing
 ↓
Review / testing / rollout
```

**Submission is not the same as release.**

EAS Submit uploads the binary to the store's distribution pipeline. Apple and Google still control store metadata, review, testing tracks, and production rollout.

---

# 1. What EAS Submit does

EAS Submit is a hosted service for uploading Android and iOS application binaries to:

```text
Google Play Console
App Store Connect
```

It can submit:

```text
Android
→ .aab

iOS
→ .ipa
```

It works with:

```text
EAS Build artifacts
+
locally built artifacts
```

For local artifacts, specify the file with `--path`.

Basic commands:

```bash
eas submit --platform android
```

```bash
eas submit --platform ios
```

You can also build and submit in one command:

```bash
eas build --platform ios --auto-submit
```

EAS Submit can run locally, from CI/CD, or through EAS Workflows.

---

# 2. What EAS Submit does not do

EAS Submit does **not** make an app production-ready.

It does not replace:

```text
Store account setup
Store listing configuration
Screenshots
App description
Privacy information
Content declarations
Age/content ratings
Store review
Release approval
Production monitoring
```

It also does not manage all store metadata automatically.

Expo's current documentation explicitly states that EAS Submit uploads the binary but does not manage store listing metadata, screenshots, or release notes. EAS Metadata can be used for supported Apple App Store metadata workflows.

Use the repository's shared documentation for those areas:

```text
store-accounts/
store-operations/
privacy-compliance/
publishing/
pre-release/
post-release/
```

---

# 3. The correct mental model

Think of EAS Submit as:

```text
"Put this exact signed binary into the store pipeline."
```

Not:

```text
"Publish my app."
```

For example, on iOS:

```text
IPA
 ↓
EAS Submit
 ↓
App Store Connect
 ↓
TestFlight processing
 ↓
Select build
 ↓
Complete metadata
 ↓
Submit for App Review
 ↓
Apple review
 ↓
App Store release
```

On Android:

```text
AAB
 ↓
EAS Submit
 ↓
Google Play Console
 ↓
Selected testing/production track
 ↓
Store setup / release checks
 ↓
Google review / processing
 ↓
Rollout
```

The exact store workflow varies by platform.

---

# 4. Before submitting

Do not start with:

```bash
eas submit
```

Start with:

```text
Release candidate
 ↓
Application identity
 ↓
Version
 ↓
Environment
 ↓
Signing
 ↓
Build
 ↓
Artifact verification
 ↓
Real-device testing
 ↓
Store readiness
 ↓
Human approval
 ↓
Submission
```

The repository's first-release and pre-release gates require the release artifact, configuration, signing, testing, privacy, store readiness, security, monitoring, and recovery plan to be verified before submission.

---

# 5. Submission prerequisites

Before using EAS Submit, confirm:

```text
[ ] Expo/EAS project is configured
[ ] Correct application identifier exists
[ ] Correct store app exists
[ ] Correct production artifact exists
[ ] Artifact is signed
[ ] Version is correct
[ ] Build number/version code is correct
[ ] Store credentials are configured
[ ] Store metadata is ready
[ ] Required privacy information is ready
[ ] Required testing is complete
[ ] Human approval is complete
```

For EAS-specific setup:

```text
eas.json
EAS credentials
EAS project
submission profile
```

See:

```text
frameworks/expo/eas.md
frameworks/expo/credentials.md
frameworks/expo/build.md
```

The repository's credential model intentionally separates signing credentials from store submission credentials.

---

# 6. Android submission

For Google Play, the release artifact should normally be:

```text
.aab
```

A production EAS build normally produces an Android App Bundle:

```bash
eas build --platform android --profile production
```

Expo's current documentation states that Google Play requires new apps to be published as app bundles rather than APKs. An APK build is useful for device/emulator installation but is not the normal Play Store submission artifact.

---

# 7. Android prerequisites

Before using EAS Submit for Google Play, you need:

```text
Google Play Developer account
+
App created in Google Play Console
+
Google Service Account credentials
+
Android package name
+
Signed production AAB
```

Expo currently documents the Google Service Account as the credential EAS Submit uses to submit on your behalf.

Your Expo configuration should contain the package name:

```json
{
 "android": {
 "package": "com.example.app"
 }
}
```

The package name must match the application you are submitting to.

Do not change it casually after the Play Store application exists.

---

# 8. Android first submission

Google Play has an important first-release consideration.

Expo's current documentation says the first submission can be handled through EAS Submit and defaults to the internal testing track. It also documents manual first-upload options.

A safe first-release flow is:

```text
Create app in Play Console
 ↓
Configure service account
 ↓
Create production AAB
 ↓
Submit to internal testing
 ↓
Verify
 ↓
Complete store setup
 ↓
Promote / release according to your rollout plan
```

Do not assume that:

```text
eas submit
→ production for everyone
```

The track and release status matter.

---

# 9. Android submission command

Basic:

```bash
eas submit --platform android
```

This can prompt you to select the build.

For a specific EAS build:

```bash
eas submit --platform android --id <BUILD_ID>
```

For a local artifact:

```bash
eas submit \
 --platform android \
 --path ./app.aab
```

Current EAS CLI supports selecting by build ID, latest build, local path, or archive URL.

---

# 10. Prefer exact build IDs for production

Avoid ambiguity.

This:

```bash
eas submit --platform android --latest
```

means:

```text
submit whatever EAS currently considers the latest Android build
```

That can be convenient for development.

For an important production release, prefer:

```bash
eas submit \
 --platform android \
 --id <APPROVED_BUILD_ID>
```

This gives you:

```text
Approved release
 ↓
Exact EAS build
 ↓
Exact artifact
 ↓
Submission
```

The current EAS CLI supports `--id` specifically for selecting a build.

---

# 11. Android release tracks

Google Play supports different release tracks.

Common examples include:

```text
Internal testing
Closed testing
Open testing
Production
```

EAS Submit can place an Android build into the configured Play track.

The track does not automatically mean public production.

For example:

```text
Internal testing
→ internal testers

Closed testing
→ selected testers

Production
→ production release process
```

Use:

```text
publishing/android/
release-strategy/
```

for the broader store and rollout process.

---

# 12. Android draft releases

If you want to upload a build without immediately rolling it out through the selected track, EAS supports a submission configuration using:

```text
releaseStatus
```

with:

```text
draft
```

Expo's current Android submission guide documents this as a way to upload without rolling out the release.

Conceptually:

```json
{
 "submit": {
 "production": {
 "android": {
 "releaseStatus": "draft"
 }
 }
 }
}
```

Use the current `eas.json` reference when configuring the exact submission profile.

---

# 13. Android Play Console still matters

EAS Submit does not replace Play Console.

You still need to manage things such as:

```text
Store listing
Privacy policy
Data safety
Content declarations
App access
Testing requirements
Target API requirements
Screenshots
Release notes
Production rollout
```

Current Play Console requirements are volatile.

Verify them immediately before submission using official Google documentation.

---

# 14. iOS submission

For iOS, the production artifact is:

```text
.ipa
```

Build:

```bash
eas build --platform ios --profile production
```

Submit:

```bash
eas submit --platform ios
```

Expo currently documents EAS Submit as the recommended way to upload iOS builds to App Store Connect and notes that it works from macOS, Linux, and Windows.

This is particularly useful because an iOS build can be submitted from a non-macOS machine when the artifact and credentials are correctly configured.

---

# 15. iOS prerequisites

You need:

```text
Apple Developer account
+
App Store Connect app
+
iOS bundle identifier
+
Signed production IPA
+
App Store Connect submission credentials
```

Your Expo configuration should contain:

```json
{
 "ios": {
 "bundleIdentifier": "com.example.app"
 }
}
```

The bundle identifier must match the intended Apple application.

Do not change it casually after the app exists in App Store Connect.

---

# 16. App Store Connect App ID

EAS Submit can use an App Store Connect app identifier through the submission profile.

Example:

```json
{
 "submit": {
 "production": {
 "ios": {
 "ascAppId": "1234567890"
 }
 }
 }
}
```

Expo's current iOS submission documentation identifies `ascAppId` as the App Store Connect app's Apple ID and explains where to find it in App Store Connect.

Do not confuse:

```text
ascAppId
```

with:

```text
bundleIdentifier
```

They identify different things.

---

# 17. iOS submission command

Basic:

```bash
eas submit --platform ios
```

Specific build:

```bash
eas submit \
 --platform ios \
 --id <BUILD_ID>
```

Local artifact:

```bash
eas submit \
 --platform ios \
 --path ./app.ipa
```

The current EAS CLI supports all of these selection modes.

---

# 18. iOS TestFlight is not App Store release

This distinction is critical.

After EAS Submit uploads the IPA:

```text
IPA
 ↓
App Store Connect
 ↓
Processing
 ↓
TestFlight
```

That does **not** automatically mean:

```text
App Store production
```

To release publicly:

```text
App Store Connect
 ↓
Complete metadata
 ↓
Select build
 ↓
Submit for App Review
 ↓
Apple review
 ↓
Release
```

Expo's current documentation explicitly states that an iOS submission becomes available in TestFlight after processing but still requires App Store Connect actions for App Store production release.

---

# 19. iOS TestFlight workflow

Use:

```text
Production IPA
 ↓
EAS Submit
 ↓
App Store Connect processing
 ↓
TestFlight
 ↓
Internal testing
 ↓
Optional external testing
 ↓
App Store submission
```

Use TestFlight to catch:

```text
production configuration problems
device issues
permissions
push notification issues
deep-link issues
authentication issues
performance problems
crashes
```

Do not treat a successful upload as proof that the app is ready for App Review.

---

# 20. iOS "What to Test"

EAS Submit supports the current CLI option:

```bash
eas submit \
 --platform ios \
 --what-to-test "Test login, onboarding, and payment flow"
```

The current EAS CLI exposes `--what-to-test` for iOS/TestFlight submissions.

Keep this short and useful.

Good:

```text
Test onboarding, login, push notifications, and checkout.
```

Avoid:

```text
Everything.
```

---

# 21. iOS TestFlight groups

The current EAS CLI supports:

```bash
--groups
```

for internal TestFlight testing groups.

Example:

```bash
eas submit \
 --platform ios \
 --groups "Internal QA"
```

Use this when the TestFlight groups are already configured and the release process intentionally targets them.

Do not use arbitrary group names in production automation.

Verify the configured App Store Connect groups first.

---

# 22. Submission profiles

Keep submission configuration in `eas.json`.

Example:

```json
{
 "submit": {
 "production": {
 "android": {
 "track": "internal"
 },
 "ios": {
 "ascAppId": "1234567890"
 }
 }
 }
}
```

The exact fields differ by platform.

A submission profile answers:

```text
Which store?
Which application?
Which credentials?
Which release target?
Which submission-specific configuration?
```

EAS documentation states that `eas.json` is not mandatory for every EAS Submit use case, but it makes switching between submission configurations easier and is important for non-interactive CI/CD workflows.

---

# 23. Build profile vs submission profile

Keep these separate.

### Build profile

```text
How do I create the artifact?
```

Example:

```json
{
 "build": {
 "production": {}
 }
}
```

### Submission profile

```text
Where and how do I upload the artifact?
```

Example:

```json
{
 "submit": {
 "production": {
 "ios": {
 "ascAppId": "1234567890"
 }
 }
 }
}
```

This separation makes the release flow easier to understand and audit.

---

# 24. Build and submit together

EAS supports:

```bash
eas build \
 --platform android \
 --profile production \
 --auto-submit
```

and:

```bash
eas build \
 --platform ios \
 --profile production \
 --auto-submit
```

This can be useful once the release workflow is mature.

But do not make this the default for a new project.

Prefer:

```text
Build
 ↓
Inspect
 ↓
Test
 ↓
Approve
 ↓
Submit
```

before moving to:

```text
Build
 ↓
Automatic submission
```

---

# 25. When auto-submit is appropriate

Consider automatic submission when:

```text
Builds are reliable
+
Artifact selection is deterministic
+
Store metadata is ready
+
Credentials are protected
+
Production approval is explicit
+
Monitoring exists
+
Recovery is documented
```

Do not enable automatic production submission just to remove one manual command.

The risk is:

```text
wrong artifact
+
wrong environment
+
wrong version
+
wrong store target
=
production release problem
```

---

# 26. EAS Workflows

EAS Workflows can automate submission after a build.

Conceptually:

```text
Git event
 ↓
Build
 ↓
Build ID
 ↓
Submit
```

Expo currently supports pre-packaged `build` and `submit` jobs in EAS Workflows.

Example:

```yaml
jobs:
 build_android:
 name: Build Android
 type: build
 params:
 platform: android
 profile: production

 submit_android:
 name: Submit Android
 type: submit
 needs:
 - build_android
 params:
 profile: production
 build_id: ${{ needs.build_android.outputs.build_id }}
```

The exact workflow schema can change. Validate it against current Expo documentation before committing a production workflow.

---

# 27. GitHub Actions submission

EAS Submit can also run from external CI/CD systems.

Typical model:

```text
GitHub Actions
 ↓
EXPO_TOKEN
 ↓
eas submit
 ↓
Store
```

For non-interactive CI:

```bash
eas submit \
 --platform android \
 --profile production \
 --non-interactive
```

or:

```bash
eas submit \
 --platform ios \
 --profile production \
 --non-interactive
```

Expo's current documentation confirms that EAS Submit can be used from GitHub Actions and other CI/CD services.

Never commit:

```text
EXPO_TOKEN
```

or store credentials.

Use protected CI secrets.

---

# 28. Exact artifact selection in CI

Avoid:

```bash
eas submit --latest
```

for a high-risk production pipeline when the build job already produced a specific build ID.

Prefer:

```text
build job
 ↓
build_id
 ↓
submit job
```

Example:

```yaml
submit_android:
 type: submit
 needs:
 - build_android
 params:
 profile: production
 build_id: ${{ needs.build_android.outputs.build_id }}
```

This creates a deterministic relationship between:

```text
source
→ build
→ artifact
→ submission
```

---

# 29. Submission credentials

Submission credentials are not the same as signing credentials.

### Android

EAS Submit requires:

```text
Google Service Account
```

with access to the application in Google Play Console.

### iOS

EAS Submit uses Apple/App Store Connect authentication and can use App Store Connect API credentials when configured.

Keep these separate from:

```text
Android keystore
iOS distribution certificate
iOS provisioning profile
```

See:

```text
frameworks/expo/credentials.md
signing/
```

The repository's credential guidance explicitly separates signing, submission, and runtime integration credentials.

---

# 30. Protect submission credentials

Never put credentials in:

```text
eas.json
README.md
GitHub repository
issue
pull request
AI prompt
chat message
shell history when avoidable
```

Prefer:

```text
EAS credential storage
+
CI secret storage
+
least privilege
+
rotation
+
recovery
```

For GitHub Actions, use GitHub's protected secrets and environments.

For EAS, use its supported credential-management mechanisms.

---

# 31. Store metadata is separate

EAS Submit handles the binary.

Store metadata is a different concern.

Examples:

```text
App name
Description
Screenshots
App icon
Promotional assets
Release notes
Privacy information
Age/content ratings
Data safety
App access instructions
Localization
```

Use:

```text
store-operations/
publishing/
templates/
privacy-compliance/
```

for these areas.

Do not put all store listing logic into `submit.md`.

---

# 32. Submission does not guarantee review

After upload:

```text
Upload successful
```

does not mean:

```text
Review passed
```

or:

```text
Production released
```

There are multiple states.

A useful model is:

```text
Binary uploaded
 ↓
Store processing
 ↓
Testing / draft
 ↓
Submission
 ↓
Review
 ↓
Approved
 ↓
Release
 ↓
Production monitoring
```

Platform-specific states differ.

Always verify the current status in the store console.

---

# 33. Submission verification

After submitting, verify the store received the intended artifact.

Check:

```text
Application
Version
Build number / version code
Upload time
Track / TestFlight
Artifact status
Processing status
Warnings
Errors
```

Compare against your release record:

```text
Git commit
EAS build ID
Platform
Profile
Environment
Version
Build number
Submission
```

This gives you traceability.

---

# 34. Android submission verification

In Google Play Console verify:

```text
Correct application
Correct version
Correct AAB
Correct version code
Correct track
Correct release status
Correct store configuration
No blocking errors
```

If the build is in:

```text
internal testing
```

do not assume it has been promoted to:

```text
production
```

The track is part of the release state.

---

# 35. iOS submission verification

In App Store Connect verify:

```text
Correct application
Correct version
Correct build number
Correct IPA
Processing completed
Export compliance status
TestFlight availability
Metadata
Review status
```

Remember:

```text
Uploaded to TestFlight
≠
App Store release
```

The App Store submission remains a separate step.

---

# 36. Export compliance

iOS applications can require export-compliance information in App Store Connect.

The exact questions depend on the application's use of encryption and other factors.

Do not guess.

Check the current App Store Connect workflow and Apple's official documentation for the specific application.

If the application uses:

```text
HTTPS
authentication
custom cryptography
encryption libraries
```

do not automatically assume the answer to Apple's compliance questions.

Treat this as a store/account requirement that needs verification.

---

# 37. First Android submission

A practical first-release sequence:

```text
Create Play Console app
 ↓
Configure package name
 ↓
Configure Google service account
 ↓
Build production AAB
 ↓
Submit
 ↓
Internal testing / draft
 ↓
Complete Play Console setup
 ↓
Testing requirements
 ↓
Production rollout
```

Expo currently documents that the default first EAS submission can create the first release on the internal testing track.

If you prefer manual first-upload control, Play Console also supports manual submission.

---

# 38. First iOS submission

A practical first-release sequence:

```text
Apple Developer account
 ↓
App Store Connect app
 ↓
Bundle identifier
 ↓
Production IPA
 ↓
EAS Submit
 ↓
App Store Connect processing
 ↓
TestFlight
 ↓
Metadata
 ↓
App Review submission
 ↓
Apple review
 ↓
Production release
```

Do not treat the first successful EAS Submit as the end of the process.

---

# 39. Submission failure debugging

If submission fails:

```text
1. Open EAS submission details.
2. Read the submission logs.
3. Identify the first meaningful error.
4. Determine whether it is:
 - credentials
 - artifact
 - application identity
 - version
 - store configuration
 - permissions
 - store policy
 - network/service
5. Fix the smallest confirmed issue.
6. Retry with the same intended artifact when possible.
```

Expo's current EAS Submit documentation points to the submission details page and its logs for diagnosing failures.

Do not rebuild the application when the failure is only a submission credential problem.

---

# 40. Common failure: wrong application

Symptoms:

```text
App not found
Application does not match
Bundle/package mismatch
```

Check:

```text
iOS bundleIdentifier
Android package
App Store Connect app
Google Play application
EAS submission profile
```

Do not change the application identifier to "make it work."

If the identifier is already registered with a store, changing it creates a different application.

---

# 41. Common failure: Android service account

Check:

```text
Service account exists
JSON key is correct
Service account has Play Console access
Correct application is configured
Google API access is enabled where required
```

Do not regenerate keys repeatedly without understanding the permission failure.

If the account is missing access, fix access.

---

# 42. Common failure: iOS authentication

Check:

```text
Apple account
App Store Connect access
App ID
Team
ascAppId
API key configuration
```

If the submission fails after a successful build, do not immediately rebuild.

The artifact may be valid.

The problem may be only:

```text
App Store Connect authentication
```

---

# 43. Common failure: version already exists

If the store rejects the binary because the version/build number is already used:

```text
Check current store state
 ↓
Check local/EAS version state
 ↓
Identify version owner
 ↓
Increment correctly
 ↓
Build again
 ↓
Submit new artifact
```

Do not randomly increment versions.

See:

```text
foundations/versioning.md
frameworks/expo/eas.md
```

---

# 44. Common failure: wrong track

If Android went to:

```text
internal
```

when you expected:

```text
production
```

check:

```text
submit profile
track configuration
command flags
Play Console state
```

Do not assume the build is lost.

Often the binary is already in Play Console and only the release promotion/state needs to be corrected.

---

# 45. Common failure: iOS uploaded but not visible

App Store Connect may need time to process the build.

Check:

```text
Submission status
Processing status
Version/build number
Processing errors
App Store Connect
TestFlight
```

Do not immediately upload the same artifact repeatedly.

First verify whether the existing upload is still processing.

---

# 46. Common failure: store rejects metadata

This is not necessarily an EAS Submit problem.

Separate:

```text
Binary upload
```

from:

```text
Store listing/review
```

If the store rejects:

```text
privacy information
screenshots
description
permissions
content rating
data safety
review information
```

use the relevant store-operation or privacy documentation.

Do not rebuild the binary unless the rejection actually requires a binary change.

---

# 47. Common failure: store rejects the binary

Possible causes include:

```text
Invalid signing
Invalid entitlements
Unsupported SDK
Missing capability
Version conflict
Invalid architecture
Platform requirement
Privacy/runtime behavior
```

Classify the failure before rebuilding.

If the store identifies a binary-level issue:

```text
Fix
→ build
→ test
→ verify
→ submit new artifact
```

If it identifies a metadata issue:

```text
Fix metadata
→ resubmit/review
```

---

# 48. Common failure: EAS Submit command works locally but not CI

Compare:

```text
EAS CLI version
EXPO_TOKEN
EAS project
submission profile
environment
store credentials
permissions
```

CI must be able to run non-interactively.

For example:

```bash
eas submit \
 --platform android \
 --profile production \
 --non-interactive
```

Do not rely on:

```text
developer login session
interactive prompts
local credential files
```

inside CI.

---

# 49. Common failure: AI recommends the wrong submission command

AI-generated commands can become stale.

Before running a command:

```text
Check current EAS CLI
Check current Expo documentation
Check project version
Check platform
Check profile
```

The repository's documentation rules explicitly require commands to be verified and volatile tooling behavior to be checked against current reliable sources.

Do not trust an AI-generated release command simply because it looks plausible.

---

# 50. AI-assisted submission workflow

AI can help prepare a submission.

Good tasks:

```text
Inspect release configuration
Check version
Check application identifiers
Review metadata completeness
Review release notes
Prepare submission command
Audit EAS profile
Analyze failed submission logs
```

Safe workflow:

```text
AI
 ↓
Audit
 ↓
Prepare
 ↓
Developer verifies
 ↓
Build
 ↓
Test
 ↓
Human approves
 ↓
Submit
```

The repository's AI guidance requires explicit human approval around production releases, store submission, credentials, secrets, and destructive actions.

---

# 51. AI submission audit prompt

```text
Audit this Expo release for store submission readiness.

Inspect:

- app.json / app.config.*
- eas.json
- package.json
- lockfile
- version configuration
- Android package name
- iOS bundle identifier
- EAS build profile
- EAS submit profile
- environment configuration
- native dependencies
- signing configuration references
- store submission configuration

Check:

1. Correct application identity
2. Correct version
3. Correct build number/version code
4. Correct environment
5. Correct production profile
6. Correct store target
7. Signing readiness
8. Submission credential readiness
9. Metadata readiness
10. Native compatibility
11. Release-test readiness
12. Human approval requirement

Return:

- READY
- BLOCKED
- NEEDS VERIFICATION

For every finding include:

- evidence
- file/path
- risk
- minimal fix
- verification step

Rules:

- Do not request secrets.
- Do not print secrets.
- Do not submit anything.
- Do not modify files.
- Do not assume current Apple or Google requirements.
- Identify requirements that must be verified against official documentation.
```

---

# 52. AI submission-failure prompt

```text
Analyze this EAS Submit failure.

Platform:
<android / ios>

Submission profile:
<profile>

Build ID:
<build id>

Application:
<app identifier>

Error:
<paste relevant error>

Return:

1. Failure category
2. First meaningful error
3. Evidence
4. Most likely cause
5. Alternative causes
6. Whether the artifact is likely still valid
7. Minimal fix
8. Whether a rebuild is actually required
9. Verification command
10. Next action

Rules:

- Do not request credentials.
- Do not invent platform requirements.
- Do not recommend rebuilding unless evidence requires it.
- Separate confirmed facts from hypotheses.
```

---

# 53. Production submission approval

Before production submission:

```text
Release candidate
 ↓
Automated checks
 ↓
Artifact verification
 ↓
Store readiness
 ↓
Human review
 ↓
APPROVE
 ↓
EAS Submit
```

The approval should identify:

```text
Platform
Version
Build number
EAS build ID
Environment
Target store
Target track
```

Example:

```text
Android
Version: 1.4.0
Version code: 52
EAS Build: <id>
Environment: production
Target: Google Play production
Approved: yes
```

This avoids vague approvals such as:

```text
"Looks good, ship it."
```

---

# 54. Submission record

Record at least:

```text
Release version
Platform
Build number
Git commit
EAS build ID
EAS submission ID
Store
Track / TestFlight
Submission time
Approver
Result
```

Do not record:

```text
passwords
private keys
service account JSON
API secrets
tokens
```

This creates a useful release history without creating another secret store.

---

# 55. Recommended release flow

For a mature Expo project:

```text
 Git commit
 ↓
 Release candidate
 ↓
 Validation
 ↓
 Production build
 ↓
 Artifact inspection
 ↓
 Real-device test
 ↓
 Store-readiness check
 ↓
 Human approval
 ↓
 ┌──────────┴──────────┐
 ↓ ↓
 Android iOS
 ↓ ↓
 EAS Submit EAS Submit
 ↓ ↓
 Google Play Console App Store Connect
 ↓ ↓
 Testing / rollout TestFlight
 ↓ ↓
 Production App Review
 └──────────┬──────────┘
 ↓
 Monitoring
```

This keeps the release path understandable and traceable.

---

# 56. First release vs later releases

Do not treat them identically.

## First release

Requires more setup:

```text
Store app creation
Account setup
Credentials
Identifiers
Initial upload
Store listing
Privacy
Testing configuration
Review setup
```

## Later release

Usually:

```text
Build
→ Verify
→ Submit
→ Review
→ Rollout
```

The first release establishes the store application and its configuration.

Later releases should reuse that established identity.

---

# 57. Manual fallback

EAS Submit is not the only way to upload an artifact.

If necessary:

```text
Android
→ Google Play Console manual upload

iOS
→ App Store Connect / Apple tooling
```

Expo currently documents manual Android submission for the first release and manual iOS upload as a fallback when EAS Submit is unavailable.

Manual submission is useful when:

```text
EAS unavailable
+
store-specific manual control is required
+
you are not using EAS
```

Do not create a second permanent release process unless there is a real need.

---

# 58. Submission security checklist

Before submission:

```text
[ ] Correct store account
[ ] Correct application
[ ] Correct environment
[ ] Correct artifact
[ ] Correct version
[ ] Correct build number
[ ] Correct credentials
[ ] No production secrets in source
[ ] CI secrets protected
[ ] Submission permissions are least-privilege
[ ] Human approval completed
```

After submission:

```text
[ ] Store received expected artifact
[ ] Version/build number verified
[ ] Track/TestFlight verified
[ ] Processing status checked
[ ] Review status checked
[ ] Production rollout verified
```

---

# 59. Submission checklist

## Android

```text
[ ] Google Play app exists
[ ] Package name matches
[ ] Google service account configured
[ ] Production AAB exists
[ ] Version code is correct
[ ] Version name is correct
[ ] Store listing is ready
[ ] Privacy/Data Safety information is ready
[ ] Required testing is complete
[ ] Correct track selected
[ ] Exact build approved
[ ] Submission completed
[ ] Play Console state verified
```

## iOS

```text
[ ] Apple Developer account active
[ ] App Store Connect app exists
[ ] Bundle identifier matches
[ ] Production IPA exists
[ ] Version is correct
[ ] Build number is correct
[ ] Submission credentials configured
[ ] Store metadata is ready
[ ] Privacy information is ready
[ ] TestFlight verification completed
[ ] Exact build approved
[ ] Submitted to App Review
[ ] Review status verified
```

---

# 60. Definition of done

A store submission is complete only when:

```text
Correct artifact
 +
Correct application
 +
Correct version
 +
Correct credentials
 +
Correct store target
 +
Artifact verified
 +
Human approval
 +
Binary uploaded
 +
Store processing verified
 +
Next store action completed
 ↓
SUBMISSION COMPLETE
```

For Android, that may mean the build is correctly placed into the intended testing or production track.

For iOS, successful upload means the build is in App Store Connect/TestFlight; App Store production still requires the App Review workflow.

---

# Related documentation

### Foundations

- `foundations/mobile-release-lifecycle.md`
- `foundations/project-configuration.md`
- `foundations/release-environments.md`
- `foundations/identifiers.md`
- `foundations/versioning.md`
- `foundations/dependency-management.md`

### Expo

- `frameworks/expo/README.md`
- `frameworks/expo/app-config.md`
- `frameworks/expo/development-builds.md`
- `frameworks/expo/eas.md`
- `frameworks/expo/credentials.md`
- `frameworks/expo/build.md`
- `frameworks/expo/ci-cd.md`
- `frameworks/expo/common-failures.md`

### Store publishing

- `publishing/android/`
- `publishing/ios/`
- `publishing/cross-platform/`

### Store operations

- `store-operations/app-listing.md`
- `store-operations/metadata.md`
- `store-operations/screenshots.md`
- `store-operations/privacy-information.md`
- `store-operations/data-safety.md`
- `store-operations/content-ratings.md`
- `store-operations/app-review.md`
- `store-operations/rejection-handling.md`
- `store-operations/resubmission.md`

### Signing

- `signing/android/`
- `signing/ios/`
- `signing/security/`

### Testing

- `testing/release-smoke-tests.md`
- `testing/device-testing.md`
- `testing/device-matrix.md`
- `testing/ios.md`
- `testing/android.md`

### Release engineering

- `release-engineering/eas/`
- `release-engineering/github-actions/`
- `release-engineering/app-store-connect-api.md`
- `release-engineering/google-play-api.md`
- `release-engineering/release-pipelines.md`

### Release strategy

- `release-strategy/beta-testing.md`
- `release-strategy/staged-rollouts.md`
- `release-strategy/phased-release.md`
- `release-strategy/hotfixes.md`
- `release-strategy/emergency-release.md`

### Post-release

- `post-release/monitoring.md`
- `post-release/rollout-monitoring.md`
- `post-release/incident-response.md`
- `post-release/rollback.md`
- `post-release/hotfix.md`

### AI

- `ai/workflows/release-audit.md`
- `ai/workflows/release-preparation.md`
- `ai/workflows/debugging.md`
- `ai/orchestration/human-approval.md`
- `ai/security/secret-protection.md`
- `ai/security/agent-permissions.md`
- `ai/security/destructive-actions.md`

---

# Official sources

Use official documentation as the authority for current EAS submission behavior.

### Expo

- EAS Submit: https://docs.expo.dev/submit/introduction/
- Submit to Google Play: https://docs.expo.dev/submit/android/
- Submit to Apple App Store: https://docs.expo.dev/submit/ios/
- Submit to app stores overview: https://docs.expo.dev/deploy/submit-to-app-stores/
- Configure EAS Submit: https://docs.expo.dev/submit/eas-json/
- EAS CLI reference: https://docs.expo.dev/eas/cli/
- EAS Workflows: https://docs.expo.dev/eas/workflows/introduction/
- EAS Build: https://docs.expo.dev/build/introduction/
- EAS credentials: https://docs.expo.dev/app-signing/managed-credentials/

### Apple

- App Store Connect Help: https://developer.apple.com/help/app-store-connect/
- Submit an app for review: https://developer.apple.com/help/app-store-connect/manage-submissions-to-app-review/submit-an-app
- TestFlight: https://developer.apple.com/testflight/

### Google

- Google Play Console Help: https://support.google.com/googleplay/android-developer/
- Prepare and roll out a release: https://support.google.com/googleplay/android-developer/answer/9859348
- Google Play App Signing: https://support.google.com/googleplay/android-developer/answer/9842756

### React Native

- React Native documentation: https://reactnative.dev/docs/getting-started

**Last verified:** August 11, 2026

Store requirements, EAS CLI behavior, Apple/App Store Connect workflows, Google Play workflows, testing requirements, and review rules can change. Re-check the official documentation immediately before a production submission.
