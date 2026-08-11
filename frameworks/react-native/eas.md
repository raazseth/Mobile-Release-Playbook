# React Native with EAS

This guide covers using **Expo Application Services (EAS)** with an existing React Native application.

It is intended for React Native projects that use EAS for one or more of:

```text
Builds
Credentials
App Store / Google Play submission
OTA updates
CI/CD
Release automation
```

EAS is not a requirement for React Native.

Use it when it reduces release complexity for the project. Keep the underlying native Android and iOS release model understandable even when EAS performs the automation.

---

# 1. Where EAS fits

A React Native release can be implemented with native tooling directly:

```text
React Native
    ↓
Xcode / Gradle
    ↓
IPA / AAB
    ↓
App Store / Google Play
```

With EAS:

```text
React Native
    ↓
EAS
    ├── Build
    ├── Credentials
    ├── Submit
    └── Update
          ↓
App Store / Google Play
```

EAS is therefore a release service layer, not a replacement for understanding the underlying platform.

For Android:

```text
EAS Build
→ Android native build
→ AAB
→ Google Play
```

For iOS:

```text
EAS Build
→ Xcode/native build
→ IPA
→ App Store Connect
```

---

# 2. When to use EAS

EAS is useful when you want:

```text
[ ] Cloud builds
[ ] Managed signing/credentials
[ ] Reproducible build profiles
[ ] Automated store submission
[ ] CI/CD integration
[ ] Expo Updates / OTA delivery
[ ] Easier release workflows for a React Native team
```

It is less useful if the project already has a mature native release pipeline and EAS would only add another abstraction.

Do not adopt EAS just because it is available.

The decision should be based on:

```text
Build complexity
+
Team workflow
+
CI requirements
+
Credential management
+
OTA requirements
+
Operational cost
```

---

# 3. Prerequisites

Before using EAS, confirm:

```text
[ ] React Native project builds locally
[ ] Android build works
[ ] iOS build works
[ ] Application IDs are correct
[ ] Bundle ID is correct
[ ] Versioning is understood
[ ] Production environments are defined
[ ] Native dependencies are compatible
[ ] Expo/EAS requirements for the selected workflow are understood
```

If the project has never built successfully outside EAS, do not use EAS as a way to hide an unresolved native build problem.

First establish a known-good native project.

---

# 4. Install EAS CLI

Install the EAS CLI according to the current Expo documentation.

A common installation is:

```bash
npm install -g eas-cli
```

Verify:

```bash
eas --version
```

For CI, prefer an explicit CLI version rather than relying on an unknown global version.

Do not assume the latest EAS CLI is compatible with every React Native/Expo configuration.

---

# 5. Authenticate with Expo

Authenticate the local CLI:

```bash
eas login
```

Then verify the account:

```bash
eas whoami
```

For CI, use the appropriate non-interactive authentication mechanism supported by Expo.

Never commit:

```text
Expo access tokens
```

to Git.

Store them in:

```text
CI secret storage
```

---

# 6. Initialize EAS in the project

From the project root:

```bash
eas build:configure
```

This creates or updates the EAS project configuration.

A typical configuration file is:

```text
eas.json
```

Do not blindly commit every generated change without reviewing it.

Understand:

```text
Build profiles
Distribution
Environment
Android configuration
iOS configuration
Auto-increment behavior
```

---

# 7. `eas.json`

A simple configuration may look like:

```json
{
  "build": {
    "development": {
      "developmentClient": true,
      "distribution": "internal"
    },
    "preview": {
      "distribution": "internal"
    },
    "production": {
      "autoIncrement": true
    }
  }
}
```

This is only an example.

The correct profiles depend on the application.

Do not copy a large `eas.json` from another project without understanding every field.

---

# 8. Build profiles

A useful starting model is:

```text
development
preview
production
```

### Development

Used for:

```text
Development clients
Local testing
Native debugging
```

### Preview

Used for:

```text
Internal testing
QA
Stakeholder testing
Release candidates
```

### Production

Used for:

```text
Store releases
Production artifacts
```

Keep profiles intentionally small.

A profile should answer:

> What kind of artifact are we producing, and who is allowed to use it?

---

# 9. Avoid profile explosion

Do not create:

```text
dev-a
dev-b
dev-c
qa-a
qa-b
staging-a
staging-b
release-a
release-b
```

