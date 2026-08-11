# Expo

Framework-specific release guidance for **Expo and React Native applications**.

This directory explains how to take an Expo project from development to a tested, signed, and publishable mobile release.

The playbook keeps the framework guidance here focused on Expo-specific implementation details. Shared mobile release concepts such as versioning, testing, signing, store operations, privacy, and production monitoring remain in the repository's common documentation.

---

## What this directory covers

The Expo guides cover the parts of release engineering that are specific to the Expo toolchain:

```text
Expo project
    ↓
App configuration
    ↓
Development build
    ↓
EAS
    ↓
Credentials
    ↓
Build
    ↓
CI/CD
    ↓
Store submission
    ↓
Post-release operation
```

It also covers:

- Expo configuration
- EAS Build
- EAS Submit
- EAS Update
- development builds
- credentials
- CI/CD
- common Expo failures
- native dependencies
- config plugins
- environment handling
- AI-assisted release workflows

---

## Start here

If you are new to Expo releases, follow this order:

```text
1. app-config.md
       ↓
2. development-builds.md
       ↓
3. eas.md
       ↓
4. credentials.md
       ↓
5. build.md
       ↓
6. ci-cd.md
       ↓
7. submit.md
       ↓
8. common-failures.md
```

For a first production release, also use the shared checklists:

```text
checklists/first-release.md
checklists/pre-release.md
checklists/production.md
```

---

## Guides

### `app-config.md`

Explains Expo application configuration and how configuration becomes native application state.

Use it for:

- `app.json`
- `app.config.js`
- `app.config.ts`
- application identifiers
- native configuration
- environment-aware configuration
- config plugins
- resolved configuration
- prebuild behavior

Core principle:

> **The source configuration is not the release. The resolved configuration and resulting artifact are the release.**

---

### `development-builds.md`

Explains how to develop against the application's actual native runtime instead of relying entirely on Expo Go.

Use it for:

- `expo-dev-client`
- local development builds
- EAS development builds
- simulators
- physical devices
- Fast Refresh
- native dependency changes
- development environments
- sharing development builds

The normal progression for a serious Expo application is:

```text
Development Build
    ↓
Preview Build
    ↓
Production Build
```

Expo Go remains useful for learning and quick prototypes, but it should not be treated as the production native runtime for applications that depend on custom native functionality.

---

### `eas.md`

Explains Expo Application Services and how EAS fits into the release process.

Use it for:

- EAS Build
- EAS Submit
- EAS Update
- EAS Workflows
- EAS credentials
- EAS environment variables
- build profiles
- submission profiles
- build inspection
- artifact handling
- release automation

The key distinction is:

```text
EAS
→ executes release operations

Apple / Google
→ control store distribution

Your application
→ must still be correct
```

EAS simplifies release execution. It does not remove the need to understand mobile release engineering.

---

### `credentials.md`

Explains the credentials required to build and submit Expo applications.

Use it for:

- Android keystores
- Android upload keys
- Google Play service accounts
- iOS distribution certificates
- provisioning profiles
- Apple Push Notification credentials
- App Store Connect API keys
- EAS-managed credentials
- local credentials
- credential recovery
- credential rotation
- CI secrets

Important distinction:

```text
Signing credentials
≠
Store submission credentials
≠
Runtime integration credentials
```

Never commit production credentials to the repository.

---

### `build.md`

Explains the Expo/EAS build process and how to move from source code to a verified Android or iOS artifact.

Use it for:

- build profiles
- Android builds
- iOS builds
- development builds
- preview builds
- production builds
- native dependencies
- build configuration
- build logs
- artifact verification
- build failures
- build inspection

The basic model is:

```text
Source
    ↓
Configuration
    ↓
Build
    ↓
Artifact
    ↓
Verify
```

A successful build is not automatically a successful release.

---

### `ci-cd.md`

