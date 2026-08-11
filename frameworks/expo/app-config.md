# Expo App Configuration

Expo app configuration is the release-time definition of how an Expo application is identified, built, configured, and prepared for native platforms.

The main configuration files are:

```text
app.json
app.config.js
app.config.ts
```

Expo uses app configuration for areas including Expo Prebuild generation, Expo Go behavior, and the OTA update manifest. The configuration file belongs at the project root, next to `package.json`. citeturn0view1

This guide focuses on **release engineering**, not general Expo development.

The goal is:

```text
Source configuration
        ↓
Resolved Expo configuration
        ↓
Native/build configuration
        ↓
Release artifact
        ↓
Verified production behavior
```

The key rule is:

> **Do not trust an app configuration file because it looks correct. Resolve it, inspect it, build it, and verify the resulting artifact.**

---

# 1. What app configuration controls

Expo app configuration can define release-relevant settings such as:

- application name
- Expo project identity
- application version
- iOS Bundle ID
- Android Application ID
- icons
- splash configuration
- URL schemes
- deep-link configuration
- native permissions
- iOS entitlements
- privacy manifests
- Android configuration
- native plugins
- update configuration
- runtime version
- environment-dependent configuration
- additional public application configuration

The complete set of properties is version-dependent.

Use the current Expo configuration reference for the SDK used by the project:

https://docs.expo.dev/versions/latest/config/app/

---

# 2. Which configuration file should you use?

Expo supports both static and dynamic configuration.

## Static

```text
app.json
```

or:

```text
app.config.json
```

Static configuration is appropriate when the values are fixed and do not need JavaScript logic.

Example:

```json
{
  "expo": {
    "name": "Example",
    "slug": "example",
    "version": "1.0.0"
  }
}
```

## Dynamic

```text
app.config.js
```

or:

```text
app.config.ts
```

Use dynamic configuration when values need to be computed or selected based on environment information.

Example:

```ts
import type { ExpoConfig, ConfigContext } from 'expo/config';

export default ({ config }: ConfigContext): ExpoConfig => ({
  ...config,
  name: 'Example',
  version: '1.0.0',
});
```

Expo currently supports TypeScript configuration and documents `app.config.ts` as a way to get TypeScript support and configuration-time logic. citeturn1view0

---

# 3. Prefer the simplest configuration that works

Use:

```text
app.json
```

when the configuration is static.

Move to:

```text
app.config.ts
```

when you actually need:

- environment-specific values
- computed configuration
- reusable configuration logic
- TypeScript
- configuration functions
- dynamic plugin configuration

Do not create an `app.config.ts` simply because TypeScript is available.

More logic means more ways for a release configuration to behave differently from what a developer expects.

A good rule:

```text
Static configuration
→ app.json

Dynamic configuration
→ app.config.ts
```

---

# 4. Configuration resolution

Expo has a defined configuration resolution process.

The important model is:

```text
Static configuration
        ↓
Dynamic configuration
        ↓
Final resolved configuration
```

Current Expo resolution rules include:

1. Static configuration is read when present.
2. `app.config.json` takes precedence over `app.json`.
3. If no static config exists, Expo can infer defaults from the project.
4. `app.config.ts` or `app.config.js` is used for dynamic configuration.
5. If both dynamic files exist, the TypeScript configuration is used.
6. A dynamic config function receives the normalized static configuration.
7. The returned dynamic configuration becomes the final configuration.
8. The final configuration cannot depend on Promises.
9. `npx expo config` shows the resolved configuration Expo will use.

These rules matter because the file you open in the editor is not necessarily the final configuration consumed by the Expo tooling. citeturn1view0

---

# 5. Always inspect the resolved configuration

Before a release, inspect what Expo actually resolves.

Use:

```bash
npx expo config
```

For the public configuration that will be available to the application:

```bash
npx expo config --type public
```

Expo specifically documents `npx expo config --type public` as a way to inspect the configuration that will be embedded in builds/updates and made available at runtime. citeturn1view0

This is one of the highest-value checks in an Expo release workflow.

Do not rely only on:

```text
app.json
```

or:

```text
app.config.ts
```

---

# 6. Basic production configuration

A simple production configuration might look like:

```json
{
  "expo": {
    "name": "Example",
    "slug": "example",
    "version": "1.4.0",
    "ios": {
      "bundleIdentifier": "com.example.app",
      "buildNumber": "42"
    },
    "android": {
      "package": "com.example.app",
      "versionCode": 42
    }
  }
}
```

This is an example only.

Use the actual identifiers and versioning strategy defined by the project.

See:

```text
foundations/identifiers.md
foundations/versioning.md
```

for the shared release rules.

---

# 7. Application name

