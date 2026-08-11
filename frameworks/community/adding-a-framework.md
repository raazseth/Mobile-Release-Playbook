# Adding a Framework

This guide explains how to add a new mobile framework to the Mobile Release Playbook.

The project is **first-class for Expo and React Native**, but it is intentionally open to other frameworks and mobile stacks.

Community support can include:

- Flutter
- Native iOS
- Native Android
- .NET MAUI
- Kotlin Multiplatform
- other cross-platform or native mobile stacks

The goal is not to make every framework a first-class maintained implementation.

The goal is to make it easy for someone who knows a framework to contribute **useful, verified release knowledge** without duplicating the entire playbook.

---

# 1. What a framework contribution should do

A framework contribution should answer:

> **"I already have an app built with this framework. What is different about releasing it?"**

It should focus on framework-specific release behavior.

For example:

```text
Core playbook
    ↓
Versioning
    ↓
Signing
    ↓
Testing
    ↓
Publishing
```

The framework guide explains:

```text
How this framework implements those steps
```

Do not rewrite the general release lifecycle.

---

# 2. Framework support model

The repository uses three levels of support.

## First-class

Currently:

```text
frameworks/
├── expo/
└── react-native/
```

These are the project's primary maintained framework paths.

## Community

Community frameworks live under:

```text
frameworks/community/
```

A community contribution may eventually become first-class if the project develops enough maintained coverage.

## Unsupported / future

A framework does not need to be supported before someone contributes it.

The repository should remain open to new implementations.

---

# 3. Where your framework belongs

Community framework contributions should use:

```text
frameworks/community/
```

For a new framework:

```text
frameworks/community/
└── <framework>/
```

Example:

```text
frameworks/community/
└── flutter/
```

A practical contribution might eventually look like:

```text
frameworks/
└── community/
    ├── README.md
    ├── adding-a-framework.md
    └── flutter/
        ├── README.md
        ├── build.md
        ├── ios-release.md
        ├── android-release.md
        ├── signing.md
        └── common-failures.md
```

Do not create every possible file immediately.

Start with the release problems you can actually document well.

---

# 4. Before creating anything

First check the repository.

Look at:

```text
README.md
architecture.md
CONTRIBUTING.md
foundations/
signing/
testing/
pre-release/
publishing/
release-engineering/
troubleshooting/
frameworks/expo/
frameworks/react-native/
```

Then ask:

```text
Is this information already covered?
        ↓
Is the missing part actually framework-specific?
        ↓
If yes
        ↓
Add only the framework-specific guidance
```

If the information applies to every framework, it probably belongs in a core section instead.

---

# 5. Do not duplicate the core playbook

This is the most important rule.

Do not create:

```text
flutter/
├── versioning.md
├── environments.md
├── testing.md
├── publishing.md
├── privacy.md
├── store-accounts.md
└── ...
```

simply by copying:

```text
foundations/
store-accounts/
testing/
publishing/
privacy-compliance/
```

Instead:

```text
Core guide
    ↓
Framework-specific difference
    ↓
Framework guide
```

For example:

```markdown
See [Versioning](../../foundations/versioning.md)
for the general release versioning model.

For Flutter-specific version configuration,
see the framework instructions below.
```

The core repository should remain the source of truth for concepts shared by all frameworks.

---

# 6. What belongs in a framework guide?

A framework guide should cover differences such as:

- project configuration
- framework-specific build commands
- native project generation
- framework build tooling
- release configuration
- signing integration
- store submission integration
- framework-specific CI/CD
- framework-specific update mechanisms
- framework-specific release failures
- framework-specific verification steps

A useful question is:

> **Would a developer using another framework need to read this?**

If the answer is no, it may belong in the framework section.

---

# 7. What does not belong in a framework guide?

Do not move general guidance into the framework directory.

Examples:

```text
What App Store review means
What versioning means
Why signing matters
Why release testing matters
What a production release is
General privacy principles
General secret management
General incident response
```

These belong in shared sections when they are framework-independent.

The framework guide should explain implementation differences.

---

# 8. Recommended framework structure

Start small.

A good initial structure is:

```text
frameworks/community/<framework>/
├── README.md
├── build.md
├── ios-release.md
├── android-release.md
├── signing.md
└── common-failures.md
```

You may omit files that do not add value.

For example, if signing is fully covered by a shared workflow and the framework adds no meaningful difference, do not create a framework-specific `signing.md`.

---

# 9. Framework README

Every community framework should have a README.

It should quickly explain:

```text
What framework this covers
Who contributed it
What is currently covered
What is not covered
Which platforms are supported
Which tooling is used
Where the shared guidance lives
Which official sources were verified
```

Example:

```markdown
# Flutter

Community release guidance for Flutter applications.

This guide focuses on the framework-specific parts of:

- iOS release
- Android release
- build configuration
- signing
- store publishing
- CI/CD

For framework-independent release guidance, see:

- `foundations/`
- `signing/`
- `testing/`
- `publishing/`

## Coverage

- [x] Android build
- [x] iOS build
- [x] Signing
- [ ] CI/CD
- [ ] Common failures

## Official sources

- Flutter documentation
- Apple documentation
- Android documentation
```

Do not claim coverage that the contribution does not actually provide.

---

# 10. Build guide

A framework build guide should answer:

```text
What do I need?
        ↓
How do I configure the project?
        ↓
How do I create a release build?
        ↓
Where is the artifact?
        ↓
How do I verify it?
```

Include:

- prerequisites
- supported tooling
- project configuration
- release mode
- build command
- expected artifact
- verification
- common failures

Example structure:

```markdown
# Build

## Prerequisites

...

## Configure

...

## Build

```bash
...
```

## Output

The release artifact should be:

```text
...
```

## Verify

- ...
- ...

## Common failures

...
```

Commands must be verified before contribution.

---

# 11. iOS release guide

If the framework has framework-specific iOS behavior, document it separately.

Cover only what differs from the shared iOS publishing workflow.

Typical areas:

- project generation
- Xcode integration
- deployment target
- framework configuration
- native dependencies
- build settings
- archive generation
- signing integration
- TestFlight build creation
- common framework-specific failures

Use the shared iOS guidance for general App Store Connect and signing concepts.

For example:

```text
Core:
publishing/ios/

Framework:
frameworks/community/<framework>/ios-release.md
```

The framework file should link back to the core guidance rather than replace it.

---

# 12. Android release guide

Similarly, document Android-specific framework behavior.

Potential areas:

- release build configuration
- Gradle integration
- Android SDK requirements
- native plugins
- manifest configuration
- signing integration
- AAB generation
- Play testing
- framework-specific failures

Use the shared Android publishing guidance for general Play Console concepts.

---

# 13. Signing

Only document framework-specific signing behavior.

Examples:

```text
Framework generates native project
→ signing configuration must be applied here
```

or:

```text
Framework uses a managed build service
→ credentials are configured through the build system
```

The shared signing documentation should remain the source of truth for general principles.

See:

```text
signing/
├── ios/
├── android/
└── security/
```

Never include real credentials in examples.

---

# 14. CI/CD

If the framework has a meaningful CI/CD workflow, document it.

A good workflow explains:

```text
Checkout
    ↓
Install framework/tooling
    ↓
Install dependencies
    ↓
Validate
    ↓
Build
    ↓
Artifact
    ↓
Test
    ↓
Submit
```

Document:

- required runtime versions
- package manager
- framework CLI
- native dependencies
- cache strategy where useful
- environment variables
- secrets
- build commands
- artifact handling
- store submission

Do not create framework-specific CI documentation when the existing release-engineering workflow already covers it.

---

# 15. Framework-specific tooling

Document important tooling such as:

```text
Framework CLI
Build system
Native project generator
Package manager
Official deployment tooling
```

Do not turn the framework directory into a complete framework tutorial.

Only document tooling needed for release engineering.

---

# 16. Configuration

Document only configuration that affects release behavior.

Examples:

```text
Application identifier
Version
Build mode
Native platform settings
Minimum OS
Signing configuration
Release channels
Update configuration
```

For general configuration concepts, link to:

```text
foundations/project-configuration.md
foundations/release-environments.md
foundations/versioning.md
```

---

# 17. Release environments

Frameworks may have their own environment mechanism.

