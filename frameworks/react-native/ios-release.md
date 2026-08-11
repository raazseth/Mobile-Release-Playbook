# React Native iOS Release

This guide covers the production release of a React Native application to the Apple App Store.

It focuses on the native iOS release path:

```text
React Native
    ↓
iOS project
    ↓
Xcode
    ↓
Archive
    ↓
IPA
    ↓
App Store Connect
    ↓
TestFlight
    ↓
App Review
    ↓
App Store
```

It is intended for React Native applications with an `ios/` native project.

This guide does not require Fastlane or EAS. Those tools can automate parts of the workflow, but the underlying Apple release process should remain understandable without them.

---

# 1. Release model

A production iOS release has several distinct stages:

```text
Source
  ↓
Configuration
  ↓
Dependencies
  ↓
Signing
  ↓
Build
  ↓
Archive
  ↓
Validation
  ↓
TestFlight
  ↓
Testing
  ↓
App Review
  ↓
Release
  ↓
Monitoring
```

Do not treat:

```text
Archive succeeded
```

as:

```text
Release succeeded
```

Each stage validates a different part of the release.

---

# 2. Prerequisites

Before preparing a production release:

```text
[ ] macOS machine available
[ ] Supported Xcode version installed
[ ] Apple Developer account active
[ ] App Store Connect access available
[ ] Correct Apple Developer Team selected
[ ] Bundle identifier registered
[ ] iOS project builds locally
[ ] CocoaPods/dependencies resolve
[ ] Production environment is configured
[ ] Signing strategy is defined
[ ] Version and build number are defined
[ ] App Store listing exists
```

Do not start signing troubleshooting before confirming the application identity.

---

# 3. Application identity

The iOS application is identified primarily by its bundle identifier.

Example:

```text
com.example.myapp
```

Verify it in:

```text
Xcode
→ Target
→ General
→ Identity
→ Bundle Identifier
```

Also verify:

```text
App Store Connect
Apple Developer account
Provisioning profile
Entitlements
CI configuration
Fastlane configuration
EAS configuration
```

All systems must refer to the same application.

See:

```text
foundations/identifiers.md
```

---

# 4. Apple Developer account

The Apple Developer account provides the signing and distribution infrastructure required to build and distribute an iOS application.

The team should have:

```text
Apple Developer access
App Store Connect access
Correct organization/team
Appropriate roles
```

Do not use a personal Apple account for a company application when the application is owned by an organization.

Account ownership should be documented.

---

# 5. App Store Connect app

Before uploading a production build, create the application in App Store Connect.

Confirm:

```text
App name
Bundle ID
SKU
Primary language
Category
Pricing
Availability
```

The Bundle ID in App Store Connect must match the application being built.

Do not create a new App Store Connect application simply because an upload fails.

First verify the identity and signing configuration.

---

# 6. Apple Developer identifiers

The Apple Developer portal contains identifiers used by the application.

The main identifier is:

```text
App ID / Bundle ID
```

Additional capabilities can affect the identifier and entitlements.

Examples:

```text
Push Notifications
Associated Domains
Sign in with Apple
Apple Pay
iCloud
App Groups
Background Modes
```

Only enable capabilities that the application actually uses.

Every additional capability increases configuration and release surface area.

---

# 7. Xcode project

A React Native project normally contains:

```text
ios/
├── MyApp.xcodeproj
├── MyApp.xcworkspace
├── Podfile
└── ...
```

When CocoaPods is used, build from:

```text
.xcworkspace
```

rather than the `.xcodeproj`.

Example:

```text
ios/MyApp.xcworkspace
```

Confirm the actual project structure before creating release scripts.

---

# 8. CocoaPods

Install JavaScript dependencies first:

```bash
npm ci
```

or the package manager used by the project.

Then install iOS dependencies:

```bash
cd ios
pod install
cd ..
```

For projects that use Bundler:

```bash
bundle exec pod install
```

Do not run:

```bash
pod update
```

as a routine release step.

`pod update` can change dependency versions and create an unrelated release change.

Prefer the existing lockfile and deterministic dependency installation.

---

# 9. Podfile

Review:

```text
ios/Podfile
```

before release.

Look for:

```text
React Native configuration
Native modules
Deployment target
Static/dynamic framework settings
Permissions
Post-install hooks
```

Do not make unrelated Podfile changes during a production release.

A release should contain only intentional changes.

---

# 10. iOS deployment target

The deployment target determines the minimum supported iOS version.

Verify:

```text
Podfile
Xcode project
Target settings
Dependencies
```

The value must be compatible with:

```text
React Native version
Native dependencies
Apple SDK
Application requirements
```

Do not increase the minimum iOS version simply to make a dependency warning disappear without checking the user impact.

---

# 11. Build configuration

At minimum, distinguish:

```text
Debug
Release
```

Production distribution should use:

```text
Release
```

Do not accidentally archive:

```text
Debug
```

for App Store distribution.

Review:

```text
Build Settings
Build Configuration
Scheme
Signing
Environment
```

---

# 12. Release scheme

The active scheme controls how Xcode builds the application.

Verify:

```text
Scheme
→ Run
→ Build Configuration
```

For production:

```text
Release
```

should be selected for Archive.

If the project has multiple targets or schemes:

```text
MyApp
MyApp-Staging
MyApp-Dev
```

confirm that the correct target is being archived.

---

# 13. Environment configuration

A production build must use the production environment.

Conceptually:

```text
Debug
  ↓
Development API

Release/TestFlight
  ↓
Production-like API

Production
  ↓
Production API
```

The exact environment model depends on the application.

Make the mapping explicit.

Do not rely on:

```text
Developer machine state
```

to decide which backend the production application uses.

---

# 14. Secrets in iOS apps

Anything shipped inside an iOS application can potentially be extracted.

Never treat the mobile application as a secure secret store.

Do not embed:

```text
Database passwords
Private API keys
Service-account credentials
Admin tokens
Signing private keys
```

If privileged access is required:

```text
iOS app
   ↓
Backend
   ↓
Privileged service
```

Public client configuration is different from a secret.

---

# 15. Version and build number

iOS uses two important release identifiers:

```text
Version
Build
```

Example:

```text
Version: 1.4.0
Build:   37
```

The version is user-facing.

The build number distinguishes individual builds.

For each uploaded build, ensure the build number is appropriate for the version being submitted.

Do not reuse a build number that App Store Connect has already received for the same version.

See:

```text
foundations/versioning.md
```

---

# 16. Versioning strategy

A practical model:

```text
1.0.0
1.1.0
1.1.1
1.2.0
```

Use the project's established versioning rules.

The important requirement is consistency.

Track:

```text
Git release
iOS version
Build number
App Store Connect build
```

A release should be traceable back to the source commit.

---

# 17. Build number automation

Build numbers can be incremented manually or through CI/release tooling.

Whatever approach is used, make it deterministic.

For example:

```text
CI run
    ↓
Build number
    ↓
Archive
```

Avoid relying on:

```text
developer memory
```

for production build numbers.

If using Fastlane or EAS, understand exactly how their automatic incrementing interacts with the Xcode project.

---

# 18. Signing model

The iOS distribution chain is:

```text
Apple Developer Team
        ↓
Bundle ID
        ↓
Certificate
        ↓
Provisioning Profile
        ↓
Entitlements
        ↓
Archive
        ↓
IPA
```

The signing configuration must match the application identity and distribution method.

See:

```text
signing/ios/
```

for detailed credential management.

---

# 19. Automatic signing

Xcode can manage signing automatically.

This can simplify development and smaller teams.

Verify:

```text
Signing & Capabilities
→ Automatically manage signing
→ Team
→ Bundle Identifier
```

Automatic signing does not mean:

```text
No signing configuration exists.
```

Xcode still creates and manages the necessary Apple signing assets.

For CI and controlled production systems, understand what credentials Xcode is using.

---

# 20. Manual signing

Manual signing may be appropriate when:

```text
CI requires deterministic credentials
Enterprise policies require explicit control
Multiple teams share infrastructure
Signing assets are centrally managed
```

You must then explicitly manage:

```text
Certificate
Provisioning profile
Bundle ID
Team
Entitlements
```

Do not switch between automatic and manual signing during a release without understanding the effect.

---

# 21. Capabilities and entitlements

Review:

```text
Signing & Capabilities
```

before release.

Common capabilities include:

```text
Push Notifications
Associated Domains
Background Modes
Sign in with Apple
Apple Pay
App Groups
Keychain Sharing
```

Capabilities can affect:

```text
Provisioning profiles
Entitlements
Native configuration
App Store review
Application behavior
```

Remove capabilities that the application no longer needs.

Do not enable capabilities "just in case."

---

# 22. Push notifications

If the app uses push notifications, verify:

```text
Push Notifications capability
APNs configuration
Entitlements
APNs environment
Backend credentials
Device token registration
Notification permission
```

