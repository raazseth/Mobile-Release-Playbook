# Expo Common Failures

Expo release failures usually fall into a small number of categories:

```text
Configuration
Dependencies
Native projects
Config plugins
Environment
Signing
EAS Build
CI/CD
Runtime
Store submission
```

The first rule is simple:

> **Find the first real failure before changing anything.**

A later error is often only a consequence of an earlier one. Expo's current EAS troubleshooting guidance recommends inspecting the earliest failed build phase and its logs before moving on. It also distinguishes build failures from runtime failures because they require different debugging paths. citeturn0search0

This guide focuses on **Expo + React Native + EAS**.

It is intentionally practical. It tells you what to check, what not to change blindly, and how to get from symptom to verified fix.

---

# 1. The troubleshooting loop

Use this sequence for almost every failure:

```text
Failure
  ↓
Identify exact command/build
  ↓
Read logs
  ↓
Find first meaningful error
  ↓
Classify failure
  ↓
Reproduce locally if possible
  ↓
Make the smallest fix
  ↓
Run validation
  ↓
Rebuild
  ↓
Verify the resulting app
```

Do not start with:

```text
Delete node_modules
Delete everything
Upgrade Expo
Change five packages
Regenerate native projects
```

unless the evidence points there.

---

# 2. First classify the failure

Ask:

```text
Did the build fail?
        │
        ├── Yes
        │    ↓
        │  Build troubleshooting
        │
        └── No
             ↓
          Does the app crash,
          hang, or behave incorrectly?
             │
             └── Runtime troubleshooting
```

Expo's official troubleshooting guide makes the same distinction: a build can fail during compilation, or it can succeed and then fail at runtime. citeturn0search0

---

# 3. Failure categories

Use this classification:

| Category | Typical symptom |
|---|---|
| Configuration | Wrong identifier, missing property, invalid config |
| Dependency | Expo/RN package mismatch |
| Config plugin | Prebuild/native configuration failure |
| Native | Xcode/Gradle/Android/iOS error |
| Environment | Missing or wrong variable |
| Signing | Credentials or provisioning failure |
| EAS | Remote build/configuration issue |
| CI/CD | Workflow, secret, or environment problem |
| Runtime | Crash, hang, blank screen |
| Store | Upload or review rejection |
| Update | OTA/runtime mismatch |

The category determines where you should look next.

---

# 4. Build failed: read the build logs first

If EAS Build fails:

```text
EAS dashboard
    ↓
Open build
    ↓
Open failed phase
    ↓
Read earliest meaningful error
```

Do not assume every line marked:

```text
[stderr]
```

is an error.

Expo explicitly notes that CLI tools commonly write warnings and diagnostics to stderr. citeturn0search0

Look for:

```text
error
failed
exception
cannot
undefined
missing
incompatible
```

but use the surrounding context to determine whether the line is actually the root failure.

---

# 5. Build failed: use the first real error

Example:

```text
Gradle failed
    ↓
Task X failed
    ↓
Dependency Y could not resolve
```

The useful error is usually:

```text
Dependency Y could not resolve
```

not:

```text
Gradle failed
```

Likewise:

```text
Xcode build failed
    ↓
Signing failed
```

The actionable failure is:

```text
Signing failed
```

not:

```text
Xcode build failed
```

The first meaningful failure is usually the best starting point.

---

# 6. Run Expo Doctor

When dependency or configuration behavior is suspicious:

```bash
npx expo-doctor
```

Expo Doctor checks areas including:

- app configuration
- `package.json`
- dependency compatibility
- configuration files
- React Native Directory compatibility
- synchronization between app config and native directories where applicable. citeturn0search2

Use it early for:

```text
Expo SDK upgrade
Native dependency change
Unexpected native error
Package mismatch
Config mismatch
```

Do not treat a clean Expo Doctor result as proof that the app is release-ready.

It is a diagnostic tool, not an end-to-end release test.

---

# 7. Failure: Expo dependency mismatch

### Symptom

You see errors such as:

```text
package version mismatch
incompatible Expo package
native module version mismatch
```

### Check

```bash
npx expo-doctor
```

Then:

```bash
npx expo install --check
```

Inspect:

```text
package.json
lockfile
Expo SDK
React Native version
native dependencies
```

### Fix

Use Expo's dependency management tooling rather than manually guessing package versions.

For example:

```bash
npx expo install <package>
```

Then:

```bash
npm install
npx expo-doctor
```

