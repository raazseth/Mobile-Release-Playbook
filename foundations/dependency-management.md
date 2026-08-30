# Dependency Management

Dependencies are part of the mobile release system, not just development tooling.

A package update can change:

- JavaScript behavior
- native iOS code
- native Android code
- permissions
- entitlements
- build configuration
- app size
- startup performance
- store compatibility
- security posture

The goal is simple:

> Keep dependencies compatible, reproducible, secure, and easy to upgrade without turning every release into a dependency migration.

This guide applies to mobile projects generally, with **Expo and React Native as first-class paths**.

---

## 1. What dependency management means

A mobile application usually has several dependency layers:

```text
Application code
 ↓
JavaScript / TypeScript packages
 ↓
React / React Native
 ↓
Expo SDK / native modules
 ↓
iOS / Android native dependencies
 ↓
Build tools
 ↓
Operating system / platform SDK
```

A dependency change at a lower layer can affect everything above it.

For example:

```text
Update native library
 ↓
Native build changes
 ↓
Permission / entitlement changes
 ↓
Runtime behavior changes
 ↓
Release testing required
```

Do not treat a dependency update as complete just because the package manager finishes successfully.

---

## 2. The dependency source of truth

Your repository should have a clear source of truth for dependencies.

Typically this includes:

```text
package.json
+
lockfile
+
native dependency configuration
+
build configuration
```

Common lockfiles include:

```text
package-lock.json
yarn.lock
pnpm-lock.yaml
bun.lock
```

Commit the lockfile used by the project.

Do not maintain multiple competing lockfiles unless the project intentionally supports multiple package managers.

A reproducible release should be traceable to:

```text
package.json
 +
lockfile
 +
source commit
 +
build configuration
```

---

## 3. Use one package manager

Choose one package manager for the repository.

Possible choices include:

- npm
- Yarn
- pnpm
- Bun

The choice is less important than consistency.

Do not have developers randomly switch between:

```text
npm
yarn
pnpm
bun
```

on the same project.

Mixing package managers can create:

- different dependency trees
- different lockfiles
- different lifecycle behavior
- inconsistent CI results
- difficult-to-reproduce builds

If the repository already has a package-manager lockfile, follow the existing project convention unless there is a deliberate migration.

---

## 4. Reproducible installs

The development install and the CI/release install should be predictable.

For npm projects, use:

```bash
npm ci
```

for clean CI or deployment installs when a valid `package-lock.json` exists.

`npm ci` requires the lockfile to match `package.json` and does not update the lockfile during installation. This makes it useful for reproducible CI installs.

For other package managers, use the equivalent immutable/frozen-lockfile installation mode supported by the project's chosen tool.

The important property is:

```text
Do not resolve a new dependency tree during the release build.
```

---

## 5. Add dependencies deliberately

Before adding a package, ask:

1. Do we actually need it?
2. Does the platform already provide the capability?
3. Does Expo or React Native already provide a supported solution?
4. Is the package maintained?
5. Does it support the project's platform versions?
6. Does it require native code?
7. Does it require a config plugin?
8. Does it introduce permissions?
9. Does it increase the app size or startup cost?
10. Does it create a new security or supply-chain risk?

Avoid adding a package for trivial functionality that can be implemented safely with existing project dependencies.

This keeps the dependency graph smaller and reduces future release work.

---

## 6. Prefer compatible libraries

For Expo projects, use Expo-compatible packages and installation workflows where available.

For Expo SDK libraries, use:

```bash
npx expo install <package>
```

For example:

```bash
npx expo install expo-camera
```

Expo documents `npx expo install` as the supported way to install Expo SDK packages and notes that it installs versions compatible with the project's Expo SDK.

Expo CLI also supports npm, Yarn, pnpm, and Bun package managers.

Do not blindly use:

```bash
npm install
```

for an Expo SDK package when the Expo installation workflow can select the compatible version.

---

## 7. Expo dependency compatibility

Expo projects have an important compatibility relationship between:

```text
Expo SDK
React
React Native
Expo modules
```

When these versions drift apart, you can get:

- React Native version mismatch errors
- native build failures
- runtime crashes
- incompatible native modules
- unexpected behavior

