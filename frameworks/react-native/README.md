# React Native

This section covers the release engineering workflow for React Native applications.

The goal is simple:

```text
React Native app
    ↓
Release-ready
    ↓
Configured
    ↓
Signed
    ↓
Built
    ↓
Tested
    ↓
Submitted
    ↓
Released
    ↓
Monitored
```

This is **not** a React Native development tutorial.

It focuses on the parts that become important when a React Native application needs to ship reliably to the App Store and Google Play.

---

# What this section covers

```text
React Native
├── Android release
├── iOS release
├── Signing
├── EAS
├── Fastlane
├── Common failures
└── Native release concerns
```

Use this section when you already have a React Native application and need to understand how to turn it into a production release.

---

# React Native release model

React Native sits above the native platform build systems.

```text
                 React Native
                      |
             +--------+--------+
             |                 |
           iOS             Android
             |                 |
           Xcode             Gradle
             |                 |
         IPA/archive          AAB
             |                 |
     App Store Connect     Google Play
```

The JavaScript/TypeScript application is only part of the release.

A production release can also depend on:

```text
Native modules
CocoaPods
Gradle
Xcode
Android SDK
Signing
Provisioning
Entitlements
Application identifiers
Environment configuration
CI/CD
Store metadata
Privacy configuration
```

A successful JavaScript build does not prove that the mobile release is ready.

---

# Framework vs release tooling

React Native is the application framework.

Tools such as:

```text
Expo
EAS
Fastlane
Xcode
Gradle
GitHub Actions
```

solve different problems.

A useful mental model is:

```text
React Native
    ↓
Application code

Expo / EAS
    ↓
Optional React Native framework and release services

Xcode / Gradle
    ↓
Native build systems

Fastlane
    ↓
Release automation

GitHub Actions / CI
    ↓
Automation environment
```

Do not treat these tools as interchangeable.

Choose the smallest release stack that solves the project's actual needs.

---

# React Native and Expo

React Native's current guidance recommends using a React Native framework such as Expo for new applications, while React Native can also be used without a framework when project constraints justify it. urlReact Native framework guidancehttps://reactnative.dev/blog/2024/06/25/use-a-framework-to-build-react-native-apps

For this playbook:

```text
React Native
+
Expo
```

are first-class maintained paths.

That does not mean every React Native project must use Expo.

If an existing application has a mature native setup, do not migrate it simply because a different workflow is newer.

---

# Choose the release path

A React Native project may use several valid release paths.

## Native tooling

```text
React Native
    ↓
Xcode / Gradle
    ↓
Store
```

Good when:

```text
Native control matters
The team already has native expertise
Existing CI is mature
```

## EAS

```text
React Native / Expo
    ↓
EAS
    ↓
Build / Submit / Update
    ↓
Store
```

Good when:

```text
Cloud builds are useful
Credential management is useful
Expo is already part of the project
The team wants a simpler release workflow
```

## Fastlane

```text
React Native
    ↓
Fastlane
    ↓
Xcode / Gradle
    ↓
Store
```

Good when:

```text
Release steps are repetitive
Store automation is needed
Existing native builds should remain the source of truth
CI needs a repeatable release command
```

Do not automatically combine:

```text
EAS
+
Fastlane
+
custom release scripts
```

for the same responsibility.

Define who owns:

```text
Build
Signing
Submission
Metadata
Release approval
```

---

# Recommended repository structure

The React Native framework section is intentionally small.

```text
frameworks/
└── react-native/
    ├── README.md
    ├── android-release.md
    ├── common-failures.md
    ├── eas.md
    ├── fastlane.md
    ├── ios-release.md
    └── signing.md
```

Each file has a specific job.

### `README.md`

Start here.

Contains:

```text
Scope
Release model
Tool choices
Navigation
Core principles
```

### `android-release.md`

Covers:

```text
Android release
Gradle
AAB
Google Play
Android-specific validation
```

### `ios-release.md`

Covers:

```text
iOS release
Xcode
Archive
IPA
App Store Connect
TestFlight
```

### `signing.md`

Covers:

```text
iOS signing
Android signing
Credentials
CI secrets
Rotation
Recovery
Security
```

### `eas.md`

Covers:

```text
Using EAS with React Native
Builds
Credentials
Submit
Updates
CI
```

### `fastlane.md`

Covers:

```text
Fastlane
Lanes
Signing automation
Store automation
CI
```

### `common-failures.md`

Covers:

```text
Build failures
Signing failures
Native dependency failures
CI failures
Release failures
```

---

# Release workflow

Use this as the high-level React Native release path:

```text
1. Prepare
      ↓
2. Verify configuration
      ↓
3. Verify dependencies
      ↓
4. Verify application identity
      ↓
5. Verify signing
      ↓
6. Build Release
      ↓
7. Validate artifact
      ↓
8. Test real build
      ↓
9. Submit to testing
      ↓
10. Review store metadata
      ↓
11. Human approval
      ↓
12. Production release
      ↓
13. Monitor
```

The exact implementation can use:

```text
Xcode
Gradle
EAS
Fastlane
CI/CD
```

but the release responsibilities remain the same.

---

# 1. Prepare

Before building:

```text
[ ] Correct Git branch/commit
[ ] Release scope is known
[ ] Dependencies are locked
[ ] Native changes are understood
[ ] Production environment is known
[ ] Version is known
[ ] Build number/version code is known
```

Avoid unrelated code changes during a release.

A release should be easy to trace back to a specific source revision.

---

# 2. Verify configuration

Check:

```text
package.json
app.json / app.config.*
android/
ios/
Gradle configuration
Xcode configuration
environment configuration
```

Verify:

```text
Application ID
Bundle ID
Version
Build number
Version code
Production API
Native capabilities
Permissions
```

Do not assume the configuration is correct because the development build works.

---

# 3. Verify dependencies

Review:

```text
package.json
lockfile
Podfile
Podfile.lock
Gradle files
native dependencies
```

Pay particular attention to changes involving:

```text
React Native
Expo
React Native modules
CocoaPods
Android Gradle Plugin
Gradle
Xcode
Android SDK
```

Native dependency changes should receive native build validation.

---

# 4. Verify application identity

Android:

```text
applicationId
```

iOS:

```text
Bundle Identifier
```

The identity must match the store application.

Check:

```text
Source
    ↓
Native configuration
    ↓
Signing
    ↓
Store
```

A mismatch here can cause:

```text
Signing failures
Store upload failures
Wrong application release
Credential mismatches
```

---

# 5. Verify signing

Before the production build:

```text
[ ] Correct signing identity
[ ] Correct credentials
[ ] Correct provisioning
[ ] Correct entitlements
[ ] Correct keystore/upload key
[ ] CI has required access
[ ] Secrets are protected
[ ] Recovery path exists
```

See:

```text
frameworks/react-native/signing.md
```

Never put real signing credentials in this repository.

---

# 6. Build Release

The development build is not enough.

The production artifact should be built using the actual Release configuration.

iOS:

```text
Release
→ Archive
→ IPA
```

Android:

```text
Release
→ AAB
```

The React Native iOS publishing documentation explicitly uses the Release scheme for App Store distribution and archives the `.xcworkspace` for upload. urlReact Native iOS publishing guidehttps://reactnative.dev/docs/publishing-to-app-store

---

# 7. Validate the artifact

Before submission, verify:

```text
Application identity
Version
Build number
Environment
Signing
Artifact type
Native capabilities
```

Android:

```text
AAB
```

iOS:

```text
Archive / IPA
```

Do not upload an artifact simply because the build command returned success.

---

# 8. Test the real build

Test the same type of binary that users will receive.

Minimum checks:

```text
[ ] Fresh install
[ ] Upgrade from previous version
[ ] Launch
[ ] Authentication
[ ] Core user flow
[ ] API communication
[ ] Offline behavior
[ ] Push notifications if used
[ ] Deep links if used
[ ] Payments if used
[ ] Permissions
[ ] Crash reporting
```

Test on real devices where platform behavior matters.

---

# 9. Submit to testing

For iOS:

```text
TestFlight
```

For Android:

```text
Internal / closed testing
```

The testing artifact should be the same production candidate, not a separate build with different application behavior.

---

# 10. Review store metadata

Verify:

```text
App name
Description
Screenshots
Privacy information
Age/content rating
Data declarations
Release notes
Review information
```