The exact command depends on the package manager and dependency involved.

### Verify

```text
Dependency check
→ tests
→ development build
→ preview/release build
```

Do not upgrade the entire dependency tree just because one package is incompatible.

---

# 8. Failure: package works in JavaScript but breaks native build

### Symptom

```text
import works
```

but:

```text
EAS Build fails
```

This often indicates that the package has native requirements.

Check:

```text
Does the package require a config plugin?
Does the package support the current Expo SDK?
Does it support the target platform?
Does it require a native setup step?
```

Expo's EAS troubleshooting guidance specifically points to config plugins and project dependencies as common causes of native build errors in projects using Continuous Native Generation. citeturn0search0

### Verify

Run:

```bash
npx expo-doctor
```

and inspect the package's official documentation.

Do not assume:

```text
npm install
```

is sufficient for every native package.

---

# 9. Failure: config plugin error

### Symptom

You see errors around:

```text
config plugin
withX
mod
prebuild
plugin resolution
```

### Check

Run:

```bash
EXPO_DEBUG=1 npx expo prebuild
```

Expo documents `EXPO_DEBUG=1` as a way to print the plugin stack and show which mods ran and in what order. citeturn0search7

For verbose static plugin resolution errors:

```bash
EXPO_CONFIG_PLUGIN_VERBOSE_ERRORS=1 npx expo prebuild
```

Use this mainly when the normal error does not reveal enough information. citeturn0search7

### Fix

Identify:

```text
Plugin
→ package
→ configuration
→ native modification
```

Then correct the plugin configuration.

Do not remove the plugin simply to make the build pass if the native feature depends on it.

---

# 10. Failure: prebuild generates unexpected native changes

### Symptom

After:

```bash
npx expo prebuild
```

you see unexpected changes in:

```text
ios/
android/
```

### Check

```bash
npx expo config --type prebuild
```

This can help inspect the evaluated configuration before native code generation. citeturn0search7

Then inspect:

```text
app.config.ts
config plugins
plugin options
Expo SDK
native directories
```

### Important

If the project uses Continuous Native Generation:

```text
app config
+
config plugins
        ↓
prebuild
        ↓
native project
```

Do not manually patch generated files without understanding whether the next prebuild will overwrite the change.

---

# 11. Failure: clean prebuild does not solve the problem

You may see advice like:

```bash
npx expo prebuild --clean
```

This removes generated native directories before compiling. Expo documents it as a tool for regenerating native projects. citeturn0search7

Use it deliberately.

Do not use:

```bash
npx expo prebuild --clean
```

as a universal fix.

If the actual problem is:

```text
wrong config
wrong plugin
wrong dependency
wrong environment
```

cleaning the native project only recreates the same problem.

---

# 12. Failure: local build works, EAS build fails

This is a common and important failure.

Expo's troubleshooting guidance says to compare:

```text
Build tool versions
Environment variables
Uploaded source files
```

between local and EAS environments. citeturn0search0

Check:

```text
Node
npm / Yarn / pnpm / Bun
Xcode
Java
Android SDK
Expo SDK
EAS CLI
environment variables
lockfile
source archive
```

### Recommended test

Start with a clean clone:

```bash
git clone <repository>
cd <repository>
npm ci
```

Then reproduce the release configuration.

This helps expose:

```text
machine-specific state
missing files
uncommitted changes
local environment variables
```

---

# 13. Failure: EAS build works locally but cloud build fails

The inverse can also happen.

Check:

```text
EAS environment variables
EAS build profile
build image/tool versions
credentials
source archive
```

If needed, reproduce locally using:

```bash
eas build --local
```

Expo provides local EAS builds specifically as a way to debug failures that are difficult to understand on the hosted service. citeturn0search4

The goal is not to permanently replace cloud builds.

The goal is to shorten the debugging loop.

---

# 14. Failure: JavaScript bundle fails

### Symptom

Native compilation may be fine, but the JavaScript bundle fails.

Try:

```bash
npx expo export
```

Expo recommends this as a faster way to isolate JavaScript bundling problems from the rest of the EAS Build process. citeturn0search0

Then fix:

```text
syntax errors
imports
module resolution
environment values
Metro configuration
```

Repeat:

```text
expo export
→ fix
→ export
```

until the bundle succeeds.

Then retry the full build.

---

# 15. Failure: app builds but crashes immediately

This is a runtime failure.