Use:

```bash
npx expo-doctor
```

to check the project.

Expo Doctor checks areas including app configuration, `package.json`, dependency compatibility, and project health.

You can also check package compatibility with:

```bash
npx expo install --check
```

Expo documents this as a CI-friendly check that exits non-zero when incompatible package versions are detected.

For an intentional dependency alignment fix:

```bash
npx expo install --fix
```

Do not run `--fix` blindly in the middle of a release without reviewing the resulting changes.

---

## 8. Expo SDK upgrades

Treat an Expo SDK upgrade as a release-affecting change.

Use the current Expo upgrade documentation and upgrade incrementally rather than jumping across multiple SDK versions without a reason.

A safe pattern is:

```text
Current release
 ↓
Upgrade one SDK level
 ↓
Align dependencies
 ↓
Run Expo Doctor
 ↓
Build
 ↓
Test
 ↓
Release or continue
```

React Native's current upgrade guidance also recommends incremental Expo SDK upgrades because they make breakages easier to identify.

Do not combine an SDK upgrade with unrelated major dependency migrations unless there is a strong reason.

---

## 9. React Native upgrades

A React Native upgrade is more than changing one version in `package.json`.

A React Native application can include:

```text
JavaScript
+
iOS project
+
Android project
+
native dependencies
+
build configuration
```

React Native's Upgrade Helper shows the changes between two React Native versions and helps identify required native project changes.

For React Native upgrades:

```text
Choose target version
 ↓
Review Upgrade Helper
 ↓
Update dependencies
 ↓
Apply native changes
 ↓
Resolve conflicts
 ↓
Build iOS
 ↓
Build Android
 ↓
Run tests
 ↓
Real-device test
```

Do not treat:

```bash
npm install react-native@...
```

as the complete upgrade process.

---

## 10. Native dependencies

A dependency becomes higher risk when it includes native code.

Examples:

- camera
- maps
- Bluetooth
- payments
- push notifications
- storage
- authentication
- background processing
- sensors

Native dependencies can affect:

- Xcode builds
- Gradle builds
- CocoaPods
- Android manifests
- iOS entitlements
- permissions
- app startup
- binary size
- architecture compatibility

Before adding a native dependency, check:

- [ ] iOS support
- [ ] Android support
- [ ] React Native compatibility
- [ ] Expo compatibility if applicable
- [ ] Required SDK versions
- [ ] Required permissions
- [ ] Required entitlements
- [ ] Configuration/plugin requirements
- [ ] Maintenance status
- [ ] Known release issues

---

## 11. Expo config plugins

Some native packages require configuration outside JavaScript.

In Expo projects this can involve config plugins.

A package may require changes to:

```text
app.json
app.config.js
app.config.ts
```

or native projects generated by Expo Prebuild.

Expo documents that `npx expo install` can automatically add some config plugins to static app configuration, but dynamic configuration may require manual changes.

After installing a native package:

```text
Install
 ↓
Review configuration
 ↓
Generate/update native projects if applicable
 ↓
Build
 ↓
Test native behavior
```

Do not assume JavaScript installation means native configuration is complete.

---

## 12. React Native Directory and library compatibility

For React Native projects, check whether a library is known to work with React Native and whether it is actively maintained.

For Expo projects, Expo Doctor can check packages against the React Native Directory by default.

This is a signal, not absolute proof.

A package being listed does not guarantee that it works with every:

- Expo SDK
- React Native version
- iOS version
- Android version
- architecture configuration

Still test the actual release configuration.

---

## 13. Lockfile discipline

Treat the lockfile as part of the release artifact.

When dependencies change:

- [ ] `package.json` changed intentionally.
- [ ] Lockfile changed intentionally.
- [ ] The dependency tree was reviewed.
- [ ] Unexpected packages were investigated.
- [ ] Native dependency changes were reviewed.
- [ ] Tests passed.
- [ ] Production build passed.

Do not:

- manually edit lockfiles unless you understand the format and have a strong reason
- delete the lockfile to "fix" dependency issues without understanding why
- commit unrelated lockfile churn
- ignore large dependency-tree changes

