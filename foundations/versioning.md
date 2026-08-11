# Versioning

Versioning gives every mobile release a clear identity.

A good versioning system should answer four questions quickly:

1. **What release is this?**
2. **Can the store accept this build?**
3. **Which exact binary is installed?**
4. **Which source commit produced it?**

For Expo and React Native, versioning spans the application version, platform-specific build identifiers, source control, and store release history.

The goal is simple:

```text
Every production binary
        ↓
has a unique build identity
        ↓
can be traced to source
        ↓
can be safely upgraded
```

---

# 1. Version vs build number

These are different concepts.

### App version

The user-facing version.

Example:

```text
1.4.0
```

It communicates the release level of the application.

### Build number

A platform-specific identifier for a particular build.

Example:

```text
iOS
Version: 1.4.0
Build: 42

Android
Version: 1.4.0
Version Code: 42
```

Think of it as:

```text
Version
→ product release

Build number
→ exact platform build
```

Do not use them interchangeably.

---

# 2. Recommended version format

For most applications, use Semantic Versioning as the human-facing convention:

```text
MAJOR.MINOR.PATCH
```

Examples:

```text
1.0.0
1.1.0
1.1.1
2.0.0
```

The usual interpretation is:

```text
MAJOR
→ incompatible or major product/API change

MINOR
→ backward-compatible feature

PATCH
→ backward-compatible fix
```

Semantic Versioning is a convention, not a replacement for platform-specific build identifiers.

Official reference:

https://semver.org/

---

# 3. Mobile versioning model

A useful release identity is:

```text
Application version
+
Platform build identifier
+
Source commit
+
Build profile
+
Environment
```

Example:

```text
Version:
1.4.0

iOS build:
42

Android versionCode:
42

Commit:
8f3a1c2

Profile:
production

Environment:
production
```

This is enough to trace a release without creating unnecessary release infrastructure.

---

# 4. Expo versioning

Expo application configuration commonly includes:

```json
{
  "expo": {
    "version": "1.4.0"
  }
}
```

For platform-specific build identifiers, Expo supports:

```json
{
  "expo": {
    "ios": {
      "buildNumber": "42"
    },
    "android": {
      "versionCode": 42
    }
  }
}
```

The exact behavior can also be controlled through EAS configuration and app configuration depending on the project's setup.

Use the current Expo documentation for the SDK version in the project:

https://docs.expo.dev/versions/latest/config/app/

---

# 5. iOS versioning

iOS uses two important values:

```text
CFBundleShortVersionString
→ user-facing version

CFBundleVersion
→ build number
```

Example:

```text
1.4.0
42
```

In an Expo configuration this commonly maps to:

```json
{
  "ios": {
    "buildNumber": "42"
  }
}
```

The iOS build number must be incremented appropriately for new builds submitted for the same app version.

Apple's current App Store Connect and Xcode tooling should be treated as the final authority for submission-specific requirements.

Official references:

https://developer.apple.com/help/app-store-connect/

https://developer.apple.com/documentation/bundleresources/information_property_list/cfbundleversion

---

# 6. Android versioning

Android uses:

```text
versionName
→ user-facing version

versionCode
→ monotonically increasing internal build identifier
```

Example:

```text
versionName:
1.4.0

versionCode:
42
```

In Expo:

```json
{
  "android": {
    "versionCode": 42
  }
}
```

Google Play uses the version code to identify and order Android releases.

The version code must increase for each new Play-distributed version.

Official reference:

https://developer.android.com/studio/publish/versioning

---

# 7. The build identifier rule

The safest rule is:

> **Never reuse a platform build identifier for a new store-distributed binary.**

Example:

```text
1.4.0 (42)
```

is uploaded.

The next build should not try to reuse:

```text
42
```

Use:

```text
43
```

This matters even when the application version has not changed.

For example:

```text
1.4.0 build 42
→ rejected

1.4.0 build 43
→ fixed build
```

The public version can remain:

```text
1.4.0
```

while the build identifier changes.

---

# 8. Versioning and rejected builds

A common mistake is:

```text
Build 42
→ store rejection
→ modify source
→ try to upload build 42 again
```

Instead:

```text
Build 42
→ rejected

Fix
→ build 43
→ submit again
```

For a new binary, use a new platform build identifier.

