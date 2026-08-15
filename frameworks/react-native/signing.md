# React Native Signing

Signing is the mechanism that proves an iOS or Android application was built by an authorized party and allows the platform to accept, install, or distribute the application.

For React Native, signing is still platform-native.

React Native does not replace:

```text
Apple code signing
Android app signing
Provisioning
App Store Connect
Google Play App Signing
```

The release tooling may change:

```text
Xcode
Fastlane
EAS
GitHub Actions
CI/CD
```

but the underlying signing model remains important.

This guide covers the signing layer for a React Native application and explains how to keep it secure, reproducible, and recoverable.

---

# 1. Signing architecture

A React Native application has two independent signing systems.

```text
 React Native
 |
 +----------+----------+
 | |
 iOS Android
 | |
 Apple signing Android signing
 | |
 Certificate Keystore
 | |
 Provisioning profile Upload key
 | |
 Entitlements Google Play App Signing
 | |
 IPA AAB
```

Do not treat iOS and Android signing as one system.

They have different:

```text
Credentials
Lifecycle
Failure modes
Rotation rules
Recovery paths
Store requirements
```

---

# 2. Core signing rule

The most important rule is:

> Signing credentials are production credentials.

Protect them with the same care as:

```text
Production API keys
Database credentials
Cloud credentials
CI secrets
```

Never commit:

```text
Private signing keys
Keystores
`.p12` files
`.p8` private keys
Credential JSON files
Passwords
CI tokens
```

to a public repository.

The repository security rules explicitly prohibit storing secrets in source code, Git history, frontend bundles, logs, or public configuration and require support for secret rotation.

---

# 3. What signing protects

Signing establishes trust around the application artifact.

It helps the platform determine:

```text
Who signed this application?
Which application identity does it belong to?
Is the artifact authorized for this distribution method?
Which capabilities/entitlements are authorized?
```

Signing does **not** prove:

```text
The app is secure
The code has no vulnerabilities
The backend is secure
The app is privacy-compliant
The release is bug-free
```

Signing is one security boundary, not the entire security model.

---

# 4. React Native signing lifecycle

A production release should follow:

```text
Source
 ↓
Application identity
 ↓
Native configuration
 ↓
Signing credentials
 ↓
Build
 ↓
Signed artifact
 ↓
Validation
 ↓
Store submission
```

If signing fails:

```text
Do not bypass it.
```

Determine which input is wrong.

---

# 5. Signing ownership

Before production, document:

```text
Application owner
Apple Developer team
App Store Connect owner
Google Play developer account
Signing credential owner
CI owner
Credential recovery owner
```

Avoid:

```text
One developer owns everything
```

for a production application.

If that person's account, machine, or credentials disappear, the team should still be able to release.

---

# 6. Source of truth

Choose one authoritative source for each signing asset.

For example:

```text
Apple Developer
 ↓
Apple signing state

Google Play
 ↓
Android signing state

CI secret manager
 ↓
CI access credentials

Fastlane match / approved credential store
 ↓
Shared signing assets
```

Do not maintain uncontrolled copies across:

```text
Developer laptops
Google Drive
Slack
Email
Git repositories
Password notes
CI variables
```

Every extra copy increases exposure and recovery complexity.

---

# 7. Access control

Use least privilege.

The security system requires least privilege for:

```text
Humans
Applications
Services
CI/CD
```

and treats signing credentials as sensitive assets.

Separate:

```text
Developer access
CI build access
Store submission access
Account administration
Credential administration
```

Do not give every developer Account Holder/Admin-level access simply because it is convenient.

---

# 8. iOS signing model

The iOS signing chain is:

```text
Apple Developer Team
 ↓
App ID / Bundle ID
 ↓
Certificate
 ↓
Provisioning Profile
 ↓
Entitlements
 ↓
Xcode Archive
 ↓
IPA
 ↓
App Store Connect
```

Apple describes a distribution provisioning profile as authorizing an app to use certain services and identifying the developer distributing or uploading the app. It contains an App ID and distribution certificate.

---

# 9. iOS certificates

An Apple signing certificate establishes the signing identity used by Xcode and related tooling.

Common application signing identities include:

```text
Apple Development
Apple Distribution
```

The exact certificate types available depend on the platform and workflow.

Apple states that Apple Developer Program membership is required to request, download, and use Apple signing certificates.

---

# 10. iOS private keys

A certificate is not the complete secret.

The associated private key is sensitive.

Conceptually:

```text
Certificate
+
Private key
=
Signing identity
```

Protect the private key.

Do not:

```text
Commit it
Email it
Paste it into chat
Put it in a public repository
Print it in CI logs
Store it in application code
```