A large unexpected lockfile diff is a release signal worth investigating.

---

## 14. Dependency updates

Not every available update needs to be installed immediately.

Prioritize updates based on:

```text
Security
 ↓
Release compatibility
 ↓
Critical bug fixes
 ↓
Required platform support
 ↓
Important features
 ↓
Routine maintenance
```

A package being one version behind is not automatically a release problem.

A known exploitable vulnerability in a package used by production code may be.

---

## 15. Major, minor, and patch updates

Treat update size as a risk signal, not a complete risk model.

### Patch

Usually contains:

- bug fixes
- small corrections

Still test the application.

### Minor

May contain:

- new features
- behavior changes
- deprecations

Review the changelog and test relevant functionality.

### Major

May contain:

- breaking API changes
- native changes
- configuration changes
- migration requirements

Treat a major update as a planned engineering change.

For React Native specifically, the project uses a `0.x.y` versioning policy where breaking changes are shipped in new minor versions. Do not assume standard semantic-versioning intuition applies directly to React Native.

---

## 16. Security and supply-chain review

Every dependency is part of the application's supply chain.

Review:

- package source
- package maintainer
- repository activity
- release history
- known vulnerabilities
- transitive dependencies
- install scripts
- native code
- permissions
- network behavior

Security guidance should be proportional to risk.

Do not perform a full manual security investigation for every harmless utility package.

Do investigate dependencies that:

- handle credentials
- process sensitive data
- execute native code
- access device capabilities
- communicate with privileged services
- run install/build scripts
- have broad transitive dependency trees

The project's security guidance treats third-party packages, lockfiles, dependency updates, CI/CD permissions, build secrets, and untrusted scripts as supply-chain security concerns.

---

## 17. Vulnerability handling

When a vulnerability is reported:

```text
Identify affected package
 ↓
Confirm affected versions
 ↓
Determine actual exposure
 ↓
Find safe upgrade/fix
 ↓
Test
 ↓
Release
```

Do not automatically upgrade everything in response to a single vulnerability.

First determine:

- Is the vulnerable package actually present?
- Is the vulnerable code path used?
- Is the issue exploitable in the mobile application?
- Is there a fixed version?
- Does the fix introduce a breaking change?
- Does the fix affect native code?
- Does the fix require a new production build?

For a release-blocking vulnerability, document the decision and mitigation.

---

## 18. Transitive dependencies

A package can bring other packages into the application.

Example:

```text
Your app
 ↓
Library A
 ↓
Library B
 ↓
Library C
```

You may not directly use Library C, but its code can still be included.

When investigating a security or compatibility issue:

- [ ] Identify the direct dependency.
- [ ] Identify the transitive dependency.
- [ ] Determine which version is actually installed.
- [ ] Check whether multiple versions are installed.
- [ ] Determine whether the vulnerable code is included.
- [ ] Update the appropriate parent dependency where possible.

Do not add a direct dependency solely to override a transitive dependency without understanding the compatibility implications.

---

## 19. Peer dependencies

Peer dependency warnings can indicate real compatibility problems.

Pay attention when the warning involves:

- React
- React Native
- Expo
- native libraries
- navigation libraries
- state management libraries with native bindings
- platform-specific packages

Do not silence peer dependency warnings simply to make installation succeed.

First determine:

```text
Expected version
vs
Installed version
vs
Actual project compatibility
```

---

## 20. Dependency changes near release

Avoid unnecessary dependency updates during the final release window.

Prefer:

```text
Dependency change
 ↓
Build
 ↓
Test
 ↓
Stabilize
 ↓
Release
```

over:

```text
Release candidate
 ↓
Random package update
 ↓
New native changes
 ↓
Release again
```

If a dependency must change because of:

- security
- platform requirements
- release blocker
- production bug

then treat the result as a new release candidate and repeat the relevant validation.

---

## 21. Native rebuild requirement

Some dependency changes require a new native build.

This is especially important for Expo and React Native projects.

If a dependency changes native code, configuration, permissions, or entitlements:

```text
Dependency change
 ↓
Native configuration changes
 ↓
New native build
 ↓
Install on device
 ↓
Test
```