Keep the same public version when the fix belongs to the same release.

Example:

```text
1.4.0 build 42
→ rejected

1.4.0 build 43
→ resubmitted
```

---

# 9. Versioning and hotfixes

Suppose production is running:

```text
1.4.0
```

A critical bug is discovered.

A patch release is appropriate when the fix is a backward-compatible correction:

```text
1.4.1
```

The release may therefore look like:

```text
1.4.0
→ 1.4.1
```

with new platform build identifiers:

```text
iOS:
1.4.1 (45)

Android:
1.4.1 (45)
```

The exact numbering sequence is project-specific.

The important rule is that platform build identifiers remain unique and increasing where the platform requires it.

---

# 10. Versioning and feature releases

A new backward-compatible feature can use:

```text
1.5.0
```

Example:

```text
1.4.1
→ bug fix

1.5.0
→ new feature

2.0.0
→ major incompatible change
```

Do not inflate the major version simply because a release is large.

Use a consistent project policy.

---

# 11. Major versions

A major version is appropriate when the project intentionally communicates a significant compatibility or product boundary.

Examples can include:

- breaking public API changes
- major architectural compatibility changes
- significant product migration
- incompatible data/client behavior

But mobile applications are not required to follow Semantic Versioning mechanically.

If the product uses another release convention, document it and apply it consistently.

---

# 12. Versioning policy

Before a project begins frequent releases, define:

```text
Version format:
MAJOR.MINOR.PATCH

Major:
Breaking / major compatibility change

Minor:
New backward-compatible feature

Patch:
Bug/security/performance fix

Build identifier:
Increment for every store-distributed binary
```

The policy should be documented in:

```text
README.md
```

or:

```text
foundations/versioning.md
```

Do not make developers guess how versions should be chosen.

---

# 13. Source control and versions

A production release should be traceable to Git.

Example:

```text
Version:
1.4.0

iOS build:
42

Android versionCode:
42

Commit:
8f3a1c2
```

Use Git tags where useful:

```text
v1.4.0
```

A tag should point to the source state used to create the release.

For example:

```text
main
 ↓
commit 8f3a1c2
 ↓
tag v1.4.0
 ↓
production build
```

This makes future debugging much easier.

---

# 14. Git tags

A simple convention:

```text
v1.0.0
v1.1.0
v1.1.1
v1.2.0
```

Avoid mixing formats such as:

```text
release-1.2
version_1.3
prod-final
final-release
v1.4.0-final-final
```

Use one convention.

Tags should be immutable in normal operation.

Do not move an existing production tag to a different commit to hide release history.

---

# 15. Release branches

Most small teams do not need complicated release branching.

A simple workflow can be:

```text
main
 ↓
release candidate
 ↓
production
```

Use a release branch when it solves a real problem, such as:

- parallel development
- longer QA cycles
- enterprise release windows
- multiple supported release lines

Do not create:

```text
develop
qa
staging
release
hotfix
production
```

just because a branching diagram looks professional.

Every branch adds coordination cost.

---

# 16. Version source of truth

Avoid maintaining the version manually in many files.

A project may have version information in:

```text
package.json
app.json / app.config.*
ios/
android/
App Store Connect
Google Play Console
```

The project should define which values are authoritative and which are generated.

A practical Expo setup can use:

```text
app.config.ts
→ application version

EAS / platform build configuration
→ build identifiers
```

The exact strategy depends on the repository.

The important rule is:

> **Do not create multiple independent version sources unless the tooling requires them.**

---

# 17. Automatic build number management

Build numbers can be generated automatically.

For example:

```text
CI run number
```

or:

```text
release counter
```

can produce platform build identifiers.

This can reduce manual mistakes.

But automation must preserve the platform rule:

```text
New store build
→ new unique build identifier
```

Do not generate numbers from a value that can reset.

For example, a CI job number that resets after migrating repositories can cause collisions.

---

# 18. Expo automatic version management

EAS can manage app version/build numbers for projects that use its remote versioning capabilities.

Expo documents `cli.appVersionSource` and EAS remote app version management as ways to keep native build version values under EAS control.

Official reference:

https://docs.expo.dev/build-reference/app-versions/

If using remote version management, document that decision clearly.

Do not mix manual and remote version management without understanding which system owns the value.

---

# 19. Local builds vs store builds