Explains how to automate Expo release workflows without turning the project into an unnecessarily complex CI/CD system.

Use it for:

- GitHub Actions
- EAS Build from CI
- EAS Workflows
- environment separation
- protected production credentials
- release triggers
- artifact traceability
- human approval
- production submission

A practical pipeline is:

```text
Pull Request
    ↓
Validation
    ↓
Merge
    ↓
Build
    ↓
Artifact verification
    ↓
Human approval
    ↓
Store submission
    ↓
Monitoring
```

Start with a manual release process. Automate only the parts that are stable and repeatedly worth automating.

---

### `submit.md`

Explains the Expo-specific path from a verified build artifact to App Store Connect and Google Play.

Use it for:

- EAS Submit
- App Store Connect
- Google Play
- submission credentials
- production submissions
- internal testing submissions
- exact artifact selection
- submission verification
- store processing

The release boundary should remain explicit:

```text
Build
→ Verify
→ Approve
→ Submit
```

Do not blindly submit the latest available artifact when a specific approved build can be identified.

---

### `common-failures.md`

Practical troubleshooting for Expo release problems.

Use it when:

- EAS Build fails
- JavaScript bundling fails
- Gradle fails
- Xcode fails
- signing fails
- credentials fail
- environment variables are missing
- config plugins fail
- local builds differ from EAS
- development builds cannot connect
- the app crashes after building
- push notifications fail
- deep links fail
- production behavior differs from development

The troubleshooting rule is:

```text
Failure
    ↓
Read logs
    ↓
Find first meaningful error
    ↓
Classify
    ↓
Make the smallest fix
    ↓
Verify
    ↓
Rebuild
```

Do not start by deleting dependencies, regenerating everything, or upgrading the entire stack.

---

# Expo release model

The repository uses a simple release progression:

```text
                  ┌──────────────────┐
                  │  Expo / RN Code  │
                  └────────┬─────────┘
                           ↓
                  ┌──────────────────┐
                  │  App Config      │
                  └────────┬─────────┘
                           ↓
                  ┌──────────────────┐
                  │ Development      │
                  │ Build            │
                  └────────┬─────────┘
                           ↓
                  ┌──────────────────┐
                  │ Preview Build    │
                  └────────┬─────────┘
                           ↓
                  ┌──────────────────┐
                  │ Device / QA      │
                  │ Verification     │
                  └────────┬─────────┘
                           ↓
                  ┌──────────────────┐
                  │ Production Build │
                  └────────┬─────────┘
                           ↓
                  ┌──────────────────┐
                  │ Human Approval   │
                  └────────┬─────────┘
                           ↓
                  ┌──────────────────┐
                  │ Store Submission │
                  └────────┬─────────┘
                           ↓
                  ┌──────────────────┐
                  │ Monitoring       │
                  └──────────────────┘
```

For JavaScript-only changes that are compatible with the installed native runtime:

```text
Code change
    ↓
Test
    ↓
EAS Update
    ↓
Compatible binaries
    ↓
Monitor
```

Native changes still require a new native build.

---

# Expo, React Native, and native code

Expo does not mean that native development disappears.

A production Expo application can contain:

```text
JavaScript / TypeScript
        +
React Native
        +
Expo modules
        +
Third-party native modules
        +
Config plugins
        +
iOS native configuration
        +
Android native configuration
```

The more native functionality an application uses, the more important development builds, native configuration, signing, and real-device testing become.

---

# Native dependency rule

When adding a dependency, first determine whether it contains native code.

If it does:

```text
Install dependency
    ↓
Check Expo compatibility
    ↓
Check config plugin requirements
    ↓
Rebuild development client
    ↓
Test
```

Do not assume that:

```bash
npm install
```

or:

```bash
npx expo install
```

is enough for an already-installed development binary.

The native code must exist in the binary.

---

# Configuration rule

Expo configuration can affect:

```text
iOS
Android
Metro
EAS
native projects
runtime behavior
store identity
```