Test on a real device.

Do not consider:

```text
Simulator notification test
```

sufficient for a production push workflow.

See:

```text
testing/push-notifications.md
integrations/push-notifications/
```

---

# 23. Associated Domains and deep links

If the app uses universal links, verify:

```text
Associated Domains capability
apple-app-site-association
Bundle ID
Team ID
Domain configuration
```

Test:

```text
Fresh install
Existing install
App not running
App already running
```

See:

```text
testing/deep-links.md
integrations/universal-links/
```

---

# 24. Permissions

Review all permissions used by the application.

Common examples:

```text
Camera
Microphone
Location
Photos
Contacts
Bluetooth
Notifications
Tracking
```

For each permission verify:

```text
Why is it needed?
Where is it requested?
Is the purpose visible to the user?
Is the usage description present?
Is the permission actually required?
```

Do not request permissions at launch unless the product flow genuinely requires them.

---

# 25. Info.plist

Review:

```text
Info.plist
```

for:

```text
Permission usage descriptions
URL schemes
Associated domains
Background modes
App configuration
Required device capabilities
Transport security settings
```

Do not add broad exceptions simply to make a network request work.

For example, avoid weakening transport security without understanding the actual endpoint problem.

---

# 26. Privacy configuration

Apple requires developers to provide accurate information about data collection and usage.

Review:

```text
App Privacy
Privacy Policy
Tracking
Analytics
Third-party SDKs
Data collection
Data sharing
```

The store declaration should match the actual application behavior.

Do not copy privacy answers from another application.

See:

```text
privacy-compliance/
```

---

# 27. App Tracking Transparency

If the application uses tracking as defined by Apple's policies, evaluate:

```text
NSUserTrackingUsageDescription
ATT permission flow
Tracking SDK behavior
Consent state
```

Do not add the ATT prompt merely because an analytics SDK exists.

Determine whether the actual data use falls within Apple's tracking definition.

Store declarations and runtime behavior must agree.

---

# 28. Third-party SDK review

Before release, list native SDKs:

```text
Analytics
Crash reporting
Attribution
Payments
Maps
Push
Authentication
Advertising
```

For each SDK verify:

```text
Version
Purpose
Permissions
Privacy behavior
Native compatibility
Store requirements
```

A dependency update can change privacy or permission behavior even when application code did not change.

---

# 29. React Native native modules

Review changes to:

```text
package.json
Podfile.lock
ios/
```

especially when adding native dependencies.

A JavaScript package can introduce native code.

Therefore:

```text
npm install
```

does not necessarily mean:

```text
JS-only change
```

After native dependency changes:

```text
pod install
→ build
→ device test
→ release test
```

---

# 30. Hermes and JavaScript engine

React Native projects may use Hermes or another JavaScript engine depending on their configuration.

Before release, confirm:

```text
Engine
React Native version
Native build configuration
Debug behavior
Release behavior
```

Do not assume that:

```text
Debug works
```

means:

```text
Release JavaScript runtime works
```

Test the actual Release build.

---

# 31. New Architecture

If the project uses React Native's New Architecture, verify:

```text
React Native version
Native modules
TurboModules
Fabric
Third-party compatibility
iOS build configuration
```

Do not enable or disable the New Architecture during a production release unless the change is intentional and tested.

Native architecture changes can affect:

```text
Build
Performance
Memory
Native modules
Release stability
```

---

# 32. Build locally

Before creating an archive:

```bash
cd ios
pod install
cd ..
```

Then open:

```text
ios/MyApp.xcworkspace
```

Build the Release configuration on a real device.

Validate:

```text
Launch
Authentication
Navigation
API
Storage
Push
Deep links
Payments
Camera
Location
Critical product flows
```

---

# 33. Build from command line

A reproducible CI-style build can use `xcodebuild`.

First inspect available schemes:

```bash
xcodebuild -workspace ios/MyApp.xcworkspace -list
```

Then build:

```bash
xcodebuild \
  -workspace ios/MyApp.xcworkspace \
  -scheme MyApp \
  -configuration Release \
  -sdk iphoneos \
  build
```

The exact command depends on the project.

Do not copy this command without replacing:

```text
workspace
scheme
configuration
```

---

# 34. Archive

For App Store distribution, create an archive.

Example:

```bash
xcodebuild \
  -workspace ios/MyApp.xcworkspace \
  -scheme MyApp \
  -configuration Release \
  -sdk iphoneos \
  -archivePath build/MyApp.xcarchive \
  archive
```

A successful archive produces:

```text
MyApp.xcarchive
```

Do not delete the archive immediately.

It is useful for:

```text
Inspection
Export
Debugging
Release evidence
```

---

# 35. Archive verification

Before exporting:

```text
[ ] Correct application
[ ] Correct bundle ID
[ ] Correct version
[ ] Correct build number
[ ] Release configuration
[ ] Correct signing
[ ] Correct entitlements
[ ] Correct architecture
[ ] Production environment
[ ] Expected dependencies
```

Inspect the archive before uploading.

---

# 36. Export IPA

The archive can be exported to an IPA through Xcode or `xcodebuild`.

A typical command is:

```bash
xcodebuild \
  -exportArchive \
  -archivePath build/MyApp.xcarchive \
  -exportPath build/export \
  -exportOptionsPlist ExportOptions.plist
```

The export options must match the intended distribution method.

Do not blindly reuse an old `ExportOptions.plist`.

Review:

```text
method
teamID
signing configuration
provisioning profiles
```

---

# 37. App Store distribution method

The exact export method depends on the intended workflow.

Examples include:

```text
App Store
Ad Hoc
Development
Enterprise
```

For App Store submission, use the distribution method appropriate to App Store Connect.

Do not use an Ad Hoc build when you intend to submit to the App Store.

---

# 38. Validate the archive

Use Xcode Organizer or the appropriate command-line validation flow.

The purpose is to catch:

```text
Signing issues
Missing entitlements
Invalid bundle
Incorrect identifiers
Architecture problems
Store submission problems
```

Do not wait for App Store Connect to be the first validation layer.

---

# 39. Upload to App Store Connect

The application can be uploaded using:

```text
Xcode Organizer
Transporter
Fastlane
App Store Connect API
CI/CD tooling
```

Choose one primary upload path.

Do not maintain several unrelated production upload systems without a reason.

---

# 40. Xcode Organizer upload

A common manual workflow:

```text
Xcode
→ Window
→ Organizer
→ Archives
→ Select archive
→ Distribute App
→ App Store Connect
→ Upload
```

Review the distribution summary before uploading.

After upload:

```text
App Store Connect
→ Build processing
```

must complete before the build becomes fully available for testing/submission.

---

# 41. TestFlight

TestFlight is the main Apple beta distribution workflow.

Use it to validate the production-like binary before App Store submission.

Test:

```text
Fresh install
Upgrade
Login
Critical workflows
Push
Deep links
Payments
Offline behavior
Permissions
Crash reporting
```

Use real devices.

---

# 42. Internal and external testing

Use appropriate TestFlight testers.

A practical model:

```text
Internal team
   ↓
QA / product testing
   ↓
External testers if needed
   ↓
Production submission
```

Keep the testing scope aligned with release risk.

A trivial patch does not necessarily need the same testing process as:

```text
Major native dependency migration
```

---

# 43. TestFlight build selection

Confirm:

```text
Version
Build number
Commit
Environment
Tester group
```

Do not distribute a staging build to production testers by accident.

The TestFlight build should represent the artifact you intend to release.

---

# 44. Upgrade testing

Always test:

```text
Previous production version
        ↓
New release
```

not only:

```text
Fresh install
```

Check:

```text
Authentication
Local storage
Database/schema migrations
Cached state
Push token
Deep links
User settings
Offline data
```

Many mobile release failures occur only during upgrade.

See:

```text
testing/upgrade-migrations.md
```

---

# 45. App Store metadata

Before submission, verify:

```text
App name
Subtitle
Description
Keywords
Screenshots
App previews
Category
Age rating
Privacy details
Support URL
Marketing URL
What's New
```

The exact fields available can change.

Use App Store Connect's current interface and requirements as the source of truth.

See:

```text
store-operations/
publishing/ios/
```

---

# 46. Screenshots

Screenshots must represent the actual application.

Verify:

```text
Correct app state
Correct version
Correct localization
Correct device class
No debug UI
No staging URLs
No test accounts unless allowed
```

Do not create marketing screenshots that materially misrepresent the product.

---

# 47. App review information

Provide App Review with enough information to evaluate the application.

Depending on the application, this may include:

```text
Demo account
Login instructions
Special configuration
Feature explanation
Hardware requirements
Review notes
```

If a feature requires authentication, make sure the reviewer can actually access it.

Do not leave review-critical information only inside internal team documentation.

---

# 48. Review readiness

Before submission:

```text
[ ] App launches successfully
[ ] No obvious crashes
[ ] Login works
[ ] Demo/review account works
[ ] Core flows work
[ ] Permissions are justified
[ ] Privacy information is accurate
[ ] Metadata is accurate
[ ] Screenshots are accurate
[ ] Subscription/IAP behavior is correct
[ ] Links work
[ ] Support information is correct
[ ] Production backend is healthy
```

---

# 49. Payments and In-App Purchases

If the application uses Apple In-App Purchase:

```text
App Store Connect products
StoreKit configuration
Product IDs
Entitlements
Receipt/transaction handling
Restore purchases
Subscription lifecycle
```

must agree.

Test:

```text
Purchase
Restore
Cancellation
Expiration
Upgrade/downgrade
Failed payment
```

See:

```text
monetization/
testing/payments.md
```

---

# 50. Sign in with Apple

If the application uses Sign in with Apple, verify:

```text
Capability
Bundle ID
Service ID if applicable
Redirect URI
Nonce handling
Backend token validation
Account linking
```

Test:

```text
New user
Existing user
Reauthentication
Account recovery
```

Do not assume the login flow works simply because the Apple button appears.

---

# 51. Crash reporting

Before release, confirm crash reporting works in the Release build.

Test:

```text
Release build
    ↓
Controlled test crash
    ↓
Crash reporting service
    ↓
Correct app version
    ↓
Correct build number
```

Verify symbolication if required.

A crash reporting integration that only works in Debug is not production-ready.

---

# 52. Analytics

Verify:

```text
Production environment
Event naming
User consent
Privacy configuration
No sensitive data
```

Do not send:

```text
Passwords
Authentication tokens
Private user content
Sensitive identifiers
```

through analytics events.

---

# 53. Performance

Before release, check:

```text
Cold start
Warm start
Memory
Scrolling
Navigation
Network requests
Image loading
Large lists
Background behavior
Battery-sensitive workflows
```

Use the Release build.

Debug performance is not a reliable proxy for production performance.

---

# 54. Network configuration

Verify:

```text
Production API
TLS
Certificates
Timeouts
Retries
Offline behavior
Error handling
```

Do not weaken network security to make a failing endpoint work.

If an endpoint fails:

```text
Identify actual network error
→ fix server/certificate/configuration
```

rather than broadly disabling security controls.

---

# 55. Background execution

If the app uses:

```text
Background fetch
Background processing
Location
Push notifications
Audio
Bluetooth
```

verify:

```text
Capabilities
Info.plist
Entitlements
Background modes
Runtime behavior
```

Test on physical devices.

iOS background behavior is constrained by the operating system and should not be treated like a permanently running background process.

---

# 56. Deep links

Test:

```text
Cold launch
Warm launch
Existing navigation stack
Logged-in state
Logged-out state
Invalid link
Expired link
```

Verify that the application handles links safely.

Do not trust arbitrary deep-link parameters.

Validate:

```text
Authentication
Authorization
Resource ownership
```

on the backend.

---

# 57. Localization

Before release:

```text
[ ] Supported languages verified
[ ] Text does not overflow
[ ] Dates formatted correctly
[ ] Numbers formatted correctly
[ ] Screenshots match localization
[ ] Store metadata matches supported markets
```

Do not assume English layout works for every language.

---

# 58. Accessibility

Test:

```text
VoiceOver
Dynamic Type
Contrast
Touch targets
Labels
Focus order
Reduced motion
```

Accessibility should be tested on the Release build before production.

See:

```text
testing/accessibility.md
```

---

# 59. Device testing

Test representative physical devices.

A practical matrix should consider:

```text
Current iPhone generation
Older supported iPhone
Small screen
Large screen
Current supported iOS
Minimum supported iOS
```

The exact matrix depends on the application's:

```text
User base
Minimum iOS version
Hardware requirements
Native dependencies
Risk
```

See:

```text
testing/device-matrix.md
```

---

# 60. Store compliance

Before submission, review:

```text
Privacy
Tracking
Payments
Subscriptions
User-generated content
Authentication
Permissions
Advertising
Children
Health-related data
Location
```

Use:

```text
privacy-compliance/
store-operations/
```

as the project checklist.

Apple's current policies remain the final authority for App Store acceptance.

---

# 61. Production backend readiness

The mobile binary is only one half of the release.

Verify:

```text
Production API
Database
Authentication
Storage
Third-party integrations
Push infrastructure
Payments
Feature flags
Monitoring
```

A successful App Store upload does not prove the backend is ready.

---

# 62. Feature flags

