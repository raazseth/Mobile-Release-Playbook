# Expo Development Builds

A development build is **your app's own development version**, built with the native modules and configuration your project actually uses.

It is different from Expo Go.

```text
Expo Go
→ fixed native runtime
→ quick learning and prototyping

Development Build
→ your native runtime
→ your native configuration
→ your native libraries
→ real project development
```

Expo recommends development builds for production-grade Expo applications because they let the project use native libraries and native configuration that are not part of the fixed Expo Go app. Development builds include `expo-dev-client`. citeturn0search0turn0search11

This guide covers:

- when to use a development build
- Expo Go vs development builds
- `expo-dev-client`
- local development builds
- EAS development builds
- device and simulator builds
- development build profiles
- rebuilding after native changes
- sharing development builds
- CI/CD
- EAS Update
- common failures
- AI-assisted workflows

---

# 1. What a development build is

A development build is a debug build of your application that includes `expo-dev-client`.

Think of it as:

```text
Expo Go
    ↓
Generic Expo development app

Development Build
    ↓
Your actual application
+
Your native dependencies
+
Your native configuration
+
Developer tooling
```

Expo describes a development build as essentially your own version of Expo Go, with freedom to use native libraries and change native configuration. citeturn0search0

---

# 2. Why development builds matter

Expo Go is useful when you are getting started, but it has a fixed set of native libraries.

A real application often needs:

```text
Custom native modules
Custom config plugins
Native permissions
Custom app configuration
Native SDKs
Custom URL schemes
Push configuration
Native authentication
Native payments
Other platform-specific functionality
```

A development build lets the native application match your project.

The development loop becomes:

```text
Change JS/TS
    ↓
Metro
    ↓
Development Build
    ↓
Fast refresh
```

When native code or native configuration changes:

```text
Change native dependency/config
    ↓
Rebuild development build
    ↓
Install updated binary
    ↓
Continue development
```

Expo documents that adding a library containing native code requires rebuilding the development client because the native code is not added to the already-installed binary automatically. citeturn0search7

---

# 3. Expo Go vs development build

| Capability | Expo Go | Development Build |
|---|---|---|
| Quick JavaScript development | Yes | Yes |
| Fast Refresh | Yes | Yes |
| Your app's native configuration | Limited | Yes |
| Custom native libraries | Limited to included libraries | Yes |
| `expo-dev-client` tooling | No | Yes |
| Custom config plugins | Not the same production path | Yes |
| Production-like native runtime | Limited | Much closer |
| Recommended for production-grade Expo apps | No | Yes |

The key difference is:

```text
Expo Go
→ Expo's native application

Development Build
→ Your native application
```

Expo's current FAQ describes Expo Go as a fixed playground-style native app and development builds as the development environment for production-grade Expo applications. citeturn0search12

---

# 4. When to use Expo Go

Use Expo Go when:

```text
You are learning Expo
You are prototyping
You only need libraries supported by Expo Go
You want the fastest possible initial setup
```

Do not build your entire release workflow around Expo Go if your application depends on native functionality that requires a custom binary.

---

# 5. When to use a development build

Use a development build when:

```text
You are building a real application
You use custom native modules
You use config plugins
You need custom native configuration
You need to test production-relevant native behavior
You want your development runtime to match your actual app
```

For a production-oriented Expo project, the normal path should become:

```text
Development Build
    ↓
Preview Build
    ↓
Production Build
```

rather than:

```text
Expo Go
    ↓
Production Build
```

---

# 6. Install `expo-dev-client`

Install it using Expo's dependency tooling:

```bash
npx expo install expo-dev-client
```

Expo's current documentation recommends this package for development builds. citeturn0search0turn0search5

After installation, the project can create a development build containing the development client.

---

# 7. Local development build

A local development build uses your own native toolchain.

Typical requirements:

### Android

```text
Android Studio
Android SDK
Android device or emulator
```

### iOS

```text
macOS
Xcode
iOS Simulator
or
iPhone
```

Expo documents local development builds as compiling the application with Expo CLI and the native development tools installed on your machine. citeturn0search0

---

# 8. Build locally

For Android:

```bash
npx expo run:android
```

For iOS:

```bash
npx expo run:ios
```