A developer may create many local builds.

Not every build needs to consume a production store build number.

Separate:

```text
Development builds
```

from:

```text
Store-distributed builds
```

The important production rule is:

```text
Every store-distributed binary
→ unique platform build identifier
```

Do not waste production build numbers on unrelated local experiments if the platform/build system can safely separate those workflows.

---

# 20. Build numbers should be boring

Do not encode too much information into build numbers.

Avoid schemes like:

```text
2026081101
```

unless there is a real operational reason.

A simple counter:

```text
42
43
44
45
```

is easier to understand.

The release record can contain:

```text
version
+
build number
+
commit
+
timestamp
```

You do not need to encode all of that into one number.

---

# 21. Version and database migrations

A mobile version can affect backend/data compatibility.

For example:

```text
App 1.4.0
→ API v1

App 1.5.0
→ API v1 + new fields

App 2.0.0
→ API v2
```

Do not assume old mobile clients disappear immediately.

Users may remain on older versions for a long time.

Therefore:

```text
New app
    ↓
must often coexist with
    ↓
older app
```

Backend changes should usually be backward compatible until the supported client versions are safely retired.

---

# 22. Version and database migrations

For local application storage, upgrades may require migrations.

Examples:

- SQLite schema
- Async storage format
- local cache format
- encrypted storage
- persisted Redux state
- local files

Test:

```text
Old version
   ↓
Upgrade
   ↓
New version
```

Do not test only:

```text
Fresh install
   ↓
New version
```

Many mobile upgrade failures happen because the developer only tests clean installs.

---

# 23. Version and API compatibility

The backend should know which client versions it supports when compatibility matters.

Possible approaches include:

```text
minimum supported app version
```

or:

```text
client capability negotiation
```

or:

```text
API compatibility layer
```

Do not force an elaborate version negotiation system unless the product actually needs it.

For most systems:

```text
Backward-compatible API
+
minimum supported app version
```

is enough.

---

# 24. Minimum supported version

At some point, an application may need to stop supporting very old versions.

Reasons include:

- security
- obsolete API
- unsupported OS
- broken authentication
- deprecated platform requirement
- infrastructure changes

A simple strategy is:

```text
Backend knows minimum supported version
        ↓
Old client receives update-required response
        ↓
User is directed to store
```

The backend should enforce the policy.

Do not rely only on a client-side check that an old client can bypass.

---

# 25. Forced updates

A forced update should be used carefully.

Useful when:

- security vulnerability exists
- backend compatibility is no longer safe
- old client cannot function correctly
- critical platform requirement changed

Avoid forcing updates for:

- minor UI changes
- optional features
- non-critical bugs

A better pattern is often:

```text
Recommended update
```

rather than:

```text
Mandatory update
```

unless there is a real compatibility or security reason.

---

# 26. Version metadata

Every production release should ideally record:

```text
Application version
Platform
Build number
Git commit
Build profile
Environment
Release date
Release notes
```

Example:

```text
Version: 1.4.0
Platform: iOS
Build: 42
Commit: 8f3a1c2
Profile: production
Environment: production
Released: 2026-08-11
```

This can live in:

- CI output
- release notes
- GitHub Release
- store metadata
- internal release record

Do not create a database just to store this information if GitHub Releases and CI artifacts already solve the problem.

---

# 27. Release naming

Keep release names consistent.

Recommended:

```text
v1.4.0
```

or:

```text
1.4.0
```

For GitHub Releases:

```text
v1.4.0
```

is easy to identify.

For internal release records:

```text
1.4.0
```

is sufficient.

Avoid:

```text
Production Release Final
August Release
Latest
Stable Build
```

Those names become ambiguous quickly.

---

# 28. Release notes

Release notes should describe user-relevant changes.

Good:

```text
- Added offline draft support.
- Improved startup performance.
- Fixed notification navigation.
```

Avoid:

```text
- Refactored 12 files.
- Updated internal hook abstraction.
- Changed service architecture.
```

unless the change affects users.

Release notes should be tied to the version.

---

# 29. Versioning and security releases

Security fixes should receive a clear version.

For example:

```text
1.4.0
→ vulnerability discovered

1.4.1
→ security fix
```

Do not hide security fixes under ambiguous version labels.

When appropriate, the release record should identify:

```text
affected versions
fixed version
required action
```

