# Project Configuration

Project configuration is the set of settings that determines **what application gets built, for which platform, with which environment, and with which release behavior**.

A mobile project can compile successfully and still be configured incorrectly for production.

The goal of this document is to make production configuration:

- explicit
- reproducible
- easy to verify
- safe to change
- compatible with Expo and React Native
- easy to extend to other mobile frameworks

This guide focuses on release-related configuration. It does not try to document every possible Expo or React Native project setting.

---

## 1. What project configuration controls

Release configuration commonly controls:

```text
Application identity
        ↓
Environment
        ↓
Versioning
        ↓
Platform settings
        ↓
Native capabilities
        ↓
Build configuration
        ↓
Signing
        ↓
Store distribution
```

Typical configuration areas include:

- app name
- bundle identifier
- Android application ID
- version
- build number/version code
- minimum OS versions
- app icons
- splash screen
- permissions
- capabilities
- URL schemes
- deep links
- push notifications
- environment variables
- API endpoints
- build profiles
- signing configuration
- update configuration

Not every project needs every setting.

The correct configuration is the smallest configuration that supports the actual product.

---

# 2. Configuration sources

A project may have configuration in several places.

For an Expo project:

```text
app.json
app.config.js
app.config.ts
eas.json
package.json
```

For a React Native project with native directories:

```text
package.json
ios/
android/
```

There may also be:

```text
.env*
CI configuration
GitHub Actions
native build scripts
Fastlane
Xcode configuration
Gradle configuration
```

The important rule is:

> Know which file or system is authoritative for each setting.

Do not assume that changing one configuration file automatically changes the final native application.

---

# 3. Avoid configuration drift

Configuration drift happens when different parts of the project disagree.

Example:

```text
Expo config
com.example.app

iOS native project
com.example.oldapp

App Store Connect
com.example.app
```

Or:

```text
Local environment
production API

CI environment
staging API
```

Or:

```text
Production build
development feature flags
```

These inconsistencies can create difficult release failures.

Before release, verify:

```text
Source configuration
        ↓
Build configuration
        ↓
Native configuration
        ↓
Actual artifact
        ↓
Store configuration
```

The actual artifact is the final verification point.

---

# 4. Application identity

The application needs a stable identity on each platform.

### iOS

Use the Bundle ID:

```text
com.example.myapp
```

In Expo:

```json
{
  "ios": {
    "bundleIdentifier": "com.example.myapp"
  }
}
```

### Android

Use the Application ID/package name:

```text
com.example.myapp
```

In Expo:

```json
{
  "android": {
    "package": "com.example.myapp"
  }
}
```

The identifiers can be the same across platforms, but they do not have to be.

Treat production identifiers as long-term identity.

See:

```text
foundations/identifiers.md
```

for the detailed identifier rules.

---

# 5. App name and slug

Do not confuse these values:

```text
Display name
Bundle ID / Application ID
Expo slug
Store listing name
```

They serve different purposes.

For example:

```text
Display name:
Acme

iOS Bundle ID:
com.acme.mobile

Android Application ID:
com.acme.mobile

Expo slug:
acme
```

A change to the visible app name does not mean the application identity should change.

Avoid changing identifiers simply because the branding changes.

---

# 6. Production environment

The production application must use production services.

At minimum, verify:

- production API base URL
- production authentication configuration
- production database/backend
- production analytics
- production crash reporting
- production push configuration
- production payment configuration
- production OAuth configuration
- production deep-link configuration

A production build should never accidentally point at:

```text
localhost
127.0.0.1
development API
staging API
test backend
developer machine
```

unless that behavior is explicitly part of the release.

---

# 7. Environment separation

Most projects need at least:

```text
Development
Production
```

Some projects also need:

```text
Staging
```

Use additional environments only when they provide real value.

A simple model:

```text
Development
    ↓
developer testing

Staging
    ↓
shared release validation

Production
    ↓
real users
```

Do not create multiple environments simply because the architecture looks more professional.

Every environment adds:

- configuration
- credentials
- maintenance
- debugging complexity
- deployment paths

Keep the environment model small.

---

# 8. Environment-specific configuration

Environment configuration should be explicit.

Example:

```text
development
API_URL=https://dev-api.example.com

staging
API_URL=https://staging-api.example.com

production
API_URL=https://api.example.com
```

The exact mechanism depends on the project.

For Expo/EAS, environment variables and build profiles can be used to provide build-specific configuration. Expo documents environment variables and EAS environment management as part of its build and deployment workflows.

Official references:

- Expo environment variables:
  https://docs.expo.dev/guides/environment-variables/
- EAS environment variables:
  https://docs.expo.dev/eas/environment-variables/

Do not put secrets into public client configuration.

---

# 9. Public configuration vs secrets

A mobile application cannot keep a true secret once the value is included in the client binary.

This distinction matters:

### Usually public configuration

```text
API base URL
App identifier
Feature configuration
Public analytics identifier
Public service configuration
```

### Sensitive secrets

```text
Database passwords
Private signing keys
OAuth client secrets
Service-account private keys
Backend credentials
AI provider private API keys
Payment private keys
```

Never embed sensitive server credentials into the mobile application.

If a value grants privileged access to a backend or third-party service, keep the privileged operation server-side.

The security rule is:

```text
Client
→ public configuration only

Server / CI secret storage
→ sensitive credentials
```

---

# 10. Environment variables

Environment variables are useful for configuration, but they are not automatically secret.

Before adding a variable, classify it:

```text
PUBLIC
or
SENSITIVE
```

For mobile applications:

```text
PUBLIC variable
→ may be bundled into the application

SENSITIVE variable
→ must not be bundled into the application
```

If the mobile app needs a sensitive operation:

```text
Mobile app
   ↓
Authenticated backend
   ↓
Secret / privileged service
```

Do not solve a backend-secret problem by hiding the value in a mobile environment variable.

---

# 11. Expo configuration

Expo projects commonly use:

```text
app.json
```

or:

```text
app.config.js
app.config.ts
```

Expo app configuration can define settings such as:

- name
- slug
- version
- orientation
- icons
- splash screen
- iOS configuration
- Android configuration
- scheme
- plugins
- updates
- extra configuration

Example:

```json
{
  "expo": {
    "name": "Acme",
    "slug": "acme",
    "version": "1.0.0",
    "ios": {
      "bundleIdentifier": "com.acme.mobile"
    },
    "android": {
      "package": "com.acme.mobile"
    }
  }
}
```

Use the current Expo app configuration reference for the exact properties supported by the SDK being used:

https://docs.expo.dev/versions/latest/config/app/

Do not copy configuration from an old Expo project without checking whether the property is still supported.

---

# 12. Dynamic Expo configuration

Use `app.config.js` or `app.config.ts` when configuration needs to be generated from environment-specific or computed values.

For example:

```text
development
→ development API

production
→ production API
```

Keep dynamic configuration understandable.

Avoid turning the config file into application business logic.

A good configuration file should answer:

```text
What app are we building?
Which platform settings apply?
Which environment are we targeting?
Which native capabilities are required?
```

It should not become a second application.

---

# 13. Expo plugins

Native capabilities and libraries may require Expo config plugins.

Examples include packages that modify:

- iOS project settings
- Android manifest
- permissions
- entitlements
- native configuration

A typical flow is:

```text
Install native package
        ↓
Configure plugin
        ↓
Generate / update native project
        ↓
Build
        ↓
Test native behavior
```

Do not assume that installing a JavaScript package is enough when the package includes native code.

Expo's config plugin documentation:

https://docs.expo.dev/config-plugins/introduction/

---

# 14. React Native native configuration

A React Native project may have native projects committed to the repository.

Typical locations:

```text
ios/
android/
```

Important iOS configuration can include:

- Bundle ID
- deployment target
- capabilities
- entitlements
- URL schemes
- permissions
- build configurations

Important Android configuration can include:

- application ID
- min SDK
- target SDK
- permissions
- manifest configuration
- intent filters
- Gradle configuration

When native projects are part of the repository, inspect them directly during release validation.

---

# 15. Expo prebuild and native directories

Expo projects can generate native projects using prebuild.

This creates or updates:

```text
ios/
android/
```

from the Expo configuration and installed native packages.

This creates an important rule:

> Know whether the native directories are generated artifacts or maintained source.

If the project relies on generated native projects:

```text
Expo config
→ prebuild
→ native project
→ build
```

If the project maintains native projects directly:

```text
Native project
→ direct source of truth for native settings
```

Do not casually mix the two workflows.

When in doubt, verify the project's current Expo workflow and repository configuration.

Expo prebuild documentation:

https://docs.expo.dev/workflow/prebuild/

---

# 16. Version configuration

The public application version should be deliberate.

Example:

```text
1.4.0
```

Platform build identifiers are separate:

```text
iOS:
1.4.0 (build 42)

Android:
1.4.0 (versionCode 42)
```

Do not assume that:

```text
version
```

and:

```text
build number/versionCode
```

serve the same purpose.

See:

```text
foundations/versioning.md
```

for detailed versioning guidance.

---

# 17. Minimum OS configuration

Define the oldest OS the application supports.

### iOS

The iOS deployment target determines the minimum supported iOS version.

### Android

Android uses `minSdk` for the minimum API level supported by the application.

These values must remain compatible with:

```text
Framework
+
Native dependencies
+
Platform requirements
```

Do not lower the minimum OS just to increase theoretical device coverage if the framework or dependencies do not support it.

See:

```text
foundations/device-support.md
```

for the support policy and testing strategy.

---

# 18. Target SDK and compile SDK

Android has several distinct SDK concepts.

```text
minSdk
→ minimum supported API level

targetSdk
→ target Android API level for app behavior and platform requirements

compileSdk
→ SDK used to compile the application
```

Do not treat them as interchangeable.

Google Play target API requirements are current and can change. Verify the current official requirement before release:

https://developer.android.com/google/play/requirements/target-sdk

The project should use versions compatible with the current framework and native dependencies.

---

# 19. App icons

Production app icons should be configured deliberately.

Verify:

- [ ] Correct icon source exists.
- [ ] Required platform assets are generated correctly.
- [ ] Android adaptive icon configuration is correct where used.
- [ ] iOS icon configuration is correct.
- [ ] No development/test icon is included.
- [ ] Icon appears correctly on a real device.
- [ ] Store assets are separate from binary icons where required.

Do not assume that a single PNG is the complete store asset strategy.

Store listing assets and application icons are related but are not the same thing.

---

# 20. Splash screen

If the project uses a configured splash screen:

- [ ] Production branding is correct.
- [ ] Background is correct.
- [ ] Image/scaling is correct.
- [ ] Splash behavior is acceptable on supported devices.
- [ ] No development branding is visible.
- [ ] The first screen loads correctly after the splash.

For Expo projects, use the current Expo splash-screen configuration and package documentation:

https://docs.expo.dev/versions/latest/sdk/splash-screen/

Do not treat splash configuration as a replacement for startup performance work.

---

# 21. Orientation

Define supported orientations intentionally.

Examples:

```text
Portrait only
Portrait + landscape
Platform-specific
```

Verify the actual production build.

Check:

- [ ] Supported orientations are correct.
- [ ] Screens render correctly after rotation.
- [ ] Keyboard behavior remains correct.
- [ ] Modals remain usable.
- [ ] Deep links preserve expected navigation.
- [ ] Tablets behave correctly where supported.

Do not leave orientation behavior to accidental defaults when it matters to the product.

---

# 22. URL schemes and deep links

If the application supports deep links, configure them intentionally.

Common concepts include:

```text
Custom URL scheme
Universal Links
Android App Links
```

For example:

```text
myapp://profile/123
```

Configuration can involve:

- app scheme
- associated domains
- Android intent filters
- domain verification
- backend/domain files

Verify both:

```text
Application configuration
+
Domain configuration
```

A correct client configuration alone does not guarantee a working production deep link.

---

# 23. Push notification configuration

Push notifications can depend on:

- application identifier
- platform credentials
- Firebase configuration
- Apple configuration
- notification permissions
- production environment
- backend provider configuration

Verify:

```text
App identity
    ↓
Push credentials/config
    ↓
Device token
    ↓
Backend provider
    ↓
Notification delivery
```

Test on real devices.

Do not treat simulator/emulator behavior as proof of production push delivery.

---

# 24. Permissions

Only request permissions the application actually needs.

Review:

- camera
- microphone
- location
- notifications
- photos/media
- contacts
- Bluetooth
- tracking
- calendar
- health-related access
- other sensitive device capabilities

For every permission:

```text
Why does the app need it?
When is it requested?
What happens if the user denies it?
Is the store declaration accurate?
```

Do not add permissions because a library happens to support the capability.

Review the actual application behavior and native configuration.

---

# 25. iOS capabilities and entitlements

Some iOS features require capabilities and entitlements.

Examples include:

- Push Notifications
- Associated Domains
- App Groups
- Sign in with Apple
- Apple Pay
- Keychain sharing

A capability change may affect:

```text
Bundle ID
→ Developer configuration
→ Entitlements
→ Provisioning/signing
→ Production build
```

After changing a capability:

- [ ] Verify Apple Developer configuration.
- [ ] Verify project entitlements.
- [ ] Verify signing.
- [ ] Build again.
- [ ] Test the feature on a real device.

Do not assume that adding an entitlement to a file is enough.

---

# 26. Android manifest configuration

Android features may require manifest configuration.

Examples:

- permissions
- intent filters
- services
- receivers
- providers
- app links
- hardware declarations

After changing native configuration:

- [ ] Inspect the resulting manifest behavior.
- [ ] Build the release artifact.
- [ ] Install it on a device.
- [ ] Test the affected feature.

Avoid adding broad permissions or components without understanding why they are needed.

---

# 27. Build profiles

A build profile should represent a known purpose.

Common examples:

```text
development
preview
production
```

The exact names are not important.

The important distinction is:

```text
Development
→ developer workflow

Preview
→ shared testing

Production
→ store distribution
```

For Expo/EAS, build profiles are configured in:

```text
eas.json
```

Expo EAS Build configuration:

https://docs.expo.dev/build/eas-json/

Do not use a development profile to produce a public production binary.

---

# 28. Production build configuration

Before a production build, verify:

- [ ] Correct profile.
- [ ] Correct environment.
- [ ] Correct identifiers.
- [ ] Correct version.
- [ ] Correct signing.
- [ ] Production API.
- [ ] Production feature flags.
- [ ] Debug behavior disabled.
- [ ] Required native configuration present.
- [ ] Required secrets available to the build system without being bundled into the client.

A useful release record is:

```text
Commit:
Build profile:
Environment:
Version:
iOS build:
Android version code:
```

---

# 29. Debug configuration

Production should not accidentally contain development-only behavior.

Review:

- debug menus
- test accounts
- development URLs
- verbose logging
- mock data
- development-only feature flags
- local network assumptions
- debug endpoints
- test notification targets
- development analytics

Do not assume that a release build automatically removes every development behavior.

Verify the actual application.

---

# 30. Logging configuration

Logging is useful during development but can become a production risk.

Production logging should:

- help diagnose real failures
- avoid sensitive data
- avoid credentials
- avoid raw tokens
- avoid unnecessary personal data
- avoid excessive volume

Never log:

```text
Passwords
Access tokens
Refresh tokens
Private keys
API secrets
Sensitive personal data
```

Use structured logging and appropriate redaction where the application has meaningful production logging.

---

# 31. Analytics configuration

Verify:

- correct production analytics project
- correct environment
- correct application identifier
- important events
- no unintended sensitive data
- no development/test traffic
- consent requirements where applicable

Do not use analytics identifiers as secrets.

Analytics configuration should help answer questions about production behavior without collecting data the product does not need.

---

# 32. Crash reporting configuration

Crash reporting should identify the production release accurately.

Verify:

- production project
- application identifier
- release/version
- build number
- source maps/symbol files where applicable
- environment
- upload process

A crash report without release/version context is much harder to diagnose.

For React Native and Expo applications, configure the crash-reporting provider according to its current official integration guidance.

---

# 33. Payments configuration

If the application uses payments:

- [ ] Production product identifiers are correct.
- [ ] Production provider configuration is selected.
- [ ] Test/sandbox configuration is not accidentally used.
- [ ] Entitlements are validated server-side where appropriate.
- [ ] Client configuration does not contain private payment credentials.
- [ ] Store-specific product configuration is correct.

Payment configuration should be treated as release-critical.

---

# 34. OAuth and authentication configuration

Production authentication should be explicitly configured.

Verify:

- production client IDs
- redirect URIs
- application identifiers
- platform-specific OAuth clients
- backend callback configuration
- allowed domains
- signing fingerprints where required
- production provider project

Do not copy development authentication configuration into production without verification.

Authentication configuration errors often appear only after the public build is installed.

---

# 35. CI/CD configuration

CI/CD should build the same application you expect to release.

Verify:

- Node/runtime version
- package manager
- lockfile
- Expo CLI/EAS tooling where applicable
- Xcode version for iOS
- Android build tooling
- environment selection
- signing configuration
- secret access
- build profile
- artifact storage
- submission configuration

Do not let CI silently use a different dependency or build environment from the one used during release validation.

---

# 36. GitHub Actions

If using GitHub Actions:

- [ ] Workflow triggers are intentional.
- [ ] Production workflows require appropriate approval.
- [ ] Secrets use GitHub's protected secret mechanisms.
- [ ] Permissions are minimal.
- [ ] Pull requests cannot unintentionally publish production releases.
- [ ] Build artifacts are identifiable.
- [ ] Failed builds are visible.
- [ ] Production submission is not triggered accidentally.

A safe pattern is:

```text
Pull request
→ validation

Main branch
→ build/test

Release action/tag
→ production build

Human approval
→ store submission
```

The exact workflow depends on the repository.

---

# 37. Secret handling in CI

CI may need sensitive credentials for:

- Apple distribution
- Google Play submission
- signing
- third-party services

Use protected secret storage.

Never commit:

```text
.env.production
*.p8
*.p12
*.jks
service-account JSON
private keys
store passwords
API tokens
```

unless the file is intentionally non-secret and contains no credentials.

If a secret is exposed:

```text
Revoke
   ↓
Rotate
   ↓
Investigate
   ↓
Verify
```

Do not merely delete the file from the latest commit. Secrets can remain in Git history.

---

# 38. Configuration validation

Before building, validate configuration.

Useful checks include:

```text
Identifier check
Environment check
Version check
Dependency check
Native configuration check
Signing check
```

For Expo projects, useful commands can include:

```bash
npx expo-doctor
```

and:

```bash
npx expo install --check
```

These commands should be used according to the current Expo documentation and the project's installed Expo SDK.

Do not treat a successful configuration check as proof that the application itself works.

Configuration validation and functional testing are separate steps.

---

# 39. Inspect the resolved configuration

When configuration is generated dynamically, inspect what the build system actually resolves.

For Expo projects, this is especially important when using:

```text
app.config.js
app.config.ts
environment variables
EAS build profiles
```

Do not validate only the source file.

Verify:

```text
Input configuration
        ↓
Resolved configuration
        ↓
Native build
        ↓
Actual artifact
```

This catches environment and dynamic-configuration mistakes.

---

# 40. Configuration changes are release changes

Treat these as potentially release-affecting:

- Bundle ID
- Application ID
- minimum OS
- target SDK
- permissions
- entitlements
- deep links
- push configuration
- native plugins
- build profile
- production API
- analytics
- crash reporting
- payment configuration
- authentication configuration

A configuration-only pull request can still require:

```text
Build
+
Device test
+
Store validation
```

Do not judge release risk by line count.

A one-line configuration change can have more impact than a hundred lines of UI code.

---

# 41. Configuration review workflow

Use this workflow before a production release:

```text
1. Identify release commit
2. Identify build profile
3. Identify environment
4. Verify identifiers
5. Verify versioning
6. Verify API endpoints
7. Verify native configuration
8. Verify permissions
9. Verify platform capabilities
10. Verify signing inputs
11. Build
12. Inspect artifact
13. Test on real devices
14. Submit
```

For each step, record only the evidence that helps the team reproduce or debug the release.

---

# 42. AI-assisted configuration audit

AI can be useful for finding configuration inconsistencies.

Good tasks include:

- compare Expo config with native projects
- find development URLs
- find production/staging mismatches
- inspect identifiers
- inspect environment variables
- identify suspicious permissions
- inspect native plugins
- inspect build profiles
- compare iOS and Android configuration
- identify likely release blockers
- generate a configuration review report

A safe workflow:

```text
Repository
    ↓
AI scans configuration
    ↓
Candidate findings
    ↓
Developer verifies
    ↓
Build
    ↓
Artifact inspection
    ↓
Real-device test
    ↓
Human approval
```

AI output is not proof.

Do not let an AI agent change production configuration or publish a release without explicit review and bounded permissions.

---

## 43. AI configuration audit prompt

```text
Audit this mobile application's release configuration.

Framework:
- Expo / React Native

Platforms:
- iOS
- Android

Inspect:

1. app.json / app.config.*
2. eas.json
3. package.json
4. ios/
5. android/
6. environment configuration
7. CI/CD configuration
8. application identifiers
9. versioning
10. minimum OS settings
11. Android SDK settings
12. permissions
13. iOS entitlements/capabilities
14. deep-link configuration
15. push notification configuration
16. analytics configuration
17. crash reporting configuration
18. authentication/OAuth configuration
19. payment configuration
20. production vs development differences

Return:

CONFIGURATION MAP
MISMATCHES
PRODUCTION RISKS
SECURITY RISKS
NATIVE CHANGES
RELEASE BLOCKERS
FILES TO VERIFY
TESTS REQUIRED

Do not modify files.

Do not invent platform requirements.

For uncertain findings, say what must be verified and identify the relevant official documentation.
```

---

# 44. Common configuration failures

## Production app points to staging

### Symptoms

- real users see test data
- staging API appears in production logs
- production authentication behaves incorrectly

### Checks

```text
Build profile
→ environment
→ API URL
→ resolved config
→ actual artifact
```

### Fix

Correct the production environment and rebuild.

Do not assume changing a server variable fixes a value already bundled into the client.

---

## Wrong Bundle ID or Application ID

### Symptoms

- upload rejected
- build associated with the wrong app
- signing mismatch
- push notifications fail
- deep links fail

### Checks

```text
Expo config
→ native project
→ signing
→ store record
→ artifact
```

### Fix

Correct the identity before submission.

For a published app, do not change the identifier casually.

---

## Configuration differs between local and CI

### Symptoms

```text
Local build works
CI build fails
```

### Checks

- runtime/tool versions
- environment variables
- build profile
- lockfile
- signing configuration
- native project state
- secret availability

### Fix

Find the first meaningful difference.

Do not immediately upgrade every tool.

---

## Native feature works locally but not in production

### Checks

- native package
- config plugin
- permissions
- entitlements
- production build profile
- signing
- actual device
- production environment

### Fix

Reproduce using the same production build path.

---

## Deep links work in development but not production

Check:

```text
Scheme/domain
→ native configuration
→ entitlements / intent filters
→ domain verification
→ production identifier
→ installed production build
```

---

## Push works in development but not production

Check:

```text
Application identity
→ production credentials
→ production notification configuration
→ device token
→ backend provider
→ production build
```

Do not rotate credentials before verifying the application identity and environment.

---

# 45. Configuration change checklist

Before merging a release-affecting configuration change:

- [ ] Purpose of the change is documented.
- [ ] Affected platforms are known.
- [ ] Environment impact is known.
- [ ] Native impact is known.
- [ ] Security impact is reviewed.
- [ ] Store impact is reviewed.
- [ ] Required tests are identified.
- [ ] Required build is identified.
- [ ] Rollback/mitigation is understood.

After the change:

- [ ] Tests pass.
- [ ] Production build passes.
- [ ] Actual artifact is inspected.
- [ ] Real-device behavior is verified.
- [ ] Store submission state is correct where applicable.

---

# 46. Production configuration gate

Before production release:

- [ ] Correct source commit.
- [ ] Correct environment.
- [ ] Correct build profile.
- [ ] Correct iOS Bundle ID.
- [ ] Correct Android Application ID.
- [ ] Correct version.
- [ ] Correct iOS build number.
- [ ] Correct Android version code.
- [ ] Correct minimum OS support.
- [ ] Correct Android SDK configuration.
- [ ] Correct API endpoints.
- [ ] Correct feature flags.
- [ ] Correct permissions.
- [ ] Correct iOS capabilities/entitlements.
- [ ] Correct Android manifest configuration.
- [ ] Correct deep links.
- [ ] Correct push configuration.
- [ ] Correct analytics.
- [ ] Correct crash reporting.
- [ ] Correct authentication/OAuth configuration.
- [ ] Correct payment configuration where applicable.
- [ ] No development credentials.
- [ ] No production secrets bundled into the client.
- [ ] Signing is correct.
- [ ] Actual production artifact is verified.
- [ ] Real-device smoke test passes.