Expo's build tools automatically run prebuild when native directories do not exist, so you normally do not need to run prebuild manually for the first build in that situation. citeturn0search0

---

# 9. Start the development server

After installing the development build:

```bash
npx expo start
```

The development build can connect to the Metro development server.

The flow is:

```text
Terminal
    ↓
npx expo start
    ↓
Metro
    ↓
Development Build
    ↓
JavaScript bundle
```

The development client launcher can help connect to development servers and switch between available development targets. citeturn0search0turn0search7

---

# 10. Local build workflow

A normal local workflow looks like:

```text
1. Install dependencies
2. Install expo-dev-client
3. Build development binary
4. Install binary
5. Start Metro
6. Open development build
7. Connect to Metro
8. Develop
```

Commands:

```bash
npx expo install expo-dev-client
```

```bash
npx expo run:android
```

or:

```bash
npx expo run:ios
```

Then:

```bash
npx expo start
```

---

# 11. EAS development build

You can also compile development builds using EAS Build.

This is useful when:

```text
You do not want to install native build tools
You want cloud builds
You want iOS builds from a non-macOS machine
You want repeatable build artifacts
You want to share development builds
```

Expo currently documents three ways to create development builds:

```text
Local Expo CLI
EAS cloud build
EAS local build
```

All produce the same type of development build, but the build environment differs. citeturn0search0

---

# 12. Configure EAS

If the project is not already configured:

```bash
eas build:configure
```

Expo's current default EAS configuration creates build profiles for:

```text
development
preview
production
```

The development profile is configured with:

```json
{
  "developmentClient": true,
  "distribution": "internal"
}
```

This produces a development client suitable for internal installation rather than store submission. citeturn0search1turn0search3

---

# 13. Recommended development profile

A typical configuration:

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

The important distinction is:

```text
developmentClient: true
```

This tells EAS to create a development build containing `expo-dev-client`.

```text
distribution: internal
```

makes the development artifact suitable for internal distribution rather than App Store / Google Play submission. citeturn0search1turn0search3

---

# 14. Build with EAS

For both platforms:

```bash
eas build --profile development --platform all
```

For Android:

```bash
eas build --profile development --platform android
```

For iOS:

```bash
eas build --profile development --platform ios
```

Expo's current EAS documentation uses the development profile for this workflow. citeturn0search1turn0search13

---

# 15. Development build vs preview build

Do not confuse these.

## Development

```text
Developer tools
+
expo-dev-client
+
Metro development server
+
Internal distribution
```

## Preview

```text
No development tools
+
Production-like configuration
+
Internal testing
```

A preview build is closer to what you will distribute, while a development build is optimized for active development.

Expo's current EAS Build documentation distinguishes development builds from preview builds in exactly this way. citeturn0search3

---

# 16. Development build vs production build

### Development build

Optimized for:

```text
Debugging
Fast iteration
Native development
Local development
Developer tools
```

### Production build

Optimized for:

```text
Store distribution
Production configuration
Release performance
Production signing
Real users
```

Do not submit a development build to the stores.

Expo documents development builds as builds that include developer tools and are not intended for app-store submission. citeturn0search3

---

# 17. Android development builds

Android development builds can run on:

```text
Physical Android device
Android Emulator
```

A development APK can be installed directly for internal development.

A typical workflow:

```bash
eas build --profile development --platform android
```

Then install the resulting development build.

For local development:

```bash
npx expo run:android
```

---

# 18. iOS development builds

iOS development builds can target:

```text
Physical iPhone
iOS Simulator
```

For a simulator-specific EAS profile:

```json
{
  "build": {
    "development-simulator": {
      "developmentClient": true,
      "distribution": "internal",
      "ios": {
        "simulator": true
      }
    }
  }
}
```

Expo documents simulator-specific development profiles for EAS Build. citeturn0search3turn0search6

For local development:

```bash
npx expo run:ios
```

---

# 19. Physical device vs simulator

Use a simulator/emulator for:

```text
Fast UI development
Navigation
Basic interaction
Automated development workflows
```

Use physical devices for:

```text
Camera
Push notifications
Bluetooth
Location
Biometrics
Performance
Device-specific behavior
Real network conditions
Store-like installation behavior
```