Do not keep debugging the EAS build logs if the build itself succeeded.

Check:

```text
Crash logs
Device logs
Production error reporting
Native logs
JavaScript logs
```

Then reproduce the release build locally where possible.

Expo recommends using production/release-mode builds to reproduce failures that only appear after a successful build. citeturn0search0

---

# 16. Failure: app hangs on splash screen

### Symptom

```text
Build succeeds
→ app launches
→ splash screen never finishes
```

Possible areas:

```text
startup JavaScript
native initialization
environment configuration
network request
authentication
push setup
native module
Expo Updates
```

Do not immediately change splash-screen configuration.

First determine:

```text
Is JavaScript running?
Is native startup completing?
Is a network request blocking startup?
Is the production environment correct?
```

Use:

```text
device logs
crash reporting
production bundle testing
```

Expo's troubleshooting guide specifically identifies "works in Expo Go but hangs on the splash screen in my build" as a runtime troubleshooting case. citeturn0search0

---

# 17. Failure: app works in Expo Go but not in release build

Do not treat Expo Go as proof that the native application is correct.

Expo Go and your production binary can differ in:

```text
native modules
native configuration
permissions
runtime configuration
release mode
environment
```

Test using:

```text
development build
preview build
production build
```

when native behavior matters.

A production bundle can also be tested faster with:

```bash
npx expo start --no-dev
```

to approximate production JavaScript bundling behavior. Expo documents this as a way to test how the JavaScript portion behaves in production mode. citeturn0search0

---

# 18. Failure: wrong environment in release build

### Symptom

The production app points to:

```text
staging API
```

or:

```text
development service
```

### Check

```text
eas.json
EAS environment
app.config.ts
EXPO_PUBLIC_* variables
CI configuration
```

EAS environments are scoped to:

```text
development
preview
production
```

and the selected environment controls which variables are available to the job. citeturn0search3turn0search5

Example:

```json
{
  "build": {
    "production": {
      "environment": "production"
    }
  }
}
```

### Verify

Inspect the resolved configuration before rebuilding.

Do not assume the profile name alone tells you which environment is being used.

---

# 19. Failure: environment variable is empty in EAS

### Symptom

Locally:

```text
API_URL exists
```

On EAS:

```text
API_URL undefined
```

### Cause

Local `.env` files are not automatically available to remote EAS jobs just because they exist on your machine.

EAS provides its own environment variable system for cloud builds and workflows. citeturn0search3

### Check

```bash
eas env:list --environment production
```

Then verify:

```text
variable name
environment
visibility
build profile
```

### Fix

Create or update the variable in the correct EAS environment.

---

# 20. Failure: secret variable is used in client code

### Symptom

A secret is configured as an EAS secret but is required by:

```text
React component
client-side JavaScript
EXPO_PUBLIC_*
```

This is not a safe secret architecture.

Anything embedded into the client can be inspected.

Expo explicitly states that values included in client-side code should be treated as public, even if the source environment variable was configured as a secret. citeturn0search3turn0search5

### Fix

Move privileged operations to a server.

Use:

```text
Mobile app
→ authenticated API
→ server-side secret
→ external service
```

not:

```text
Mobile app
→ embedded secret
→ external service
```

---

# 21. Failure: `.env` works locally but causes cloud confusion

Keep local environment files out of Git:

```text
.env
.env.local
```

when they contain machine-specific or sensitive values.

Expo recommends using `.gitignore` for local environment files and using EAS environment variables for cloud builds. citeturn0search3turn0search8

Avoid having multiple competing sources silently override each other.

Use an explicit model:

```text
Local development
→ local environment

EAS build
→ EAS environment

Production
→ production EAS environment
```

---

# 22. Failure: iOS signing error

### Symptoms

Examples:

```text
provisioning profile
certificate
entitlements
signing
credentials
```

### Check

```text
Bundle ID
Apple team
Distribution certificate
Provisioning profile
App Store Connect configuration
EAS credentials
```

Do not change the Bundle ID simply because signing failed.

The identifier must match the intended App Store Connect application.

See:

```text
signing/ios/
```

for the full signing workflow.

---

# 23. Failure: Android signing error

### Symptoms

Examples:

```text
keystore
upload key
signing config
credential
```

### Check

```text
Application ID
Keystore
Upload key
Play App Signing
EAS credentials
```

Do not generate a new production key casually.

First determine whether:

```text
existing signing identity
```

must be preserved.

See:

```text
signing/android/
```

for the signing workflow.

---

# 24. Failure: Android build fails in Gradle

### First step

Find the earliest meaningful Gradle error.

Do not start by changing:

```text
Gradle version
Java version
Android Gradle Plugin
```

unless the logs indicate a compatibility problem.

### Check

```text
Expo SDK
React Native
native dependency
Java
Gradle
Android Gradle Plugin
compile/target SDK
config plugin
```

If the error started after installing a native package, investigate that package first.

---

# 25. Failure: iOS build fails in Xcode

### First step

Find the first meaningful Xcode error.

Check:

```text
signing
native dependency
Pod installation
deployment target
entitlements
Info.plist
config plugin
Swift/Objective-C compilation
```

If the failure started after adding a native package:

```text
Inspect package
→ inspect plugin
→ inspect native changes
```

Do not update Xcode or every dependency immediately.

---

# 26. Failure: CocoaPods problem

### Symptoms

```text
pod install
Pods
dependency resolution
native module
```

Check:

```text
Expo SDK
React Native
native dependency
iOS deployment target
Podspec
config plugin
```

If using CNG, remember that native projects may be generated from app configuration and config plugins.

The right fix may be:

```text
dependency configuration
```

rather than:

```text
manual Podfile modification
```

---

# 27. Failure: native directories are out of sync

### Symptom

Your configuration says:

```text
Bundle ID A
```

but native files contain:

```text
Bundle ID B
```

Expo Doctor can check for app-config fields that are not synchronized when native directories exist. citeturn0search2

Check:

```bash
npx expo-doctor
```

Then determine whether the project uses:

```text
CNG / generated native projects
```

or:

```text
committed native projects
```

Do not assume one model.

---

# 28. Failure: `eas.json` profile is missing

### Symptom

An EAS workflow reports:

```text
Missing build profile in eas.json
```

Expo's current EAS Workflows troubleshooting documentation identifies this as a common workflow failure. Prepackaged build jobs use the `production` profile unless another profile is specified. citeturn0search1

### Check

```text
eas.json
build.<profile>
```

Example:

```json
{
  "build": {
    "production": {}
  }
}
```

Then verify the profile actually matches the intended environment and artifact.

---

# 29. Failure: EAS workflow does not start

If using EAS Workflows:

```text
Open workflow run
→ inspect trigger
```

Check:

```text
GitHub connection
workflow file location
branch
tag
path filters
event type
```

Expo's current workflow troubleshooting guide also notes that workflow files must be inside:

```text
.eas/workflows/
```

and that some events read the workflow from the triggering commit or default branch depending on the event. citeturn0search1

Also check skip markers such as:

```text
[eas skip]
[skip eas]
[no eas]
```

which can prevent certain event-triggered workflows from running. citeturn0search1

---

# 30. Failure: GitHub Actions can build but EAS cannot see variables

Remember the boundary:

```text
GitHub Actions environment
        ≠
EAS Build environment
```

A variable set only in GitHub Actions does not automatically become an EAS Build environment variable.

Use EAS environment variables when the value is needed by the remote EAS build.

Expo documents this distinction directly in its EAS environment variable guidance. citeturn0search3

---

# 31. Failure: CI secret unavailable

Check:

```text
Repository secret
Organization secret
GitHub environment secret
Job environment
Workflow permissions
```

For production:

```text
job
→ production environment
→ approval
→ secret becomes available
```

Do not move the secret into source code just to make the workflow pass.

---

# 32. Failure: production build succeeds but app is wrong

This is more dangerous than a build failure.

Example:

```text
Build:
SUCCESS

App:
wrong API
wrong bundle ID
wrong analytics project
wrong push configuration
```

The build system successfully produced the artifact.

Your configuration was wrong.

Check:

```text
resolved Expo config
environment
artifact
native configuration
runtime behavior
```

The release artifact, not the source file, is the final verification point.

---

# 33. Failure: Android app cannot install

### Check

```text
Application ID
APK/AAB type
Signing
minSdk
Device Android version
ABI
Existing installed app identity
```

If testing an APK:

```text
Check whether it is compatible with the device.
```

If testing an AAB:

```text
Remember that AAB is a distribution format, not the normal direct-install artifact.
```

Do not change the application ID just to make a test install easier.

---

# 34. Failure: iOS app cannot install on test device

Check:

```text
Signing
Provisioning
Device registration
Distribution method
Bundle ID
Entitlements
```