Use feature flags when a high-risk feature needs controlled rollout.

Example:

```text
Build
 ↓
Feature disabled
 ↓
Release
 ↓
Enable for internal users
 ↓
Monitor
 ↓
Increase rollout
```

This can reduce the need for emergency store releases.

Do not create a feature flag system for every small UI change.

---

# 63. Release strategy

A production iOS release can follow:

```text
TestFlight
    ↓
App Review
    ↓
Release
    ↓
Controlled rollout
    ↓
Monitoring
```

Apple also provides platform-level release controls that can affect how a version becomes available.

Use the current App Store Connect options available to the application rather than assuming every release must follow the same rollout strategy.

See:

```text
release-strategy/
```

---

# 64. Emergency release

If a production issue is discovered:

```text
Incident
 ↓
Assess impact
 ↓
Mitigate if possible
 ↓
Prepare smallest safe fix
 ↓
Build
 ↓
Test
 ↓
Submit
 ↓
Release
 ↓
Monitor
```

Do not skip:

```text
Signing
Testing
Privacy
Store requirements
```

simply because the release is urgent.

---

# 65. Release rollback

An App Store release is not equivalent to a server deployment.

You generally cannot assume:

```text
"rollback"
```

means:

```text
restore previous binary immediately
```

Instead, plan for:

```text
Feature flag disablement
Backend mitigation
Corrective release
Compatible OTA update where applicable
Store release controls
```

The recovery mechanism should be decided before production.

---

# 66. CI/CD

A production iOS CI pipeline should be reproducible.

Conceptually:

```text
Git tag
 ↓
Checkout exact commit
 ↓
Install Node dependencies
 ↓
Install Ruby dependencies
 ↓
Install Pods
 ↓
Run tests
 ↓
Build Release
 ↓
Archive
 ↓
Validate
 ↓
Upload TestFlight
 ↓
Human approval
 ↓
Submit
```

The exact implementation can use:

```text
GitHub Actions
Fastlane
EAS
Xcode Cloud
Another CI system
```

Do not introduce multiple CI systems unless they solve different problems.

---

# 67. Xcode version pinning

Xcode versions affect:

```text
Swift
Clang
Apple SDK
Build behavior
Signing
Store compatibility
```

Record the Xcode version used for production builds.

For CI:

```text
Pin or explicitly control the Xcode version
```

when reproducibility matters.

Do not rely indefinitely on:

```text
latest Xcode
```

for a production pipeline.

---

# 68. Node and Ruby versions

React Native release builds depend on more than Xcode.

Record:

```text
Node
Package manager
Ruby
Bundler
CocoaPods
React Native
Xcode
```

Use version management appropriate to the repository.

Examples:

```text
.nvmrc
.ruby-version
Gemfile
Gemfile.lock
```

The exact files depend on the project.

---

# 69. Clean build

A clean build is useful for diagnosing:

```text
Stale artifacts
Pod changes
Native dependency changes
Build cache problems
```

Examples:

```bash
xcodebuild clean
```

and:

```text
Product
→ Clean Build Folder
```

Do not make clean builds the default solution for every build failure.

First identify whether the failure is actually caused by stale state.

---

# 70. Release artifact traceability

Every production IPA should map to:

```text
Git SHA
Version
Build number
Xcode version
React Native version
Dependency lockfile
Environment
Signing configuration
CI run
Archive
App Store Connect build
```

Keep this information in release evidence.

Do not store:

```text
private signing keys
passwords
API tokens
```

in release evidence.

---

# 71. Common failure: provisioning profile mismatch

Symptoms:

```text
Provisioning profile doesn't include bundle identifier
Provisioning profile doesn't include capability
Signing certificate mismatch
```

Check:

```text
Bundle ID
Team
Certificate
Profile
Capabilities
Entitlements
```

Do not immediately create a new profile.

First determine which input is inconsistent.

---

# 72. Common failure: code signing error

Check:

```text
Apple Developer account
Team
Certificate
Keychain
Provisioning profile
Bundle identifier
Signing settings
```

For CI, also check:

```text
Credential installation
Keychain access
Certificate password
Profile location
```

The first useful error in the Xcode log is more valuable than the final generic failure message.

---

# 73. Common failure: CocoaPods fails

Check:

```text
Ruby
Bundler
CocoaPods
Podfile
Podfile.lock
React Native version
Native dependency
```

Try to reproduce:

```bash
cd ios
bundle exec pod install
```

Do not delete:

```text
Podfile.lock
```

as the first troubleshooting step.

---