Do not treat simulator success as proof that native device functionality works.

---

# 20. Development build on a physical iPhone

A local build has an important advantage.

Expo's current documentation states that local compilation is the way to install a development build on an iPhone without a paid Apple Developer account. citeturn0search0

For EAS cloud development builds on physical iOS devices, normal Apple signing and device distribution requirements apply.

Do not assume:

```text
iOS Simulator
=
physical iPhone
```

They have different native and device constraints.

---

# 21. Connect to Metro

Once the development build is installed:

```bash
npx expo start
```

Then open the development build.

Depending on the setup, it can:

```text
detect a local development server
```

or:

```text
scan the QR code
```

Expo's current development-build launcher supports connecting to detected local servers and switching between development targets. citeturn0search7

---

# 22. Rebuild when native code changes

This is one of the most important rules.

If you add:

```text
native dependency
```

or change:

```text
native configuration
```

you usually need to rebuild the development client.

Example:

```bash
npx expo install expo-secure-store
```

The JavaScript dependency may install immediately, but the native code must exist inside the development binary.

So:

```text
Install native package
        ↓
Rebuild development client
        ↓
Install new binary
        ↓
Start Metro
```

Expo explicitly documents this requirement. citeturn0search7

---

# 23. What usually does not require a rebuild

Normal JavaScript or TypeScript changes usually do not require a new development binary.

For example:

```text
React component
Navigation logic
State management
API calls
Business logic
Styles
Most JavaScript changes
```

The loop is:

```text
Edit
→ Save
→ Fast Refresh
```

This is the main productivity benefit of development builds.

---

# 24. What usually requires a rebuild

Rebuild when the installed binary needs to change.

Common examples:

```text
Native dependency added
Native dependency removed
Config plugin changed
Native permissions changed
Bundle identifier changed
Android application ID changed
Native app configuration changed
Native SDK configuration changed
iOS capabilities changed
Android manifest configuration changed
Native code changed
```

The exact rebuild requirement depends on what changed.

If the change affects native runtime behavior, treat it as a rebuild candidate.

---

# 25. Development build and app configuration

The development binary is produced from the project's configuration.

Important inputs can include:

```text
app.json
app.config.js
app.config.ts
config plugins
native dependencies
eas.json
environment
platform configuration
```

The resulting binary is what matters.

Do not assume that changing a configuration file changes an already-installed binary.

---

# 26. Development builds and environments

Keep development configuration separate from production.

Example:

```text
Development Build
→ development API
→ development analytics
→ development push configuration
→ development feature flags

Preview Build
→ staging API
→ shared testing services

Production Build
→ production API
→ production services
```

The environment model in this repository uses:

```text
Development
Preview
Production
```

as the default practical model. See:

```text
foundations/release-environments.md
```

A development build should not accidentally point to production systems unless that is an explicit, controlled requirement.

---

# 27. Development build and EAS environment

A build profile and an environment are related but different concepts.

For example:

```text
Build profile:
development

Environment:
development
```

is straightforward.

But the names can differ:

```text
Build profile:
internal-dev

Environment:
development
```

The important requirement is that the relationship is explicit and reviewable.

See:

```text
foundations/release-environments.md
```

and:

```text
frameworks/expo/ci-cd.md
```

---

# 28. Development build and EAS Update

A development build can also be used to preview compatible EAS Updates.

The model is:

```text
Development Build
        ↓
expo-dev-client
        ↓
EAS Update
        ↓
Preview update
```

Expo documents that `expo-dev-client` can launch compatible published EAS Updates from a development build. citeturn0search2

This can be useful for:

```text
PR previews
Testing published JavaScript updates
Verifying update configuration
Testing channels
```

---

# 29. Native compatibility still matters

EAS Update does not remove native compatibility requirements.

Think:

```text
Native binary
        +
Compatible JavaScript update
```

not:

```text
Any JavaScript
        +
Any binary
```

If JavaScript expects native functionality that the installed binary does not contain:

```text
crash
or
broken behavior
```

can result.

Expo recommends managing runtime compatibility so updates are only delivered to compatible native runtimes. citeturn0search14

---

# 30. Development builds and runtime versions

If the native runtime changes:

```text
Add native library
Remove native library
Change native configuration
```