Do not assume an OTA JavaScript update can safely deliver a change that requires a new native binary.

The release strategy must match the type of dependency change.

---

## 22. Expo Go vs development/production builds

A library working in Expo Go does not automatically prove that the production application is correct.

If a library requires custom native code:

- [ ] Check whether it is supported in Expo Go.
- [ ] Use a development build where required.
- [ ] Build the production binary.
- [ ] Test the actual native behavior.

Expo documents that libraries requiring custom native code may require a development build rather than Expo Go.

The production binary is the final validation target.

---

## 23. Dependency upgrade workflow

Use this workflow for intentional upgrades.

### Step 1: Define the reason

Write down why the dependency is being updated.

Examples:

```text
Security fix
Platform requirement
Bug fix
Feature requirement
React Native upgrade
Expo SDK upgrade
Maintenance
```

If there is no meaningful reason, defer the update.

### Step 2: Check compatibility

Review:

- current version
- target version
- React Native support
- Expo support
- iOS support
- Android support
- native changes
- migration notes

### Step 3: Update

Use the package manager appropriate for the repository.

For Expo packages:

```bash
npx expo install <package>
```

For Expo dependency alignment:

```bash
npx expo install --check
```

For an intentional alignment fix:

```bash
npx expo install --fix
```

For React Native upgrades, use the current React Native Upgrade Helper and apply the required native changes.

### Step 4: Inspect the diff

Review:

```text
package.json
lockfile
app configuration
iOS configuration
Android configuration
native project files
```

### Step 5: Validate

Run the project's:

- unit tests
- integration tests
- type checks
- lint checks
- build checks

For Expo projects, also run:

```bash
npx expo-doctor
```

### Step 6: Build

Build both platforms if the dependency affects both.

```text
iOS
+
Android
```

### Step 7: Real-device test

Test the functionality affected by the dependency.

### Step 8: Release

Only after the dependency change is stable.

---

## 24. CI dependency checks

CI should catch dependency drift before release.

At minimum, consider:

```text
Install from lockfile
 ↓
Dependency compatibility check
 ↓
Tests
 ↓
Build
```

For Expo projects:

```bash
npx expo install --check
npx expo-doctor
```

Expo documents `npx expo install --check` as suitable for CI because it exits non-zero when package versions are incompatible.

Do not automatically modify dependencies inside CI.

CI should detect problems, not silently rewrite the dependency tree.

---

## 25. Dependency automation

Automated dependency tools can be useful.

Examples include:

- Dependabot
- Renovate
- package-manager update tooling
- security scanners

Use automation to create review inputs.

Prefer:

```text
Automated update
 ↓
Review
 ↓
Test
 ↓
Build
 ↓
Merge
```

Avoid:

```text
Automated update
 ↓
Automatic production release
```

Especially avoid automatically merging native dependency upgrades without build and release validation.

---

## 26. AI-assisted dependency management

AI can reduce dependency maintenance work, but it should not be trusted blindly.

Useful AI tasks:

- explain why a package is needed
- identify direct vs transitive dependencies
- summarize changelogs
- compare upgrade paths
- inspect dependency diffs
- identify likely native changes
- prepare upgrade plans
- explain build failures after an upgrade
- draft migration checklists
- review lockfile changes
- identify suspicious dependency additions

A safe workflow:

```text
Dependency update
 ↓
AI analysis
 ↓
Candidate risks
 ↓
Developer verifies against package docs
 ↓
Run tests
 ↓
Build iOS + Android
 ↓
Real-device test
 ↓
Human approval
```

Do not let AI decide that a dependency is safe solely from its own reasoning.

Do not give an AI agent:

- package registry credentials
- signing keys
- store credentials
- production secrets
- unrestricted production access

AI-generated commands and upgrade recommendations are untrusted until verified.

---

## 27. AI dependency audit prompt

A reusable prompt can be used with ChatGPT, Claude, Codex, Claude Code, or another coding agent:

```text
Audit the dependency changes in this mobile application for release risk.

Project:
- Framework: Expo / React Native
- Platforms: iOS and Android
- Release type: production

Inspect:
1. package.json changes
2. lockfile changes
3. direct dependencies
4. important transitive dependencies
5. React / React Native compatibility
6. Expo SDK compatibility if applicable
7. native modules
8. config plugins
9. iOS configuration changes
10. Android configuration changes
11. permissions and entitlements
12. known migration requirements
13. build implications
14. security concerns

Return:

BLOCKERS
WARNINGS
NATIVE CHANGES
SECURITY CONCERNS
TESTS REQUIRED
BUILD REQUIREMENTS
QUESTIONS / UNKNOWNs

Do not invent package requirements.

For uncertain claims, identify the package documentation that must be checked.

Do not modify dependencies automatically.

Treat all findings as recommendations that require developer verification.
```

---

## 28. Dependency-related release failures

Common failures include:

### React Native version mismatch

Possible causes:

- React Native version does not match Expo SDK expectations.
- Lockfile contains an unexpected version.
- Multiple React Native versions are installed.
- Native build and JavaScript dependency versions are out of sync.

For Expo projects, run:

```bash
npx expo-doctor
npx expo install --check
```

Expo documents these tools for identifying and correcting dependency-version mismatches.

---

### Build works locally but fails in CI

Check:

- lockfile is committed
- CI uses the expected Node/package-manager version
- CI uses an immutable install
- environment variables match
- native tooling versions match
- generated native files are handled correctly
- package install scripts behave consistently

Do not immediately delete the lockfile or upgrade everything.

Find the first meaningful difference between local and CI environments.

---

### iOS build fails after dependency update

Check:

- CocoaPods changes
- Xcode compatibility
- native module support
- iOS deployment target
- entitlements
- config plugins
- generated native files
- architecture support

Then rebuild from a clean state if appropriate.

---

### Android build fails after dependency update

Check:

- Gradle configuration
- Android Gradle Plugin compatibility
- compile/target SDK requirements
- Kotlin compatibility where applicable
- native module configuration
- manifest changes
- dependency conflicts

Do not upgrade unrelated Gradle/Kotlin/Android tooling unless the dependency actually requires it.

---

### Runtime crash after dependency update

Check:

```text
Did JavaScript change?
 ↓
Did native code change?
 ↓
Did configuration change?
 ↓
Did permissions change?
 ↓
Did the dependency version change?
 ↓
Does the crash occur on one platform only?
```

Compare against the previous release.

If the crash is release-specific, treat the dependency update as a likely suspect until evidence says otherwise.

---

## 29. Dependency removal

Removing a dependency also requires validation.

Before removing it:

- [ ] No code imports it.
- [ ] No native code depends on it.
- [ ] No config plugin depends on it.
- [ ] No build script depends on it.
- [ ] No platform configuration depends on it.
- [ ] No runtime feature depends on it.

After removal:

- [ ] `package.json` updated.
- [ ] Lockfile updated.
- [ ] Native configuration cleaned up.
- [ ] iOS build passes.
- [ ] Android build passes.
- [ ] Relevant functionality tested.

Do not remove a package just because the JavaScript import is unused. Native configuration may still depend on it.

---

## 30. Dependency inventory

For production applications, maintain enough information to answer:

```text
What dependencies do we ship?
Why do we use them?
Which ones contain native code?
Which versions are installed?
Which versions are supported?
Which ones need special configuration?
Which ones are security-sensitive?
```

You do not need a separate database for this.

The repository can usually provide the source of truth through:

```text
package.json
lockfile
native configuration
build configuration
documentation
```

Add a dedicated inventory document only when the project's size or risk justifies it.

---

## 31. Release dependency gate

Before a production release:

- [ ] Lockfile is committed.
- [ ] Dependency changes are intentional.
- [ ] No unexpected dependency changes exist.
- [ ] Expo compatibility is checked where applicable.
- [ ] React Native compatibility is checked.
- [ ] Native dependencies are reviewed.
- [ ] Config plugins are reviewed.
- [ ] Security issues are reviewed.
- [ ] Tests pass.
- [ ] iOS build passes.
- [ ] Android build passes.
- [ ] Real-device testing is complete.
- [ ] Production artifact is verified.

If a dependency changed after the previous tested release:

```text
New dependency state
 ↓
New validation
 ↓
New build
 ↓
New release candidate
```

Do not release an untested dependency tree.

---

# 32. What not to do

Avoid these common shortcuts:

### Do not delete the lockfile to fix an install problem

It may hide the actual dependency-resolution problem and produce a completely different dependency tree.

### Do not blindly run every upgrade command

A major upgrade can change native code and build behavior.

### Do not ignore peer dependency warnings

Determine whether the warning represents a real compatibility issue.

### Do not assume npm/Yarn installation proves native compatibility

Native dependencies require native builds and testing.

### Do not rely only on package popularity

Maintenance, compatibility, security, native behavior, and project fit matter more.

### Do not auto-merge native dependency updates

Require build and test validation.

### Do not let AI choose production dependency upgrades without verification

AI can analyze and recommend. Developers remain responsible for the decision.

---

# 33. Recommended dependency workflow

For normal maintenance:

```text
Check updates
 ↓
Review reason
 ↓
Check compatibility
 ↓
Update
 ↓
Review package + lockfile diff
 ↓
Run tests
 ↓
Run dependency checks
 ↓
Build iOS
 ↓
Build Android
 ↓
Real-device test
 ↓
Merge
```

For an urgent security update:

```text
Identify vulnerability
 ↓
Confirm exposure
 ↓
Find safe fixed version
 ↓
Update
 ↓
Run tests
 ↓
Build affected platforms
 ↓
Real-device smoke test
 ↓
Human approval
 ↓
Release
 ↓
Monitor
```

For an Expo SDK or React Native upgrade:

```text
Choose target version
 ↓
Read current upgrade guidance
 ↓
Update framework dependencies
 ↓
Apply native changes
 ↓
Run compatibility checks
 ↓
Build iOS
 ↓
Build Android
 ↓
Run tests
 ↓
Real-device testing
 ↓
Release
```

---

# 34. Definition of done

Dependency management is in a good state when:

```text
Dependencies are intentional
 +
Versions are reproducible
 +
Compatibility is verified
 +
Security is reviewed
 +
Native impact is understood
 +
Tests pass
 +
iOS builds
 +
Android builds
 +
Real devices pass
 ↓
 RELEASE READY
```

The goal is not to keep every dependency at the newest version.

The goal is to keep the application's dependency set:

- compatible
- secure
- reproducible
- maintainable
- supportable in production


---

# Related documentation

### Foundations

- `foundations/README.md`
- `foundations/device-support.md`
- `foundations/identifiers.md`
- `foundations/mobile-release-lifecycle.md`
- `foundations/project-configuration.md`
- `foundations/release-environments.md`
- `foundations/versioning.md`

### Pre-release

- `pre-release/README.md`
- `pre-release/release-readiness.md`

### Release engineering

- `release-engineering/README.md`
- `release-engineering/release-configurations.md`

### Testing

- `testing/README.md`

### Checklists

- `checklists/first-release.md`

---

# Official sources

Use official documentation for current dependency behavior.

### Expo

- Expo SDK reference: https://docs.expo.dev/versions/latest/
- Expo CLI: https://docs.expo.dev/more/expo-cli/
- Expo Doctor: https://docs.expo.dev/develop/tools/
- Using libraries: https://docs.expo.dev/workflow/using-libraries/
- Config plugins: https://docs.expo.dev/config-plugins/development-and-debugging/
- React Native version mismatch: https://docs.expo.dev/troubleshooting/react-native-version-mismatch/

### React Native

- Upgrading React Native: https://reactnative.dev/docs/upgrading
- Versioning policy: https://reactnative.dev/releases/versioning-policy
- React Native Directory: https://reactnative.directory/

### npm

- `npm ci`: https://docs.npmjs.com/cli/v11/commands/npm-ci
- `package-lock.json`: https://docs.npmjs.com/cli/v11/configuring-npm/package-lock-json

### Security

- npm security documentation: https://docs.npmjs.com/about-audit-reports

**Last verified:** August 11, 2026

Dependency compatibility, package-manager behavior, Expo SDK support, React Native releases, and platform build requirements can change. Re-check the official documentation before major upgrades and production releases.