Apple explicitly treats certificates and account credentials as sensitive assets.

---

# 11. iOS provisioning profiles

A provisioning profile connects:

```text
App ID
+
Signing certificate
+
Distribution/development configuration
+
Authorized capabilities
```

The exact contents depend on the profile type.

Apple's current documentation distinguishes development and distribution provisioning profiles and notes that a profile authorizes app services and establishes the developer/distribution relationship.

---

# 12. iOS development signing

Development signing is used for:

```text
Local device testing
Development builds
Debugging
```

A development provisioning profile can associate:

```text
App ID
Development certificate
Registered devices
```

Apple documents this relationship for manually created development profiles.

For most teams, automatic signing through Xcode is simpler for development.

---

# 13. iOS distribution signing

Distribution signing is used for:

```text
TestFlight
App Store submission
Other supported distribution methods
```

For App Store Connect distribution, the profile is associated with:

```text
Explicit App ID
Distribution certificate
```

Apple documents this as the App Store Connect provisioning profile workflow.

---

# 14. Automatic signing

Xcode can manage signing automatically.

Typical setup:

```text
Xcode
→ Target
→ Signing & Capabilities
→ Automatically manage signing
→ Select Team
```

This is often the simplest choice for local development.

Automatic signing can also manage distribution provisioning profiles during supported Xcode distribution workflows.

Automatic signing does not mean signing is irrelevant.

You still need to understand:

```text
Team
Bundle ID
Capabilities
Certificates
Provisioning
CI access
```

---

# 15. Manual signing

Manual signing gives more explicit control over:

```text
Certificate
Provisioning profile
Bundle ID
Team
Entitlements
```

It may be appropriate for:

```text
Complex CI
Multiple targets
Strict credential control
Centralized release infrastructure
Special distribution requirements
```

Use it when the control is valuable.

Do not switch to manual signing simply because a signing error appears.

---

# 16. Automatic vs manual signing

| Area | Automatic | Manual |
|---|---|---|
| Local development | Usually simpler | More setup |
| Team onboarding | Easier | More explicit |
| CI | Can be harder to reason about | More deterministic |
| Credential control | Less explicit | More explicit |
| Multiple targets | Can become complex | More control |
| Debugging | Xcode manages more | More visibility |
| Maintenance | Lower | Higher |

Recommendation:

```text
Development
→ Prefer automatic signing unless there is a reason not to.

Production CI
→ Use the simplest signing model that provides deterministic, secure builds.
```

---

# 17. Entitlements

Entitlements are capabilities granted to the application.

Examples:

```text
Push Notifications
Associated Domains
Sign in with Apple
Apple Pay
App Groups
Keychain Sharing
iCloud
Background Modes
```

The signing configuration must authorize the capabilities the application actually uses.

Treat:

```text
Entitlements
+
Provisioning profile
+
Xcode configuration
```

as one system.

---

# 18. Entitlement failure

If an entitlement is present in the application but not correctly authorized, the build or runtime behavior may fail.

Debug in this order:

```text
Capability enabled?
 ↓
App ID configured?
 ↓
Entitlement present?
 ↓
Provisioning profile supports it?
 ↓
Correct certificate/team?
 ↓
Correct build configuration?
```

Do not repeatedly regenerate certificates before checking the entitlement mismatch.

---

# 19. iOS bundle identifier

The Bundle ID is a critical signing input.

Example:

```text
com.company.product
```

It must remain consistent across:

```text
Xcode
Apple Developer
App Store Connect
Provisioning profiles
CI
Fastlane
EAS
```

Never use a client-controlled value for authorization decisions.

The bundle identifier is application identity, not a security authorization mechanism.

---

# 20. iOS signing with React Native

React Native does not create a separate signing model.

The native iOS project remains responsible for:

```text
Signing
Capabilities
Provisioning
Entitlements
Certificates
Archives
```

The React Native layer sits above this:

```text
React Native JS/TS
 ↓
Native iOS project
 ↓
Xcode signing
```

---

# 21. Android signing model

The Android production chain is:

```text
React Native
 ↓
Gradle
 ↓
Signing configuration
 ↓
AAB
 ↓
Google Play
 ↓
Google Play App Signing
```

Android uses a keystore/signing-key model rather than Apple's certificate/profile model.

---

# 22. Android signing key

The Android signing key is used to sign the application artifact.

Protect:

```text
Keystore
Private key
Keystore password
Key password
```

Never commit:

```text
*.jks
*.keystore
```

to a public repository when they contain production credentials.

---

# 23. Upload key vs app signing key

When Google Play App Signing is used, distinguish:

```text
Upload key
```

from:

```text
App signing key
```

Conceptually:

```text
Developer / CI
 ↓
Upload key
 ↓
Google Play
 ↓
Google Play App Signing
 ↓
App signing key
 ↓
Users
```

Do not assume losing the upload key is equivalent to losing the app signing key.

Google Play provides mechanisms for resetting an upload key when required.

---

# 24. Google Play App Signing

For new applications, Google Play App Signing should generally be treated as part of the production signing model.

It reduces the operational burden of keeping the ultimate app signing key on developer machines or CI.

The team still needs to protect:

```text
Upload key
CI credentials
Google Play access
Recovery process
```

Do not assume Google-managed signing eliminates all signing risk.

---

# 25. Android `signingConfigs`

React Native Android projects commonly configure signing through Gradle.

A simplified structure can look like:

```gradle
android {
 signingConfigs {
 release {
 storeFile file(System.getenv("ANDROID_KEYSTORE_PATH"))
 storePassword System.getenv("ANDROID_KEYSTORE_PASSWORD")
 keyAlias System.getenv("ANDROID_KEY_ALIAS")
 keyPassword System.getenv("ANDROID_KEY_PASSWORD")
 }
 }

 buildTypes {
 release {
 signingConfig signingConfigs.release
 }
 }
}
```

This is an example pattern.

Do not copy it directly without adapting it to the project's Gradle setup.

---

# 26. Never hard-code Android secrets

Avoid:

```gradle
storePassword "password"
keyPassword "password"
```

inside:

```text
build.gradle
```

or any committed configuration.

Use:

```text
CI secret storage
Local secret configuration
Approved credential manager
```

The secret should be injected at build time.

---

# 27. Android application identity

The Android application identifier is usually:

```text
com.company.product
```

Verify it across:

```text
android/app/build.gradle
Google Play Console
Gradle configuration
CI
Fastlane
EAS
```

Do not accidentally build a staging application using the production application ID.

---

# 28. Android debug signing

Android debug builds normally use a debug keystore.

That key is not the production signing key.

Do not confuse:

```text
debug.keystore
```

with:

```text
production upload/signing key
```

A debug build should never be treated as a production release artifact.

---

# 29. Android release signing

The release build must use the intended production signing configuration.

Verify:

```text
Build variant
Signing config
Application ID
Version code
Artifact
```

Build:

```bash
cd android
./gradlew bundleRelease
```

The exact Gradle task may differ for flavors.

---

# 30. Android flavors

If the project has:

```text
development
staging
production
```

flavors, signing configuration must remain explicit.

Example:

```text
dev
→ debug signing

staging
→ controlled signing

production
→ production signing
```

Do not let Gradle silently select a production key for a development build.

---

# 31. iOS and Android signing are different

Do not force a shared credential architecture between platforms.

Use:

```text
iOS
→ Apple Developer signing system

Android
→ Android/Google Play signing system
```

Share only the operational principles:

```text
Least privilege
Secret storage
Rotation
Recovery
Auditability
CI isolation
```

---

# 32. Local development credentials

Local credentials should be scoped to development whenever possible.

Developers should not need:

```text
Production signing private key
Production store administrator credentials
Production deployment secrets
```

for normal feature development.

A developer can build and test locally without having unrestricted production release access.

---

# 33. CI signing credentials

CI should receive only the credentials required for its job.

For example:

```text
Build CI
→ signing credential

Test CI
→ no production signing credential

Store submission CI
→ store API credential

Production release
→ protected environment + approval
```

Do not expose production signing credentials to every CI job.

---

# 34. GitHub Actions example

Conceptual Android setup:

```yaml
jobs:
 build:
 runs-on: ubuntu-latest

 environment: production

 steps:
 - uses: actions/checkout@v4

 - name: Build
 run: ./gradlew bundleRelease
 env:
 ANDROID_KEYSTORE_PASSWORD: ${{ secrets.ANDROID_KEYSTORE_PASSWORD }}
 ANDROID_KEY_PASSWORD: ${{ secrets.ANDROID_KEY_PASSWORD }}
```

The actual keystore must also be supplied securely.

Do not print it into logs.

---

# 35. GitHub Actions iOS signing

For iOS, CI commonly needs access to:

```text
Apple signing identity
Provisioning profile
App Store Connect authentication
```

Possible approaches include:

```text
Xcode automatic/cloud signing
Fastlane match
Manual certificate/profile installation
EAS credentials
CI-specific signing infrastructure
```

Choose one primary strategy.

Avoid combining multiple signing authorities unless required.

---

# 36. Fastlane signing

Fastlane provides several signing approaches, including:

```text
match
cert
sigh
Xcode signing
manual management
```

Fastlane currently describes `match` as a way to synchronize certificates and profiles across a team. It can use Git, Google Cloud, or Amazon S3 as credential storage.

Use Fastlane when it reduces actual release complexity.

Do not add it only because it is popular.

---

# 37. `match`

A common Fastlane model is:

```text
Private credential store
 ↓
match
 ↓
Certificates + profiles
 ↓
CI / developer machine
 ↓
Xcode build
```

Fastlane's current documentation says `match` stores signing identities separately and supports encrypted Git storage or cloud storage.

If using Git storage:

```text
The repository must be private.
```

The encryption password is itself a secret.

---

# 38. `match` and production safety

Do not let every developer have permission to modify the signing repository.

Prefer:

```text
Read access
→ normal build users

Write/admin access
→ small trusted release group
```

For CI builds that should only consume existing credentials:

```text
match(..., readonly: true)
```

This reduces accidental credential changes.

---

# 39. EAS signing

EAS Build can manage signing credentials for React Native and Expo projects.

Expo's current documentation states that EAS Build can generate and manage:

```text
Android keystores
iOS provisioning profiles
iOS distribution certificates
```

or use credentials supplied by the project.

This can be useful for teams that want:

```text
Cloud builds
Credential management
Store submission
CI integration
```

without maintaining the entire native signing pipeline themselves.

---

# 40. EAS does not remove signing concepts

Even with EAS:

```text
Bundle ID
Certificate
Provisioning profile
Entitlements
Keystore
Upload key
```

still matter.

EAS automates the operational handling.

It does not change Apple's or Google's underlying signing model.

---

# 41. Choosing EAS vs Fastlane vs native tooling

| Need | Good starting point |
|---|---|
| Simple local iOS signing | Xcode |
| Simple local Android signing | Gradle |
| Cloud React Native builds | EAS |
| EAS credential management | EAS |
| Store automation | Fastlane or EAS |
| Existing mature native CI | Keep existing tooling |
| Complex custom native release | Native tooling + targeted automation |

Do not use:

```text
EAS + Fastlane + custom signing scripts
```

for the same responsibility unless there is a concrete reason.

---

# 42. Credential storage

A practical hierarchy:

```text
Production
→ Managed secret manager / protected CI secret store

Shared Apple signing assets
→ Approved encrypted credential store

Developer machine
→ macOS Keychain / local secure storage

Temporary CI files
→ ephemeral workspace
```

Avoid:

```text
Public Git
Shared folders
Plaintext files
Chat
Email
Issue comments
```

---

# 43. Secret files

Potentially sensitive files include:

```text
*.p12
*.mobileprovision
*.provisionprofile
*.p8
*.jks
*.keystore
credentials.json
service-account.json
```

Not every file with these extensions is automatically a production secret, but treat production credential files as sensitive until verified.

Add appropriate patterns to:

```text
.gitignore
```

and verify with:

```bash
git status
git ls-files
```

before pushing.

---

# 44. `.gitignore`

A React Native project should protect common credential artifacts.

Example:

```gitignore
# Android signing
*.jks
*.keystore
*.p12

# iOS signing
*.mobileprovision
*.provisionprofile
*.p8

# Local credentials
credentials.json
service-account.json

# Environment files
.env
.env.*
!.env.example
```

Adapt this to the repository.

Do not blindly ignore a file if the repository intentionally tracks a non-secret example or test fixture.

---

# 45. Prevent accidental commits

Before committing:

```bash
git status
```

Then inspect:

```bash
git diff --cached
```

Before pushing:

```bash
git ls-files | grep -E '\.(jks|keystore|p12|p8|mobileprovision|provisionprofile)$'
```

Use secret scanning in CI where practical.

The goal is prevention, not discovering a leaked key after publication.

---

# 46. If a signing secret is committed

Treat it as compromised.

Do not simply:

```bash
git rm secret.p12
git commit
```

The secret may remain in Git history.

Follow:

```text
1. Revoke / invalidate
2. Rotate
3. Remove exposure
4. Inspect repository history
5. Check CI logs
6. Check forks/caches where applicable
7. Update deployment systems
8. Verify the new credential
```

The security system explicitly recommends:

```text
Revoke first.
Rotate second.
Investigate third.
```


---

# 47. iOS certificate rotation

Do not rotate Apple distribution certificates on a schedule just for the sake of rotation.

Rotate when:

```text
Compromised
Expired
Required by Apple/tooling
Ownership changes
Security policy requires it
```

Apple currently supports cloud-managed certificates, which can be automatically created ahead of expiration in supported workflows.