Do not disclose sensitive exploit details unnecessarily in public release notes.

---

# 30. Versioning and dependency upgrades

A dependency upgrade does not automatically require a major version bump.

For example:

```text
1.4.0
→ dependency update
```

may remain:

```text
1.4.0
```

if it is part of an unreleased build.

But if the dependency change reaches users as a new release, apply the project's normal versioning policy.

The risk level of the dependency change matters more than the number of lines changed.

---

# 31. Versioning and native changes

Native changes often require a new binary.

Examples:

- native dependency update
- permission change
- entitlement change
- app capability change
- minimum OS change
- application identifier change
- native build configuration

A new native binary should receive a new platform build identifier.

Whether it also receives a new public application version depends on the release policy.

---

# 32. OTA versioning

If using Expo Updates, distinguish:

```text
Native runtime version
```

from:

```text
JavaScript/update release
```

An OTA update must be compatible with the installed native runtime.

Do not treat an OTA update as if it were a completely new native store version.

For Expo, the current update/runtime model should be checked against the installed Expo SDK and EAS Update configuration:

https://docs.expo.dev/versions/latest/sdk/updates/

---

# 33. Runtime compatibility

A useful model is:

```text
Native build
1.4.0 / runtime R1
        ↓
Compatible OTA updates
        ↓
R1 updates
```

When native functionality changes:

```text
Native change
        ↓
new binary
        ↓
new runtime
```

The exact runtime/versioning strategy depends on the Expo configuration.

Do not invent a custom runtime-version system unless the project needs it.

---

# 34. Versioning in CI

CI should expose release identity.

Example:

```text
VERSION=1.4.0
BUILD_NUMBER=42
COMMIT=8f3a1c2
ENVIRONMENT=production
PROFILE=production
```

This information should be visible in build logs and artifacts.

If a production crash occurs, the team should be able to move from:

```text
Crash:
1.4.0 (42)
```

to:

```text
Commit:
8f3a1c2
```

without guessing.

---

# 35. Versioning validation

Before a store build:

### Application version

- [ ] Correct format.
- [ ] Matches release intent.
- [ ] Matches store release expectations.

### iOS

- [ ] Correct version.
- [ ] Build number is valid.
- [ ] Build number is not reused.

### Android

- [ ] Correct versionName.
- [ ] VersionCode is valid.
- [ ] VersionCode is greater than the previous distributed version.

### Source

- [ ] Commit is known.
- [ ] Git tag is created where used.
- [ ] Release notes match the version.

---

# 36. Versioning audit

A useful automated check can report:

```text
Application version
iOS build number
Android version code
Git commit
Git tag
Build profile
Environment
```

Example:

```text
Version:       1.4.0
iOS build:     42
Android code:  42
Commit:        8f3a1c2
Tag:           v1.4.0
Profile:       production
Environment:   production
```

If any value is unexpected, stop before submission.

---

# 37. AI-assisted version audit

AI can help inspect version consistency.

Useful tasks:

- compare `package.json`
- inspect `app.json` / `app.config.*`
- inspect `eas.json`
- inspect native iOS settings
- inspect Android Gradle configuration
- identify version/build mismatches
- inspect Git tags
- prepare release notes
- identify likely store submission conflicts

Safe workflow:

```text
Repository
    ↓
AI audits version configuration
    ↓
Candidate issues
    ↓
Developer verifies
    ↓
Build
    ↓
Inspect artifact
    ↓
Submit
```

AI should not silently increment or publish production versions without explicit workflow rules and human approval.

---

# 38. AI version audit prompt

```text
Audit versioning for this Expo / React Native mobile repository.

Inspect:

1. package.json
2. app.json / app.config.*
3. eas.json
4. ios/
5. android/
6. Git tags
7. CI/CD configuration
8. release scripts

Determine:

- application version
- iOS build number
- Android version code
- version source of truth
- build-number source of truth
- current production version if discoverable
- likely next release values
- mismatches
- duplicate build-number risks
- native versioning risks
- OTA/runtime compatibility risks

Return:

VERSION MAP
SOURCE OF TRUTH
MISMATCHES
RELEASE RISKS
RECOMMENDED CHANGES
FILES TO VERIFY

Do not modify files.

Do not invent the current store version.

If the previous production build number cannot be verified, mark it as NEEDS VERIFICATION.
```