# 74. Common failure: archive works locally but fails in CI

Compare:

```text
Xcode
macOS
Ruby
Bundler
CocoaPods
Node
Package manager
Environment variables
Signing credentials
Keychain
Provisioning profiles
Git commit
```

The difference is usually in the environment or credentials.

Do not change application code until the environment difference is understood.

---

# 75. Common failure: app crashes only in Release

Check:

```text
Release compiler settings
Hermes
JavaScript minification
Native module configuration
Environment variables
Dead code stripping
Symbolication
```

Run the exact Release build locally.

Do not use Debug as the only validation environment.

---

# 76. Common failure: app points to staging

Check:

```text
Release configuration
Info.plist
xcconfig
Build settings
Environment variables
API configuration
Feature flags
```

Trace:

```text
Xcode configuration
→ bundled configuration
→ runtime environment
→ API endpoint
```

Do not fix this with a hard-coded production URL inside application code unless that is the intended architecture.

---

# 77. Common failure: push works in Debug but not production

Check:

```text
Entitlements
APNs environment
Provisioning
Push capability
Backend credentials
Device token
Notification payload
```

Debug and distribution builds can have different signing/configuration behavior.

Always test the actual distribution build.

---

# 78. Common failure: deep links work locally but not production

Check:

```text
Bundle ID
Associated domains
apple-app-site-association
Team ID
Domain
Entitlements
Universal link configuration
```

Test from:

```text
Safari
Messages
Email
External application
```

not only by triggering the link from inside the app.

---

# 79. Common failure: App Store upload rejected

Classify the failure first:

```text
Technical
Metadata
Privacy
Payment
Content
Account
Signing
Binary
```

Then use the specific evidence from App Store Connect.

Do not make random changes after a rejection.

See:

```text
troubleshooting/review-rejected.md
troubleshooting/metadata-rejected.md
troubleshooting/privacy-rejected.md
```

---

# 80. Common failure: app crashes after release

Immediately capture:

```text
App version
Build number
iOS version
Device
Crash signature
Crash frequency
Affected users
Recent changes
```

Then determine:

```text
Client bug
Backend bug
Configuration bug
Native dependency bug
Store distribution issue
```

If a feature flag can mitigate the issue:

```text
Disable
→ monitor
→ prepare fix
```

Do not wait for a store release if the issue can safely be mitigated server-side.

---

# 81. Production release checklist

```text
[ ] Correct Git commit
[ ] Correct bundle identifier
[ ] Correct Apple Team
[ ] Correct App Store Connect app
[ ] Correct version
[ ] Correct build number
[ ] Correct Release configuration
[ ] Correct production environment
[ ] Dependencies locked
[ ] Pods installed
[ ] Native modules verified
[ ] Signing verified
[ ] Entitlements verified
[ ] Permissions verified
[ ] Privacy information verified
[ ] Analytics verified
[ ] Crash reporting verified
[ ] Push verified
[ ] Deep links verified
[ ] Payments verified if applicable
[ ] Fresh install tested
[ ] Upgrade tested
[ ] Real-device testing completed
[ ] Accessibility checked
[ ] Performance checked
[ ] Archive created
[ ] Archive validated
[ ] IPA verified
[ ] TestFlight upload completed
[ ] TestFlight testing completed
[ ] Store metadata verified
[ ] Review information verified
[ ] Human approval completed
[ ] Production release initiated
[ ] Monitoring ready
```

---

# 82. Definition of done

An iOS release is ready when:

```text
Correct source
      +
Correct application identity
      +
Correct production configuration
      +
Valid signing
      +
Successful Release build
      +
Validated archive
      +
Real-device testing
      +
TestFlight validation
      +
Accurate store metadata
      +
Review readiness
      +
Human approval
      +
Post-release monitoring
      ↓
RELEASE READY
```

The goal is not:

```text
"Xcode Archive succeeded."
```

The goal is:

```text
"The exact source produced the intended signed application,
the application was tested as a production binary,
App Store Connect information is accurate,
and the release can be monitored and recovered safely."
```

---

# Related documentation

### Foundations

- `foundations/mobile-release-lifecycle.md`
- `foundations/project-configuration.md`
- `foundations/release-environments.md`
- `foundations/identifiers.md`
- `foundations/versioning.md`
- `foundations/dependency-management.md`
- `foundations/device-support.md`

### React Native

- `frameworks/react-native/README.md`
- `frameworks/react-native/android-release.md`
- `frameworks/react-native/common-failures.md`
- `frameworks/react-native/eas.md`
- `frameworks/react-native/fastlane.md`