the runtime compatibility may change.

With EAS Update:

```text
Native runtime
        ↓
runtimeVersion
        ↓
Compatible updates
```

See:

```text
frameworks/expo/updates.md
```

if the repository adds a dedicated Expo Updates guide.

Do not publish an update to a binary that cannot support it.

---

# 31. Sharing development builds

Development builds can be shared with teammates.

A typical flow:

```text
Developer
    ↓
EAS Build
    ↓
Development artifact
    ↓
EAS dashboard
    ↓
Teammate
    ↓
Install
    ↓
Connect to development server
```

Expo documents downloading development builds from the EAS dashboard or using EAS CLI. citeturn0search8

For EAS CLI:

```bash
eas build:run --profile development
```

The exact profile name should match the project's configuration. citeturn0search8

---

# 32. Development build distribution is not store distribution

Internal distribution is for:

```text
Developers
QA
Designers
Product stakeholders
Internal testers
```

It is not the same as:

```text
TestFlight
Google Play testing
App Store
Google Play production
```

Use the right distribution method for the stage of the release.

---

# 33. Development build lifecycle

A practical lifecycle:

```text
Create project
    ↓
Install expo-dev-client
    ↓
Configure development profile
    ↓
Build development client
    ↓
Install on device
    ↓
Run Metro
    ↓
Develop
    ↓
Native change?
    │
    ├── No → Fast Refresh
    │
    └── Yes
          ↓
       Rebuild
          ↓
       Continue
```

When the feature is ready:

```text
Development
    ↓
Preview Build
    ↓
Testing
    ↓
Production Build
```

---

# 34. EAS development builds in CI

Development builds can be automated.

For example:

```text
Pull Request
    ↓
CI validation
    ↓
EAS development build
    ↓
Install / share
    ↓
Developer or QA testing
```

EAS Workflows supports build jobs for development profiles. Expo documents development-build workflows using `.eas/workflows/` and the `build` job type. citeturn0search6turn0search13

Do not build every development commit unless the project actually benefits from it.

Cloud builds cost time and resources.

Use them where they remove real friction.

---

# 35. Practical CI strategy

A simple setup:

```text
Pull Request
    ↓
Lint / Typecheck / Unit Tests
```

Then, when needed:

```text
Native changes
    ↓
Development Build
```

For release validation:

```text
Release candidate
    ↓
Preview Build
```

For publishing:

```text
Approved release
    ↓
Production Build
```

This avoids turning every source change into an expensive native build.

---

# 36. Development build fingerprints

EAS tooling can identify compatible development builds using fingerprints.

The current EAS CLI includes:

```bash
eas build:dev
```

which can run a development client build when a matching fingerprint exists or create a new one. citeturn0search9

This can reduce unnecessary rebuilding when the existing development binary is still compatible.

Use this as an optimization, not as a substitute for understanding native changes.

---

# 37. Development build debugging

The development client provides a developer menu.

You can open it through supported device gestures or keyboard shortcuts during development.

Expo documents:

```text
Cmd + D
```

on macOS workflows and:

```text
Ctrl + D
```

for the relevant CLI development flow, as well as device gestures. citeturn0search7

Use the development menu for:

```text
Debugging
Developer tools
Development server selection
Other development features
```

---

# 38. Development build networking

A development build may need to connect to a Metro server running on your machine.

Common causes of connection problems:

```text
Device and computer are on different networks
Firewall
VPN
Incorrect host
Metro not running
Local network restrictions
```

First check:

```bash
npx expo start
```

Then verify that the device can reach the development machine.

Do not change application networking configuration before verifying the local development network.

---

# 39. Development build with VPNs

A VPN can interfere with local development networking.

If:

```text
Metro is running
+
device cannot connect
```

check:

```text
VPN
Firewall
Local network
Wi-Fi isolation
Device network
```

Do not immediately change the app's API URL.

The issue may be the development server connection rather than your application backend.

---

# 40. Development build with native dependency changes

Use this sequence:

```text
Add native dependency
        ↓
Install with Expo tooling
        ↓
Check compatibility
        ↓
Rebuild development client
        ↓
Install new binary
        ↓
Start Metro
        ↓
Test
```

For example:

```bash
npx expo install <native-package>
```

Then rebuild:

```bash
npx expo run:android
```

or:

```bash
npx expo run:ios
```

or:

```bash
eas build --profile development --platform <platform>
```

---

# 41. Development build with config plugins

If a package uses a config plugin:

```text
Package
    ↓
Config plugin
    ↓
Native project configuration
    ↓
Development build
```

Changing plugin configuration can require rebuilding.

If a plugin behaves unexpectedly, inspect the resolved Expo configuration and native project changes rather than repeatedly rebuilding without evidence.

See:

```text
frameworks/expo/app-config.md
```

and:

```text
frameworks/expo/common-failures.md
```

---

# 42. Development build with native directories

Expo projects can use Continuous Native Generation or maintain native projects directly.

Do not assume all Expo projects have the same structure.

If the project has:

```text
ios/
android/
```

understand whether they are:

```text
generated
```

or:

```text
actively maintained native projects
```

before editing native files.

The correct development-build workflow depends on the project's native project strategy.

---

# 43. Development build and production parity

A development build is not a production build.

But it should be much closer to the application's real native runtime than Expo Go.

Use:

```text
Development Build
→ native integration testing

Preview Build
→ production-like release testing

Production Build
→ final release artifact
```

Do not use the development build as the only release validation layer.

---

# 44. Development build and secrets

A development build does not make secrets safe.

Anything shipped inside the application can potentially be inspected.

Never embed:

```text
private API keys
database credentials
service-account keys
Apple private keys
Google private keys
production secrets
```

into the client.

Use:

```text
Mobile app
    ↓
Authenticated backend
    ↓
Privileged service
```

for operations that require server-side secrets.

See:

```text
signing/security/secret-storage.md
```

and:

```text
ai/security/secret-protection.md
```

---

# 45. Development builds and production data

Avoid using production data by default.

Prefer:

```text
Development
→ development data

Preview
→ safe test/staging data

Production
→ production data
```

If production access is genuinely required:

```text
explicit approval
+
least privilege
+
read-only where possible
+
auditing
```

A development binary should not automatically receive unrestricted production access.

---

# 46. AI-assisted development-build workflow

AI tools can help with:

```text
Expo configuration inspection
Dependency compatibility checks
EAS profile review
Build-log analysis
Native change detection
Development-build checklists
CI workflow preparation
```

A safe workflow is:

```text
AI analyzes repository
        ↓
AI identifies required native changes
        ↓
Developer reviews
        ↓
AI prepares changes
        ↓
Developer runs validation
        ↓
Development build
        ↓
Real device verification
```

AI should not silently:

```text
rotate signing credentials
change production identifiers
publish production builds
change store permissions
delete credentials
```

---

# 47. AI prompt: development-build audit

Use this with ChatGPT, Claude, Codex, or another coding agent:

```text
Audit this Expo project for development-build readiness.

Inspect:

- package.json
- app.json / app.config.*
- eas.json
- native directories if present
- config plugins
- Expo dependencies
- development environment configuration
- CI workflows

Determine:

1. Whether expo-dev-client is configured
2. Whether a development EAS build profile exists
3. Whether the profile uses developmentClient correctly
4. Whether internal distribution is configured appropriately
5. Whether native dependencies require a rebuild
6. Whether development and production environments are separated
7. Whether development builds could accidentally access production secrets
8. Whether CI has unnecessary production access
9. What is missing
10. What should be fixed first

Rules:

- Do not modify files.
- Do not request or print secrets.
- Separate confirmed facts from assumptions.
- Use official Expo documentation for current behavior.
- Do not recommend unrelated upgrades.
- Give exact file paths and minimal changes.
```

---

# 48. AI build-log debugging

When a development build fails, give the AI:

```text
Expo SDK
React Native version
Platform
Build profile
Recent native dependency changes
Exact command
First meaningful error
Relevant build logs
```

Ask for:

```text
Failure type
First real error
Evidence
Likely cause
Alternative causes
Minimal fix
Verification
```

Do not paste:

```text
private keys
service account JSON
tokens
passwords
```

AI output remains a hypothesis until verified.

---

# 49. Common failure: development client opens but cannot load app

Check:

```text
Metro running?
Device connected?
Network reachable?
Correct development server?
QR/deep link?
```