Configuration changes should therefore be treated as release inputs.

Before a production build, verify:

```text
Application ID
Bundle ID
Version
Build number
Environment
Permissions
Capabilities
Native configuration
EAS profile
```

---

# Environment model

Keep release environments explicit:

```text
Development
    ↓
Preview
    ↓
Production
```

A practical mapping is:

```text
Development Build
→ development services

Preview Build
→ staging / test services

Production Build
→ production services
```

The exact environment model can vary by project.

What matters is that a developer can answer:

> **Which backend, credentials, configuration, and services does this build use?**

Never rely on profile names alone.

---

# EAS and credentials

EAS can manage signing credentials remotely or use local credentials.

For most projects, prefer the simplest secure model:

```text
EAS-managed credentials
+
protected environment variables
+
protected CI secrets
```

Use local credentials when there is a specific reason.

Keep these concepts separate:

```text
Android keystore
Android upload key
Google Play service account

iOS distribution certificate
iOS provisioning profile
Apple Push key
App Store Connect API key
```

They have different purposes and different recovery paths.

---

# Build profiles

A common `eas.json` structure is:

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
    "production": {}
  }
}
```

The names are conventions.

The important thing is that each profile has a clear purpose.

Avoid creating many profiles such as:

```text
dev
dev2
dev-new
qa
qa2
staging
staging-new
release-test
release-test-2
```

unless the release workflow genuinely needs them.

---

# EAS Update boundary

EAS Update is useful for compatible JavaScript and asset changes.

Use a new native build when a change requires:

```text
Native dependency
Native code
Config plugin change
Native permission
iOS capability
Android manifest change
Native SDK
Native application configuration
```

The rule is:

```text
Native runtime changes
→ Build

Compatible JS/assets changes
→ Update may be appropriate
```

Never use OTA updates to bypass native compatibility.

---

# CI/CD boundary

Keep responsibilities separated:

```text
GitHub
→ source control
→ validation
→ workflow triggers

EAS
→ mobile builds
→ mobile release operations

Apple / Google
→ store distribution

Monitoring
→ production health
```

Do not duplicate the same release logic in:

```text
GitHub Actions
+
EAS Workflows
+
custom scripts
```

unless there is a clear reason.

---

# AI-assisted Expo development

AI is part of the playbook, but it is not a trusted release authority.

AI can help with:

```text
Repository inspection
Configuration audits
Dependency analysis
Build-log analysis
Release checklists
Metadata drafting
Release notes
CI workflow preparation
Troubleshooting
Documentation
```

A safe workflow is:

```text
AI analyzes
    ↓
AI proposes
    ↓
Developer reviews
    ↓
Tests
    ↓
Build
    ↓
Human verifies
    ↓
Human approves
    ↓
Release
```

Do not give an AI agent unrestricted access to:

```text
Production credentials
Signing keys
Store account administration
Production submission
Credential rotation
Destructive EAS operations
```

The AI documentation in this repository provides the broader security, approval, and evaluation rules.

See:

```text
ai/
```

---

# AI is useful for release work

Good AI-assisted tasks include:

### Configuration audit

```text
Inspect app.config.*
Inspect eas.json
Inspect package.json
Identify inconsistencies
```

### Build debugging

```text
Read EAS logs
Find first meaningful error
Classify failure
Suggest minimal fix
```

### Release audit

```text
Check version
Check identifiers
Check environment
Check credentials references
Check build profile
Check release blockers
```

### Release preparation

```text
Prepare checklist
Prepare release notes
Prepare workflow
Prepare store metadata draft
```

AI output must still be verified.

---

# What this directory does not own

Do not put general mobile guidance here when it already belongs in shared documentation.

For example:

```text
General versioning
→ foundations/versioning.md

General signing
→ signing/

General testing
→ testing/

General store metadata
→ store-operations/