unless there is a real operational need.

Too many profiles create:

```text
Configuration drift
Confusion
Release mistakes
Hard-to-debug environment differences
```

Prefer:

```text
development
preview
production
```

and add profiles only when a real workflow requires them.

---

# 10. Build a development client

For projects using Expo development builds:

```bash
eas build --profile development
```

This produces a development-oriented native build.

The exact behavior depends on the project's Expo configuration and build profile.

Use development builds when the project requires native modules or native configuration that cannot be tested reliably in a generic development environment.

---

# 11. Preview builds

A preview build is useful for:

```text
QA
Internal distribution
Product testing
Release candidate validation
```

Example:

```bash
eas build --profile preview
```

The preview build should use:

```text
Realistic configuration
Test backend or production-like backend
Production-like permissions
Real native integrations
```

Do not call a build "preview" while silently pointing it at an unrelated development environment.

Make environment selection explicit.

---

# 12. Production builds

Android:

```bash
eas build --platform android --profile production
```

iOS:

```bash
eas build --platform ios --profile production
```

Both:

```bash
eas build --platform all --profile production
```

The resulting artifacts depend on the platform configuration.

Typical production artifacts are:

```text
Android → AAB
iOS → IPA
```

Do not upload an artifact without verifying:

```text
Application identity
Version
Environment
Signing
Build profile
```

---

# 13. Build only one platform

When debugging or releasing one platform:

```bash
eas build --platform android --profile production
```

or:

```bash
eas build --platform ios --profile production
```

This reduces iteration time.

For a cross-platform release:

```bash
eas build --platform all --profile production
```

Use separate platform verification even when both builds are triggered together.

A successful Android build does not prove the iOS release is correct.

---

# 14. Android with EAS

The Android flow is:

```text
React Native
    ↓
EAS Build
    ↓
Gradle
    ↓
AAB
    ↓
Google Play
```

Before production:

```text
[ ] Application ID verified
[ ] versionCode verified
[ ] target SDK verified
[ ] Production environment verified
[ ] Signing verified
[ ] AAB generated
[ ] Real-device test passed
[ ] Play testing completed
```

See:

```text
frameworks/react-native/android-release.md
checklists/android.md
signing/android/
publishing/android/
```

---

# 15. iOS with EAS

The iOS flow is:

```text
React Native
    ↓
EAS Build
    ↓
Xcode
    ↓
IPA
    ↓
App Store Connect
```

Before production:

```text
[ ] Bundle ID verified
[ ] Version verified
[ ] Build number verified
[ ] Apple team verified
[ ] Signing verified
[ ] Entitlements verified
[ ] IPA generated
[ ] TestFlight testing completed
[ ] App Store submission ready
```

See:

```text
frameworks/react-native/ios-release.md
signing/ios/
publishing/ios/
checklists/ios.md
```

---

# 16. Credentials

EAS can help manage platform credentials.

This can include:

```text
Android signing credentials
Apple certificates
Provisioning profiles
App Store Connect credentials
```

The exact credential workflow depends on the platform and project configuration.

Treat credentials as production secrets even when EAS manages them.

Do not assume:

```text
EAS manages it
→ security is automatically solved
```

You still need:

```text
Access control
Credential ownership
Recovery process
Rotation process
CI permissions
Auditability
```

---

# 17. EAS-managed credentials

When appropriate, EAS can manage credentials for the project.

Before choosing managed credentials, establish:

```text
Who owns the credentials?
Who can access them?
What happens if the Expo account is unavailable?
How are credentials recovered?
How are team members granted access?
How are credentials rotated?
```

For production applications, document the recovery path.

Do not let the release process depend on one developer's personal account without a recovery plan.

---

# 18. Local credentials

You can also maintain platform credentials outside EAS-managed storage depending on the workflow.

This can be useful when:

```text
Company controls signing infrastructure
Existing CI pipeline owns credentials
Security policy requires external secret management
Migration away from EAS is expected
```

The important requirement is:

```text
Credential ownership must be explicit.
```

Do not mix multiple credential authorities without documenting which system is the source of truth.

---

# 19. Android credential model

For Google Play releases, understand:

```text
Upload key
        ↓
EAS build artifact
        ↓
Google Play
        ↓
Google Play App Signing
        ↓
User
```