Understand whether your project uses:

```text
Cloud-managed certificates
Local Apple Distribution certificate
Fastlane-managed credentials
EAS-managed credentials
```

before rotating anything.

---

# 48. Android key rotation

Treat Android signing-key changes as high-risk.

There is an important distinction between:

```text
Upload key rotation
```

and:

```text
App signing key change
```

Do not rotate the ultimate app signing key casually.

Use Google Play's supported recovery/upgrade mechanisms when required.

The production playbook should document the exact recovery path for the application.

---

# 49. Provisioning profile rotation

Provisioning profiles can become invalid because of:

```text
Certificate changes
Capability changes
App ID changes
Device changes
Expiration
Configuration changes
```

Do not regenerate every profile whenever a build fails.

First identify why the existing profile is no longer valid.

Apple provides profile editing and regeneration workflows for these cases.

---

# 50. Capability changes

Adding a capability can require updated signing/provisioning state.

Example:

```text
Add Push Notifications
 ↓
App ID capability
 ↓
Entitlements
 ↓
Provisioning state
 ↓
New build
```

Treat capability changes as release changes.

Do not enable a capability without testing the resulting production signing configuration.

---

# 51. Multiple targets

If the React Native project has:

```text
Main app
Share extension
Notification extension
Widget
Watch app
Staging target
```

each target may have its own:

```text
Bundle identifier
Entitlements
Signing
Provisioning
```

Do not assume one signing configuration covers every target.

Document target ownership.

---

# 52. Multiple environments

For:

```text
development
staging
production
```

decide whether each environment has:

```text
Separate bundle ID
Separate App Store Connect app
Separate signing
Separate backend
```

A common pattern is:

```text
com.company.app.dev
com.company.app
```

But do not create separate store applications unless there is an actual need.

---

# 53. CI isolation

A production signing secret should not be available to:

```text
Pull request from an untrusted fork
Untrusted contributor branch
Arbitrary workflow
Third-party action
Debug job
```

Use protected environments and approval controls where supported.

The CI workflow should follow:

```text
Untrusted code
 ↓
Tests
 ↓
No production signing access

Trusted release ref
 ↓
Approval
 ↓
Production signing
```

This reduces supply-chain risk.

---

# 54. Third-party GitHub Actions

Signing credentials can be exposed if a third-party CI action is compromised or misconfigured.

Prefer:

```text
Trusted actions
Pinned versions
Minimal permissions
Minimal secrets
```

Do not pass production signing secrets to steps that do not require them.

The security system explicitly treats CI/CD permissions and build secrets as supply-chain security concerns.

---

# 55. Secret exposure in logs

Avoid commands that print:

```text
Environment variables
Keychain contents
Credential files
Private keys
Passwords
Tokens
```

Review CI logs after introducing signing automation.

If a secret appears:

```text
Revoke
Rotate
Investigate
```

Do not assume that deleting the log line later solves the exposure.

---

# 56. Signing and AI agents

AI coding agents can inspect signing configuration, but they should not receive unrestricted access to production credentials.

Good AI tasks:

```text
Inspect signing configuration
Find mismatched Bundle IDs
Find hard-coded secrets
Review Gradle signing configuration
Review Xcode signing settings
Explain signing errors
Check `.gitignore`
Generate release checklists
```

Avoid giving an agent unrestricted access to:

```text
Production private keys
Apple account credentials
Google Play account credentials
Signing repositories
Production CI environments
```

The security system requires permission boundaries for AI tools and says AI output/actions must not be treated as trusted.

---

# 57. AI signing audit prompt

```text
Audit this React Native project's mobile signing setup.

Inspect:

- android/
- ios/
- package.json
- Gradle signing configuration
- Xcode signing configuration
- entitlements
- provisioning references
- CI workflows
- Fastlane configuration
- EAS configuration
- .gitignore

Check:

1. Application identifiers
2. Android signing configuration
3. Android keystore handling
4. Upload-key handling
5. iOS certificates
6. Provisioning profiles
7. Entitlements
8. Apple Team configuration
9. CI secret exposure
10. Hard-coded credentials
11. Production signing access
12. Fork/PR secret exposure
13. Credential rotation path
14. Credential recovery path
15. Signing source of truth
16. Duplicate credential systems

Rules:

- Never request secrets.
- Never print secrets.
- Never expose private keys.
- Do not modify signing credentials.
- Do not revoke or rotate credentials.
- Do not publish or submit an application.
- Report evidence with file paths.
- Distinguish confirmed findings from assumptions.
- Prefer the smallest secure change.
```

---

# 58. Signing troubleshooting flow

When signing fails:

```text
Build fails
 ↓
Identify platform
 ├── iOS
 └── Android
 ↓
Identify artifact
 ├── Development
 ├── Internal/Test
 └── Production
 ↓
Verify application identity
 ↓
Verify signing configuration
 ↓
Verify credential validity
 ↓
Verify capabilities/entitlements
 ↓
Verify CI environment
 ↓
Rebuild
```

Do not start by regenerating every credential.

---

# 59. iOS troubleshooting

Check in this order:

```text
1. Bundle ID
2. Apple Team
3. Target
4. Scheme
5. Build configuration
6. Capabilities
7. Entitlements
8. Certificate
9. Provisioning profile
10. Keychain access
11. CI credential installation
```

This order reduces unnecessary credential churn.

---

# 60. Android troubleshooting

Check:

```text
1. Application ID
2. Build variant
3. Signing config
4. Keystore path
5. Keystore password
6. Key alias
7. Key password
8. Upload-key state
9. Google Play configuration
10. Version code
```

Again:

```text
Configuration first
Credentials second
```

unless there is evidence of credential compromise.

---

# 61. Common failure: wrong iOS Bundle ID

Symptoms:

```text
Provisioning profile mismatch
Signing error
App Store upload rejection
Capabilities unavailable
```

Check:

```text
Xcode target
Apple Developer App ID
App Store Connect
Provisioning profile
```

All must refer to the intended application.

---

# 62. Common failure: missing iOS capability

Symptoms:

```text
Entitlement error
Push not working
Associated domain not working
Apple Pay issue
```

Check:

```text
Capability
App ID
Entitlements
Provisioning profile
Build configuration
```

Regenerate only the affected signing assets if required.

---

# 63. Common failure: expired iOS certificate

Check:

```text
Certificate status
Private key availability
CI installation
Provisioning profile
```

Then:

```text
Create/obtain replacement
Update provisioning state
Update CI
Build
Validate
```

Do not delete every existing certificate without understanding which applications or machines depend on them.

---

# 64. Common failure: lost iOS private key

If the certificate exists but the private key is unavailable:

```text
Existing certificate
+
No private key
```

may not be usable for local signing.

Determine:

```text
Can the signing identity be recovered?
Is another authorized machine holding the private key?
Is the project using match/EAS/cloud-managed signing?
Does a replacement certificate need to be created?
```

Do not create unnecessary new certificates until ownership is understood.

---

# 65. Common failure: Android keystore lost

First determine whether:

```text
Google Play App Signing
```

is enabled.

If the lost credential is the:

```text
Upload key
```

the recovery path differs from losing the:

```text
App signing key
```

Do not delete or replace signing configuration blindly.

Record the application's Google Play signing state in its recovery documentation.

---

# 66. Common failure: wrong Android keystore

Symptoms:

```text
Upload rejected
Signature mismatch
Existing app cannot be updated
```

Check:

```text
Keystore
Alias
Certificate fingerprint
Google Play app
CI secret
```

Do not create a new production application just to bypass a signing mismatch.

---

# 67. Common failure: CI cannot sign iOS

Check:

```text
Certificate installed?
Private key installed?
Provisioning profile installed?
Correct Team?
Keychain accessible?
Correct Xcode version?
Correct scheme?
```

If using Fastlane:

```text
match access
match password
readonly state
```

If using EAS:

```text
EAS project
credential profile
Apple account access
```

If using manual signing:

```text
Certificate
Profile
Export options
```

---

# 68. Common failure: CI cannot sign Android

Check:

```text
Keystore available?
Correct path?
Correct password?
Correct alias?
Correct key password?
Correct build variant?
```

Do not log the values while debugging.

Instead log safe metadata:

```text
Keystore file exists: yes/no
Alias configured: yes/no
Build variant: release
```

---

# 69. Credential recovery

A production signing system must answer:

```text
Who can recover credentials?
Where are backups?
How are backups protected?
How is access verified?
How is a replacement credential created?
How is CI updated?
How is the store verified afterward?
```

Document this before an incident.

Recovery documentation should never contain:

```text
Actual passwords
Private keys
Secret values
```

It should contain:

```text
Location
Owner
Procedure
Dependencies
Approval requirements
Validation steps
```

---

# 70. Signing incident response

If credentials are suspected to be compromised:

```text
1. Stop affected release automation
2. Identify exposed credential
3. Revoke/invalidate where appropriate
4. Rotate/replace
5. Update CI
6. Update local/shared signing systems
7. Check store access
8. Review logs and Git history
9. Verify new release
10. Document incident
```

Prioritize containment over perfect diagnosis during active compromise.