The top-level:

```json
{
  "expo": {
    "name": "Example"
  }
}
```

defines the app name used by Expo and the standalone app's home-screen identity in the Expo configuration model. citeturn0view0

Do not confuse:

```text
name
```

with:

```text
slug
```

or:

```text
Bundle ID
Application ID
```

They serve different purposes.

Changing the visible name does not mean the application identity should change.

---

# 8. Expo slug

Example:

```json
{
  "expo": {
    "slug": "example"
  }
}
```

The slug is a URL-friendly project name associated with the Expo project. Expo documents it as a unique project name within the relevant account context. citeturn0view0

Do not use the slug as a substitute for:

```text
iOS Bundle ID
Android Application ID
```

Keep those identifiers explicit.

---

# 9. Expo project ownership

Expo configuration can also include:

```json
{
  "expo": {
    "owner": "example-team"
  }
}
```

The owner identifies the Expo account that owns the project.

For team projects, make sure the Expo project ownership matches the intended organization/account.

Do not accidentally create production builds under a personal Expo account when the project is intended to belong to a team account.

---

# 10. iOS Bundle ID

The production iOS identifier belongs under:

```text
ios.bundleIdentifier
```

Example:

```json
{
  "expo": {
    "ios": {
      "bundleIdentifier": "com.example.app"
    }
  }
}
```

This identifier must align with:

```text
Apple Developer
App Store Connect
Signing
Push configuration
Associated domains
OAuth configuration
Other native services
```

Do not change it casually after production release.

See:

```text
foundations/identifiers.md
```

and:

```text
signing/ios/
```

---

# 11. Android Application ID

The Android application identifier belongs under:

```text
android.package
```

Example:

```json
{
  "expo": {
    "android": {
      "package": "com.example.app"
    }
  }
}
```

It must align with:

```text
Google Play
Android signing
Firebase where applicable
Push configuration
Deep links
Other platform services
```

Treat the production application ID as stable identity.

See:

```text
foundations/identifiers.md
```

---

# 12. Version configuration

Expo can define the application version through:

```json
{
  "expo": {
    "version": "1.4.0"
  }
}
```

Platform-specific build identifiers can also be configured:

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

The exact source of truth depends on whether the project manages versions manually or through EAS version management.

See:

```text
foundations/versioning.md
```

Do not maintain several independent version sources without a deliberate reason.

---

# 13. Environment-specific configuration

One of the main reasons to use `app.config.ts` is environment-specific configuration.

For example:

```text
Development
→ com.example.app.dev

Preview
→ com.example.app.preview

Production
→ com.example.app
```

Or:

```text
Development
→ development API

Preview
→ staging API

Production
→ production API
```

Expo documents using dynamic app configuration together with environment variables to switch configuration between environments. citeturn1view0

Keep the logic explicit.

Example:

```ts
import type { ExpoConfig, ConfigContext } from 'expo/config';

export default ({ config }: ConfigContext): ExpoConfig => {
  const environment = process.env.APP_ENV ?? 'development';

  const identifiers = {
    development: 'com.example.app.dev',
    preview: 'com.example.app.preview',
    production: 'com.example.app',
  } as const;

  return {
    ...config,
    ios: {
      ...config.ios,
      bundleIdentifier: identifiers[environment as keyof typeof identifiers],
    },
    android: {
      ...config.android,
      package: identifiers[environment as keyof typeof identifiers],
    },
  };
};
```

The example is illustrative.

The project should use its own validated environment model.

---

# 14. Environment variables are not automatically secrets

A mobile application cannot keep a true secret once a value is bundled into the client.

Public configuration can include:

```text
API URL
App identifier
Public feature configuration
Public analytics identifier
```

Do not put:

```text
Database passwords
Private API keys
Signing private keys
OAuth client secrets
Service-account credentials
Payment private keys
```

into client-visible configuration.

Expo explicitly warns that `EXPO_PUBLIC_*` variables are visible in compiled application code. citeturn0view3

---

# 15. Expo public environment variables

Expo CLI automatically loads variables with the:

```text
EXPO_PUBLIC_
```

prefix from `.env` files for use in JavaScript. citeturn0view3

Example:

```text
EXPO_PUBLIC_API_URL=https://staging.example.com
```

Application code can use:

```ts
const apiUrl = process.env.EXPO_PUBLIC_API_URL;
```

The reference must use static dot notation for Expo CLI inlining:

```ts
process.env.EXPO_PUBLIC_API_URL
```

Do not rely on:

```ts
process.env['EXPO_PUBLIC_API_URL']
```

or:

```ts
const { EXPO_PUBLIC_API_URL } = process.env;
```

Expo documents that those alternative forms are not supported for its inlining behavior. citeturn0view3

