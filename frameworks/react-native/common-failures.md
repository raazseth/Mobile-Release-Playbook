# React Native Common Failures

This guide covers common **React Native release and build failures** across iOS and Android.

It is intentionally focused on release engineering:

```text
Failure
→ Identify the layer
→ Find the first real error
→ Make the smallest fix
→ Rebuild
→ Verify the actual release artifact
```

Do not treat every React Native problem as a JavaScript problem.

A React Native application can fail in:

```text
JavaScript
Metro
React Native
Native modules
iOS / Xcode
CocoaPods
Android / Gradle
Android SDK
JDK
Signing
Release configuration
CI/CD
Runtime
Store submission
```

The first useful question is:

> **What actually failed?**

---

# 1. Troubleshooting rule

Use this sequence:

```text
Failure
   ↓
Identify exact command / build
   ↓
Read logs
   ↓
Find first meaningful error
   ↓
Classify the failure
   ↓
Reproduce locally if possible
   ↓
Make the smallest fix
   ↓
Run validation
   ↓
Rebuild
   ↓
Verify the release artifact
```

Do not start with:

```text
Delete node_modules
Delete Pods
Delete Gradle caches
Upgrade React Native
Upgrade Xcode
Upgrade everything
```

Those actions can destroy useful evidence and create unrelated changes.

---

# 2. Failure classification

| Failure layer | Typical symptom | Start here |
|---|---|---|
| JavaScript | Runtime exception, broken screen | JS stack / Metro |
| Metro | Bundle or resolver failure | Metro output |
| Dependency | Version or package conflict | `package.json` + lockfile |
| Native module | Native build/runtime failure | Package documentation + native project |
| iOS | Xcode compilation/signing failure | Xcode build log |
| CocoaPods | Pod resolution/install failure | `Podfile` / Podfile.lock |
| Android | Gradle compilation failure | Gradle output |
| Toolchain | JDK/Gradle/Xcode incompatibility | Tool versions |
| Release | Debug works, release fails | Release configuration |
| Signing | Keystore/certificate/provisioning failure | Signing configuration |
| CI/CD | Local works, CI fails | CI environment |
| Runtime | App installs but crashes | Device logs / crash report |
| Store | Upload or submission failure | Store diagnostics |

---

# 3. Always capture the environment

Before debugging a difficult release failure, record:

```text
React Native version
React version
Node version
Package manager + version
iOS version
Xcode version
CocoaPods version
Android SDK
Android Gradle Plugin
Gradle version
JDK version
Build variant/configuration
Git commit
```

Useful commands:

```bash
node --version
npm --version
```

```bash
java -version
```

Android:

```bash
cd android
./gradlew -version
```

Windows:

```powershell
cd android
.\gradlew.bat -version
```

For the project itself:

```bash
git status
git rev-parse HEAD
```

Do not guess which version is being used when the failure depends on the toolchain.

See `frameworks/react-native/local-setup.md` for how to install and verify this toolchain in the first place, and for the checklist to run before trusting a local release build.

---

# 4. Failure: `npm install` or dependency installation fails

### Symptoms

```text
ERESOLVE
peer dependency conflict
package not found
lockfile error
postinstall failure
```

### Check

```text
package.json
lockfile
Node version
package-manager version
peer dependencies
React version
React Native version
native dependencies
```

Run the project's normal package-manager install first.

Do not immediately delete the lockfile.

### Fix

Prefer:

```text
Identify conflicting dependency
→ determine compatible version
→ update intentionally
→ regenerate lockfile
→ test
```

Avoid:

```text
force install
ignore all peer dependencies
random package upgrades
```

unless the project explicitly accepts the resulting risk.

---

# 5. Failure: React Native version mismatch

### Symptoms

```text
Multiple React Native versions
React Native module mismatch
Unexpected native behavior
Metro package mismatch
Build errors after dependency changes
```

### Check

```text
package.json
lockfile
installed dependency tree
native project
React Native version
React version
```

For a dependency-related release issue, inspect the actual resolved dependency tree rather than only the version written in `package.json`.

If the project is upgrading React Native, use the official Upgrade Helper and apply the version-specific native template changes. React Native's upgrade guide explicitly treats upgrades as both dependency and project-file changes.

### Do not

```text
Upgrade React Native
+
Upgrade every dependency
+
Upgrade Gradle
+
Upgrade Xcode
```

in one uncontrolled change.

---

# 6. Failure: Metro cannot resolve a module

### Symptoms

```text
Unable to resolve module
Module not found
Cannot find package
```

### Check

```text
Import path
Package installation
File name casing
Package exports
Platform-specific file
Metro configuration
Monorepo configuration
Symlinks/workspaces
```

Example:

```text
import Button from "./button";
```

but the actual file is:

```text
Button.tsx
```

Case-sensitive environments can expose problems that were hidden on another machine.