Determine whether the build is intended for:

```text
development
internal distribution
TestFlight
App Store
```

The installation path depends on the distribution model.

---

# 35. Failure: push notifications work in development but not production

Check separately:

```text
Build
+
credentials
+
entitlements
+
environment
+
notification provider
+
device token
```

Do not assume:

```text
Expo Go works
```

means:

```text
production push works
```

Verify the production binary and production notification configuration.

---

# 36. Failure: deep links work locally but not in production

Check:

```text
scheme
Universal Links
Associated Domains
Android App Links
intent filters
domain verification
bundle/application ID
production configuration
```

Also verify:

```text
cold start
warm start
already-installed state
```

A deep link implementation is not complete until the real production artifact is tested.

---

# 37. Failure: payments work in development but fail in release

Check:

```text
environment
store product IDs
bundle/application ID
entitlements
billing configuration
backend verification
test account
```

Do not put payment secrets in the application.

The app should request privileged operations through the appropriate backend or platform mechanism.

See:

```text
testing/payments.md
monetization/
```

---

# 38. Failure: OTA update breaks the app

If using Expo Updates:

```text
Native build
+
runtimeVersion
+
update channel/environment
```

must be compatible.

If an OTA update contains native assumptions unsupported by the installed binary:

```text
the update may fail or behave incorrectly
```

Use:

```text
native build
→ runtime compatibility
→ update
```

as the mental model.

For native changes:

```text
new native build
```

is generally required.

---

# 39. Failure: build cache appears stale

If a build behaves differently after a known configuration or dependency change:

```text
First inspect inputs.
```

Then, if cache is actually suspected:

```text
invalidate relevant cache
→ rebuild
→ compare
```

Do not permanently disable caching because one build behaved strangely.

Cache problems should be demonstrated, not assumed.

---

# 40. Failure: build works on one developer machine

This usually indicates hidden local state.

Compare:

```text
Node
package manager
lockfile
environment variables
native tooling
Expo SDK
Xcode
Java
Android SDK
local modifications
```

Use a clean clone:

```bash
git clone <repository>
npm ci
```

If the project cannot reproduce from a clean clone, the build process is not sufficiently reproducible.

---

# 41. Failure: release build differs from development

This is expected in some ways.

Production may differ in:

```text
minification
__DEV__
logging
environment
native configuration
signing
permissions
store distribution
```

To test the JavaScript production bundle:

```bash
npx expo start --no-dev
```

Expo documents this as a way to test production-like JavaScript bundling behavior without performing the entire native release build. citeturn0search0

Then test the actual release binary for native behavior.

---

# 42. Failure: build takes too long

First determine where time is spent:

```text
Queue
→ dependency install
→ native build
→ archive
→ upload
```

Do not immediately add infrastructure.

Possible improvements:

```text
dependency caching
smaller dependency graph
stable build profile
appropriate EAS plan/resources
avoid unnecessary builds
```

If PR changes do not affect native code, avoid triggering expensive native builds unnecessarily.

---

# 43. Failure: repeated CI builds

A common mistake is:

```text
every push
→ Android build
→ iOS build
```

for every branch.

This wastes:

```text
time
money
build capacity
developer attention
```

Prefer:

```text
PR
→ cheap validation

Relevant native change
→ preview build

Release
→ production build
```

Use path/change-based decisions only when they are reliable enough to justify the complexity.

---

# 44. Failure: AI gives a confident but wrong fix

Do not trust an AI diagnosis because it sounds plausible.

Use:

```text
AI hypothesis
    ↓
Find evidence
    ↓
Verify against official docs
    ↓
Test minimal fix
    ↓
Rebuild
```

Good AI output:

```text
"The log shows X.
This usually indicates Y.
I cannot prove Z from the provided log."
```

Bad AI output:

```text
"Upgrade everything and delete all caches."
```

The repository's AI guidance treats AI output as untrusted until inspected, verified, tested, and approved.

---

# 45. AI debugging prompt

Use this when feeding logs to ChatGPT, Claude, Codex, or another coding agent:

```text
You are debugging an Expo / React Native release failure.

Do not modify files.

Analyze only the evidence provided.

Project:
<project>

Expo SDK:
<version>

React Native:
<version>

Platform:
<ios / android>

Build profile:
<profile>

Environment:
<environment>

Command:
<command>

Recent changes:
<changes>

Logs:
<logs>

Return exactly:

1. FAILURE TYPE
2. FIRST REAL ERROR
3. EVIDENCE
4. LIKELY ROOT CAUSE
5. ALTERNATIVE HYPOTHESES
6. MINIMAL FIX
7. VERIFICATION STEPS
8. RELEASE RISK

Rules:

- Separate facts from hypotheses.
- Do not invent commands.
- Do not invent Expo requirements.
- Prefer official Expo, Apple, and Android documentation.
- Do not recommend unrelated upgrades.
- Do not expose or request secrets.
- Do not change production configuration.
- If evidence is insufficient, say NEEDS MORE EVIDENCE.
```

This makes AI useful without turning it into an uncontrolled release operator.

---

# 46. What not to do

Avoid these as default troubleshooting actions:

```text
Delete package-lock.json
Delete yarn.lock
Delete pnpm-lock.yaml
Upgrade Expo blindly
Upgrade React Native blindly
Upgrade every dependency
Regenerate native projects blindly
Change bundle ID
Generate new signing keys
Disable security checks
Commit secrets
Ignore failing tests
Publish because "the build passed"
```

Each can hide the actual problem or create a second one.

---

# 47. Minimal recovery strategy

When stuck:

```text
1. Save the failing logs.
2. Record the exact command/profile.
3. Record the commit.
4. Record recent changes.
5. Run Expo Doctor.
6. Reproduce locally.
7. Compare environments.
8. Find the first real error.
9. Make one targeted change.
10. Re-run the smallest useful test.
11. Rebuild.
12. Verify the app.
```

Do not make multiple unrelated changes between attempts.

You lose causality when you do.

---

# 48. When to use a clean clone

Use a clean clone when:

```text
works on one machine
fails in CI

works locally
fails on EAS

native project seems inconsistent

dependency state is suspicious

uncommitted changes may matter
```

Example:

```bash
git clone <repository>
cd <repository>
npm ci
npx expo-doctor
```

Then reproduce the failure.

This is one of the fastest ways to separate repository problems from developer-machine state.

---

# 49. When to use local EAS Build

Use:

```bash
eas build --local
```

when:

```text
EAS cloud logs are not enough
native toolchains are available
you need a faster debugging loop
```

Expo documents local builds as a debugging option for reproducing hosted EAS build failures. citeturn0search4

Do not assume local and hosted builds are identical.

Compare:

```text
tool versions
environment variables
source archive
credentials
```

---

# 50. When to escalate

Escalate when you have:

```text
Exact failure
+
Relevant logs
+
Reproduction steps
+
Environment/tool versions
+
Recent changes
```

A useful issue contains:

```text
Expo SDK
React Native
EAS CLI
Platform
Build profile
Commit
Command
Expected result
Actual result
First meaningful error
Minimal reproduction
```

Do not open an issue saying:

```text
Build doesn't work.
```

---

# 51. Common failure decision tree

```text
Something failed
      │
      ├── Build failed?
      │      │
      │      └── Yes
      │          ↓
      │      Read earliest failed phase
      │          ↓
      │      Classify
      │          │
      │          ├── Dependency
      │          ├── Config plugin
      │          ├── Native
      │          ├── Signing
      │          └── Environment
      │
      └── Build succeeded?
             │
             └── App fails
                 ↓
              Runtime
                 ↓
              Device logs
                 ↓
              Production config
                 ↓
              Native/runtime dependency
```

---

# 52. Fast diagnostic commands

## Project health

```bash
npx expo-doctor
```

## Dependency compatibility

```bash
npx expo install --check
```

## Resolved config

```bash
npx expo config
```

## Prebuild configuration

```bash
npx expo config --type prebuild
```

## Debug config plugins

```bash
EXPO_DEBUG=1 npx expo prebuild
```

## Production JavaScript behavior

```bash
npx expo start --no-dev
```

## JavaScript bundle isolation

```bash
npx expo export
```

## Local Android release build

```bash
npx expo run:android --variant release
```

## Local iOS release build

```bash
npx expo run:ios --configuration Release
```

## Local EAS build

```bash
eas build --local
```

These commands should be used according to the project setup and current Expo tooling. Expo's current troubleshooting documentation explicitly recommends several of them for narrowing build and runtime failures. citeturn0search0turn0search4

---

# 53. Environment diagnostics

For EAS environments:

```bash
eas env:list --environment development
```

```bash
eas env:list --environment preview
```

```bash
eas env:list --environment production
```