---

# 16. `.env` files

A practical local setup can use:

```text
.env
.env.local
```

Example:

```text
EXPO_PUBLIC_API_URL=http://localhost:3000
```

Local-machine-specific configuration should normally remain out of Git.

A common `.gitignore` rule is:

```gitignore
.env*.local
```

Expo recommends ignoring local environment files such as `.env.local` because they commonly contain machine-specific configuration. citeturn0view3

Do not assume that every `.env` file is secret.

If it is committed, its contents should be treated as public.

---

# 17. Do not use NODE_ENV as your environment switch

Do not build the release model around:

```text
NODE_ENV=development
NODE_ENV=staging
NODE_ENV=production
```

Expo explicitly recommends against using `NODE_ENV` to switch between environment files because Expo tooling can force or change `NODE_ENV` for certain operations. citeturn0view3

Use a dedicated variable when you need an application environment selector:

```text
APP_ENV
```

or the project's chosen equivalent.

For example:

```text
APP_ENV=development
APP_ENV=preview
APP_ENV=production
```

Keep the environment selection explicit.

---

# 18. EAS environment mapping

When using EAS, environment selection should align with the build profile.

A common model is:

```text
development
    ↓
development environment

preview
    ↓
preview environment

production
    ↓
production environment
```

Example:

```json
{
  "build": {
    "development": {
      "environment": "development"
    },
    "preview": {
      "environment": "preview"
    },
    "production": {
      "environment": "production"
    }
  }
}
```

Keep this mapping in:

```text
eas.json
```

rather than hiding it in application logic.

See:

```text
foundations/release-environments.md
```

for the shared environment model.

---

# 19. Build-time vs runtime configuration

This distinction is critical.

## Build-time configuration

Values that affect the native application or build artifact.

Examples:

```text
Bundle ID
Application ID
Native permissions
Entitlements
Native plugins
App icon
Splash configuration
Native dependencies
```

Changing these generally requires a new native build.

## Runtime configuration

Values that can be changed without rebuilding when they are obtained from a trusted runtime source.

Examples:

```text
Feature flags
Remote configuration
Rollout percentage
Non-sensitive UI configuration
```

Do not build a remote configuration system just to avoid changing a static value that rarely changes.

Keep configuration simple.

---

# 20. The `extra` field

Expo supports:

```json
{
  "expo": {
    "extra": {
      "apiEnvironment": "production"
    }
  }
}
```

Values in `extra` can be accessed through:

```ts
Constants.expoConfig.extra
```

Expo documents that `extra` values are passed to the application and are available through `Constants.expoConfig.extra`. citeturn1view0turn1view1

Treat `extra` as public application configuration.

Do not use it as a secret store.

---

# 21. Reading configuration at runtime

When application code needs Expo configuration, prefer:

```ts
import Constants from 'expo-constants';

const config = Constants.expoConfig;
```

Do not directly import:

```ts
import appConfig from './app.config';
```

Expo warns that directly importing the configuration file imports the entire file rather than the processed configuration.

Use the processed runtime configuration instead. citeturn1view0

---

# 22. Inspecting public configuration

Use:

```bash
npx expo config --type public
```

This is especially useful before:

- production builds
- environment changes
- identifier changes
- OTA update publishing
- native configuration changes
- store submissions

Look for:

```text
name
slug
version
ios.bundleIdentifier
ios.buildNumber
android.package
android.versionCode
scheme
updates
runtimeVersion
plugins
extra
```

and any project-specific release-critical properties.

---

# 23. Native project awareness

Expo projects can exist in different native workflows.

You may have:

```text
app.json
app.config.ts
```

without checked-in native projects.

Or:

```text
app.json
app.config.ts
ios/
android/
```

When native projects are present, do not assume every app-config value automatically changes the native project in every workflow.

This is especially important for:

- Bundle ID
- Application ID
- permissions
- entitlements
- native dependencies
- capabilities
- Info.plist
- AndroidManifest
- Gradle configuration

Before production release:

```text
Resolved Expo config
        +
Native project
        ↓
must agree on release-critical values
```

See:

```text
foundations/project-configuration.md
```

for the shared configuration model.

---

# 24. Expo Prebuild

Expo config is also used by Expo Prebuild to generate or update native projects.

The basic relationship is:

```text
Expo app config
        ↓
Expo Prebuild
        ↓
ios/
android/
```

Config plugins can extend this process for libraries and native capabilities. Expo documents config plugins as a mechanism primarily used to configure `npx expo prebuild`. citeturn1view0

If the project uses generated native projects, understand whether:

```text
native changes
```

are generated from:

```text
app.config.ts
```

or maintained manually.