Do not confuse:

```text
Upload key
```

with:

```text
App signing key
```

See:

```text
signing/android/
```

for the platform-specific rules.

---

# 20. iOS credential model

The iOS release chain includes:

```text
Bundle ID
+
Apple Developer Team
+
Certificates
+
Provisioning profile
+
Entitlements
        ↓
IPA
        ↓
App Store Connect
```

EAS can automate parts of this process, but the underlying Apple signing model still matters.

If signing fails, debug the actual Apple identity/configuration instead of repeatedly regenerating credentials.

See:

```text
signing/ios/
```

---

# 21. Environment variables

EAS supports environment configuration for builds and related workflows.

Keep the distinction clear:

```text
Public application configuration
vs
Sensitive secrets
```

Anything bundled into a mobile application should be treated as observable by the user.

Never put:

```text
Database passwords
Private API keys
Service-account credentials
Admin secrets
Signing private keys
```

into a client bundle.

For privileged operations:

```text
Mobile app
→ backend
→ privileged service
```

---

# 22. Environment separation

A practical model:

```text
development
    ↓
Development backend

preview
    ↓
Preview/staging backend

production
    ↓
Production backend
```

The exact model can differ.

The important requirement is that the mapping is explicit and testable.

Do not depend on developer memory to choose the correct backend.

---

# 23. EAS environment variables

When using EAS environment variables, document:

```text
Variable
Purpose
Environment
Visibility
Owner
Source
```

Example:

```text
API_URL
development
preview
production
```

Sensitive values should remain in protected secret storage.

Do not print sensitive environment variables in build logs.

---

# 24. Build reproducibility

A production EAS build should be traceable to:

```text
Git commit
Build profile
EAS project
React Native/Expo version
Dependency lockfile
Environment
Build ID
Artifact
```

The goal is:

```text
Artifact
    ↓
exact source
    ↓
exact configuration
    ↓
exact build
```

Without this, debugging production releases becomes much harder.

---

# 25. Lockfile

Commit the project's dependency lockfile:

```text
package-lock.json
yarn.lock
pnpm-lock.yaml
```

Use the package manager expected by the repository.

Do not regenerate the lockfile during every release unless dependencies actually changed.

A production build should resolve dependencies predictably.

---

# 26. EAS build cache

EAS builds can use caching to improve build performance.

Caching is useful, but it can also preserve stale state.

If:

```text
Cached build fails
Clean build succeeds
```

investigate the cache rather than permanently deleting it.

The goal is:

```text
Fast
+
reproducible
+
correct
```

not merely:

```text
fast
```

---

# 27. Build logs

When a build fails, capture:

```text
EAS build ID
Platform
Profile
Git commit
First meaningful error
Relevant native logs
Dependency changes
```

Do not copy only the final:

```text
Build failed
```

line.

The useful information is normally earlier in the build output.

---

# 28. EAS build failure workflow

Use:

```text
Build fails
   ↓
Open EAS build logs
   ↓
Find first meaningful error
   ↓
Classify
   ├── JavaScript
   ├── Expo config
   ├── Native dependency
   ├── Android
   ├── iOS
   ├── Credentials
   ├── Environment
   └── CI/toolchain
   ↓
Reproduce locally if possible
   ↓
Apply smallest fix
   ↓
Build again
```

Do not immediately change the EAS configuration.

---

# 29. EAS local builds

EAS supports local build workflows in supported configurations.

This can be useful when:

```text
Cloud build failure
Native debugging required
Build environment needs investigation
Enterprise network restrictions exist
```

The exact local-build requirements are platform-specific and can change.

Use the current Expo EAS Build documentation before relying on local builds.

The key diagnostic value is:

```text
Cloud build fails
vs
Local build fails
```

If both fail:

```text
Project/configuration is likely the problem.
```

If only cloud fails:

```text
Build environment/configuration is likely different.
```

---

# 30. EAS Submit

EAS Submit can upload builds to the platform stores.

Android:

```bash
eas submit --platform android
```

iOS:

```bash
eas submit --platform ios
```

Both:

```bash
eas submit --platform all
```

Submission is not the same as production release.

The flow is:

```text
Build
→ Submit
→ Store processing
→ Testing/review
→ Production release
```

Keep human approval before production release.

---

# 31. Automated submission