---

# 39. Common versioning failures

## Duplicate iOS build number

### Problem

```text
Build 42
→ already uploaded
```

### Fix

Use:

```text
Build 43
```

Do not reuse the identifier.

---

## Duplicate Android version code

### Problem

```text
versionCode 42
→ already distributed
```

### Fix

Increment:

```text
43
```

---

## Version changed but build identifier did not

This can be valid or invalid depending on platform state, but the production release should always have a unique platform build identity.

Check the platform's current store state before submission.

---

## Build number reset after migration

### Problem

CI changes from:

```text
42
```

to:

```text
1
```

### Fix

Restore a monotonically increasing sequence.

Record the last distributed platform build number before migrating version management.

---

## Store version and repository version disagree

### Problem

Repository:

```text
1.5.0
```

Store:

```text
1.4.0
```

This may be expected if `1.5.0` is unreleased.

But if the production binary is supposed to be `1.5.0`, investigate.

Do not assume the repository's current version equals the public store version.

---

# 40. Version migration

Changing version-management strategy requires care.

For example:

```text
Manual build numbers
        ↓
EAS remote version management
```

Before migration:

1. Identify the latest distributed iOS build number.
2. Identify the latest distributed Android version code.
3. Record current application version.
4. Configure the new source of truth.
5. Set the starting values correctly.
6. Build a test release.
7. Verify the resulting identifiers.
8. Document the new process.

Do not let the new system start from:

```text
1
```

when the store is already at:

```text
42
```

---

# 41. Version rollback

Mobile releases cannot generally be rolled back like a server deployment.

If:

```text
1.5.0
```

is broken, you normally cannot simply publish:

```text
1.4.0
```

as a replacement because stores enforce version/build ordering and users may already have the newer version.

The practical response is usually:

```text
1.5.0
→ incident
→ mitigation
→ 1.5.1 hotfix
```

or:

```text
1.5.0
→ server-side mitigation
→ 1.5.1
```

This is why mobile versioning and release safety matter.

---

# 42. Version support policy

A product should eventually define how many old versions it supports.

Example:

```text
Current:
1.5.x

Supported:
1.4.x and newer

Unsupported:
< 1.4
```

The exact policy depends on:

- security
- backend compatibility
- user base
- update frequency
- platform requirements
- support cost

Avoid supporting every historical version forever.

But do not break old clients without understanding their active user population.

---

# 43. Recommended release sequence

For a normal release:

```text
1. Complete change
        ↓
2. Choose version
        ↓
3. Update release metadata
        ↓
4. Increment platform build identifiers
        ↓
5. Commit
        ↓
6. Tag
        ↓
7. Build
        ↓
8. Test
        ↓
9. Submit
        ↓
10. Release
        ↓
11. Monitor
```

For a rejected binary:

```text
Build 42
    ↓
Rejected
    ↓
Fix
    ↓
Build 43
    ↓
Resubmit
```

For a production hotfix:

```text
1.5.0
    ↓
Bug
    ↓
1.5.1
    ↓
New build identifiers
    ↓
Release
```

---

# 44. Release version record

For each release, keep a small record:

```yaml
version: 1.4.0

ios:
  build: "42"

android:
  versionCode: 42

source:
  commit: 8f3a1c2
  tag: v1.4.0

build:
  profile: production
  environment: production

release:
  date: 2026-08-11
```

This is an example format.

Use whatever representation fits the repository.

Do not introduce a database or release-management service just to store this information unless the project actually needs one.

---

# 45. Versioning checklist

## Before development release

- [ ] Versioning policy is known.
- [ ] Application version is selected.
- [ ] Build identifier strategy is known.

## Before production build

- [ ] Version is correct.
- [ ] iOS build number is unique.
- [ ] Android version code is unique/increasing.
- [ ] Source commit is known.
- [ ] Build profile is correct.
- [ ] Environment is correct.

## Before submission

- [ ] Store version matches intended release.
- [ ] Correct binary is selected.
- [ ] Build identifiers are correct.
- [ ] Release notes match the version.

## After release

- [ ] Production version is recorded.
- [ ] Build identifiers are recorded.
- [ ] Git tag exists where used.
- [ ] Monitoring can identify the release.

---

# 46. Production version gate