### Fix

Start with the exact unresolved module.

Do not reset the entire project until you know whether the problem is:

```text
missing package
bad import
Metro configuration
filesystem casing
workspace resolution
```

---

# 7. Failure: Metro cache appears stale

### Symptoms

```text
Old code is still running
Removed code still appears
Dependency change is ignored
```

### First check

```text
Is the correct process running?
Is the correct project being served?
Is the correct build connected to Metro?
Did the file actually change?
```

If the evidence points to cache state, restart Metro using the project's supported cache-reset procedure.

Do not use cache deletion as the first diagnosis.

React Native's upgrade documentation also notes that some "old version still running" problems can be cache-related.

---

# 8. Failure: app cannot connect to Metro

### Symptoms

```text
Unable to connect to development server
Could not connect to localhost
Red screen says Metro is unavailable
```

### Check

```text
Metro is running
Device/emulator can reach the development machine
Correct port
ADB port forwarding where required
Firewall
VPN
Network configuration
```

Android device:

```bash
adb devices
```

Then confirm the device is actually connected.

### Important

This is normally a **development** problem.

A properly packaged release build should not depend on a developer's Metro server. React Native's Android publishing documentation explicitly notes that release builds bundle the JavaScript and do not require a running bundler.

---

# 9. Failure: debug works, release fails

This is one of the most important React Native release failures.

### Common causes

```text
Release environment
Minification
R8 / ProGuard
Hermes
Native module initialization
Permissions
Manifest
Signing
Bundled JavaScript
Missing assets
Release-only configuration
```

### Debug sequence

```text
1. Reproduce with the actual release build.
2. Capture device logs.
3. Compare debug and release configuration.
4. Check environment values.
5. Check native dependencies.
6. Check minification/shrinking.
7. Check release-only code paths.
```

Do not assume:

```text
debug works
→ production is correct
```

---

# 10. Failure: release build cannot find JavaScript

### Symptoms

```text
Blank screen
JS bundle missing
Metro connection error in release
Application starts but React Native does not load
```

### Check

```text
Release Gradle configuration
Bundle task
React Native Gradle Plugin
Build variant
Assets
Custom bundle paths
```

For Android, React Native's release build process bundles JavaScript into the AAB. The official documentation also warns that `org.gradle.configureondemand=true` can cause release builds to skip bundling JavaScript and assets.

Check:

```text
android/gradle.properties
```

for unexpected release-affecting Gradle properties.

---

# 11. Failure: Android Gradle build fails

### First rule

Find the **first meaningful Gradle error**.

Not:

```text
BUILD FAILED
```

but the actual cause before it.

### Check

```text
React Native version
Gradle version
Android Gradle Plugin
JDK
compileSdk
targetSdk
minSdk
Kotlin version where applicable
native dependency
manifest
resource configuration
```

### Useful command

```bash
cd android
./gradlew bundleRelease --stacktrace
```

Windows:

```powershell
cd android
.\gradlew.bat bundleRelease --stacktrace
```

### Do not

Blindly change:

```text
Gradle
AGP
Kotlin
JDK
compileSdk
```

all at once.

Change the component that the evidence identifies.

---

# 12. Failure: Gradle works locally but fails in CI

### Check

```text
JDK
Node
package-manager version
lockfile
Gradle wrapper
Android SDK
environment variables
secrets
working directory
CI cache
```

Compare:

```text
Local
vs
CI
```

Find the first meaningful difference.

Do not assume the CI machine has the same Android SDK or JDK as the developer machine.

---

# 13. Failure: JDK mismatch

### Symptoms

```text
Unsupported class file version
Gradle JVM error
Android Gradle Plugin incompatibility
Build fails before compilation
```

### Check

```bash
java -version
```

and:

```bash
cd android
./gradlew -version
```

Verify compatibility between:

```text
React Native
Android Gradle Plugin
Gradle
JDK
```

Do not upgrade the JDK simply because a newer version exists.

The supported combination depends on the project's React Native and Android toolchain versions.

---

# 14. Failure: Android SDK mismatch

### Symptoms

```text
compileSdk not found
SDK platform missing
Manifest merger failure
resource not found
target SDK error
```

### Check

```text
compileSdk
targetSdk
minSdk
installed SDK platforms
Android build tools
native dependencies
```

Make sure CI and local development have the required SDK components.

Do not increase the target SDK as a random build fix.

Target API requirements can also be store requirements, so current Android/Google Play documentation must be checked separately.

---

# 15. Failure: Android manifest merger error

### Symptoms

```text
Manifest merger failed
uses-sdk conflict
duplicate provider
duplicate permission
attribute already defined
```

### Check

```text
AndroidManifest.xml
native dependency manifests
applicationId
minSdk
permissions
providers
services
receivers
intent filters
```

The useful question is:

```text
Which two declarations are conflicting?
```

