# React Native Local Environment Setup

This guide covers what a machine needs so a **release build** — not just a development build — can actually be produced locally.

This is **not** a React Native onboarding tutorial.

```text
"How do I write React Native code?"
    → reactnative.dev, not this guide

"My machine can build the app in debug mode, why does the release build fail?"
    → this guide
```

It focuses on the parts of local setup that matter for release engineering:

```text
Toolchain versions that must match CI
Native build dependencies (Xcode, Gradle, CocoaPods, JDK, Android SDK)
Environment variables the build system depends on
Verifying the machine before trusting a local release build
Where local setup ends and cloud/CI builds take over
```

If the project already builds a debug app on your machine, this guide is for the next question: **can this machine also produce the exact artifact CI produces?**

---

# 1. Why local setup matters for a release, not just development

A working development build proves comparatively little.

```text
Debug build succeeds
    ≠
Release build succeeds
    ≠
Release build matches what CI would produce
```

Debug builds are more forgiving: they skip minification, use debug signing, and often tolerate a slightly mismatched toolchain. A release build exercises the parts of the toolchain that only run in release mode — R8/ProGuard, release signing, Xcode's Release configuration, CocoaPods resolved for the exact deployment target.

See `frameworks/react-native/common-failures.md` §9 ("debug works, release fails") for what goes wrong when this distinction is ignored.

---

# 2. What you actually need, by platform

```text
Both platforms
  Node.js
  Watchman (recommended, especially on macOS)
  A package manager (npm / yarn / pnpm — whatever the project uses)

Android (any OS)
  JDK
  Android Studio (or just the command-line SDK tools)
  Android SDK + build tools
  An emulator or physical device

iOS (macOS only)
  Xcode
  Xcode Command Line Tools
  CocoaPods
  Ruby (usually via a version manager, not the system Ruby)
  iOS Simulator or a physical iPhone
```

> **Important:** iOS builds require macOS. There is no supported way to produce an iOS release build on Windows or Linux locally — those platforms can still build and test Android, and can drive iOS builds through a cloud service (EAS Build, a macOS CI runner, or a Mac in the cloud).

---

# 3. Node.js and the package manager

Install Node using a version manager (`nvm`, `fnm`, `volta`) rather than a single system-wide install, so different projects can pin different Node versions without conflicting.

```bash
node --version
npm --version
```

If the repository has an `.nvmrc` or a `"engines"` field in `package.json`, use that exact version — don't assume "whatever Node I already have" is close enough. A Node version mismatch between your machine and CI is a common source of "works locally, fails in CI" (see `common-failures.md` §35).

Use whichever package manager the project's lockfile indicates:

```text
package-lock.json → npm
yarn.lock         → yarn
pnpm-lock.yaml    → pnpm
```

Do not introduce a second lockfile by running a different package manager than the one the project uses.

---

# 4. Watchman

Watchman is Meta's file-watching service. Metro uses it to detect file changes quickly, especially on large projects.

```bash
# macOS (Homebrew)
brew install watchman

# verify
watchman --version
```

It's not strictly required — Metro falls back to Node's built-in file watching — but its absence is a common cause of slow refresh or missed file-change detection on large projects, especially on macOS.

---

# 5. JDK (Java Development Kit)

Android builds run on the JVM through Gradle, so a JDK is required even though you're not writing Java or Kotlin yourself.

```bash
java -version
```

The required JDK version is tied to the project's Android Gradle Plugin (AGP) and Gradle version, and that pairing changes across React Native releases. Don't assume "the latest JDK" or "whatever I have installed" is correct — check the JDK version the project's `android/build.gradle` and Gradle wrapper actually expect, and install that one. A JDK mismatch produces build failures that look unrelated to Java at all (see `common-failures.md` §13).