Start with:

```bash
npx expo start
```

Then reconnect from the development client launcher.

Do not rebuild the native binary if the binary itself is working and the problem is only Metro connectivity.

---

# 50. Common failure: native module is undefined

Example:

```text
NativeModules.X is undefined
```

Likely areas:

```text
Native package installed
but development binary was not rebuilt
```

Check:

```text
Was the package added after the current development build?
Does it require a config plugin?
Is the package compatible with the current Expo SDK?
```

Then:

```text
Rebuild development client
```

Expo explicitly documents rebuilding after adding native-code dependencies. citeturn0search7

---

# 51. Common failure: works in Expo Go but not development build

This usually means the two runtimes differ.

Check:

```text
native dependency
config plugin
native configuration
permissions
environment
app configuration
```

Do not assume the development build is wrong simply because Expo Go worked.

The development build is the more relevant runtime for an application using custom native functionality.

---

# 52. Common failure: development build works but preview fails

Now investigate release differences:

```text
Environment
Build profile
Native configuration
Signing
Minification
Production configuration
Network
API
Permissions
```

This is why preview builds exist.

Development success does not prove release success.

---

# 53. Common failure: preview works but production fails

Focus on:

```text
Production environment
Production credentials
Store configuration
Production API
Production native configuration
Production update channel
```

The build may be technically valid while production configuration is wrong.

Use:

```text
release-readiness
+
production checklist
+
artifact verification
```

before submission.

---

# 54. Common failure: adding a package breaks the build

Use:

```text
1. Identify whether the package has native code.
2. Check Expo SDK compatibility.
3. Check whether a config plugin is required.
4. Run Expo Doctor.
5. Rebuild the development client.
6. Inspect the first native build error.
```

Do not immediately upgrade Expo, React Native, or unrelated packages.

---

# 55. Common failure: iOS simulator build works but device build fails

The simulator and physical device are different targets.

Check:

```text
iOS signing
device provisioning
native capabilities
architecture
device-specific behavior
permissions
```

If using EAS, make sure the profile is actually configured for the intended target.

---

# 56. Common failure: Android emulator works but physical device fails

Check:

```text
USB debugging
device authorization
network
permissions
ABI/device compatibility
Android version
native behavior
```

Do not assume emulator behavior proves physical-device behavior.

---

# 57. Common failure: new iOS device cannot install development build

If a new physical iOS device is registered after an existing development build was created, the build may need to be recreated.

Expo's current team-sharing documentation explicitly notes that newly registered iOS devices require a new development build for installation. citeturn0search8

Use the supported internal-distribution workflow.

---

# 58. Common failure: development build was accidentally submitted

Stop the release workflow.

A development build is not a production artifact.

Verify:

```text
Build profile
developmentClient
distribution
artifact
```

Then build the correct:

```text
preview
or
production
```

artifact.

Do not try to turn a development binary into a production binary through store metadata.

---

# 59. Development build checklist

## Project

- [ ] Expo project is configured.
- [ ] `expo-dev-client` is installed.
- [ ] Native dependencies are compatible.
- [ ] Config plugins are understood.
- [ ] Environment is explicitly configured.

## EAS

- [ ] EAS project is linked.
- [ ] `eas.json` exists.
- [ ] Development profile exists.
- [ ] `developmentClient` is `true`.
- [ ] Internal distribution is configured.
- [ ] Correct platform profile exists.

## Local development

- [ ] Native toolchain is installed when using local builds.
- [ ] Development binary installs.
- [ ] Metro starts.
- [ ] Device can reach Metro.
- [ ] Fast Refresh works.

## Native changes

- [ ] Native dependencies trigger a rebuild.
- [ ] Config changes are reflected in the binary.
- [ ] Native configuration is verified.
- [ ] Device testing is performed where relevant.

## Security

- [ ] Development secrets are protected.
- [ ] Production secrets are not embedded.
- [ ] Production data is not exposed by default.
- [ ] CI does not receive unnecessary production credentials.

## Release progression

- [ ] Development build works.
- [ ] Preview build is tested.
- [ ] Production build is separately verified.
- [ ] Development builds are never submitted to stores.

---

# 60. Recommended Expo development workflow