For a controlled pipeline:

```text
Git tag/release
    ↓
EAS build
    ↓
Artifact validation
    ↓
Human approval
    ↓
EAS submit
    ↓
Store testing
    ↓
Production rollout
```

Do not automatically submit every successful branch build to production.

Use separate:

```text
development
preview
production
```

release paths.

---

# 32. EAS Update

EAS Update provides an OTA mechanism for compatible JavaScript/assets updates.

Conceptually:

```text
Native application
        ↓
Compatible runtime
        ↓
OTA JavaScript/assets update
```

This can reduce the need for a new store submission for certain changes.

However:

> OTA updates do not replace native builds.

If a change requires:

```text
New native module
Native configuration
Permission changes
Entitlement changes
Native dependency update
Native runtime change
```

a new native build is generally required.

See:

```text
frameworks/expo/updates.md
```

for the broader update model.

---

# 33. Runtime compatibility

Every OTA update should target a compatible native runtime.

Think:

```text
Native runtime version
        ↓
Compatible JavaScript update
```

Do not ship JavaScript that expects native functionality absent from the installed binary.

A useful mental model is:

```text
Native binary
    =
capabilities contract

OTA update
    =
code that must stay within that contract
```

---

# 34. OTA release safety

Before publishing an OTA update:

```text
[ ] Correct project
[ ] Correct environment
[ ] Correct runtime compatibility
[ ] Tests pass
[ ] Critical flows pass
[ ] Rollback path exists
[ ] Update channel/branch is correct
[ ] Human approval completed
```

Do not use OTA updates as a bypass for store review when the change actually requires native changes or policy review.

---

# 35. Channels and branches

When using EAS Update, keep the deployment model understandable.

A practical model can be:

```text
development
preview
production
```

Map each to the intended update environment.

Avoid creating a large number of update channels without a real need.

Configuration drift between:

```text
branch
channel
build profile
environment
```

is a common source of release mistakes.

---

# 36. Native vs OTA decision

Use this rule:

```text
Does the change require native code/configuration?
        │
        ├── Yes
        │    ↓
        │  New native build
        │
        └── No
             ↓
          OTA may be appropriate
```

Examples that generally require a native build:

```text
New native package
Native permission
Native entitlement
Android manifest change
iOS Info.plist change
New native capability
Native dependency upgrade
Native architecture change
```

Examples that may fit OTA:

```text
JavaScript bug fix
React UI change
Copy/content change
Compatible JS-only feature change
```

Always verify compatibility with the current EAS Update/runtime model.

---

# 37. Versioning with EAS

Versioning should remain understandable even when EAS manages increments.

Maintain:

```text
User-facing version
+
Platform build number/version code
```

For example:

```text
1.8.0
```

with platform-specific build identifiers.

If using:

```json
"autoIncrement": true
```

under an EAS build profile, understand what is being incremented and where the resulting value comes from.

Do not let automated versioning become invisible to the release process.

Record the final version in release evidence.

---

# 38. EAS and app configuration

React Native projects using EAS may have:

```text
app.json
app.config.js
app.config.ts
```

depending on the project.

Treat configuration as source code.

Review:

```text
Application ID
Bundle ID
App name
Version
Build number
Version code
Plugins
Permissions
URL schemes
Associated domains
Android configuration
iOS configuration
```

Do not change application identity from an environment variable unless the multi-app architecture is intentional and documented.

---

# 39. Config plugins

Config plugins can modify native projects during build configuration.

This is powerful because it can automate:

```text
Info.plist changes
AndroidManifest changes
Gradle configuration
Native dependencies
iOS capabilities
```

It is also a release risk.

Before using a plugin:

```text
Understand what it changes
Check permissions
Check generated native changes
Check platform compatibility
Test release builds
```

Do not add a config plugin simply because it makes a configuration change convenient.

---

# 40. Prebuild and native projects

Expo/EAS projects may use generated native projects or committed native projects depending on the architecture.

Before running commands that generate or modify native projects, determine:

```text
Are ios/ and android/ committed?
Are they generated?
Are there manual native changes?
Are config plugins the source of truth?
```

Do not overwrite a manually maintained native project without understanding what will be regenerated.

---

# 41. EAS and existing React Native projects

An existing React Native application does not automatically become an Expo-managed project just because EAS is introduced.