---

# 47. Recommended configuration model

For most Expo and React Native projects, keep the configuration model simple:

```text
Source code
    +
Expo/native configuration
    +
Build profiles
    +
Environment configuration
    +
Protected CI secrets
```

Avoid creating a custom configuration service unless the product has a real requirement for runtime configuration management.

For many applications:

```text
Development
→ local/development config

Preview
→ test config

Production
→ production config
```

is enough.

---

# 48. What not to do

### Do not put secrets in `.env`

A `.env` file is not automatically secure.

### Do not hard-code staging URLs

Keep environment selection explicit.

### Do not duplicate configuration everywhere

Too many copies create drift.

### Do not assume Expo config always controls native projects

Verify the actual project workflow.

### Do not change production identifiers casually

They define application identity.

### Do not make CI silently choose a different environment

The release should be traceable.

### Do not let AI modify production configuration without review

AI output and actions require verification and bounded permissions.

### Do not create a configuration platform for a problem that a few files can solve

Prefer simple configuration until the project has a real scaling or operational requirement.

---

# 49. Definition of done

Project configuration is ready when:

```text
Configuration is explicit
        +
Environment is correct
        +
Identifiers are correct
        +
Native settings are aligned
        +
Build profile is correct
        +
Secrets are protected
        +
Actual artifact is verified
        +
Real-device behavior is tested
        ↓
   CONFIGURATION READY
```

The key rule is:

> **Do not trust configuration because it looks correct. Build it, inspect the result, and verify the behavior.**

---

# Related documentation

### Foundations

- `foundations/mobile-release-lifecycle.md`
- `foundations/release-environments.md`
- `foundations/identifiers.md`
- `foundations/versioning.md`
- `foundations/device-support.md`
- `foundations/dependency-management.md`

### Signing

- `signing/ios/`
- `signing/android/`
- `signing/security/`

### Release engineering

- `release-engineering/eas/`
- `release-engineering/github-actions/`
- `release-engineering/environment-management.md`
- `release-engineering/release-pipelines.md`

### Testing

- `testing/release-smoke-tests.md`
- `testing/device-testing.md`

### AI

- `ai/workflows/release-audit.md`
- `ai/workflows/debugging.md`
- `ai/security/secret-protection.md`
- `ai/security/destructive-actions.md`

---

# Official sources

Use official documentation for current configuration behavior.

### Expo

- App configuration: https://docs.expo.dev/versions/latest/config/app/
- Environment variables: https://docs.expo.dev/guides/environment-variables/
- EAS environment variables: https://docs.expo.dev/eas/environment-variables/
- EAS Build configuration: https://docs.expo.dev/build/eas-json/
- Config plugins: https://docs.expo.dev/config-plugins/introduction/
- Prebuild: https://docs.expo.dev/workflow/prebuild/
- Splash screen: https://docs.expo.dev/versions/latest/sdk/splash-screen/
- Build properties: https://docs.expo.dev/versions/latest/sdk/build-properties/

### React Native

- Environment variables and native configuration should be verified against the current React Native and platform documentation:
  https://reactnative.dev/docs/getting-started

### Apple

- Bundle identifier: https://developer.apple.com/documentation/BundleResources/Information-Property-List/CFBundleIdentifier
- App Store Connect Help: https://developer.apple.com/help/app-store-connect/
- Apple Developer: https://developer.apple.com/

### Android

- Android app manifest: https://developer.android.com/guide/topics/manifest/manifest-intro
- Android build configuration: https://developer.android.com/build
- Android compatibility: https://developer.android.com/guide/practices/compatibility
- Google Play target API requirements: https://developer.android.com/google/play/requirements/target-sdk

### GitHub Actions

- GitHub Actions documentation: https://docs.github.com/actions

**Last verified:** August 11, 2026

Configuration properties, Expo tooling, Android SDK requirements, Apple build requirements, and store workflows can change. Re-check the relevant official source before changing release configuration or creating a production build.