Find the actual source of the conflict before editing the manifest.

Do not delete a permission or component simply to make the merger pass.

Determine which dependency requires it and whether it is actually needed.

---

# 16. Failure: Android native dependency breaks the build

### Symptoms

```text
Build worked before package X
Build fails after package X
```

### Check

```text
Package version
React Native compatibility
Android requirements
Gradle requirements
Manifest changes
Kotlin/Java code
Native architecture support
New Architecture support
```

Compare:

```text
Before dependency
vs
After dependency
```

If the dependency contains native code, a successful JavaScript install does not prove Android compatibility.

---

# 17. Failure: iOS build fails in Xcode

### First rule

Find the first meaningful Xcode error.

Typical categories:

```text
Swift/Objective-C compilation
C/C++ compilation
Signing
Provisioning
Pods
Deployment target
Entitlements
Info.plist
Native module
Framework linking
```

### Check

```text
Xcode version
React Native version
iOS deployment target
Pods
native dependencies
signing configuration
```

Do not update Xcode and every dependency before identifying the actual error.

---

# 18. Failure: CocoaPods installation fails

### Symptoms

```text
pod install failed
Unable to find compatible version
Pod dependency conflict
Could not find podspec
```

### Check

```text
Podfile
Podfile.lock
iOS deployment target
React Native version
native dependency
podspec
CocoaPods version
Ruby environment
```

If the failure started after adding a native package:

```text
Inspect package
→ inspect podspec
→ inspect required iOS version
→ inspect React Native compatibility
```

Do not delete `Podfile.lock` automatically.

A lockfile change can hide the original dependency-resolution problem.

---

# 19. Failure: Pods work locally but fail in CI

### Check

```text
CocoaPods version
Ruby version
Podfile.lock
Xcode version
deployment target
repository state
CI cache
```

The safest starting point is to make local and CI toolchain versions explicit.

Do not solve the problem by committing generated or machine-specific files unless the project's architecture requires them.

---

# 20. Failure: iOS native dependency changed but Pods were not updated

### Symptoms

```text
Header not found
Undefined symbols
Framework missing
Pod not installed
```

### Check

```text
package.json
Podfile
Podfile.lock
native module configuration
```

After a native dependency change, update native dependencies using the workflow expected by the project.

Then:

```text
Install/update pods
→ build
→ test
```

Do not manually edit generated pod files to hide the underlying dependency problem.

---

# 21. Failure: signing fails on iOS

### Symptoms

```text
Signing certificate not found
Provisioning profile mismatch
No profiles for ...
Code signing failed
Entitlements mismatch
```

### Check

```text
Bundle identifier
Apple team
Signing certificate
Provisioning profile
Entitlements
Capabilities
Xcode signing settings
CI credentials
```

The most common conceptual mistake is treating these as separate unrelated values.

They must agree:

```text
App identity
+
Team
+
Certificate
+
Provisioning
+
Entitlements
```

Do not create new certificates or profiles just because Xcode reports a signing error.

First determine whether the existing signing setup is correct.

See:

```text
signing/ios/
```

---

# 22. Failure: Android signing fails

### Symptoms

```text
Keystore not found
Alias not found
Wrong password
Release signing config missing
```

### Check

```text
Keystore path
Alias
Store password
Key password
Gradle properties
CI secret injection
release signingConfig
```

Do not regenerate a production signing key casually.

For Google Play releases, understand the difference between:

```text
upload key
```

and:

```text
Google Play app signing key
```

React Native's official Android publishing guide documents the upload-key model and recommends keeping signing credentials out of Git.

---

# 23. Failure: R8 / ProGuard breaks release

### Symptoms

```text
Debug works
Release crashes
Class not found
Method not found
Reflection-based library fails
```

### Check

```text
R8/minification
ProGuard rules
Native library requirements
Reflection
Code generation
Release-only configuration
```

React Native's Android publishing documentation warns that code shrinking can require library-specific rules and should be thoroughly tested.

### Fix

Prefer:

```text
Identify missing class/rule
→ add narrow rule
→ rebuild
→ verify
```

Avoid:

```text
Keep everything
```

as the first fix.

Broad keep rules reduce shrinking effectiveness and can hide the actual problem.

---

# 24. Failure: Hermes-only crash

### Symptoms

```text
Debug works with one JS engine
Release crashes
Runtime error references Hermes
```

### Check

```text
Hermes configuration
React Native version
native module compatibility
release build
minification
source map
```

Capture the actual stack trace.

Do not disable Hermes immediately.

First establish whether:

```text
Hermes
```

is the cause or simply where the exception becomes visible.

---

# 25. Failure: release crash stack is unreadable

Release JavaScript stacks may contain minified function names and offsets.

Example:

```text
p@1:132161
f@1:131854
```

Do not debug from these values alone.

Generate and retain the source map for the exact build.