Use these to verify that the variable exists in the environment actually used by the job. EAS environment variables are explicitly scoped by environment. citeturn0search3turn0search6

Never print secret values.

---

# 54. Release failure record

For a production failure, record:

```text
Date
Platform
Version
Build number
EAS build ID
Git commit
Build profile
Environment
Command
First error
Root cause
Fix
Verification
```

Example:

```text
Platform:
Android

Version:
1.4.0

Build:
42

Profile:
production

Environment:
production

Root cause:
Incompatible native dependency

Fix:
Updated dependency to supported version

Verification:
Preview build + production build + real device
```

This creates useful release history instead of repeating the same debugging work.

---

# 55. Definition of done

A failure is not fixed because:

```text
the command exits successfully
```

It is fixed when:

```text
Root cause understood
        +
Targeted change made
        +
Validation passes
        +
Build succeeds
        +
Artifact is correct
        +
Real-device behavior works
        ↓
   FAILURE RESOLVED
```

For production failures:

```text
Fix
→ rebuild
→ verify
→ release
→ monitor
```

---

# Related documentation

### Foundations

- `foundations/project-configuration.md`
- `foundations/release-environments.md`
- `foundations/identifiers.md`
- `foundations/versioning.md`
- `foundations/dependency-management.md`

### Expo

- `frameworks/expo/app-config.md`
- `frameworks/expo/build.md`
- `frameworks/expo/ci-cd.md`
- `frameworks/expo/eas.md`
- `frameworks/expo/submit.md`

### Signing

- `signing/ios/`
- `signing/android/`
- `signing/security/`

### Testing

- `testing/release-smoke-tests.md`
- `testing/device-testing.md`
- `testing/ios.md`
- `testing/android.md`
- `testing/network.md`
- `testing/offline.md`
- `testing/deep-links.md`
- `testing/push-notifications.md`
- `testing/payments.md`
- `testing/upgrade-migrations.md`

### Release engineering

- `release-engineering/eas/`
- `release-engineering/github-actions/`
- `release-engineering/environment-management.md`
- `release-engineering/release-pipelines.md`

### Release strategy

- `release-strategy/hotfixes.md`
- `release-strategy/emergency-release.md`
- `release-strategy/staged-rollouts.md`
- `release-strategy/feature-flags.md`

### Post-release

- `post-release/monitoring.md`
- `post-release/crash-analysis.md`
- `post-release/incident-response.md`
- `post-release/rollback.md`
- `post-release/hotfix.md`

### AI

- `ai/workflows/debugging.md`
- `ai/workflows/release-audit.md`
- `ai/workflows/release-preparation.md`
- `ai/security/secret-protection.md`
- `ai/security/destructive-actions.md`
- `ai/evals/hallucination-tests.md`

---

# Official sources

Use official documentation as the authority for current Expo behavior.

### Expo

- EAS Build troubleshooting: https://docs.expo.dev/build-reference/troubleshooting/
- EAS local builds: https://docs.expo.dev/build-reference/local-builds/
- Expo Doctor: https://docs.expo.dev/develop/tools/
- EAS environment variables: https://docs.expo.dev/eas/environment-variables/
- EAS environment variable usage: https://docs.expo.dev/eas/environment-variables/usage/
- EAS environment variable management: https://docs.expo.dev/eas/environment-variables/manage/
- EAS Workflows troubleshooting: https://docs.expo.dev/eas/workflows/troubleshooting/
- Config plugin debugging: https://docs.expo.dev/config-plugins/development-and-debugging/
- Config plugins: https://docs.expo.dev/config-plugins/introduction/
- Prebuild: https://docs.expo.dev/workflow/prebuild/
- App configuration: https://docs.expo.dev/versions/latest/config/app/
- Environment variables: https://docs.expo.dev/guides/environment-variables/

### Apple

- Apple Developer: https://developer.apple.com/
- App Store Connect Help: https://developer.apple.com/help/app-store-connect/

### Android

- Android Developers: https://developer.android.com/
- Android build: https://developer.android.com/build
- Google Play Console Help: https://support.google.com/googleplay/android-developer/

### React Native

- React Native documentation: https://reactnative.dev/docs/getting-started

**Last verified:** August 11, 2026

Expo SDK behavior, EAS CLI commands, build infrastructure, native tooling, environment handling, and store requirements change over time. Re-check the official documentation for the project's Expo SDK and tooling versions before changing a production workflow.