Do not submit until:

- [ ] Public version is intentional.
- [ ] iOS build number is new.
- [ ] Android version code is new.
- [ ] Version source of truth is known.
- [ ] Build source commit is known.
- [ ] Production environment is correct.
- [ ] Correct build profile is used.
- [ ] Git tag/release record is correct where applicable.
- [ ] Store metadata matches the release.
- [ ] Upgrade path from the previous production version is tested.
- [ ] Local data migration is tested where applicable.
- [ ] Backend compatibility is verified.
- [ ] Human approval is complete.

---

# 47. Recommended operating model

For most Expo/React Native projects:

```text
Semantic version
        +
platform build counter
        +
Git commit
        +
Git tag
```

Example:

```text
v1.4.0
iOS build 42
Android versionCode 42
commit 8f3a1c2
```

Keep the system simple.

Do not encode:

```text
date
branch
environment
platform
commit
```

into the public version or build number.

Keep that information in the release record.

---

# 48. What not to do

### Do not reuse store build numbers

Platform stores expect new builds to have new identifiers.

### Do not use the version number as the build number

They serve different purposes.

### Do not manually update five version sources

Define a source of truth.

### Do not reset build numbers after CI migration

Preserve the store's existing sequence.

### Do not assume repository version equals production version

Unreleased changes may already be present.

### Do not test only fresh installs

Upgrade paths matter.

### Do not use version numbers as database migration identifiers

Application release version and data schema version are different concerns.

### Do not let AI invent the next production build number

It should verify the current store state or clearly mark the value as unknown.

### Do not overengineer release numbering

A simple, traceable scheme is better than a clever one.

---

# 49. Definition of done

Versioning is ready when:

```text
Public version is clear
        +
Platform build identifiers are unique
        +
Source commit is traceable
        +
Release tag is consistent
        +
Upgrade path is tested
        +
Backend compatibility is understood
        +
Production artifact is identifiable
        ↓
   VERSION READY
```

The key rule is:

> **A version should tell users what changed, while the build identity tells engineers exactly what was shipped.**

---

# Related documentation

### Foundations

- `foundations/mobile-release-lifecycle.md`
- `foundations/project-configuration.md`
- `foundations/release-environments.md`
- `foundations/identifiers.md`
- `foundations/device-support.md`
- `foundations/dependency-management.md`

### Testing

- `testing/upgrade-migrations.md`
- `testing/release-smoke-tests.md`
- `testing/device-testing.md`

### Release engineering

- `release-engineering/eas/`
- `release-engineering/github-actions/`
- `release-engineering/release-pipelines.md`
- `release-engineering/environment-management.md`

### Release strategy

- `release-strategy/hotfixes.md`
- `release-strategy/staged-rollouts.md`
- `release-strategy/feature-flags.md`

### Post-release

- `post-release/monitoring.md`
- `post-release/hotfix.md`
- `post-release/rollback.md`

### AI

- `ai/workflows/release-audit.md`
- `ai/workflows/release-preparation.md`
- `ai/workflows/debugging.md`

---

# Official sources

Use official documentation for current platform and tooling behavior.

### Semantic Versioning

- https://semver.org/

### Expo

- App configuration: https://docs.expo.dev/versions/latest/config/app/
- App version management: https://docs.expo.dev/build-reference/app-versions/
- EAS Build configuration: https://docs.expo.dev/build/eas-json/
- EAS Build: https://docs.expo.dev/build/introduction/
- EAS Update: https://docs.expo.dev/versions/latest/sdk/updates/
- EAS environment variables: https://docs.expo.dev/eas/environment-variables/

### Apple

- App Store Connect Help: https://developer.apple.com/help/app-store-connect/
- CFBundleVersion: https://developer.apple.com/documentation/bundleresources/information_property_list/cfbundleversion
- CFBundleShortVersionString: https://developer.apple.com/documentation/bundleresources/information_property_list/cfbundleshortversionstring

### Android

- Version your app: https://developer.android.com/studio/publish/versioning
- Android app release documentation: https://developer.android.com/studio/publish

### React Native

- React Native documentation: https://reactnative.dev/docs/getting-started

**Last verified:** August 11, 2026

Store rules, platform version requirements, Expo/EAS behavior, and build tooling can change. Re-check the official documentation before implementing or changing production version management.