React Native's release debugging documentation explains how to use source maps and `metro-symbolicate` to translate release stack traces back to source locations.

Example:

```bash
npx metro-symbolicate
```

Android source-map example:

```bash
adb logcat -d | npx metro-symbolicate <exact-source-map>
```

The source map must correspond to the exact crashing build/commit.

---

# 26. Failure: crash happens only after upgrade

### Symptoms

```text
Previous release works
New release installs
Existing users crash
Fresh install works
```

This strongly suggests an upgrade-state problem.

Check:

```text
Local storage schema
Database migration
Async storage state
Secure storage
Cached state
Feature flags
Old serialized data
Authentication state
Native storage
```

Test:

```text
Old production build
→ create realistic state
→ install new build
→ launch
→ execute critical flows
```

Do not validate only with a fresh install.

---

# 27. Failure: app crashes on startup

Use this sequence:

```text
Crash on launch
      ↓
Native crash or JS crash?
      ↓
Native
→ device/system logs
→ native module initialization
→ signing/configuration

JS
→ source map
→ Metro/bundle
→ startup code
```

Check recent changes first:

```text
Native dependency
Environment
Navigation initialization
Push setup
Analytics
Authentication
Storage migration
```

Do not rewrite the application startup sequence without evidence.

---

# 28. Failure: app works on one Android architecture but not another

### Symptoms

```text
arm64 works
x86 emulator fails
```

or:

```text
physical device works
emulator fails
```

Check:

```text
ABI
native libraries
NDK
third-party native dependency
build configuration
```

React Native supports architecture-specific development builds and provides `--active-arch-only` to speed local Android development. That optimization should not accidentally become the production artifact configuration.

Before release, verify that the production artifact supports the intended device architectures.

---

# 29. Failure: New Architecture breaks a native library

### Symptoms

```text
Works with old architecture
Fails after enabling New Architecture
```

### Check

```text
Native dependency support
React Native version
library version
TurboModule/Fabric support
library maintenance status
```

The correct fix may be:

```text
Upgrade dependency
```

or:

```text
Replace dependency
```

rather than changing unrelated build settings.

If the project is intentionally using the New Architecture, test all important native dependencies against it.

---

# 30. Failure: iOS deployment target conflict

### Symptoms

```text
deployment target mismatch
Pod requires newer iOS version
Xcode build error
```

### Check

```text
Project deployment target
Pod deployment target
Native dependency requirements
Supported devices
Product support policy
```

Do not raise the minimum iOS version without understanding which users will lose support.

Do not lower it below a native dependency's actual requirement.

---

# 31. Failure: deep links work in debug but not release

### Check

```text
URL scheme
Universal Links / App Links
Associated domains
Intent filters
Signing
Entitlements
Application ID / bundle ID
Production domain
```

Test:

```text
App not installed
App installed but terminated
App in background
App already open
```

A release-only deep-link problem is often configuration, not React Native navigation code.

See:

```text
integrations/deep-links/
integrations/universal-links/
integrations/app-links/
testing/deep-links.md
```

---

# 32. Failure: push notifications work in development but not production

### Check

```text
Production credentials
APNs environment
Firebase configuration
Bundle ID / application ID
Device token registration
Notification permissions
Server credentials
Notification payload
```

Test:

```text
Fresh install
Permission granted
Foreground
Background
Terminated
Notification tap
Token refresh
```

Do not assume a development notification proves the production setup is correct.

See:

```text
integrations/push-notifications/
testing/push-notifications.md
```

---

# 33. Failure: environment points to staging

### Symptoms

```text
Production app calls staging API
Wrong analytics project
Wrong OAuth redirect
Wrong feature flags
```

### Check

```text
Build configuration
Environment variables
Native config
CI environment
Release profile
Bundled JavaScript
```

Remember:

```text
Release build
≠
Production configuration
```

A release artifact can be perfectly valid technically and still be configured for the wrong backend.

---

# 34. Failure: environment variable is undefined

### Check

```text
Variable name
Loading mechanism
Build-time vs runtime configuration
CI environment
Native configuration
Bundling
```

For client-side React Native code:

```text
Anything bundled into the app
```

should be treated as visible to the user.

Do not solve a missing variable by putting a secret directly into the JavaScript bundle.

---

# 35. Failure: local build works, CI build fails

Use a comparison table:

| Area | Local | CI |
|---|---|---|
| Node | ? | ? |
| Package manager | ? | ? |
| Java | ? | ? |
| Gradle | ? | ? |
| Xcode | ? | ? |
| CocoaPods | ? | ? |
| Android SDK | ? | ? |
| Environment | ? | ? |
| Secrets | ? | ? |
| Lockfile | ? | ? |
| Git commit | ? | ? |
| Cache | ? | ? |

Find the first real difference.

Do not assume CI is "random".

Most CI failures are deterministic differences in:

```text
toolchain
environment
dependencies
credentials
repository state
```

---

# 36. Failure: CI release uses the wrong commit

### Symptoms

```text
Artifact does not contain latest code
Version is unexpected
Release notes do not match
```

### Check

```text
Checkout step
Git SHA
branch/tag
workflow trigger
build context
uncommitted local changes
```

Record the exact commit in the release evidence.

The release artifact should always be traceable to source.

---

# 37. Failure: CI cache causes stale build

### Symptoms

```text
Old dependency
Old native configuration
Old generated file
Different result between cache hit/miss
```

### Check

```text
Cache key
lockfile
Gradle cache
Node dependency cache
CocoaPods cache
generated native state
```

Try one controlled cache-invalidated build.

If the clean build succeeds:

```text
Do not stop there.
```

Determine why the cache was stale and fix the cache key or invalidation rule.

React Native's build documentation notes that CI native builds should be treated carefully with caching and warns about poisoned cache problems.

---

# 38. Failure: clean build fixes the problem

A clean build is useful evidence.

It does **not** automatically identify the root cause.

If:

```text
Dirty build → fails
Clean build → succeeds
```

ask:

```text
What stale state was being reused?
```

Potential causes:

```text
Gradle cache
Metro cache
Pods
generated native files
build output
dependency state
```

Do not make "clean everything" the permanent fix.

---

# 39. Failure: `node_modules` deletion fixes the problem

Same rule:

```text
It is evidence, not a diagnosis.
```

Check:

```text
lockfile
package manager
dependency tree
postinstall scripts
native dependencies
```

If reinstalling changes the dependency tree, inspect the lockfile and package-manager behavior.

Production releases should be reproducible without relying on repeated manual cleanup.

---

# 40. Failure: native project is out of sync

This commonly happens after:

```text
React Native upgrade
Native dependency upgrade
Config change
Prebuild
Manual native edits
```

Compare:

```text
JavaScript/config source
vs
ios/
vs
android/
```

Ask:

```text
Is this project generated?
Are native files committed?
Are native changes intentional?
Which source is authoritative?
```

Do not overwrite native directories until you know whether they contain intentional manual changes.

---

# 41. Failure: React Native upgrade breaks the build

Treat an upgrade as a migration, not a package bump.

Use:

```text
Current version
      ↓
Target version
      ↓
Upgrade Helper
      ↓
Dependency changes
      ↓
Native template changes
      ↓
Build iOS
      ↓
Build Android
      ↓
Tests
      ↓
Real-device testing
```

React Native's official upgrade guide explicitly recommends using Upgrade Helper to identify dependency and project-file changes.

Do not skip native template changes.

---

# 42. Failure: dependency upgrade breaks only one platform

Example:

```text
Android works
iOS fails
```

or:

```text
iOS works
Android fails
```

Check platform-specific changes:

```text
iOS
→ Podspec
→ deployment target
→ entitlements
→ Swift/Obj-C
→ Xcode

Android
→ Gradle
→ manifest
→ Kotlin/Java
→ SDK
→ ABI
```

Do not roll back the entire dependency tree until the platform-specific change is understood.

---

# 43. Failure: build succeeds but app cannot install

### Android

Check:

```text
Application ID conflict
Signing
ABI
Minimum SDK
Existing installed package
Device Android version
```

### iOS

Check:

```text
Bundle ID
Provisioning
Signing
Device registration
Deployment target
Entitlements
```

An artifact that builds successfully is not necessarily installable on the intended test device.

---

# 44. Failure: release artifact is wrong

Before submission, inspect:

```text
Application ID
Version
Build number / version code
Signing
Environment
Bundle contents
Target platform
Build variant
```

For Android, React Native's official publishing guide identifies the release AAB as the production artifact generated by the release build process.

Do not upload an artifact simply because the filename says:

```text
release
```

Verify its contents.

---

# 45. Failure: store upload fails

Separate:

```text
Build failure
```

from:

```text
Store validation failure
```

If the artifact exists and is valid, inspect the store's exact message.

Common categories:

```text
Version conflict
Signing
Application identity
Target API
Missing metadata
Policy declaration
Unsupported configuration
```

Do not change application code for a store metadata problem.

---

# 46. Failure: production crash cannot be reproduced locally

Collect:

```text
App version
Build number
Platform
OS version
Device
Git commit
Crash stack
Source map
Relevant logs
User action before crash
```

Then classify:

```text
JavaScript
Native
Network
Storage
Dependency
Environment
```

Do not reproduce only with the latest source code if the crashing version was built from an older commit.

Use the exact source state whenever possible.

---

# 47. Release crash symbolication

For React Native JavaScript crashes:

```text
Crash
 ↓
Exact app version
 ↓
Exact Git commit
 ↓
Exact source map
 ↓
metro-symbolicate
 ↓
Source location
```