For a serious Expo project:

```text
                 ┌──────────────────┐
                 │  Source changes  │
                 └────────┬─────────┘
                          ↓
                 ┌──────────────────┐
                 │ Development Build│
                 │ expo-dev-client  │
                 └────────┬─────────┘
                          ↓
                 ┌──────────────────┐
                 │ Local / EAS      │
                 │ development      │
                 └────────┬─────────┘
                          ↓
                 ┌──────────────────┐
                 │ Feature complete │
                 └────────┬─────────┘
                          ↓
                 ┌──────────────────┐
                 │ Preview Build    │
                 └────────┬─────────┘
                          ↓
                 ┌──────────────────┐
                 │ QA / real device │
                 └────────┬─────────┘
                          ↓
                 ┌──────────────────┐
                 │ Production Build │
                 └────────┬─────────┘
                          ↓
                 ┌──────────────────┐
                 │ Store submission │
                 └──────────────────┘
```

The important boundary is:

```text
Development
≠
Preview
≠
Production
```

Each stage has a different purpose.

---

# 61. Definition of done

A development-build setup is ready when:

```text
expo-dev-client installed
        +
development profile configured
        +
development binary builds
        +
binary installs
        +
Metro connection works
        +
Fast Refresh works
        +
native dependency changes are understood
        +
environment is separated
        +
real-device testing is possible
        ↓
DEVELOPMENT BUILD READY
```

The goal is not to make development builds complicated.

The goal is to make the development runtime match the application you are actually going to ship.

---

# Related documentation

### Foundations

- `foundations/project-configuration.md`
- `foundations/release-environments.md`
- `foundations/device-support.md`
- `foundations/dependency-management.md`
- `foundations/versioning.md`

### Expo

- `frameworks/expo/app-config.md`
- `frameworks/expo/build.md`
- `frameworks/expo/ci-cd.md`
- `frameworks/expo/credentials.md`
- `frameworks/expo/common-failures.md`

### Signing

- `signing/ios/`
- `signing/android/`
- `signing/security/`

### Testing

- `testing/device-testing.md`
- `testing/device-matrix.md`
- `testing/ios.md`
- `testing/android.md`
- `testing/network.md`
- `testing/deep-links.md`
- `testing/push-notifications.md`

### Release engineering

- `release-engineering/eas/`
- `release-engineering/environment-management.md`
- `release-engineering/github-actions/`
- `release-engineering/release-pipelines.md`

### Release strategy

- `release-strategy/beta-testing.md`
- `release-strategy/feature-flags.md`
- `release-strategy/hotfixes.md`

### AI

- `ai/workflows/debugging.md`
- `ai/workflows/ai-qa.md`
- `ai/workflows/release-preparation.md`
- `ai/security/secret-protection.md`
- `ai/security/destructive-actions.md`

---

# Official sources

### Expo

- Development builds: https://docs.expo.dev/develop/development-builds/introduction/
- Use a development build: https://docs.expo.dev/develop/development-builds/use-development-builds/
- Development builds FAQ: https://docs.expo.dev/develop/development-builds/faq/
- Configure development build with EAS: https://docs.expo.dev/tutorial/eas/configure-development-build/
- EAS Build configuration: https://docs.expo.dev/build/eas-json/
- EAS CLI: https://docs.expo.dev/eas/cli/
- EAS Workflows development builds: https://docs.expo.dev/eas/workflows/examples/create-development-builds/
- Automate development builds: https://docs.expo.dev/tutorial/cicd/development-builds/
- `expo-dev-client`: https://docs.expo.dev/versions/latest/sdk/dev-client/
- Preview EAS Updates in development builds: https://docs.expo.dev/eas-update/expo-dev-client/
- EAS Update and runtime compatibility: https://docs.expo.dev/build/updates/

### React Native

- React Native documentation: https://reactnative.dev/docs/getting-started

### Android

- Android Developers: https://developer.android.com/

### Apple

- Apple Developer: https://developer.apple.com/

**Last verified:** August 11, 2026

Expo tooling, EAS CLI commands, development-build behavior, native dependencies, and platform requirements change over time. Re-check the official Expo documentation for the project's Expo SDK and tooling versions before changing a development-build workflow.