Do not let the repository contain conflicting sources of truth.

---

# 25. Config plugins

Native libraries may require configuration changes beyond JavaScript.

Expo config plugins can modify native project configuration during Prebuild.

Typical areas include:

```text
Info.plist
AndroidManifest.xml
Entitlements
Gradle
Native capabilities
Permissions
```

Use plugins when they solve an actual native configuration problem.

Do not add custom config plugins just to wrap simple static configuration.

After adding or changing a plugin:

```text
Install
    ↓
Resolve config
    ↓
Run/update Prebuild when appropriate
    ↓
Inspect native changes
    ↓
Build
    ↓
Test on a real device
```

---

# 26. iOS native configuration

Expo exposes platform-specific iOS configuration for areas such as:

```text
bundleIdentifier
buildNumber
infoPlist
entitlements
privacyManifests
associatedDomains
usesAppleSignIn
googleServicesFile
```

The exact schema is SDK-dependent.

Examples from the current Expo configuration reference include:

```json
{
  "expo": {
    "ios": {
      "bundleIdentifier": "com.example.app",
      "buildNumber": "42",
      "supportsTablet": true,
      "infoPlist": {},
      "entitlements": {}
    }
  }
}
```

Expo warns that arbitrary `ios.infoPlist` and `ios.entitlements` values are applied without validation, so incorrect configuration can lead to platform or App Store problems. citeturn1view2

Do not add native keys unless you understand the platform requirement.

---

# 27. iOS privacy manifests

Current Expo configuration includes support for:

```text
ios.privacyManifests
```

which can define privacy manifest information in the generated native project. citeturn1view2

This area is tied to current Apple requirements and can change.

Do not copy a privacy manifest from another project without verifying:

```text
Which APIs are actually used
Which SDKs are included
Which required reasons apply
```

Use Apple's current documentation as the authority.

---

# 28. Android native configuration

Expo also exposes Android-specific configuration.

Release-sensitive areas can include:

```text
package
versionCode
permissions
intent filters
adaptive icon
googleServicesFile
queries
software keyboard configuration
native plugin configuration
```

The exact available properties depend on the Expo SDK.

Use the current Expo app configuration reference instead of relying on an old example:

https://docs.expo.dev/versions/latest/config/app/

---

# 29. URL schemes

Expo supports:

```json
{
  "expo": {
    "scheme": "example"
  }
}
```

This creates a custom URL scheme such as:

```text
example://
```

Expo documents `scheme` as build-time configuration for linking into the app. It does not affect Expo Go in the same way as a standalone build. citeturn1view1

Treat schemes as part of application identity and deep-link configuration.

Before production:

```text
Scheme
    ↓
iOS
    +
Android
    ↓
Deep-link routing
    ↓
OAuth callbacks
    ↓
Tested release build
```

See:

```text
integrations/deep-links/
```

for the shared release guidance.

---

# 30. Updates configuration

Expo configuration can define:

```text
updates
```

for `expo-updates`.

Examples include:

```text
updates.enabled
updates.checkAutomatically
updates.useEmbeddedUpdate
updates.fallbackToCacheTimeout
updates.url
updates.runtimeVersion
```

The current Expo reference documents these settings and their behavior. citeturn1view1

Treat update configuration as release-critical.

An incorrect update configuration can affect which JavaScript bundle users receive.

See:

```text
release-strategy/
release-engineering/
post-release/
```

for the broader release process.

---

# 31. Runtime version

If the project uses Expo Updates, runtime compatibility matters.

A native build can establish a runtime boundary:

```text
Native build
        ↓
Runtime version
        ↓
Compatible OTA updates
```

Expo supports runtime version policies including:

```text
nativeVersion
sdkVersion
appVersion
fingerprint
```

and platform-specific runtime configuration. citeturn1view2

Do not change runtime version strategy casually.

Before changing it, understand:

```text
Which native builds exist
Which updates are published
Which users are on which runtime
```

See:

```text
release-environments.md
```

and:

```text
release-strategy/
```

---

# 32. Do not use OTA to bypass native changes

If a change requires native code or native configuration:

```text
New native build
```

is generally required.

Do not assume an OTA update can safely change:

```text
Native permissions
Native dependencies
Bundle ID
Application ID
Entitlements
Native capabilities
Native SDK configuration
```

A useful rule is:

```text
JavaScript-only compatible change
→ OTA may be appropriate

Native/runtime change
→ new native build
```

Always verify compatibility with the project's Expo Updates configuration.

---

# 33. Configuration and signing

App configuration affects signing because signing is tied to application identity and native capabilities.

For example:

```text
ios.bundleIdentifier
        ↓
Apple Developer identifier
        ↓
Signing configuration
        ↓
Production binary
```