React Native's official release debugging documentation emphasizes that the source map must correspond to the exact crashing build because small source changes can significantly alter offsets.

Keep release artifacts and source maps according to the project's observability policy.

---

# 48. Failure: app hangs or freezes

Do not immediately classify a hang as a JavaScript infinite loop.

Possible causes:

```text
Main-thread native work
JS thread blockage
Synchronous storage
Large rendering operation
Network wait incorrectly handled
Native module deadlock
Startup initialization
Memory pressure
```

Collect:

```text
Device logs
Performance traces
Crash/ANR reports
Recent release diff
```

For Android, inspect ANR information when available.

For iOS, inspect relevant crash/hang diagnostics.

---

# 49. Failure: memory problem appears only in release

Check:

```text
Large assets
Image handling
Navigation state
Lists
Native modules
Caches
Release optimization
Hermes
Device memory
```

Test on:

```text
Low-memory device
Real production-like data
Long sessions
Background/foreground cycles
```

Do not rely on a high-end development machine.

---

# 50. Failure: app size suddenly increases

Compare:

```text
Previous artifact
vs
Current artifact
```

Look for:

```text
Native dependency
Large asset
ABI configuration
Debug resources
Duplicate libraries
Unused native code
Bundled data
```

React Native's build documentation notes that building multiple Android ABIs increases artifact size, and development-only architecture optimizations should not accidentally become the production configuration.

Measure before optimizing.

---

# 51. Failure: Android release uses only one ABI

This can happen when a developer carries a local development optimization into production.

Check:

```text
reactNativeArchitectures
abiFilters
splits
Gradle configuration
CI overrides
```

A development command such as:

```bash
npx react-native run-android --active-arch-only
```

is intended to speed local development. It should not accidentally define the production artifact's supported architectures.

---

# 52. Failure: iOS app launches but native feature is unavailable

Check:

```text
Entitlement
Info.plist
Permission
Capability
Native dependency
Initialization
Signing profile
```

Examples:

```text
Camera
Location
Push notifications
Bluetooth
Background modes
Associated domains
```

A JavaScript permission request alone does not guarantee the native capability is correctly configured.

---

# 53. Failure: permission behavior differs in production

Check:

```text
AndroidManifest.xml
Info.plist
iOS entitlements
Runtime permission request
Application ID / Bundle ID
Release signing
```

Test:

```text
Fresh install
Permission denied
Permission granted
Permission revoked
App restarted
```

Do not test only the "permission granted" path.

---

# 54. Failure: native module works after reinstall but not upgrade

This usually points toward:

```text
stored state
migration
native initialization
cached configuration
old persisted data
```

Test:

```text
Old app
→ real state
→ upgrade
→ launch
```

Do not conclude that the native module itself is broken without checking persisted state.

---

# 55. Failure: wrong app opens after deep link

Check:

```text
Application ID
Bundle ID
URL scheme
Intent filters
Associated domains
Installed application variants
```

Multiple development/staging applications can make this especially confusing.

Use distinct identifiers for distinct independently installed applications where the product requires it.

---

# 56. Failure: release build contains development configuration

Check:

```text
Build variant
Environment variables
Gradle configuration
Xcode configuration
Scheme
Bundle configuration
API URLs
Analytics
Feature flags
```

A release configuration should be explicit.

Do not depend on:

```text
"NODE_ENV=production"
```

as proof that every native and application setting is production-ready.

---

# 57. Failure: signing secret accidentally committed

If a signing credential or secret was committed:

```text
1. Stop using the exposed credential.
2. Determine where it was exposed.
3. Revoke/rotate it where supported.
4. Remove it from active configuration.
5. Clean repository history if required.
6. Audit CI logs and access.
7. Verify the replacement credential.
```

Do not simply delete the file in a new commit.

Git history may still contain it.

Never ask an AI agent to print the secret while debugging.

---

# 58. Failure: AI agent gives a wrong fix

AI can help diagnose:

```text
logs
dependency changes
configuration
release diffs
```

but its output is not authoritative.

Use:

```text
AI analysis
   ↓
Evidence check
   ↓
Official documentation
   ↓
Developer verification
   ↓
Change
```

For release-impacting changes:

```text
AI assists
+
Human verifies
+
Human approves
```

Do not give an AI agent unrestricted access to:

```text
signing keys
production secrets
store credentials
production infrastructure
destructive commands
```

---

# 59. AI debugging prompt

Use this when an AI tool is helping diagnose a React Native failure:

```text
Analyze this React Native release failure.

Project:
<project>

React Native:
<version>

Platform:
<iOS / Android>

Build type:
<debug / release / CI / store>

Commit:
<git SHA>

Command:
<command>

Error:
<relevant logs>

Recent changes:
<diff or summary>

Determine:

1. First meaningful error
2. Failure layer
3. Confirmed facts
4. Most likely root cause
5. Alternative causes
6. Smallest safe fix
7. Verification steps
8. Whether a native rebuild is required

Rules:

- Do not guess.
- Separate evidence from hypotheses.
- Do not recommend unrelated upgrades.
- Do not request secrets.
- Do not modify production configuration automatically.
- Use official documentation for volatile framework/platform requirements.
```