Separate these concepts:

```text
React Native
```

is the application framework.

```text
Expo
```

provides a broader React Native platform/tooling ecosystem.

```text
EAS
```

provides cloud services and tooling for builds, submissions, updates, and related workflows.

A project can use parts of this ecosystem without adopting every Expo capability.

Make the architecture explicit.

---

# 42. EAS with bare React Native

If the project has an existing:

```text
android/
ios/
```

native setup, evaluate whether EAS can build that project without forcing an unnecessary migration.

The right approach depends on:

```text
Expo modules
Expo configuration
Native project structure
Build requirements
CI requirements
```

Do not migrate a mature native project simply to use EAS Build unless the migration has a clear payoff.

---

# 43. CI/CD with EAS

A production pipeline can be:

```text
Pull request
    ↓
Tests
    ↓
Merge
    ↓
Release tag
    ↓
EAS build
    ↓
Artifact verification
    ↓
Human approval
    ↓
EAS submit
    ↓
Store testing
    ↓
Production
```

CI should control:

```text
Source commit
Build profile
Environment
Credentials
Approval
Release evidence
```

EAS should not become an opaque step that nobody on the team understands.

---

# 44. GitHub Actions example

A minimal conceptual workflow:

```yaml
name: Mobile Release

on:
  workflow_dispatch:

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: <project-version>

      - run: npm ci

      - run: npx eas-cli build --platform android --profile production --non-interactive
        env:
          EXPO_TOKEN: ${{ secrets.EXPO_TOKEN }}
```

This is intentionally incomplete.

Before production use, add:

```text
Version control
Environment protection
Approval
Artifact verification
Platform credentials
Failure handling
Release evidence
```

Do not copy this directly into production without adapting it to the repository.

---

# 45. EAS secret handling

Never print:

```text
EXPO_TOKEN
Apple credentials
Google credentials
Keystore passwords
Private keys
Production API secrets
```

in CI logs.

Use:

```text
GitHub Actions secrets/environments
or
approved secret manager
```

with least privilege.

The AI and security systems in this repository require secrets to remain isolated from source code, logs, prompts, and untrusted tools.

---

# 46. Human approval

Production release should have a clear approval boundary:

```text
Automated build
      ↓
Automated validation
      ↓
Human review
      ↓
Store submission/release
```

AI and CI can prepare:

```text
artifact
release notes
checks
risk report
store metadata
```

but production release should not become an uncontrolled autonomous action.

---

# 47. AI-assisted EAS release audit

AI can inspect:

```text
eas.json
app.json
app.config.*
package.json
lockfile
android/
ios/
CI workflows
```

Ask it to verify:

```text
Build profiles
Platform identifiers
Environment mapping
Versioning
Native configuration
Signing references
EAS Update compatibility
CI secrets
Production profile
```

Use AI for:

```text
inspection
comparison
checklists
release summaries
failure diagnosis
```

Do not give an agent unrestricted permission to:

```text
publish production builds
rotate signing credentials
delete releases
modify store configuration
```

---

# 48. EAS audit prompt

```text
Audit this React Native project for EAS production release readiness.

Inspect:

- eas.json
- app.json / app.config.*
- package.json
- lockfile
- android/
- ios/
- CI workflows

Check:

1. EAS build profiles
2. Development/preview/production separation
3. Android application ID
4. iOS bundle identifier
5. Versioning
6. Build number/version code
7. Environment mapping
8. Signing configuration
9. Native configuration
10. Config plugins
11. EAS Update compatibility
12. CI authentication
13. Secret handling
14. Production release safety
15. Store submission readiness

For each finding return:

- severity
- evidence
- file
- risk
- minimal fix
- verification

Rules:

- Do not print secrets.
- Do not request secrets.
- Do not modify files.
- Do not invent platform requirements.
- Verify volatile Expo, Apple, and Google requirements against official documentation.
- Separate confirmed facts from assumptions.
```

---

# 49. Common failure: EAS CLI authentication fails

Check:

```text
Expo account
Authentication method
EXPO_TOKEN
CI secret
Token permissions
Project ownership
```

Useful local command:

```bash
eas whoami
```

Do not paste authentication tokens into issue trackers, chat messages, or logs.

---

# 50. Common failure: EAS project mismatch

Symptoms:

```text
Wrong project
Wrong owner
Wrong update channel
Wrong credentials
Build belongs to unexpected Expo project
```

Check:

```text
Project ID
Owner
EAS project configuration
Account
Build profile
CI environment
```

This is especially important when:

```text
forks
multiple apps
staging apps
organization accounts
```

exist.

---

# 51. Common failure: build profile uses wrong environment

Symptoms:

```text
Preview build calls production API
Production build calls staging API
```

Check:

```text
eas.json
EAS environment
app.config.*
CI variables
build profile
```

Trace:

```text
Profile
→ environment
→ configuration
→ bundled value
```

Do not fix this by changing application code to detect the environment dynamically unless that is intentionally part of the architecture.

---

# 52. Common failure: EAS credentials are wrong

Check:

```text
Application identity
Credential owner
Certificate/profile
Keystore
EAS credential state
Apple team
Google Play account
```

Do not regenerate credentials repeatedly.

Credential rotation should be intentional.

---

# 53. Common failure: EAS build succeeds but app crashes

EAS only proves that the build completed.

It does not prove:

```text
Application behavior
Environment
Native integrations
Permissions
Deep links
Push notifications
Payments
Upgrade behavior
```

Run the same release verification used for native builds.

See:

```text
checklists/pre-release.md
testing/release-smoke-tests.md
testing/device-testing.md
```

---

# 54. Common failure: OTA update breaks the app

Immediately establish:

```text
Native runtime version
Update version
Update channel
Update branch
Git commit
Affected users
```

Then determine:

```text
Is the update compatible with the installed native runtime?
```

If not:

```text
Stop further rollout
Publish a compatible update if possible
Otherwise release a new native build
```

Do not keep publishing updates while the runtime compatibility is unknown.

---

# 55. Common failure: native change shipped through OTA

If an update requires a new native capability:

```text
OTA is the wrong deployment mechanism.
```

Examples:

```text
New native module
New permission
Native manifest change
Info.plist change
Entitlement
Native dependency update
Native configuration
```

Create and release a new native binary.

---

# 56. Common failure: production EAS build is not reproducible

Check:

```text
Git SHA
lockfile
EAS CLI version
Expo/RN version
Build profile
Environment
Credentials
Native project state
Config plugins
```

If builds differ:

```text
Find the first input that changed.
```

Do not treat cloud builds as inherently deterministic.

Reproducibility still depends on controlling inputs.

---

# 57. Common failure: EAS build is slow

Measure first.

Potential sources:

```text
Dependency installation
Native dependency compilation
Gradle
CocoaPods
Xcode
Cache misses
Large repository
Large assets
```

Do not optimize by adding more CI infrastructure without knowing the bottleneck.

Start with:

```text
Build timing
Cache hit/miss
Dependency changes
Native dependency count
Artifact size
```

---

# 58. EAS cost control

EAS is a hosted build/service platform.

Track:

```text
Build frequency
Platform
Build duration
CI duplication
Preview build volume
Failed builds
Unused builds
```

Avoid:

```text
Repeated builds for identical commits
Automatic builds on every trivial branch
Unnecessary platform builds
```

A practical pattern:

```text
PR
→ validation

Merge
→ build only when release artifact is needed

Release
→ production build
```

Adapt this to team size and development workflow.

---

# 59. Migration away from EAS

Do not make EAS the only source of knowledge.

Keep the repository's native release documentation accurate:

```text
frameworks/react-native/android-release.md
frameworks/react-native/ios-release.md
signing/
publishing/
release-engineering/
```

This gives the team a fallback if:

```text
EAS becomes unavailable
Cost changes
Security requirements change
Architecture changes
CI moves
```

A hosted tool should simplify operations, not make the application impossible to release without it.

---

# 60. Recommended EAS release workflow

```text
1. Confirm Git commit
        ↓
2. Confirm EAS project
        ↓
3. Confirm build profile
        ↓
4. Confirm environment
        ↓
5. Confirm version
        ↓
6. Run tests
        ↓
7. Build
        ↓
8. Verify artifact
        ↓
9. Install/test real build
        ↓
10. Test upgrade path
        ↓
11. Submit to store testing
        ↓
12. Human approval
        ↓
13. Production rollout
        ↓
14. Monitor
```

---

# 61. Production checklist