and:

```text
android.package
        ↓
Android application identity
        ↓
Signing
        ↓
Play distribution
```

Do not change identifiers after signing setup without checking the complete dependency chain.

See:

```text
signing/
foundations/identifiers.md
```

---

# 34. Configuration and permissions

Permissions are release configuration.

Examples include:

```text
Camera
Location
Notifications
Microphone
Contacts
Photos
Bluetooth
```

Only request permissions the application actually needs.

For each permission:

```text
Why is it needed?
        ↓
Is it actually used?
        ↓
Is the platform description correct?
        ↓
Is the store disclosure correct?
        ↓
Is the production build requesting it?
```

Do not add broad permissions just because a library supports them.

See:

```text
pre-release/permissions-audit.md
privacy-compliance/
```

---

# 35. Configuration and third-party services

App configuration often connects the app to external services.

Examples:

```text
Firebase
Maps
Push notifications
OAuth
Analytics
Crash reporting
Payments
Deep links
```

For each service, verify:

```text
Environment
+
Application identifier
+
Configuration file/key
+
Native capability
+
Production account
```

A correct API URL does not mean the whole production configuration is correct.

---

# 36. Configuration and Firebase

Where Firebase is used, platform-specific configuration files can be referenced from Expo configuration.

Examples:

```text
ios.googleServicesFile
android.googleServicesFile
```

The files identify the native Firebase application configuration.

Do not commit secrets or private credentials unnecessarily.

More importantly, verify that the configuration file belongs to the intended environment and application identifier.

A common failure is:

```text
Production build
→ staging Firebase project
```

or:

```text
Preview build
→ production Firebase project
```

---

# 37. Configuration and analytics

Do not assume:

```text
API = production
```

means:

```text
Analytics = production
```

Explicitly verify:

```text
Analytics project
Analytics identifier
Environment metadata
Debug mode
Event destination
```

Production analytics should not be polluted with uncontrolled developer traffic.

---

# 38. Configuration and crash reporting

Crash reporting should identify the release accurately.

Where supported, preserve:

```text
Environment
Version
Build
Commit
```

The configuration should point to the intended project/account.

Before production:

```text
Install production build
        ↓
Trigger safe test crash if appropriate
        ↓
Confirm crash appears in correct project
        ↓
Confirm version/build metadata
```

Do not perform destructive production tests merely to verify configuration.

---

# 39. Configuration and push notifications

Push configuration is especially sensitive to application identity.

Verify:

```text
Development
→ test push configuration

Preview
→ test/internal configuration

Production
→ production configuration
```

Check:

```text
Bundle ID
Application ID
Push credentials
Project/account
Notification service
Environment
```

Then test on a real device.

Do not use a production push configuration for routine development testing unless the workflow explicitly requires it.

---

# 40. Configuration and payments

Payment configuration should be environment-specific.

Prefer:

```text
Development
→ sandbox/test

Preview
→ sandbox/test

Production
→ production
```

Never place privileged payment credentials in the mobile app.

The client should communicate with a trusted backend for operations that require private credentials.

---

# 41. Configuration and OAuth

OAuth configuration can depend on:

```text
Bundle ID
Application ID
URL scheme
Universal links
Redirect URI
Client ID
Environment
```

If one of these changes, the OAuth flow may break.

Before release:

```text
Install production build
        ↓
Start authentication
        ↓
Complete provider login
        ↓
Return to app
        ↓
Verify correct environment
```

Do not test OAuth only in Expo Go when production behavior depends on native identifiers or schemes.

---

# 42. White-label applications

Dynamic app configuration can support multiple branded applications.

For example:

```text
Brand A
→ com.company.brand-a

Brand B
→ com.company.brand-b
```

This can be useful, but it increases release complexity.

Each application may need separate:

```text
Store record
Signing
Identifiers
Push
OAuth
Analytics
Crash reporting
Deep links
Privacy configuration
```

Do not introduce white-label configuration unless the product actually requires it.

---

# 43. Configuration validation in CI

CI should validate the resolved configuration before production builds.

A simple check:

```bash
npx expo config --type public
```

Then inspect or validate:

```text
name
version
Bundle ID
Application ID
environment
scheme
runtimeVersion
update configuration
```

For automated checks, fail the build when critical values are wrong.

Example logic:

```text
Expected environment:
production

Resolved environment:
staging

→ FAIL
```

Do not let a production workflow continue after a known environment mismatch.

---

# 44. Configuration validation script

A project can add a small deterministic validation script.

For example:

```text
scripts/build-validation/
```

The script can check:

```text
Production Bundle ID
Production Application ID
Production environment
Expected app name
Expected version
Required configuration
Forbidden development URLs
Forbidden staging URLs
```