For example:

```text
Development
Preview
Production
```

The framework contribution should explain:

```text
How does this framework select the environment?
How are environment variables passed?
How does CI select the environment?
What is bundled into the client?
What remains server-side?
```

Do not create a framework-specific environment model that contradicts the shared project guidance.

See:

```text
foundations/release-environments.md
```

---

# 18. Versioning

If the framework has special version configuration, document it.

For example:

```text
Framework config
    ↓
iOS version
    +
Android version
```

Explain:

- application version
- platform build identifier
- where the value is configured
- how CI handles it
- how store builds are incremented

Use:

```text
foundations/versioning.md
```

for the shared versioning model.

---

# 19. Framework-specific updates

If the framework supports an update mechanism beyond a normal store release, document it carefully.

For example:

```text
OTA update
Hot update
Runtime update
Code push
```

Explain:

- what can be updated
- what requires a new native build
- compatibility rules
- rollback behavior
- release verification

Do not present an update mechanism as a replacement for native store releases when native changes are required.

---

# 20. Common failures

A framework contribution becomes much more useful when it documents failures that developers actually encounter.

Use this structure:

```text
Symptom
→ Likely causes
→ Checks
→ Fix
→ Verification
```

Example:

```markdown
## Release build fails after adding native package

### Symptom

The development build works, but the production build fails.

### Likely causes

- native dependency not configured
- incompatible plugin
- release-only native configuration
- incorrect platform version

### Checks

...

### Fix

...

### Verify

Build the same release configuration again and test on a real device.
```

Do not create generic troubleshooting content that already belongs under:

```text
troubleshooting/
```

Add it there if the problem applies across frameworks.

---

# 21. Official sources

Framework contributions should prefer primary sources.

Use:

```text
Framework official documentation
+
Apple official documentation
+
Android official documentation
+
official release tooling documentation
```

For example:

### Flutter

```text
https://docs.flutter.dev/
```

### Apple

```text
https://developer.apple.com/
```

### Android

```text
https://developer.android.com/
```

The actual framework contribution should link to the specific official pages relevant to the documented workflow.

Do not rely on a random blog when the framework owner already documents the behavior.

---

# 22. Source verification

For every important framework-specific instruction, ask:

```text
Is this official?
        ↓
If not
        ↓
Is it verified from a real working workflow?
        ↓
If not
        ↓
Do not present it as verified guidance.
```

For volatile information, include:

```text
Source
Last verified
Applies to
```

Example:

```markdown
> Last verified: August 2026
>
> Verify current requirements against the official Flutter and
> platform documentation before a production release.
```

Do not treat the repository as a replacement for current platform documentation.

---

# 23. Version-sensitive framework guidance

Framework behavior can change between versions.

Always identify the version scope when it matters.

Example:

```text
Flutter 3.x
Expo SDK 55
React Native 0.8x
```

Avoid writing:

> "Run this command."

when the command only works for one version.

Prefer:

```text
For Flutter X.Y and later:
...
```

or:

```text
The exact command may differ by framework version.
Verify against the current official documentation.
```

Do not guess version compatibility.

---

# 24. Framework support maturity

A community framework can start small.

Use a simple maturity model:

```text
Community
    ↓
Verified
    ↓
Maintained
    ↓
First-class
```

### Community

A contributor has added useful documentation.

### Verified

The workflow has been tested against a real project or current official documentation.

### Maintained

The framework coverage is regularly updated.

### First-class

The project intentionally maintains the framework as a primary implementation.

Do not label a framework "officially supported" unless the project has actually decided that.

---

# 25. Contribution quality levels

A useful contribution does not need to be huge.

### Level 1: Fix

Correct an existing error.

### Level 2: Guide

Add a missing release workflow.

### Level 3: Troubleshooting

Document a real failure and its fix.

### Level 4: Framework implementation

Add a coherent framework release path.

### Level 5: Automation

Add a deterministic validation script or workflow.

### Level 6: AI workflow

Add a useful AI-assisted release task with clear boundaries and validation.

Small, verified contributions are better than large unverified documentation dumps.

---

# 26. AI-assisted framework contributions

AI can help a contributor understand an unfamiliar release workflow.