General privacy
→ privacy-compliance/

General production monitoring
→ post-release/

General AI security
→ ai/security/
```

The Expo directory should explain:

> **How Expo implements or changes the shared release concept.**

---

# Framework support model

The playbook is currently first-class around:

```text
Expo
React Native
```

but the repository is intentionally open to other frameworks.

Other frameworks belong under:

```text
frameworks/community/
```

For example:

```text
frameworks/
├── expo/
├── react-native/
└── community/
    ├── flutter/
    ├── native-ios/
    └── ...
```

Community contributions should explain framework-specific release behavior without duplicating the entire core playbook.

See:

```text
frameworks/community/README.md
frameworks/community/adding-a-framework.md
```

---

# Documentation rules

Expo documentation should follow the repository's documentation standards.

## Prefer practical guidance

Good:

```text
Run:
npx expo install expo-dev-client

Then build the development client.

Verify that the app opens and connects to Metro.
```

Avoid:

```text
Expo is an innovative and powerful ecosystem
that enables developers to build world-class apps.
```

The documentation should help someone ship an application, not market the framework.

---

## Verify volatile information

Treat these as changeable:

```text
Expo SDK behavior
EAS CLI commands
EAS build configuration
EAS Workflows
Apple requirements
Google Play requirements
signing behavior
store submission rules
environment handling
AI tooling
```

Prefer official documentation as the source of truth.

If a command or requirement can change, link to the current official documentation.

---

## Never invent commands

Before documenting:

```bash
eas ...
npx expo ...
xcodebuild ...
gradle ...
```

verify that:

```text
the command exists
the syntax is correct
the command applies to the stated workflow
the behavior matches the current tooling
```

If a command depends on a specific version, document that dependency.

---

## Keep secrets out

Never include:

```text
API keys
passwords
private keys
keystores
service-account JSON
Apple private keys
tokens
production credentials
```

Examples must use placeholders:

```text
<EXPO_TOKEN>
<BUILD_ID>
<APP_IDENTIFIER>
```

Never ask contributors to paste real credentials into an issue, pull request, AI tool, or documentation file.

---

# Contribution model

Expo documentation is intended to be maintained by developers who actually use the tooling.

If you find:

```text
incorrect command
outdated behavior
missing failure case
missing platform detail
better workflow
```

open a contribution or issue with:

```text
What changed
+
Evidence
+
Affected Expo/EAS version
+
Affected platform
+
Recommended documentation change
```

Do not update volatile documentation based only on an old blog post or memory.

Prefer:

```text
Official Expo documentation
Official Apple documentation
Official Android documentation
Official GitHub documentation
```

Then add practical experience around those sources.

---

# Suggested learning path

If you are new to Expo release engineering:

```text
Expo basics
    ↓
App configuration
    ↓
Development builds
    ↓
EAS Build
    ↓
Signing
    ↓
Preview builds
    ↓
Testing
    ↓
EAS Submit
    ↓
CI/CD
    ↓
EAS Update
    ↓
Production monitoring
```

Do not start with:

```text
Kubernetes
complex CI infrastructure
multiple release agents
custom build platforms
```

The simplest working release pipeline is the right starting point.

---

# Quick reference

### Start Expo development

```bash
npx expo start
```

### Install development client

```bash
npx expo install expo-dev-client
```

### Local Android development build

```bash
npx expo run:android
```

### Local iOS development build

```bash
npx expo run:ios
```

### Configure EAS

```bash
eas build:configure
```

### Development EAS build

```bash
eas build --profile development --platform <platform>
```

### Preview EAS build

```bash
eas build --profile preview --platform <platform>
```

### Production EAS build

```bash
eas build --profile production --platform <platform>
```

### Inspect credentials

```bash
eas credentials
```

### Submit

```bash
eas submit --platform <platform>
```

### Publish compatible update

```bash
eas update
```

These commands should always be checked against the current Expo/EAS CLI documentation before being used in production automation.

---

# Release checklist

Before calling an Expo application release-ready:

```text
[ ] Expo configuration verified
[ ] Application identifiers verified
[ ] Version verified
[ ] Build number verified
[ ] Environment verified
[ ] Dependencies verified
[ ] Native changes understood
[ ] Development build tested
[ ] Preview build tested
[ ] Signing verified
[ ] Production build created
[ ] Production artifact inspected
[ ] Real-device smoke test passed
[ ] Store metadata ready
[ ] Submission credentials verified
[ ] Exact production artifact identified
[ ] Human approval completed
[ ] Store submission completed
[ ] Post-release monitoring enabled
```

For the full release process, use:

```text
checklists/first-release.md
checklists/pre-release.md
checklists/production.md
checklists/post-release.md
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

