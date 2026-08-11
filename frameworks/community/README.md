
# Community Frameworks

This directory is the community extension point for frameworks that are not currently maintained as first-class implementations.

The playbook is intentionally **first-class for Expo and React Native**, while remaining open to developers who want to add release guidance for other mobile frameworks. The existing project guidance defines community frameworks as an extension layer rather than a separate copy of the playbook. fileciteturn16file12L1-L35

The goal is simple:

> **If you know a mobile framework well, you should be able to contribute accurate, practical release knowledge without waiting for the core project to officially support it.**

---

## What belongs here

Community framework documentation should explain **how a particular framework handles mobile release engineering**.

Examples:

- Flutter
- Native iOS
- Native Android
- .NET MAUI
- Kotlin Multiplatform
- other mobile frameworks

A framework contribution may cover:

```text
Project configuration
        ↓
Environment setup
        ↓
Versioning
        ↓
Build
        ↓
Signing
        ↓
Testing
        ↓
Store submission
        ↓
Framework-specific failures
```

The shared playbook remains the source of truth for concepts that apply to mobile applications generally.

The framework documentation explains the implementation differences. fileciteturn16file1L1-L40

---

# First-class vs community support

## First-class frameworks

The initial maintained framework implementations are:

```text
frameworks/
├── expo/
└── react-native/
```

These receive direct project-level documentation and maintenance.

See:

- [`../expo/`](../expo/)
- [`../react-native/`](../react-native/)

## Community frameworks

Community implementations live here:

```text
frameworks/community/
```

A community framework is not automatically a first-class framework.

It may start as:

```text
Community
    ↓
Verified
    ↓
Maintained
    ↓
First-class
```

Moving a framework into first-class support should be a deliberate maintenance decision, not an automatic result of adding a few files.

---

# How to add a framework

Start with:

```text
frameworks/community/
└── <framework>/
```

For example:

```text
frameworks/community/
└── flutter/
```

A useful initial contribution can be:

```text
frameworks/community/
└── flutter/
    ├── README.md
    ├── build.md
    ├── ios-release.md
    ├── android-release.md
    ├── signing.md
    └── common-failures.md
```

Do not create every file simply because the structure exists.

Start with the release problems you can actually document and verify. The repository's framework contribution guide explicitly recommends starting small and expanding only when verified material exists. fileciteturn16file1L70-L120

See [`adding-a-framework.md`](./adding-a-framework.md) for the complete contribution process.

---

# Do not duplicate the core playbook

This is the most important rule for this directory.

Do not copy:

```text
foundations/
signing/
testing/
publishing/
privacy-compliance/
post-release/
```

into every framework directory.

Instead:

```text
Shared concept
      ↓
Core documentation
      ↓
Framework-specific difference
      ↓
Community framework guide
```

For example:

```markdown
See [Versioning](../../foundations/versioning.md)
for the shared versioning model.

This framework-specific guide explains how the framework
configures the iOS and Android build identifiers.
```

The core repository should remain the source of truth for shared release concepts. fileciteturn16file1L40-L85

---

# What a good framework contribution looks like

A good contribution answers:

> **"I already know this framework. What do I need to know to release an application built with it?"**

It should be:

- practical
- verified
- framework-specific
- easy to follow
- linked to official sources
- clear about version scope
- honest about uncertainty
- safe with credentials
- small enough to maintain

A developer should be able to move from:

```text
Framework project
        ↓
Release configuration
        ↓
Build
        ↓
Verify
        ↓
Test
        ↓
Submit
```

without needing to guess what the framework-specific step means.

---

# What does not belong here

Do not use this directory for:

- generic mobile release theory
- App Store policy explanations
- Google Play policy explanations
- general signing concepts
- generic testing guidance
- generic privacy guidance
- generic CI/CD concepts
- generic AI guidance
- complete framework tutorials
- vendor marketing
- unverified commands

If the information applies equally to Expo, React Native, Flutter, and native apps, it probably belongs in the core playbook.

If it is specifically about how one framework implements the release process, it belongs here.

---

# Recommended framework structure

A framework can start with:

```text
<framework>/
├── README.md
├── build.md
├── ios-release.md
├── android-release.md
├── signing.md
└── common-failures.md
```

Not every framework needs every file.

For example:

```text
<framework>/
├── README.md
├── build.md
└── common-failures.md
```

is better than six mostly empty files.

---

# README.md

Each framework should have its own README.

It should state:

- what framework it covers
- current framework/version scope
- supported platforms
- current coverage
- known gaps
- framework-specific tooling
- links to shared documentation
- official sources
- verification status

Example:

```markdown
# Flutter

Community release guidance for Flutter applications.

## Coverage

- [x] Android build
- [x] iOS build
- [x] Signing
- [ ] CI/CD
- [ ] Common failures

## Shared guidance

See:

- `foundations/`
- `signing/`
- `testing/`
- `publishing/`

## Official sources

- Flutter documentation
- Apple documentation
- Android documentation
```

Do not claim support that has not been verified.

---

# Build documentation

A framework build guide should explain:

```text
Prerequisites
      ↓
Configuration
      ↓
Release build
      ↓
Artifact
      ↓
Verification
```

At minimum include:

- required tooling
- supported framework version
- configuration
- release build command
- expected artifact
- verification steps
- common build failures

Commands must be verified before they are published.

The project's documentation rules explicitly require commands and code to be checked rather than guessed. fileciteturn16file16L1-L20

---

# iOS documentation

If the framework has iOS-specific release behavior, document it in:

```text
ios-release.md
```

Focus on differences such as:

- native project generation
- Xcode integration
- deployment target
- native dependencies
- framework-specific build settings
- archive generation
- signing integration
- TestFlight workflow
- framework-specific failures

Do not duplicate general App Store Connect guidance.

Use the shared publishing and signing documentation for the general process.

---

# Android documentation

If the framework has Android-specific release behavior, document it in:

```text
android-release.md
```

Useful areas include:

- release build configuration
- Gradle integration
- Android SDK requirements
- native plugins
- manifest configuration
- signing integration
- AAB generation
- Play testing
- framework-specific failures

Again, keep general Google Play guidance in the shared publishing sections.

---

# Signing documentation

Only document framework-specific signing behavior.

For example:

```text
Framework
    ↓
Generates native project
    ↓
Native signing configuration
```

or:

```text
Framework
    ↓
Managed build system
    ↓
Credentials configured through build tooling
```

Never include:

- real certificates
- private keys
- passwords
- API tokens
- production credentials

Use the shared signing documentation for general security and credential-management principles.

---

# Common failures

A framework contribution becomes much more useful when it documents failures developers actually encounter.

Use:

```text
Symptom
→ Likely causes
→ Checks
→ Fix
→ Verification
```

Example:

```markdown
## Release build fails after adding a native package

### Symptom

Development works, but the production build fails.

### Likely causes

- native dependency configuration
- incompatible plugin
- release-only native configuration
- framework/platform version mismatch

### Checks

...

### Fix

...

### Verify

Build the same release configuration again and test on a real device.
```

Generic failures should instead go into:

```text
troubleshooting/
```

---

# Framework version scope

Framework tooling changes.

A community guide should clearly identify the version it was written or verified against when that matters.

Prefer:

```text
Verified with:
<framework> X.Y
```

over vague statements such as:

```text
This always works.
```

For version-sensitive instructions:

```text
Framework version
        ↓
Tooling version
        ↓
Platform version
        ↓
Expected behavior
```

If the version cannot be verified:

```text
NEEDS VERIFICATION
```

Do not hide uncertainty.

The repository documentation rules require volatile information and version-specific behavior to be treated carefully and verified against current authoritative sources. fileciteturn16file17L1-L30

---

# Official sources

Framework contributions should prefer primary sources:

```text
Framework official documentation
        +
Apple documentation
        +
Android documentation
        +
Official build/release tooling
```

Examples:

- [Apple Developer](https://developer.apple.com/)
- [App Store Connect](https://developer.apple.com/help/app-store-connect/)
- [Android Developers](https://developer.android.com/)
- [Google Play Console Help](https://support.google.com/googleplay/android-developer/)
- [Expo Documentation](https://docs.expo.dev/)
- [React Native Documentation](https://reactnative.dev/docs/getting-started)
- [Flutter Documentation](https://docs.flutter.dev/)
- [.NET MAUI Documentation](https://learn.microsoft.com/dotnet/maui/)
- [Kotlin Multiplatform Documentation](https://www.jetbrains.com/help/kotlin-multiplatform-dev/)

Use the most specific official page available for a particular claim.

Do not rely on an old blog post when the framework or platform documents the current behavior.

---

# Source verification

Before publishing a framework instruction:

```text
Is it framework-specific?
        ↓
Is the source authoritative?
        ↓
Is the information current enough?
        ↓
Can the command/configuration be verified?
        ↓
Can a developer reproduce it?
```

If any answer is no, either verify it or clearly mark it as needing verification.

The repository's contribution guidance requires official-source verification, command verification, version awareness, and explicit handling of unknowns. fileciteturn16file1L170-L240

---

# AI-assisted community contributions

AI can make framework contributions faster, but it does not make them automatically correct.

Useful AI tasks include:

- repository inspection
- documentation comparison
- release workflow drafting
- configuration auditing
- troubleshooting analysis
- command discovery
- checklist generation
- release-note drafting
- finding duplicated guidance

A safe contribution workflow is:

```text
Official framework documentation
        ↓
AI research / draft
        ↓
Contributor verification
        ↓
Real build / test
        ↓
Official sources checked
        ↓
Pull request
        ↓
Human review
```

Do not publish AI-generated release instructions without verification.

The existing project guidance explicitly treats AI output as untrusted until it is inspected, verified, tested, and approved. fileciteturn16file16L17-L22

---

# AI coding agents

AI coding agents may help create or update framework documentation.

A safe workflow is:

```text
Read repository
        ↓
Find existing guidance
        ↓
Identify framework-specific gaps
        ↓
Draft documentation
        ↓
Check links
        ↓
Run validation
        ↓
Show Git diff
        ↓
Human review
```

Agents should not receive unrestricted access to:

- production credentials
- signing keys
- store credentials
- payment systems
- production infrastructure

The resulting changes should remain reviewable as a normal Git diff.

The project documentation rules require explicit human approval for production releases, signing credentials, secrets, store submission, destructive actions, account changes, and other high-impact operations. fileciteturn16file16L10-L18

---

# Community contribution checklist

Before opening a pull request:

```text
[ ] I checked the existing core documentation.
[ ] The contribution is actually framework-specific.
[ ] I used frameworks/community/<framework>/.
[ ] I did not copy the core playbook.
[ ] I documented only release-relevant behavior.
[ ] Commands were verified.
[ ] Framework version requirements were checked.
[ ] Official sources were used.
[ ] Important assumptions are documented.
[ ] Common failures are included where useful.
[ ] No secrets or credentials are included.
[ ] No unnecessary dependencies or infrastructure were added.
[ ] Links were checked.
[ ] Markdown was checked.
[ ] Unknown or unverified information is clearly marked.
[ ] CHANGELOG.md was updated if the contribution is meaningful.
```

This keeps contributions small, reviewable, and maintainable. fileciteturn16file3L1-L35

---

# Maintainer review

Maintainers should check:

## Scope

- Is this framework-specific?
- Does it belong here?
- Is existing guidance being duplicated?

## Accuracy

- Do commands work?
- Are configuration examples valid?
- Are platform claims supported?

## Sources

- Are official sources used?
- Are volatile requirements linked?
- Are version-specific claims clear?

## Practicality

- Can a developer execute the workflow?
- Is success clearly defined?
- Are failures covered?

## Security

- Are credentials excluded?
- Are secrets handled safely?
- Are AI workflows bounded?

## Maintenance

- Is the framework/version scope clear?
- Can another contributor verify the instructions later?
- Are links specific enough to remain useful?

The project's framework contribution guidance uses these same review concerns for community additions. fileciteturn16file13L1-L45

---

# Framework maturity

A community framework can mature over time.

## Community

Someone has contributed useful documentation.

## Verified

The documented workflow has been tested against a real project and/or current official documentation.

## Maintained

The framework documentation is actively kept current.

## First-class

The project intentionally maintains the framework as a primary implementation.

First-class status is a maintenance commitment.

Do not add that commitment simply because a framework has a README.

The existing contribution guidance recommends reliable coverage, verified workflows, active maintenance, community demand, and clear ownership before considering first-class status. fileciteturn16file13L45-L70

---

# When not to add a framework

Do not add a community framework contribution if it is:

- unverified
- outdated
- copied from another repository
- mostly generic mobile advice
- framework marketing
- a general framework tutorial
- based on guessed commands
- impossible to reproduce
- dependent on unsafe credentials
- unnecessarily large

It is better to have no guide than a guide that causes someone to ship incorrectly.

---

# Avoid vendor lock-in

Document official framework tooling when it is the practical choice.

But do not turn the repository into vendor promotion.

If multiple valid approaches exist, explain:

```text
Recommended approach
Alternative approach
Trade-offs
```

Only call one approach mandatory when the framework or platform actually requires it.

---

# Updating community documentation

When framework tooling changes:

```text
Identify changed behavior
        ↓
Check official source
        ↓
Identify affected versions
        ↓
Update guide
        ↓
Verify commands
        ↓
Update source references
        ↓
Update CHANGELOG.md if meaningful
```

Do not silently leave known-broken instructions in the repository.

If an old workflow is still useful historically, label it clearly as historical or deprecated.

---

# Ownership

Community framework documentation should make its maintenance status clear.

A framework README can include:

```markdown
## Status

Community maintained.

If this guide is outdated, please open an issue or pull request.
```

Do not assign permanent maintainers without agreement.

The goal is visibility and continuity, not bureaucracy.

---

# Framework contribution flow

A healthy community contribution looks like:

```text
Developer knows framework
        ↓
Finds a missing release workflow
        ↓
Checks existing playbook
        ↓
Adds only framework-specific guidance
        ↓
Verifies against official sources
        ↓
Tests the workflow
        ↓
Opens pull request
        ↓
Maintainer reviews
        ↓
Community framework improves
```

This is the intended extension model.

---

# Repository relationship

The overall structure is:

```text
mobile-release-playbook/
│
├── foundations/
│   └── shared release concepts
│
├── signing/
│   └── shared signing guidance
│
├── testing/
│   └── shared testing guidance
│
├── publishing/
│   └── shared store publishing
│
├── release-engineering/
│   └── shared automation
│
├── troubleshooting/
│   └── shared failure diagnosis
│
├── frameworks/
│   ├── expo/
│   │   └── first-class implementation
│   │
│   ├── react-native/
│   │   └── first-class implementation
│   │
│   └── community/
│       ├── README.md
│       ├── adding-a-framework.md
│       └── <community-framework>/
│
└── ai/
    └── AI-assisted release workflows
```

This separation keeps shared concepts centralized while allowing the framework layer to grow through community contributions. fileciteturn16file12L1-L35

---

# Final standard

A community framework contribution should pass this test:

> **Can a developer who already knows the framework use the documentation to safely build, verify, and release a real mobile application?**

If yes, it belongs here.

If it only explains what the framework is, it probably does not.

The standard is:

```text
Framework knowledge
        +
Release-specific guidance
        +
Official sources
        +
Real verification
        +
Clear boundaries
        ↓
Useful community contribution
```

Keep the framework layer open, practical, and easy to extend.

---

# Related documentation

### Contribution

- [`adding-a-framework.md`](./adding-a-framework.md)
- [`../../CONTRIBUTING.md`](../../CONTRIBUTING.md)

### Foundations

- [`../../foundations/mobile-release-lifecycle.md`](../../foundations/mobile-release-lifecycle.md)
- [`../../foundations/project-configuration.md`](../../foundations/project-configuration.md)
- [`../../foundations/release-environments.md`](../../foundations/release-environments.md)
- [`../../foundations/versioning.md`](../../foundations/versioning.md)
- [`../../foundations/device-support.md`](../../foundations/device-support.md)
- [`../../foundations/dependency-management.md`](../../foundations/dependency-management.md)

### Signing

- [`../../signing/`](../../signing/)

### Testing

- [`../../testing/`](../../testing/)

### Publishing

- [`../../publishing/`](../../publishing/)

### Release engineering

- [`../../release-engineering/`](../../release-engineering/)

### Troubleshooting

- [`../../troubleshooting/`](../../troubleshooting/)

### AI

- [`../../ai/`](../../ai/)

### Governance

- [`../../governance/`](../../governance/)

---

# Official sources

Use the official documentation for the framework being added and the platforms it targets.

### Apple

https://developer.apple.com/

https://developer.apple.com/help/app-store-connect/

### Android

https://developer.android.com/

https://support.google.com/googleplay/android-developer/

### Expo

https://docs.expo.dev/

### React Native

https://reactnative.dev/docs/getting-started

### Flutter

https://docs.flutter.dev/

### .NET MAUI

https://learn.microsoft.com/dotnet/maui/

### Kotlin Multiplatform

https://www.jetbrains.com/help/kotlin-multiplatform-dev/

These are examples of possible community frameworks. Their presence here does not mean the repository currently maintains first-class support for them.

**Last verified:** August 11, 2026

Framework tooling and platform requirements change. Contributors should verify current official documentation before submitting or updating release instructions.