Keep the script simple.

Do not build a configuration platform for a handful of checks.

---

# 45. Release configuration snapshot

Before a production build, record the resolved release configuration.

Example:

```text
Environment:
production

App:
Example

Version:
1.4.0

iOS Bundle ID:
com.example.app

Android Application ID:
com.example.app

Build profile:
production

Runtime version:
1.4.0

Commit:
8f3a1c2
```

Do not record secrets.

This makes release debugging much easier.

---

# 46. AI-assisted configuration audit

AI can help audit Expo configuration.

Useful tasks:

- inspect `app.json`
- inspect `app.config.ts`
- inspect `eas.json`
- inspect `.env` usage
- identify environment mismatches
- compare iOS and Android identifiers
- identify likely secret exposure
- identify native plugin configuration
- inspect update/runtime configuration
- compare configuration with release requirements

A safe workflow:

```text
Repository
        ↓
AI reads configuration
        ↓
AI identifies potential risks
        ↓
Developer verifies against official docs
        ↓
Resolved config is inspected
        ↓
Build
        ↓
Real-device verification
        ↓
Human approval
```

AI should not silently change production configuration.

---

# 47. AI configuration audit prompt

```text
Audit this Expo / React Native repository for release configuration.

Inspect:

- app.json
- app.config.js
- app.config.ts
- eas.json
- package.json
- ios/
- android/
- .env files and environment-variable references
- CI/CD configuration

Determine:

1. application name
2. Expo project identity
3. iOS Bundle ID
4. Android Application ID
5. application version
6. iOS build number
7. Android version code
8. environment model
9. production API configuration
10. public environment variables
11. possible secrets
12. native plugins
13. permissions
14. iOS entitlements
15. privacy manifest configuration
16. URL schemes
17. update configuration
18. runtime version
19. Firebase configuration where present
20. analytics/crash configuration where discoverable

Check for:

- development configuration in production
- staging configuration in production
- mismatched identifiers
- duplicate sources of truth
- unsafe secrets
- invalid or obsolete configuration
- native configuration drift
- OTA/runtime risks
- missing production values

Use current official Expo documentation for version-sensitive claims.

Do not modify files.

Do not invent missing values.

Mark anything that cannot be verified as NEEDS VERIFICATION.

Return:

CONFIGURATION MAP
PRODUCTION RISKS
SECURITY RISKS
IDENTIFIER RISKS
ENVIRONMENT RISKS
UPDATE/RUNTIME RISKS
NATIVE CONFIGURATION RISKS
FILES TO VERIFY
RECOMMENDED FIXES
```

---

# 48. Common failure: wrong environment

### Symptom

Production build connects to staging.

### Check

```text
APP_ENV
EAS environment
app.config.ts
EXPO_PUBLIC_API_URL
CI configuration
```

Then run:

```bash
npx expo config --type public
```

### Fix

Correct the environment mapping.

Rebuild if the incorrect value was bundled into the application.

Do not assume changing a server-side variable will change a client value that was already embedded into the binary.

---

# 49. Common failure: wrong Bundle ID

### Symptom

iOS signing or App Store submission fails.

### Check

```text
ios.bundleIdentifier
Apple Developer Bundle ID
App Store Connect
native project if committed
```

### Fix

Make the configuration and Apple records consistent.

Do not create a new production Bundle ID simply to bypass a configuration problem.

---

# 50. Common failure: wrong Android Application ID

### Symptom

Google Play or signing configuration does not match.

### Check

```text
android.package
Android applicationId
Google Play application
native project if committed
```

### Fix

Align the configuration with the existing production application identity.

Do not change a production application ID casually.

---

# 51. Common failure: app.config.ts works locally but fails in CI

### Check

```text
Required environment variables
Node/runtime version
Imported files
Config dependencies
CI environment
```

Remember:

```text
app.config.ts
```

runs as configuration code.

It must be deterministic and should not depend on unavailable local state.

Avoid:

```text
local filesystem assumptions
developer machine paths
interactive prompts
network calls
secrets that are not available in CI
```

Expo configuration functions cannot use Promises in the final configuration process. citeturn1view0

---

# 52. Common failure: config changed but native app did not

### Symptom

`app.config.ts` shows the new value, but the native project/build still uses the old value.

### Check

```text
ios/
android/
Expo Prebuild workflow
native configuration
config plugins
```

This often indicates configuration drift or a workflow misunderstanding.

### Fix

Determine which source is authoritative for the project.

Then regenerate/update native configuration when appropriate or change the native project directly if the project intentionally manages native code manually.

Do not repeatedly run commands without understanding which source is supposed to own the value.

---

# 53. Common failure: secret exposed through configuration