### Expo

- `frameworks/expo/app-config.md`
- `frameworks/expo/development-builds.md`
- `frameworks/expo/eas.md`
- `frameworks/expo/credentials.md`
- `frameworks/expo/build.md`
- `frameworks/expo/ci-cd.md`
- `frameworks/expo/submit.md`
- `frameworks/expo/common-failures.md`

### React Native

- `frameworks/react-native/`

### Signing

- `signing/ios/`
- `signing/android/`
- `signing/security/`

### Testing

- `testing/device-testing.md`
- `testing/device-matrix.md`
- `testing/ios.md`
- `testing/android.md`
- `testing/release-smoke-tests.md`
- `testing/upgrade-migrations.md`
- `testing/deep-links.md`
- `testing/push-notifications.md`
- `testing/payments.md`

### Publishing

- `publishing/ios/`
- `publishing/android/`
- `publishing/cross-platform/`

### Release engineering

- `release-engineering/eas/`
- `release-engineering/github-actions/`
- `release-engineering/environment-management.md`
- `release-engineering/release-pipelines.md`

### AI

- `ai/README.md`
- `ai/tools/`
- `ai/agents/`
- `ai/workflows/`
- `ai/orchestration/`
- `ai/security/`
- `ai/evals/`

### Community frameworks

- `frameworks/community/README.md`
- `frameworks/community/adding-a-framework.md`

---

# Official sources

Use official documentation as the authority for current Expo and EAS behavior.

### Expo

- Expo documentation: https://docs.expo.dev/
- Expo development builds: https://docs.expo.dev/develop/development-builds/introduction/
- Expo app configuration: https://docs.expo.dev/versions/latest/config/app/
- Expo configuration: https://docs.expo.dev/workflow/configuration/
- Expo Prebuild: https://docs.expo.dev/workflow/prebuild/
- Config plugins: https://docs.expo.dev/config-plugins/introduction/
- EAS CLI: https://docs.expo.dev/eas/cli/
- EAS Build: https://docs.expo.dev/build/introduction/
- EAS Build configuration: https://docs.expo.dev/build/eas-json/
- EAS Submit: https://docs.expo.dev/submit/introduction/
- EAS Update: https://docs.expo.dev/eas-update/introduction/
- EAS environment variables: https://docs.expo.dev/eas/environment-variables/
- EAS Workflows: https://docs.expo.dev/eas/workflows/introduction/
- EAS credentials: https://docs.expo.dev/app-signing/managed-credentials/

### React Native

- React Native documentation: https://reactnative.dev/docs/getting-started

### Apple

- Apple Developer: https://developer.apple.com/
- App Store Connect: https://developer.apple.com/help/app-store-connect/

### Android

- Android Developers: https://developer.android.com/
- Google Play Console Help: https://support.google.com/googleplay/android-developer/

**Last verified:** August 11, 2026

Expo SDK behavior, EAS CLI commands, build infrastructure, native tooling, Apple and Google platform requirements, and store policies change over time. Re-check the official documentation for the project's Expo SDK and tooling versions before changing a production release workflow.