```text
[ ] EAS project is correct
[ ] Correct Expo account/organization
[ ] Correct Git commit
[ ] Correct build profile
[ ] Correct environment
[ ] Android application ID is correct
[ ] iOS bundle ID is correct
[ ] versionName/version is correct
[ ] Android versionCode is correct
[ ] iOS build number is correct
[ ] Signing is correct
[ ] Credentials are recoverable
[ ] Secrets are protected
[ ] Native dependencies are compatible
[ ] Release build succeeds
[ ] AAB/IPA verified
[ ] Fresh install tested
[ ] Upgrade tested
[ ] Critical flows tested
[ ] Push tested if applicable
[ ] Deep links tested if applicable
[ ] Payments tested if applicable
[ ] Crash reporting verified
[ ] OTA compatibility verified if using EAS Update
[ ] Store testing completed
[ ] Human approval completed
[ ] Rollout monitoring ready
```

---

# 62. Definition of done

An EAS release is ready when:

```text
Correct source
      +
Correct EAS project
      +
Correct profile
      +
Correct environment
      +
Correct credentials
      +
Valid native artifact
      +
Real-device testing
      +
Store validation
      +
Human approval
      +
Post-release monitoring
      ↓
RELEASE READY
```

The goal is not:

```text
EAS build succeeded.
```

The goal is:

```text
The exact source produced the intended artifact,
with the intended environment and signing,
and the artifact has been tested and approved.
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

### React Native

- `frameworks/react-native/README.md`
- `frameworks/react-native/android-release.md`
- `frameworks/react-native/ios-release.md`
- `frameworks/react-native/signing.md`
- `frameworks/react-native/fastlane.md`
- `frameworks/react-native/common-failures.md`

### Expo

- `frameworks/expo/README.md`
- `frameworks/expo/app-config.md`
- `frameworks/expo/build.md`
- `frameworks/expo/credentials.md`
- `frameworks/expo/development-builds.md`
- `frameworks/expo/eas.md`
- `frameworks/expo/submit.md`
- `frameworks/expo/updates.md`

### Testing

- `testing/device-testing.md`
- `testing/device-matrix.md`
- `testing/android.md`
- `testing/ios.md`
- `testing/upgrade-migrations.md`
- `testing/release-smoke-tests.md`

### Release engineering

- `release-engineering/build-systems.md`
- `release-engineering/eas/`
- `release-engineering/github-actions/`
- `release-engineering/environment-management.md`
- `release-engineering/release-pipelines.md`

### AI

- `ai/agents/release-auditor.md`
- `ai/agents/debugging-agent.md`
- `ai/workflows/release-audit.md`
- `ai/workflows/debugging.md`
- `ai/orchestration/human-approval.md`
- `ai/security/secret-protection.md`
- `ai/security/agent-permissions.md`

---

# Official sources

Use official Expo and platform documentation as the authority for current EAS behavior.

## Expo

- Expo documentation: https://docs.expo.dev/
- EAS Build: https://docs.expo.dev/build/introduction/
- EAS Build configuration: https://docs.expo.dev/build/eas-json/
- EAS environment variables: https://docs.expo.dev/eas/environment-variables/
- EAS Submit: https://docs.expo.dev/submit/introduction/
- EAS Update: https://docs.expo.dev/eas-update/introduction/
- EAS Update runtime versions: https://docs.expo.dev/eas-update/runtime-versions/
- EAS credentials: https://docs.expo.dev/app-signing/managed-credentials/
- EAS CLI: https://docs.expo.dev/eas/

## React Native

- React Native documentation: https://reactnative.dev/docs/getting-started
- React Native Android publishing: https://reactnative.dev/docs/signed-apk-android.html
- React Native upgrading: https://reactnative.dev/docs/upgrading

## Android

- Android Developers: https://developer.android.com/
- Android app signing: https://developer.android.com/studio/publish/app-signing
- Android App Bundles: https://developer.android.com/guide/app-bundle

## Apple

- Apple Developer: https://developer.apple.com/
- App Store Connect Help: https://developer.apple.com/help/app-store-connect/

---

**Last verified:** August 11, 2026

EAS, Expo, React Native, Xcode, Gradle, Android SDK, Apple signing, Google Play, and App Store policies change over time. Verify the current official documentation for the exact versions and release configuration used by the project before production deployment.