---

# 60. What not to do

Avoid these common shortcuts.

## Do not delete the lockfile

It can create a different dependency tree and hide the original issue.

## Do not upgrade everything

A large upgrade makes the failure harder to isolate.

## Do not clean everything first

Cleaning is a diagnostic tool, not a root-cause analysis.

## Do not ignore peer dependency warnings

Determine whether the warning affects the actual release.

## Do not assume npm installation proves native compatibility

Native packages require native builds and testing.

## Do not change signing credentials casually

Signing identities can be difficult to recover.

## Do not disable security checks to make CI pass

Fix the underlying configuration.

## Do not commit secrets

Especially:

```text
keystores
certificates with private keys
passwords
tokens
service-account credentials
```

## Do not let AI publish production releases automatically

Keep a human approval boundary.

---

# 61. Minimal recovery strategy

When the project is badly broken:

```text
1. Stop making unrelated changes.
2. Record the current Git SHA.
3. Capture the exact failure.
4. Identify the last known-good commit.
5. Compare the changes.
6. Reproduce the failure.
7. Revert only the likely breaking change if appropriate.
8. Verify the build.
9. Fix forward with a focused change.
```

If the previous release is healthy:

```text
Known-good release
        ↓
Compare current release
        ↓
Identify regression
```

This is usually faster than rebuilding the entire project configuration from scratch.

---

# 62. When to clean

Cleaning is appropriate when the evidence suggests stale generated state.

Examples:

```text
Generated build output is inconsistent
Pods are stale after a native dependency change
Gradle output is stale
Metro is serving stale state
```

Use the narrowest cleanup first.

Examples:

```text
Android build output
Pods
Metro cache
```

before:

```text
Entire machine
All global caches
Entire dependency tree
```

After cleaning:

```text
Rebuild
→ verify
→ determine whether the cleanup actually fixed the issue
```

---

# 63. When to rollback

Rollback is appropriate when:

```text
Current release causes a serious production regression
+
previous release is known-good
```

Examples:

```text
Critical startup crash
Broken authentication
Payment failure
Severe data corruption risk
Major navigation failure
```

If the issue is native:

```text
Rollback/mitigate current store rollout
→ build corrected native version
→ test
→ release
```

If the issue is JavaScript-only and the application uses a compatible OTA mechanism:

```text
rollback/fix compatible update
```

Do not assume React Native itself provides OTA updates. That is a separate deployment capability.

---

# 64. Release failure decision tree

```text
Did the build fail?
        │
        ├── Yes
        │    ↓
        │  Native/toolchain/dependency/config?
        │
        └── No
             ↓
          Does the app crash?
             │
             ├── Yes
             │    ↓
             │  Native or JavaScript?
             │
             └── No
                  ↓
               Wrong behavior?
                  │
                  ├── Environment/config
                  ├── Dependency
                  ├── Platform integration
                  └── Application logic
```

Then:

```text
Can it be reproduced?
        │
        ├── Yes → isolate regression
        │
        └── No
             ↓
        collect production evidence
```

---

# 65. Release failure matrix

| Symptom | First checks | Avoid first |
|---|---|---|
| Metro cannot resolve module | package/import/Metro config | deleting everything |
| Metro connection failure | Metro/network/device | production changes |
| Android Gradle failure | first Gradle error/toolchain | random upgrades |
| iOS Xcode failure | first Xcode error/signing/pods | reinstalling Xcode |
| Pods fail | Podfile/lockfile/dependency | deleting lockfile |
| Debug works, release fails | release config/R8/native modules | assuming JS bug |
| Signing fails | identity/credentials/profile | generating new keys |
| App crashes after upgrade | persisted state/migration | fresh-install-only testing |
| Release JS crash | source map/exact commit | debugging minified offsets |
| CI fails, local works | environment/toolchain/cache | changing source blindly |
| Store rejects build | exact store error | unrelated code changes |
| Wrong backend | environment/build config | changing API code |
| Native module breaks | package/native config | force-installing dependencies |
| App size grows | artifact diff/dependencies/ABIs | random optimization |
| Production crash | exact version/commit/logs | testing latest source only |

---

# 66. Release verification after a fix

A fix is not complete when:

```text
the build passes.
```

Verify:

```text
[ ] Original failure is gone
[ ] Build succeeds
[ ] Release artifact is generated
[ ] Artifact has correct version
[ ] Artifact has correct application identity
[ ] Signing is correct
[ ] Production environment is correct
[ ] Real device test passes
[ ] Upgrade test passes if relevant
[ ] Critical user flow passes
[ ] CI passes
[ ] No new regression was introduced
```