Keep platform policy documentation in:

```text
store-operations/
privacy-compliance/
publishing/
```

Do not duplicate the full store policy inside this framework guide.

---

# 11. Human approval

The release should have a clear approval boundary.

```text
Automated build
      ↓
Automated checks
      ↓
Testing
      ↓
Human review
      ↓
Production release
```

AI and automation may prepare:

```text
Release audit
Risk summary
Release notes
Metadata draft
Failure analysis
Checklist
```

They should not silently make high-impact production decisions.

---

# 12. Production release

Once approved:

```text
iOS
→ App Store Connect
→ Release

Android
→ Google Play
→ Rollout
```

The exact store controls can change over time.

Use the current official platform documentation as the authority.

---

# 13. Monitor

After release:

```text
Crash rate
ANRs
App startup
Performance
API errors
Authentication failures
Payment failures
User reports
Store reviews
```

The release is not complete until the production application is being monitored.

See:

```text
post-release/
observability/
```

---

# Native projects matter

A React Native project is still a native mobile application.

Expect to work with:

```text
iOS
├── Xcode
├── CocoaPods
├── Info.plist
├── Entitlements
├── Signing
└── App Store Connect

Android
├── Gradle
├── AndroidManifest.xml
├── Signing
├── Build variants
└── Google Play
```

When a release fails, determine whether the failure belongs to:

```text
JavaScript
React Native
Native dependency
iOS
Android
Build tooling
Signing
Environment
Store
```

Do not assume every problem is a React Native problem.

---

# Release environments

Keep environments explicit.

A practical starting point:

```text
development
preview
production
```

For example:

```text
development
    ↓
development API

preview
    ↓
staging/preview API

production
    ↓
production API
```

The exact model depends on the application.

The important rule is:

> A production build must not accidentally depend on a developer's local environment.

---

# Versioning

Track both application version and platform build identifier.

Example:

```text
Version: 1.5.0
iOS Build: 42
Android versionCode: 42
```

The exact numbering strategy can differ.

What matters is that every release can be traced to:

```text
Version
+
Platform build identifier
+
Git commit
```

See:

```text
foundations/versioning.md
```

---

# Native dependency changes

Treat native dependencies as release-sensitive changes.

Examples:

```text
React Native upgrade
Expo SDK upgrade
Native module addition
Native module removal
CocoaPods change
Gradle change
Android SDK change
Xcode change
```

After such changes:

```text
Install dependencies
    ↓
Build iOS
    ↓
Build Android
    ↓
Run release tests
    ↓
Verify native integrations
```

Do not rely only on JavaScript tests.

---

# React Native upgrades

React Native follows a regular release cadence and uses the `0.x.y` versioning model. The official release page is the source of truth for currently supported versions. urlReact Native releaseshttps://reactnative.dev/releases/

Before upgrading:

```text
1. Read release notes
2. Check supported dependencies
3. Check native changes
4. Update
5. Build both platforms
6. Run tests
7. Test critical flows
8. Test production builds
```

Do not upgrade React Native immediately before a critical production release unless there is a clear reason.

---

# Current React Native versions

Do not hard-code a React Native version into this guide.

React Native versions change regularly.

Use:

```text
https://reactnative.dev/releases/
```

and:

```text
https://reactnative.dev/versions
```

to determine:

```text
Latest stable
Active versions
Unsupported versions
Upcoming versions
```

At the time this document was last reviewed, the official React Native documentation listed 0.86 as the latest stable documentation version. This is intentionally not treated as a permanent requirement. urlReact Native documentation versionshttps://reactnative.dev/versions

---

# EAS

EAS can simplify parts of the React Native release process.

Use:

```text
frameworks/react-native/eas.md
```

for:

```text
EAS Build
Credentials
Submit
Updates
CI/CD
```

The important rule is:

```text
EAS is optional.
```

Do not introduce it simply because another project uses it.

---

# Fastlane

Fastlane is useful for repeatable release automation.

Use:

```text
frameworks/react-native/fastlane.md
```

for:

```text
Fastfile
Lanes
iOS automation
Android automation
Signing
Store submission
CI
```

Fastlane should automate a known workflow.

It should not become a hidden release-management system.

---

# Signing

Signing is a separate security concern.