The security system explicitly defines:

```text
Detection
Triage
Containment
Revocation
Recovery
Communication
Postmortem
```

for serious security incidents.

---

# 71. Backup strategy

Back up only what is necessary.

For production signing, recovery may require:

```text
Credential metadata
Encrypted private keys where appropriate
Provisioning profiles
Recovery instructions
Account ownership
Store access
```

Backups must themselves be protected.

Do not create five plaintext copies of the same production key "for safety."

That increases exposure rather than resilience.

---

# 72. Recovery testing

A credential backup that has never been tested is not a reliable recovery plan.

Periodically verify:

```text
Can the team restore the signing configuration?
Can CI access it?
Can a release build be produced?
Can the store accept the artifact?
```

Do this in a controlled environment.

Do not test recovery by intentionally breaking the production signing system.

---

# 73. Signing observability

Record safe metadata about production builds:

```text
Platform
Application ID
Version
Build number/version code
Git SHA
Signing strategy
CI run
Build timestamp
Artifact identifier
```

Do not record:

```text
Private keys
Passwords
Tokens
Raw credentials
```

This gives release traceability without creating a new secret store.

---

# 74. Signing and reproducibility

A production artifact should be traceable to:

```text
Source commit
+
Dependency lockfile
+
Build environment
+
Application identity
+
Signing configuration
+
CI run
```

The goal is:

```text
Artifact
 ↓
Exact source
 ↓
Exact configuration
 ↓
Known signing identity
```

This makes release debugging and incident response much easier.

---

# 75. Signing checklist: iOS

```text
[ ] Apple Developer team verified
[ ] App Store Connect app verified
[ ] Bundle ID verified
[ ] App ID verified
[ ] Target verified
[ ] Scheme verified
[ ] Capabilities verified
[ ] Entitlements verified
[ ] Certificate verified
[ ] Private key protected
[ ] Provisioning profile verified
[ ] CI signing access verified
[ ] Production credentials are not exposed to PR jobs
[ ] No signing secrets committed
[ ] Recovery path documented
[ ] Rotation path documented
[ ] Test archive succeeds
[ ] TestFlight upload succeeds
```

---

# 76. Signing checklist: Android

```text
[ ] Google Play application verified
[ ] Application ID verified
[ ] Production build variant verified
[ ] Release signing config verified
[ ] Keystore protected
[ ] Key alias verified
[ ] Keystore password protected
[ ] Key password protected
[ ] Upload key understood
[ ] Google Play App Signing state verified
[ ] CI signing access verified
[ ] Production credentials are not exposed to PR jobs
[ ] No keystore committed
[ ] Recovery path documented
[ ] Upload-key recovery path documented
[ ] AAB builds successfully
[ ] Google Play accepts the artifact
```

---

# 77. Signing checklist: CI

```text
[ ] Production secrets scoped to protected jobs
[ ] PR jobs cannot access production signing secrets
[ ] Third-party actions minimized
[ ] Actions pinned appropriately
[ ] Secret values never printed
[ ] Temporary credential files cleaned up
[ ] Build logs reviewed
[ ] Signing configuration is reproducible
[ ] CI environment is documented
[ ] Credential ownership documented
[ ] Recovery procedure documented
```

---

# 78. Signing checklist: repository

```text
[ ] No private signing keys
[ ] No keystores
[ ] No credential JSON
[ ] No Apple private keys
[ ] No store passwords
[ ] No hard-coded signing secrets
[ ] `.gitignore` reviewed
[ ] Secret scanning enabled where practical
[ ] Example configuration contains placeholders only
[ ] Signing documentation contains procedures, not secrets
```

---

# 79. Recommended repository structure

The signing documentation in this playbook should remain separated by platform:

```text
signing/
├── ios/
│ ├── certificates.md
│ ├── provisioning-profiles.md
│ ├── distribution.md
│ ├── api-keys.md
│ └── key-rotation.md
│
├── android/
│ ├── keystore.md
│ ├── upload-key.md
│ ├── play-app-signing.md
│ └── key-rotation.md
│
└── security/
 ├── secret-storage.md
 ├── ci-secrets.md
 ├── access-control.md
 └── recovery.md
```

This file provides the React Native-level model.

Platform-specific credential procedures should live in the platform directories.

---

# 80. Recommended release architecture

For a React Native project, prefer a clear ownership model:

```text
 React Native
 |
 +----------+----------+
 | |
 iOS Android
 | |
 Xcode Gradle
 | |
 Apple signing Android signing
 | |
 IPA/AAB AAB
 | |
 App Store Connect Google Play
```

Automation can sit around this:

```text
 CI/CD
 |
 +--------+--------+
 | |
 iOS build Android build
 | |
 Xcode/EAS Gradle/EAS
 | |
 Fastlane Fastlane
 | |
 Store Store
```

Keep the underlying platform ownership clear.

---

# 81. Recommended signing strategy

For a small or growing React Native project:

```text
Development
→ Xcode automatic signing
→ Android debug signing

Production
→ One documented signing authority per platform
→ Protected CI secrets
→ Human approval
→ Store-controlled distribution

Recovery
→ Documented and tested
```

If using EAS:

```text
EAS
→ build + credential management where useful
```

If using Fastlane:

```text
Fastlane
→ signing synchronization + release automation where useful
```

Do not use both merely because both exist.

---

# 82. What not to do

Avoid:

```text
❌ Commit production keystores
❌ Commit `.p8` private keys
❌ Store passwords in Gradle
❌ Put signing credentials in app code
❌ Give every developer production admin access
❌ Give production secrets to PR jobs
❌ Share signing keys through chat
❌ Regenerate certificates for every error
❌ Rotate keys without a recovery plan
❌ Treat debug signing as production signing
❌ Assume EAS/Fastlane makes security automatic
❌ Let AI agents access unrestricted production credentials
```

---

# 83. Definition of done

Signing is production-ready when:

```text
Application identity is correct
 +
Signing ownership is explicit
 +
Credentials are protected
 +
CI access is least-privileged
 +
Builds are reproducible
 +
Capabilities match signing state
 +
Recovery is documented
 +
Rotation is understood
 +
Secrets are not in source control
 +
Production release is human-controlled
 ↓
SIGNING READY
```

The goal is not:

```text
"Signing works on my laptop."
```

The goal is:

```text
"The team can securely produce the correct signed artifact,
from a known source, using controlled credentials,
and recover when signing infrastructure fails."
```

---

# 84. Security review

For production signing, evaluate:

| Area | Requirement |
|---|---|
| Identity | Bundle ID/package name is correct |
| Ownership | Account and credential owners are known |
| Access | Least privilege |
| Secrets | Stored outside source control |
| CI | Production secrets isolated |
| Signing | Platform signing model understood |
| Rotation | Replacement procedure documented |
| Recovery | Credential recovery documented |
| Observability | Safe release metadata recorded |
| AI | No unrestricted signing access |
| Supply chain | CI actions and dependencies reviewed |

The security system's baseline is practical security that is layered, testable, maintainable, and based on realistic threats rather than security theater.

---

# Official sources

Use official platform documentation as the final authority for current signing behavior.

## Apple

- Apple Developer: https://developer.apple.com/
- Apple Certificates: https://developer.apple.com/support/certificates/
- Certificates, Identifiers & Profiles: https://developer.apple.com/help/account/
- Create development provisioning profiles: https://developer.apple.com/help/account/provisioning-profiles/create-a-development-provisioning-profile/
- Create App Store provisioning profiles: https://developer.apple.com/help/account/provisioning-profiles/create-an-app-store-provisioning-profile/
- Provisioning profile updates: https://developer.apple.com/help/account/provisioning-profiles/provisioning-profile-updates
- Edit/download/delete provisioning profiles: https://developer.apple.com/help/account/provisioning-profiles/edit-download-or-delete-profiles/
- Cloud-managed certificates: https://developer.apple.com/help/account/certificates/cloud-managed-certificates/
- App Store Connect: https://appstoreconnect.apple.com/
- App Store Connect Help: https://developer.apple.com/help/app-store-connect/

## React Native

- React Native documentation: https://reactnative.dev/docs/getting-started
- Publishing to App Store: https://reactnative.dev/docs/publishing-to-app-store

## Fastlane

- Fastlane code signing: https://docs.fastlane.tools/codesigning/getting-started/
- Fastlane match: https://docs.fastlane.tools/actions/match/
- Fastlane App Store Connect API: https://docs.fastlane.tools/app-store-connect-api/

## Expo / EAS

- EAS Build: https://docs.expo.dev/build/introduction/
- EAS app credentials: https://docs.expo.dev/app-signing/app-credentials/
- EAS: https://docs.expo.dev/eas/

## Android

- Android Developers: https://developer.android.com/
- App signing: https://developer.android.com/studio/publish/app-signing
- Google Play App Signing: https://support.google.com/googleplay/android-developer/answer/9842756

---

**Last verified:** August 11, 2026

Apple, Google Play, React Native, Xcode, Gradle, Fastlane, Expo, EAS, signing APIs, certificate systems, and store requirements change over time. Verify the current official documentation for the exact platform and tooling versions used by the project before production release.