### Symptom

A private key or credential appears in:

```text
app.config.ts
extra
EXPO_PUBLIC_*
compiled JavaScript
```

### Response

Treat it as exposed.

```text
Revoke
    ↓
Rotate
    ↓
Remove from source
    ↓
Remove from future builds
    ↓
Audit Git history
    ↓
Move privileged operation server-side
```

Do not assume that moving the variable to another client-side configuration field makes it secret.

---

# 54. Common failure: OTA update uses wrong configuration

### Symptom

A production application receives an update configured for another environment.

### Check

```text
EAS environment
Update command
app.config.ts
runtimeVersion
Update channel/branch strategy where used
```

Verify the resolved configuration before publishing.

Do not publish an OTA update simply because the JavaScript build succeeds.

---

# 55. Production configuration checklist

## Identity

- [ ] `name` is correct.
- [ ] `slug` is correct.
- [ ] Expo owner/account is correct.
- [ ] iOS Bundle ID is correct.
- [ ] Android Application ID is correct.

## Version

- [ ] Application version is correct.
- [ ] iOS build number is correct.
- [ ] Android version code is correct.
- [ ] Version source of truth is known.

## Environment

- [ ] Production environment is explicit.
- [ ] Production API is configured.
- [ ] Development/staging URLs are absent from production configuration.
- [ ] Analytics configuration is correct.
- [ ] Crash reporting configuration is correct.
- [ ] Push configuration is correct.
- [ ] Payment configuration is correct where applicable.

## Native

- [ ] Required plugins are configured.
- [ ] Permissions are correct.
- [ ] iOS entitlements are correct.
- [ ] Privacy manifest configuration is correct where applicable.
- [ ] Android native configuration is correct.
- [ ] URL schemes are correct.
- [ ] Deep links are correct.

## Updates

- [ ] Update configuration is intentional.
- [ ] Runtime version strategy is understood.
- [ ] OTA compatibility is verified.
- [ ] Production update environment is correct.

## Security

- [ ] No private credentials are in app config.
- [ ] No privileged secrets use `EXPO_PUBLIC_*`.
- [ ] `extra` contains public configuration only.
- [ ] CI secrets remain in protected secret storage.

## Verification

- [ ] `npx expo config` was inspected.
- [ ] `npx expo config --type public` was inspected.
- [ ] Native configuration was checked where applicable.
- [ ] Production build was created.
- [ ] Production artifact was tested on a real device.
- [ ] Human approval is complete.

---

# 56. Production release gate

Do not submit the production build until:

```text
Resolved config
        ↓
matches intended release
        ↓
native configuration
        ↓
matches resolved identity
        ↓
environment
        ↓
is production
        ↓
secrets
        ↓
are protected
        ↓
artifact
        ↓
is verified
```

If any critical value is uncertain:

```text
STOP
→ verify
→ correct
→ rebuild
→ test
```

Do not guess.

---

# 57. Recommended configuration structure

For a medium-sized Expo application:

```text
mobile-app/
├── app.config.ts
├── eas.json
├── package.json
├── .env
├── .env.local
├── src/
├── scripts/
│   └── build-validation/
├── ios/
│   └── ...                  # if native projects are committed
├── android/
│   └── ...                  # if native projects are committed
└── ...
```

A simple `app.config.ts` should remain focused on configuration:

```text
Identity
+
Version
+
Environment
+
Native configuration
+
Plugins
+
Update configuration
```

Do not turn it into a general-purpose application configuration framework.

---

# 58. Example production-oriented `app.config.ts`

```ts
import type { ExpoConfig, ConfigContext } from 'expo/config';

const environments = {
  development: {
    name: 'Example Dev',
    bundleIdentifier: 'com.example.app.dev',
    packageName: 'com.example.app.dev',
  },
  preview: {
    name: 'Example Preview',
    bundleIdentifier: 'com.example.app.preview',
    packageName: 'com.example.app.preview',
  },
  production: {
    name: 'Example',
    bundleIdentifier: 'com.example.app',
    packageName: 'com.example.app',
  },
} as const;

type Environment = keyof typeof environments;

export default ({ config }: ConfigContext): ExpoConfig => {
  const environment =
    (process.env.APP_ENV as Environment | undefined) ?? 'development';

  const current = environments[environment];

  if (!current) {
    throw new Error(`Unsupported APP_ENV: ${environment}`);
  }

  return {
    ...config,

    name: current.name,

    ios: {
      ...config.ios,
      bundleIdentifier: current.bundleIdentifier,
    },

    android: {
      ...config.android,
      package: current.packageName,
    },

    extra: {
      ...config.extra,
      environment,
    },
  };
};
```