### Signing

- `signing/ios/certificates.md`
- `signing/ios/provisioning-profiles.md`
- `signing/ios/distribution.md`
- `signing/ios/api-keys.md`
- `signing/ios/key-rotation.md`
- `signing/security/secret-storage.md`
- `signing/security/ci-secrets.md`

### Testing

- `testing/device-testing.md`
- `testing/device-matrix.md`
- `testing/ios.md`
- `testing/accessibility.md`
- `testing/localization.md`
- `testing/performance.md`
- `testing/network.md`
- `testing/offline.md`
- `testing/deep-links.md`
- `testing/push-notifications.md`
- `testing/payments.md`
- `testing/upgrade-migrations.md`
- `testing/release-smoke-tests.md`

### Publishing

- `publishing/ios/app-store-connect.md`
- `publishing/ios/build-upload.md`
- `publishing/ios/testflight.md`
- `publishing/ios/metadata.md`
- `publishing/ios/screenshots.md`
- `publishing/ios/app-review.md`
- `publishing/ios/production-release.md`

### Store operations

- `store-operations/app-listing.md`
- `store-operations/metadata.md`
- `store-operations/screenshots.md`
- `store-operations/privacy-information.md`
- `store-operations/content-ratings.md`
- `store-operations/app-review.md`
- `store-operations/rejection-handling.md`

### Privacy

- `privacy-compliance/privacy-policy.md`
- `privacy-compliance/data-collection.md`
- `privacy-compliance/permissions.md`
- `privacy-compliance/tracking.md`
- `privacy-compliance/analytics.md`
- `privacy-compliance/regional-requirements.md`

### Release engineering

- `release-engineering/build-systems.md`
- `release-engineering/release-configurations.md`
- `release-engineering/fastlane/`
- `release-engineering/eas/`
- `release-engineering/github-actions/`
- `release-engineering/environment-management.md`
- `release-engineering/release-pipelines.md`

### Post-release

- `post-release/monitoring.md`
- `post-release/crash-analysis.md`
- `post-release/performance.md`
- `post-release/reviews-and-ratings.md`
- `post-release/rollout-monitoring.md`
- `post-release/incident-response.md`
- `post-release/rollback.md`
- `post-release/hotfix.md`

### AI

- `ai/agents/release-auditor.md`
- `ai/agents/qa-agent.md`
- `ai/agents/security-auditor.md`
- `ai/workflows/release-audit.md`
- `ai/workflows/ai-qa.md`
- `ai/workflows/debugging.md`
- `ai/orchestration/human-approval.md`
- `ai/security/secret-protection.md`
- `ai/security/agent-permissions.md`

---

# Official sources

Use Apple's current documentation as the authority for App Store Connect, signing, review, distribution, and platform requirements.

## Apple

- Apple Developer: https://developer.apple.com/
- App Store Connect: https://appstoreconnect.apple.com/
- App Store Connect Help: https://developer.apple.com/help/app-store-connect/
- Apple Developer Account Help: https://developer.apple.com/help/account/
- App Store Review Guidelines: https://developer.apple.com/app-store/review/guidelines/
- Human Interface Guidelines: https://developer.apple.com/design/human-interface-guidelines/
- Apple Platform Deployment: https://developer.apple.com/documentation/xcode/distributing-your-app-for-beta-testing-and-releases

## React Native

- React Native documentation: https://reactnative.dev/docs/getting-started
- Publishing to App Store: https://reactnative.dev/docs/publishing-to-app-store
- React Native upgrading: https://reactnative.dev/docs/upgrading

## Xcode

- Xcode documentation: https://developer.apple.com/xcode/
- Xcode Help: https://developer.apple.com/documentation/xcode

## Fastlane

- Fastlane documentation: https://docs.fastlane.tools/
- App Store Connect API: https://docs.fastlane.tools/app-store-connect-api/
- Code signing: https://docs.fastlane.tools/codesigning/getting-started/

## Expo / EAS

If the React Native application uses EAS:

- EAS Build: https://docs.expo.dev/build/introduction/
- EAS Submit: https://docs.expo.dev/submit/introduction/
- EAS Update: https://docs.expo.dev/eas-update/introduction/

---

**Last verified:** August 11, 2026

Apple, App Store Connect, Xcode, iOS, React Native, CocoaPods, Fastlane, Expo, and EAS change over time. Verify the current official documentation and the exact platform requirements for the versions used by the project before production deployment.