Install a JDK through a version manager where possible (e.g. `sdkman`, or Android Studio's bundled JDK) rather than relying on whatever the OS ships.

---

# 6. Android Studio and the Android SDK

You don't strictly need the Android Studio IDE to build a React Native app — the Gradle command line is what actually builds it — but Android Studio is still the easiest way to install and manage:

```text
Android SDK platforms
Android SDK build tools
Android SDK command-line tools
Android emulator images
```

After installing Android Studio, open **SDK Manager** and install:

- the SDK platform matching the project's `compileSdkVersion`
- the build tools version the project expects
- at least one system image if you plan to use an emulator

## Environment variables

The Android build tooling needs to know where the SDK lives:

```bash
# macOS/Linux (add to your shell profile)
export ANDROID_HOME=$HOME/Library/Android/sdk   # macOS default location
export PATH=$PATH:$ANDROID_HOME/emulator
export PATH=$PATH:$ANDROID_HOME/platform-tools
```

```powershell
# Windows (PowerShell)
$env:ANDROID_HOME = "$env:LOCALAPPDATA\Android\Sdk"
$env:Path += ";$env:ANDROID_HOME\platform-tools;$env:ANDROID_HOME\emulator"
```

Verify it resolved correctly:

```bash
adb --version
```

If `adb` isn't found, the environment variable or `PATH` entry is wrong — this is one of the most common Android setup failures, and it silently breaks every subsequent step (device detection, install, logcat).

---

# 7. Android emulator vs. physical device

```text
Emulator
→ fast to set up, good for UI iteration
→ does not prove real-device behavior

Physical device
→ required for camera, real push delivery, real network conditions,
  performance characteristics, device-specific bugs
```

Don't treat emulator success as proof a release is device-ready — see `common-failures.md` §28 for architecture-specific failures that only show up on physical hardware.

For a physical device:

```bash
adb devices
```

confirms the device is connected and authorized. If it isn't listed, check USB debugging is enabled in Developer Options and that you've accepted the device's USB debugging authorization prompt.

---

# 8. Xcode (macOS only)

Install Xcode from the Mac App Store, then install the command-line tools:

```bash
xcode-select --install
```

Verify:

```bash
xcodebuild -version
```

The required Xcode version tracks the React Native version in use, and Apple also requires a current Xcode version to submit to the App Store — check both the project's requirements and Apple's current minimum before assuming an older Xcode install is fine.

Open Xcode at least once after installing it and accept the license agreement — some command-line tooling fails silently if you skip this.

---

# 9. CocoaPods and Ruby

CocoaPods manages iOS native dependencies (`Podfile` / `Podfile.lock`) and is invoked automatically by most React Native build scripts, but it's worth being able to run it directly when debugging.

```bash
pod --version
```

CocoaPods runs on Ruby. Use a Ruby version manager (`rbenv`, `rvm`, `asdf`) rather than macOS's system Ruby — Apple's bundled Ruby changes with OS updates and is not meant to be modified for development use.

If the project has a `Gemfile`, install CocoaPods through Bundler instead of a global gem install, so the CocoaPods version stays pinned to what the project expects:

```bash
bundle install
bundle exec pod install
```

Running a global `pod install` when the project expects a Bundler-pinned CocoaPods version is a common cause of "Pods work locally but fail in CI" (see `common-failures.md` §19).

---

# 10. iOS Simulator vs. physical device

```text
Simulator
→ no Apple Developer account required
→ cannot test camera, push notifications, many hardware-dependent features
→ different performance and memory characteristics than real hardware

Physical iPhone
→ requires code signing (a free Apple ID works for personal device testing;
  a paid Apple Developer Program membership is required for TestFlight/App Store)
→ the only way to validate camera, push, Face ID/Touch ID, real performance
```

Don't validate a release candidate on simulator alone — see `frameworks/react-native/signing.md` for what device-level signing actually requires.

---

# 11. Apple Silicon (M-series) considerations

Most current tooling runs natively on Apple Silicon, but some older native dependencies or Ruby gems may still need Rosetta:

```bash
softwareupdate --install-rosetta
```

If `pod install` or a native dependency's build step fails specifically on Apple Silicon with architecture-related errors, that's the first thing to check — not a CocoaPods reinstall.

---

# 12. Verifying your environment

The React Native CLI includes a built-in environment checker:

```bash
npx react-native doctor
```

It inspects your installed Node, Watchman, JDK, Android SDK, Xcode, and CocoaPods versions against what the project expects, flags what's missing or mismatched, and can offer to install some of them for you.

Run this **before** debugging a confusing build failure — it catches a large share of "my machine isn't set up right" problems before you waste time chasing a symptom.

---

# 13. A machine setup checklist

Use this before trusting a local release build:

## Both platforms

- [ ] Node version matches the project's `.nvmrc` / `engines` field.
- [ ] The correct package manager (matching the lockfile) is used — not a second one.
- [ ] Watchman is installed (macOS/Linux).
- [ ] `npx react-native doctor` reports no unresolved issues.

## Android

- [ ] JDK version matches what the project's Gradle configuration expects.
- [ ] `ANDROID_HOME` is set and `adb` resolves on `PATH`.
- [ ] Required SDK platform and build tools versions are installed.
- [ ] `cd android && ./gradlew -version` succeeds.
- [ ] A release build actually runs: `./gradlew bundleRelease` (or the project's equivalent).

## iOS (macOS only)

- [ ] Xcode is installed, opened once, and its license accepted.
- [ ] Command-line tools are installed (`xcode-select --install`).
- [ ] CocoaPods is installed at the version the project expects (via Bundler if the project uses one).
- [ ] `pod install` (or `bundle exec pod install`) completes cleanly.
- [ ] A signed release archive can actually be produced (see `frameworks/react-native/signing.md`).

---

# 14. Matching your machine to CI

A local release build is only useful as a release signal if it's actually representative of what CI would produce.

```text
Local machine                          CI runner
Node version           ──compare──►    Node version
Package manager version──compare──►    Package manager version
JDK version             ──compare──►    JDK version
Gradle wrapper version  ──compare──►    Gradle wrapper version
Xcode version            ──compare──►    Xcode version
CocoaPods version        ──compare──►    CocoaPods version
```

Use `common-failures.md` §35's comparison table when something builds locally but fails in CI (or the reverse). The fix is almost always a version pinned differently between the two environments, not a "flaky CI" problem.

Where possible, pin these versions in files that both a human and CI read the same way:

```text
.nvmrc               → Node version
.ruby-version         → Ruby version (for CocoaPods/Fastlane)
Gemfile.lock          → exact CocoaPods/Fastlane versions
android/gradle/wrapper/gradle-wrapper.properties → Gradle version
```

This turns "what version should I install?" from a guess into a file you can read.

---

# 15. When you don't need full native tooling

Not every contributor needs the entire native toolchain installed.

```text
Full native toolchain required for:
  - producing a local release build
  - debugging a native build failure
  - testing on a physical device without a cloud build service

Not required for:
  - most day-to-day JavaScript/TypeScript work using Metro + Fast Refresh
  - using a cloud build service (EAS Build, a hosted CI runner) instead
    of a local release build
```

If the project uses EAS Build for iOS and Android release artifacts, a contributor who only touches JavaScript can often work productively without ever installing Xcode or Android Studio locally — see `frameworks/react-native/eas.md`. This guide is still the reference for whoever *is* responsible for producing or debugging a local release build.

---

# 16. Monorepo and workspace considerations

In a monorepo (Yarn/npm/pnpm workspaces, Nx, Turborepo), verify:

```text
Which package.json is authoritative for the React Native app?
Is the lockfile at the repo root or per-package?
Does the native project (ios/ or android/) live inside the RN package
  or somewhere else in the workspace?
Are native dependencies hoisted in a way the native build tooling expects?
```

A native build failure that only happens inside a monorepo, and not in a standalone reproduction, often traces back to dependency hoisting or a symlink the native build tool doesn't resolve the way Node does. Don't assume it's a React Native bug before checking the workspace structure.

---

# 17. Security: keep local credentials out of the repository

Local setup often involves installing real credentials on your machine — an Apple Developer login, an Android keystore, a service account key. None of these belong in the repository, in shell history committed to a dotfiles repo, or in a screen share.

```text
Never commit:
  keystores
  provisioning profiles with embedded private keys
  App Store Connect API keys (.p8)
  Google Play service account JSON
  Apple ID passwords or app-specific passwords
```

See `frameworks/react-native/signing.md` and `signing/security/` for how credentials should actually be stored and rotated. A local machine set up for release builds is a machine that can produce signed, submittable artifacts — treat access to it accordingly.

---

# 18. AI-assisted environment audit

AI tools can help diagnose a broken local setup faster than manually re-reading every log line, but they don't have access to your machine — you still have to run the commands and paste the output.

A useful prompt:

```text
Help me verify my local React Native development environment for release builds.

My setup:
- OS: <macOS / Windows / Linux, version>
- Node: <output of `node --version`>
- Package manager: <npm / yarn / pnpm, version>
- JDK: <output of `java -version`>
- Android SDK: <ANDROID_HOME value, installed platforms/build tools>
- Xcode (if macOS): <output of `xcodebuild -version`>
- CocoaPods (if macOS): <output of `pod --version`>
- `npx react-native doctor` output: <paste>

Project requirements (from package.json / gradle files / Podfile):
<paste relevant version constraints>

Identify:
1. Any version mismatch between my machine and what the project expects
2. Anything `react-native doctor` flagged that I haven't resolved
3. The most likely cause if a release build (not a debug build) is failing
4. What to check next, in order

Rules:
- Do not ask me to paste secrets, credentials, or keystore contents.
- Separate confirmed mismatches from guesses.
- Recommend the current official React Native / Android / Apple documentation
  for anything version-specific, since these requirements change over time.
```

Treat the output as a hypothesis to verify, not a fix to apply blindly — see `ai/security/` for the repository's broader rules on AI-assisted debugging.

---

# 19. What this guide doesn't cover

```text
App name, package name (applicationId) / Bundle ID, and app icons
→ foundations/identifiers.md
→ foundations/project-configuration.md (§5 app name/slug, §19 app icons)
→ frameworks/react-native/android-release.md, frameworks/react-native/ios-release.md
→ publishing/cross-platform/assets.md (icon/splash pixel specs)

Writing React Native application code
→ reactnative.dev

Signing credentials and how they're stored/rotated
→ frameworks/react-native/signing.md, signing/

Producing the actual release artifact
→ frameworks/react-native/android-release.md
→ frameworks/react-native/ios-release.md

Diagnosing a build that's already failing
→ frameworks/react-native/common-failures.md

Cloud build services as an alternative to local builds
→ frameworks/react-native/eas.md
→ frameworks/react-native/fastlane.md
```

Don't duplicate those here — this guide's job ends once your machine can reliably produce the same artifact CI would.

---

# Related documentation

### React Native

- `frameworks/react-native/README.md`
- `frameworks/react-native/android-release.md`
- `frameworks/react-native/ios-release.md`
- `frameworks/react-native/signing.md`
- `frameworks/react-native/eas.md`
- `frameworks/react-native/fastlane.md`
- `frameworks/react-native/common-failures.md`

### Expo

- `frameworks/expo/local-setup.md`
- `frameworks/expo/development-builds.md`

### Signing

- `signing/ios/`
- `signing/android/`
- `signing/security/`

### Troubleshooting

- `troubleshooting/build-fails.md`
- `troubleshooting/ci-fails.md`
- `troubleshooting/signing-fails.md`

### Foundations

- `foundations/identifiers.md`
- `foundations/project-configuration.md`
- `foundations/dependency-management.md`
- `foundations/release-environments.md`

### Publishing

- `publishing/cross-platform/assets.md`

---

# Official sources

## React Native

- Set up your environment: https://reactnative.dev/docs/set-up-your-environment
- Environment troubleshooting (`react-native doctor`): https://reactnative.dev/docs/environment-setup
- Without a framework: https://reactnative.dev/docs/getting-started-without-a-framework

## Android

- Install Android Studio: https://developer.android.com/studio/install
- Set up the Android SDK: https://developer.android.com/studio/intro/update#sdk-manager

## Apple

- Xcode: https://developer.apple.com/xcode/
- CocoaPods: https://cocoapods.org/

## Watchman

- Watchman: https://facebook.github.io/watchman/docs/install

---

**Last verified:** September 4, 2026

Node, JDK, Xcode, Android Studio, CocoaPods, and their version compatibility with a given React Native release all change over time. Verify current requirements against the official documentation above and the specific React Native version the project uses before trusting a version number in this guide.