Good uses:

```text
Repository inspection
Documentation comparison
Command verification assistance
Configuration audit
Migration analysis
Troubleshooting
Drafting documentation
Generating checklists
```

A safe workflow is:

```text
Framework documentation
        ↓
AI research/draft
        ↓
Contributor verifies
        ↓
Real build/test
        ↓
Official sources checked
        ↓
Pull request
```

Do not submit AI-generated release instructions without verification.

---

# 27. AI research prompt

A contributor can use an AI tool with a prompt like:

```text
I am adding release documentation for <FRAMEWORK> to an open-source
mobile release playbook.

The repository already contains framework-independent guidance for:

- release lifecycle
- environments
- versioning
- signing
- testing
- publishing
- post-release operations

Research only the framework-specific release differences.

Focus on:

1. project configuration
2. iOS release build
3. Android release build
4. signing integration
5. environment configuration
6. versioning
7. CI/CD
8. store submission integration
9. update/OTA behavior if applicable
10. common release failures

For every important claim:

- prefer official framework documentation
- prefer Apple documentation for iOS requirements
- prefer Android documentation for Android requirements
- identify version-specific behavior
- identify anything that needs verification

Do not duplicate general mobile release guidance.

Return:

FRAMEWORK RELEASE MODEL
FRAMEWORK-SPECIFIC DIFFERENCES
REQUIRED FILES
COMMANDS TO VERIFY
COMMON FAILURES
OFFICIAL SOURCES
UNKNOWN / NEEDS VERIFICATION
```

The contributor remains responsible for verifying the output.

---

# 28. AI coding agents

AI coding agents can help implement documentation or scripts.

If an agent has repository access:

```text
Read repository
    ↓
Find existing guidance
    ↓
Identify gaps
    ↓
Draft framework-specific files
    ↓
Check links
    ↓
Run validation
    ↓
Show diff
    ↓
Human review
```

Do not give the agent unrestricted access to:

- production credentials
- signing keys
- store credentials
- payment systems
- production infrastructure

The repository contribution should be reviewable as a normal Git diff.

---

# 29. Framework contribution checklist

Before opening a PR:

```text
[ ] I checked the existing core guidance.
[ ] I confirmed the contribution is framework-specific.
[ ] I used frameworks/community/<framework>/.
[ ] I avoided copying the entire playbook.
[ ] I documented only release-relevant behavior.
[ ] I verified important commands.
[ ] I checked framework version requirements.
[ ] I used official sources.
[ ] I documented important assumptions.
[ ] I included common failures where useful.
[ ] I did not include secrets.
[ ] I did not add unnecessary dependencies or infrastructure.
[ ] I checked links.
[ ] I checked Markdown formatting.
[ ] I identified anything that still needs verification.
[ ] I updated CHANGELOG.md if the contribution is meaningful.
```

---

# 30. Maintainer review checklist

Maintainers should review a framework contribution for:

## Scope

- Is this actually framework-specific?
- Does it belong under `frameworks/community/`?
- Does it duplicate existing guidance?

## Correctness

- Do commands work?
- Are configuration examples valid?
- Are platform claims supported?

## Sources

- Are official sources used?
- Are volatile requirements linked to current documentation?
- Are version-specific claims identified?

## Practicality

- Can a developer follow the workflow?
- Is success clearly defined?
- Are common failures covered?

## Security

- Are credentials handled safely?
- Are secrets excluded?
- Are AI workflows bounded?

## Maintenance

- Is the framework version scope clear?
- Will future contributors know what needs updating?
- Are sources specific enough to re-verify?

---

# 31. When a framework should become first-class

Do not make a framework first-class just because a contributor added one guide.

Consider first-class status when there is:

```text
Reliable coverage
+
Verified workflows
+
Active maintenance
+
Real community demand
+
Clear ownership
```

Potential signals:

- multiple useful contributions
- repeated community usage
- maintained release workflows
- current official-source verification
- framework-specific troubleshooting
- CI/build coverage where useful

First-class support is a maintenance commitment.

Do not create that commitment casually.

---

# 32. When a framework should not be added

Do not add a framework contribution if the contribution is:

- copied from another repository
- unverified
- outdated
- mostly generic mobile advice
- vendor marketing
- a framework tutorial unrelated to release engineering
- dependent on unsafe credentials
- based on guessed commands
- impossible to reproduce
- unnecessarily large

It is better to have no framework guide than a guide that causes a developer to ship incorrectly.

---

# 33. Avoid vendor lock-in

A framework guide can document its official tooling without turning the playbook into vendor promotion.

For example:

```text
Framework
→ official build tooling
→ platform store
```

Explain the workflow.

Do not claim:

> "This is the only correct way."

unless the framework or platform actually requires it.

When multiple valid approaches exist:

```text
Recommended approach
Alternative approach
Trade-offs
```

Keep the recommendation practical.

---

# 34. Community ownership

A community framework contribution should make ownership clear where useful.

Example:

```markdown
## Maintainers

Community contribution.

If this guide is outdated, please open an issue or pull request.
```

Do not assign permanent ownership without agreement.

The goal is to make maintenance visible, not bureaucratic.

---

# 35. Updating a framework guide

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

Do not silently change a release workflow that was previously valid without explaining the scope.

---

# 36. Deprecating framework guidance

If a framework workflow becomes obsolete:

```text
Mark deprecated
        ↓
Explain why
        ↓
Point to replacement
        ↓
Remove later when appropriate
```

Do not leave known-broken commands in the playbook just because they worked historically.

Historical information can be useful, but it should be clearly labeled as historical.

---

# 37. Example contribution

Suppose a contributor knows Flutter.

They should not create:

```text
frameworks/community/flutter/
├── entire-foundations-copy.md
├── entire-signing-copy.md
├── entire-testing-copy.md
└── entire-publishing-copy.md
```

Instead:

```text
frameworks/community/flutter/
├── README.md
├── build.md
├── ios-release.md
├── android-release.md
├── signing.md
└── common-failures.md
```

And the guides should link to:

```text
foundations/
signing/
testing/
publishing/
release-engineering/
```

This keeps the repository maintainable.

---

# 38. Example framework flow

A framework-specific release guide should generally look like:

```text
Framework project
      ↓
Framework release configuration
      ↓
Environment selection
      ↓
Version configuration
      ↓
Native build
      ↓
Signing
      ↓
Release artifact
      ↓
Device testing
      ↓
Store upload
      ↓
Store testing
      ↓
Production release
```

The shared playbook defines the release lifecycle.

The framework contribution explains how that framework moves through it.

---

# 39. Recommended first contribution

If you are adding a framework for the first time, do not try to document everything.

Start with:

```text
1. README
2. Build
3. iOS release
4. Android release
5. Signing
6. Common failures
```

Then expand only when you have verified material.

A small, accurate framework contribution is much more valuable than a large speculative one.

---

# 40. Final standard

A framework contribution should pass this test:

> **Can a developer who already knows the framework use this guide to safely build and release a real mobile application?**

If yes, the contribution is useful.

If it only explains what the framework is, it is probably outside this repository's scope.

---

# Related documentation

### Core

- `foundations/mobile-release-lifecycle.md`
- `foundations/project-configuration.md`
- `foundations/release-environments.md`
- `foundations/versioning.md`
- `foundations/device-support.md`
- `foundations/dependency-management.md`

### Signing

- `signing/ios/`
- `signing/android/`
- `signing/security/`

### Testing

- `testing/`
- `testing/release-smoke-tests.md`

### Publishing

- `publishing/ios/`
- `publishing/android/`
- `publishing/cross-platform/`

### Release engineering

- `release-engineering/`
- `release-engineering/github-actions/`

### Troubleshooting

- `troubleshooting/`

### AI

- `ai/README.md`
- `ai/workflows/`
- `ai/prompts/`
- `ai/evals/`
- `ai/security/`

### Governance

- `CONTRIBUTING.md`
- `governance/documentation-style.md`
- `governance/source-verification.md`
- `governance/maintenance.md`

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

Do not assume that every framework listed above is currently maintained by this repository. They are examples of potential community contributions.

**Last verified:** August 11, 2026

Framework tooling and platform requirements change. Contributors should verify the current official documentation before submitting release instructions.