For store-bound releases:

```text
[ ] Store test track / TestFlight validation passes
[ ] Release evidence is recorded
[ ] Rollback/mitigation path is known
```

---

# 67. Release evidence

For difficult failures, retain:

```text
Git SHA
React Native version
Platform
OS version
Device
Build command
CI run
Error logs
Crash stack
Source map
Dependency diff
Native diff
Final fix
Verification result
```

This turns a one-off failure into reusable project knowledge.

---

# 68. Common failure ownership

When a failure happens, route it to the right layer.

```text
JavaScript / Metro
→ application / React Native layer

Native module
→ dependency + native platform layer

Gradle
→ Android build layer

CocoaPods / Xcode
→ iOS build layer

Signing
→ release credentials layer

CI
→ automation/toolchain layer

Store
→ publishing layer

Crash
→ runtime/observability layer
```

Do not ask the wrong layer to solve the problem.

For example:

```text
Google Play metadata rejection
```

does not require:

```text
React Native architecture changes.
```

---

# 69. Definition of done

A React Native failure is considered resolved when:

```text
Root cause understood
        +
Minimal fix applied
        +
Build verified
        +
Release artifact verified
        +
Affected platform tested
        +
Relevant upgrade/fresh-install path tested
        +
CI verified
        +
Production impact checked
```

The goal is not:

```text
"the command stopped failing."
```

The goal is:

```text
"The actual failure is understood, the fix is narrow,
the release artifact is valid, and the affected user path works."
```

---

# Related documentation

### Foundations

- `foundations/mobile-release-lifecycle.md`
- `foundations/project-configuration.md`
- `foundations/release-environments.md`
- `foundations/versioning.md`
- `foundations/device-support.md`
- `foundations/dependency-management.md`

### React Native

- `frameworks/react-native/README.md`
- `frameworks/react-native/local-setup.md`
- `frameworks/react-native/android-release.md`
- `frameworks/react-native/ios-release.md`
- `frameworks/react-native/signing.md`
- `frameworks/react-native/fastlane.md`

### Expo

- `frameworks/expo/README.md`
- `frameworks/expo/build.md`
- `frameworks/expo/eas.md`
- `frameworks/expo/credentials.md`
- `frameworks/expo/ci-cd.md`
- `frameworks/expo/common-failures.md`
- `frameworks/expo/updates.md`

### Testing

- `testing/unit.md`
- `testing/integration.md`
- `testing/e2e.md`
- `testing/device-testing.md`
- `testing/android.md`
- `testing/ios.md`
- `testing/deep-links.md`
- `testing/push-notifications.md`
- `testing/upgrade-migrations.md`
- `testing/release-smoke-tests.md`

### Troubleshooting

- `troubleshooting/build-fails.md`
- `troubleshooting/signing-fails.md`
- `troubleshooting/archive-fails.md`
- `troubleshooting/upload-fails.md`
- `troubleshooting/ci-fails.md`
- `troubleshooting/crash-after-release.md`
- `troubleshooting/push-notifications-broken.md`
- `troubleshooting/deep-links-broken.md`

### Security

- `signing/security/`
- `privacy-compliance/`
- `ai/security/secret-protection.md`
- `ai/security/agent-permissions.md`

### AI

- `ai/agents/debugging-agent.md`
- `ai/agents/release-auditor.md`
- `ai/workflows/debugging.md`
- `ai/workflows/release-audit.md`
- `ai/security/destructive-actions.md`
- `ai/orchestration/human-approval.md`

---

# Official sources

Use official documentation as the authority for current framework and platform behavior.

## React Native

- React Native documentation: https://reactnative.dev/docs/getting-started
- React Native releases: https://reactnative.dev/releases/
- React Native versioning policy: https://reactnative.dev/releases/versioning-policy
- React Native upgrading: https://reactnative.dev/docs/upgrading
- React Native debugging release builds: https://reactnative.dev/docs/debugging-release-builds
- React Native Android publishing: https://reactnative.dev/docs/signed-apk-android.html
- React Native build performance: https://reactnative.dev/docs/build-speed

## Android

- Android Developers: https://developer.android.com/
- Android app signing: https://developer.android.com/studio/publish/app-signing
- Android App Bundles: https://developer.android.com/guide/app-bundle
- Google Play Console Help: https://support.google.com/googleplay/android-developer/

## Apple

- Apple Developer: https://developer.apple.com/
- App Store Connect Help: https://developer.apple.com/help/app-store-connect/

## Gradle

- Gradle documentation: https://docs.gradle.org/

---

**Last verified:** August 11, 2026

React Native, Xcode, Gradle, Android SDK, JDK, CocoaPods, native libraries, and store requirements change over time. For volatile behavior, verify the current official documentation for the exact React Native and platform versions used by the project.