Use:

```text
frameworks/react-native/signing.md
```

for:

```text
iOS certificates
Provisioning profiles
Entitlements
Android keystores
Upload keys
CI secrets
Rotation
Recovery
```

Never store real signing credentials in this repository.

---

# Common failures

Use:

```text
frameworks/react-native/common-failures.md
```

when the release fails.

Start by classifying the failure:

```text
Build
Signing
Native dependency
Environment
CI
Store
Runtime
```

Then identify the first meaningful error.

Do not repeatedly retry a failed release without understanding why it failed.

---

# Debugging release failures

Use this general flow:

```text
Failure
   ↓
Identify platform
   ↓
Identify stage
   ↓
Find first meaningful error
   ↓
Classify cause
   ↓
Reproduce
   ↓
Make smallest safe fix
   ↓
Rebuild
   ↓
Verify
```

Stages:

```text
Dependency install
Build
Archive
Signing
Upload
Store processing
Runtime
```

This prevents unrelated configuration changes from accumulating during debugging.

---

# AI-assisted React Native releases

AI can reduce release work when used as an assistant rather than an authority.

Useful tasks:

```text
Repository audit
Release checklist generation
Configuration review
Dependency change review
Build failure analysis
Native error explanation
Store metadata drafting
Release note drafting
Test-case generation
CI workflow review
```

A good workflow is:

```text
Repository
    ↓
AI inspection
    ↓
Suggested changes
    ↓
Developer review
    ↓
Tests
    ↓
Build
    ↓
Human approval
    ↓
Release
```

Do not give an AI agent unrestricted access to:

```text
Signing keys
Production credentials
App Store accounts
Google Play accounts
Production deployment
Destructive release actions
```

The repository's AI guidance treats model output as untrusted and requires validation and explicit controls around high-impact actions.

---

# AI release audit

A useful audit prompt:

```text
Audit this React Native project for production mobile release readiness.

Inspect:

- package.json
- lockfile
- android/
- ios/
- app.json / app.config.*
- Gradle configuration
- Xcode configuration
- signing configuration
- CI workflows
- Fastlane configuration
- EAS configuration

Check:

1. Application identifiers
2. Versioning
3. Production environment
4. Native dependencies
5. iOS signing
6. Android signing
7. Release build configuration
8. Permissions and capabilities
9. Store readiness
10. CI secret handling
11. Test coverage for critical release paths
12. Upgrade safety
13. Crash reporting
14. Rollback/mitigation options

For each finding return:

- severity
- evidence
- file
- risk
- minimal fix
- verification

Rules:

- Do not request secrets.
- Do not print secrets.
- Do not modify production credentials.
- Do not publish or submit the application.
- Do not invent platform requirements.
- Verify volatile requirements against official documentation.
- Separate confirmed findings from assumptions.
```

---

# Security rules

React Native release automation should follow:

```text
Least privilege
+
Protected secrets
+
Explicit environments
+
No secrets in Git
+
No secrets in app bundles
+
Protected production CI
+
Human approval for high-impact actions
```

Do not trust the client for:

```text
Authorization
Ownership
Administrative access
Sensitive operations
```

A mobile application is an untrusted client.

Backend authorization remains authoritative.

---

# CI/CD

A practical CI pipeline:

```text
Pull request
    ↓
Lint / tests
    ↓
Native validation
    ↓
Merge
    ↓
Release tag
    ↓
Production build
    ↓
Artifact validation
    ↓
Test distribution
    ↓
Human approval
    ↓
Store release
    ↓
Monitoring
```

Not every project needs all stages on every commit.

Keep the pipeline proportional to:

```text
Release risk
Team size
Application complexity
Build cost
```

---

# Build reproducibility

A production artifact should be traceable to:

```text
Git commit
React Native version
Dependency lockfile
Build configuration
Environment
Signing configuration
CI run
```

For every release, be able to answer:

```text
What source produced this artifact?

Which configuration was used?

Which version was released?

Which signing path was used?

Which CI run built it?
```

If these questions cannot be answered, release debugging becomes unnecessarily difficult.

---

# Production checklist

Before a React Native production release:

```text
[ ] Correct Git commit
[ ] Release scope reviewed
[ ] Dependencies locked
[ ] React Native version verified
[ ] Native dependencies verified
[ ] Android application ID verified
[ ] iOS Bundle ID verified
[ ] Version verified
[ ] Build number/version code verified
[ ] Production environment verified
[ ] Signing verified
[ ] Permissions verified
[ ] Native capabilities verified
[ ] Release build succeeds
[ ] Android AAB verified
[ ] iOS archive/IPA verified
[ ] Fresh install tested
[ ] Upgrade tested
[ ] Critical flows tested
[ ] Push tested if applicable
[ ] Deep links tested if applicable
[ ] Payments tested if applicable
[ ] Crash reporting verified
[ ] Store metadata verified
[ ] Test distribution completed
[ ] Human approval completed
[ ] Production release completed
[ ] Monitoring active
```

---

# What belongs elsewhere

Do not duplicate the entire playbook here.

Use the relevant section instead.

| Topic | Location |
|---|---|
| Release lifecycle | `foundations/` |
| Versioning | `foundations/versioning.md` |
| Device support | `foundations/device-support.md` |
| Signing | `frameworks/react-native/signing.md` + `signing/` |
| Android release | `frameworks/react-native/android-release.md` |
| iOS release | `frameworks/react-native/ios-release.md` |
| EAS | `frameworks/react-native/eas.md` |
| Fastlane | `frameworks/react-native/fastlane.md` |
| Testing | `testing/` |
| Store publishing | `publishing/` |
| Store operations | `store-operations/` |
| Privacy | `privacy-compliance/` |
| Release automation | `release-engineering/` |
| Troubleshooting | `troubleshooting/` |
| AI workflows | `ai/` |
| Post-release | `post-release/` |

This keeps each document focused and prevents the framework directory from becoming another copy of the whole repository.

---

# Definition of done

A React Native release process is healthy when:

```text
Application is configured
        +
Native projects are understood
        +
Dependencies are controlled
        +
Signing is secure
        +
Release artifacts are reproducible
        +
Real-device testing is performed
        +
Store submission is controlled
        +
Production is monitored
        ↓
RELEASE READY
```

The goal is not:

```text
"React Native build succeeded."
```

The goal is:

```text
"The team can reliably take a known React Native source revision,
produce the correct iOS and Android artifacts,
verify them, submit them safely,
and understand what to do when something fails."
```

---

# Official sources

Use official documentation as the authority for current React Native and platform behavior.

### React Native

- Documentation: https://reactnative.dev/docs/getting-started
- Releases: https://reactnative.dev/releases/
- Documentation versions: https://reactnative.dev/versions
- Versioning policy: https://reactnative.dev/releases/versioning-policy
- Environment setup: https://reactnative.dev/docs/set-up-your-environment
- Without a framework: https://reactnative.dev/docs/getting-started-without-a-framework
- Integration with existing apps: https://reactnative.dev/docs/integration-with-existing-apps
- Publishing to App Store: https://reactnative.dev/docs/publishing-to-app-store

### Expo

- Expo documentation: https://docs.expo.dev/
- EAS Build: https://docs.expo.dev/build/introduction/
- EAS Submit: https://docs.expo.dev/submit/introduction/
- EAS Update: https://docs.expo.dev/eas-update/introduction/

### Apple

- Apple Developer: https://developer.apple.com/
- App Store Connect: https://appstoreconnect.apple.com/
- App Store Connect Help: https://developer.apple.com/help/app-store-connect/
- App Store Review Guidelines: https://developer.apple.com/app-store/review/guidelines/

### Android

- Android Developers: https://developer.android.com/
- App signing: https://developer.android.com/studio/publish/app-signing
- Android App Bundles: https://developer.android.com/guide/app-bundle
- Google Play Console Help: https://support.google.com/googleplay/android-developer/

### Fastlane

- Fastlane documentation: https://docs.fastlane.tools/
- React Native: https://docs.fastlane.tools/getting-started/cross-platform/react-native/
- Code signing: https://docs.fastlane.tools/codesigning/getting-started/

---

**Last reviewed:** August 11, 2026

React Native, Expo, EAS, Xcode, Gradle, Android SDK, App Store Connect, Google Play, and release tooling change over time. Verify current official documentation before following a volatile command, platform requirement, or store rule.