This example demonstrates a pattern, not a universal template.

Before adopting it, decide:

```text
Does the project really need separate application identities?
Does EAS already provide the required environment mapping?
Does the app need the environment at runtime?
```

Avoid adding configuration logic that solves no actual problem.

---

# 59. Configuration review with an AI agent

A useful bounded workflow is:

```text
AI
→ inspect repository

AI
→ resolve configuration assumptions

AI
→ identify mismatches

AI
→ propose changes

Developer
→ verify official documentation

Developer
→ inspect diff

Developer
→ run resolved config

Developer
→ build

Developer
→ test

Human
→ approve release
```

AI should not have unrestricted access to:

```text
Production store accounts
Signing credentials
Payment systems
Production databases
Production infrastructure
```

The agent should operate with the minimum permissions required for the task.

---

# 60. What not to do

### Do not put secrets in `app.config.ts`

Configuration can become part of the client-visible release.

### Do not assume `extra` is private

Treat it as public application configuration.

### Do not use `EXPO_PUBLIC_*` for secrets

Expo explicitly states these values are visible in compiled application code. citeturn0view3

### Do not use `NODE_ENV` as the entire release environment system

Expo recommends against using it to switch environment files. citeturn0view3

### Do not duplicate configuration sources without a reason

Multiple sources create drift.

### Do not assume app config and native projects are always synchronized

Verify the actual native project when it is committed.

### Do not blindly copy old Expo configuration

Configuration properties and behavior change between SDK versions.

### Do not put application business logic in `app.config.ts`

Keep it a configuration layer.

### Do not let AI silently modify production configuration

Require review and verification.

### Do not create a custom configuration platform too early

A few clear files and explicit validation are usually enough.

---

# 61. Definition of done

Expo app configuration is release-ready when:

```text
Configuration source is clear
        +
Final resolved configuration is known
        +
Identifiers are correct
        +
Version is correct
        +
Environment is correct
        +
Native configuration is aligned
        +
Plugins are verified
        +
Update/runtime configuration is understood
        +
Secrets are protected
        +
Production artifact is inspected
        +
Real-device behavior is verified
        ↓
   EXPO CONFIG READY
```

The key rule is:

> **The source file is not the release. The resolved configuration and resulting artifact are the release.**

---

# Related documentation

### Foundations

- `foundations/project-configuration.md`
- `foundations/release-environments.md`
- `foundations/identifiers.md`
- `foundations/versioning.md`
- `foundations/dependency-management.md`

### Expo

- `frameworks/expo/build.md`
- `frameworks/expo/eas.md`
- `frameworks/expo/submit.md`
- `frameworks/expo/common-failures.md`

### Signing

- `signing/ios/`
- `signing/android/`
- `signing/security/`

### Testing

- `testing/release-smoke-tests.md`
- `testing/device-testing.md`
- `testing/upgrade-migrations.md`

### Release engineering

- `release-engineering/eas/`
- `release-engineering/environment-management.md`
- `release-engineering/release-pipelines.md`

### Release strategy

- `release-strategy/feature-flags.md`
- `release-strategy/hotfixes.md`
- `release-strategy/staged-rollouts.md`

### Post-release

- `post-release/monitoring.md`
- `post-release/rollback.md`
- `post-release/hotfix.md`

### AI

- `ai/workflows/release-audit.md`
- `ai/workflows/release-preparation.md`
- `ai/workflows/debugging.md`
- `ai/security/secret-protection.md`
- `ai/security/destructive-actions.md`

---

# Official sources

Use official documentation as the authority for current Expo behavior.

### Expo

- App configuration reference: https://docs.expo.dev/versions/latest/config/app/
- Configure with app config: https://docs.expo.dev/workflow/configuration/
- Environment variables: https://docs.expo.dev/guides/environment-variables/
- EAS environment variables: https://docs.expo.dev/eas/environment-variables/
- EAS Build configuration: https://docs.expo.dev/build/eas-json/
- Expo Prebuild: https://docs.expo.dev/workflow/prebuild/
- Config plugins: https://docs.expo.dev/config-plugins/introduction/
- Expo Updates: https://docs.expo.dev/versions/latest/sdk/updates/

### Apple

- Apple Developer: https://developer.apple.com/
- App Store Connect Help: https://developer.apple.com/help/app-store-connect/

### Android

- Android Developers: https://developer.android.com/
- Google Play Console Help: https://support.google.com/googleplay/android-developer/

### React Native

- React Native documentation: https://reactnative.dev/docs/getting-started

**Last verified:** August 11, 2026

Expo configuration properties, EAS behavior, native platform requirements, and release tooling can change. Re-check the official documentation for the Expo SDK and platform versions used by the project before changing production configuration.
