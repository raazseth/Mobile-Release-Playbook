# React Native Android Release

This guide covers the **framework-specific Android release workflow for React Native applications**.

It assumes the project uses the standard React Native Android project structure:

```text
android/
├── app/
├── gradle/
├── build.gradle
├── gradle.properties
├── gradlew
├── gradlew.bat
└── settings.gradle
```

The guide focuses on:

- release configuration
- Gradle
- Android application identity
- versioning
- signing
- JavaScript bundling
- Android App Bundle generation
- release testing
- Google Play preparation
- CI/CD considerations
- React Native-specific failures

Shared Android publishing concepts remain in:

```text
checklists/android.md
publishing/android/
signing/android/
testing/android.md
foundations/versioning.md
foundations/identifiers.md
```

The framework guide explains **how React Native implements the shared release process** rather than duplicating the entire Android release playbook.

---

# 1. Release model

A React Native Android release should follow:

```text
React Native source
        ↓
Android project configuration
        ↓
Release configuration
        ↓
Signing
        ↓
JavaScript + native build
        ↓
AAB
        ↓
Artifact verification
        ↓
Real-device testing
        ↓
Google Play testing
        ↓
Production release
        ↓
Monitoring
```

The repository architecture deliberately keeps this framework-specific layer separate from the shared release lifecycle. The core repository is framework-agnostic, while React Native provides the implementation details for Android. fileciteturn28file2

---

# 2. Prerequisites

Before creating a production Android build, confirm:

```text
[ ] React Native project builds in development
[ ] Android project exists
[ ] Android SDK is installed
[ ] Required JDK is installed
[ ] Gradle wrapper works
[ ] Application ID is correct
[ ] Version name is correct
[ ] Version code is correct
[ ] Release signing is configured
[ ] Production environment is correct
[ ] Native dependencies are compatible
[ ] Release build has been tested
```

Do not start by changing Gradle versions or Android SDK versions unless there is a concrete compatibility problem.

For framework and platform versions, verify the project's React Native version and the corresponding React Native/Android documentation.

---

# 3. Know your Android project

A React Native Android application is still an Android application.

The important boundary is:

```text
React Native
    ↓
Android native project
    ↓
Gradle
    ↓
Android artifact
```

React Native provides the JavaScript/React layer, but the final release is produced by Android's native build system.

This means Android release failures can come from:

```text
JavaScript
React Native
Metro
Native modules
Gradle
Android Gradle Plugin
JDK
Android SDK
Manifest
Signing
Packaging
```

Debug the layer that actually failed.

---

# 4. Application identity

The Android application ID identifies the application distributed through Google Play.

Verify the project configuration before release.

Typical configuration is found under:

```text
android/app/build.gradle
```

or the corresponding Gradle configuration used by the project.

You may see:

```gradle
defaultConfig {
    applicationId "com.example.app"
}
```

The production application ID must match the intended Google Play application.

Do not casually change:

```text
applicationId
```

after an application has already been published.

Changing it creates a different Android application identity from the store's perspective.

See:

```text
foundations/identifiers.md
```

for the shared identifier rules.

---

# 5. Versioning

Android has two important release values:

```text
versionName
versionCode
```

Conceptually:

```text
versionName
→ user-facing version

versionCode
→ internal monotonically increasing release identifier
```

Example:

```gradle
defaultConfig {
    versionCode 42
    versionName "1.4.0"
}
```

For every Google Play release:

```text
versionCode
```

must be increased appropriately.

Do not treat `versionName` as a substitute for `versionCode`.

See:

```text
foundations/versioning.md
```

for the repository-wide versioning model.

---

# 6. Build variants

Android Gradle projects can support build variants such as:

```text
debug
release
```

Projects may also define flavors:

```text
development
staging
production
```

which can create variants such as:

```text
developmentDebug
developmentRelease
stagingDebug
stagingRelease
productionDebug
productionRelease
```

Do not create flavors just because they are possible.

Use them when the application genuinely needs separate native configurations or application variants.

For a simple application:

```text
debug
release
```

may be enough.

---

# 7. Release configuration

The release build should not accidentally inherit development behavior.

Before building, verify:

```text
[ ] Debug logging is not unintentionally enabled
[ ] Development endpoints are not used
[ ] Test configuration is not bundled
[ ] Development-only permissions are not present
[ ] Release signing is selected
[ ] Production application ID is correct
[ ] Production version is correct
[ ] Production API configuration is correct
```

A successful `release` Gradle task does not prove the configuration is correct.

The artifact itself must be verified.

---

# 8. JavaScript bundling

A React Native release must contain the JavaScript required to run the application.

The official React Native Android publishing guide documents:

```bash
npx react-native build-android --mode=release
```

as the current command for generating a release Android App Bundle. The command uses Gradle's `bundleRelease` task and bundles the JavaScript required by the application. citeturn0search0

The expected artifact is:

```text
android/app/build/outputs/bundle/release/app-release.aab
```

For projects with custom build variants, the output path can differ.

---

# 9. Why release bundling matters

Development normally uses Metro:

```text
React Native app
      ↓
Metro
      ↓
Device
```

A production release should not depend on a developer's running Metro server.

The release artifact should contain the required JavaScript and assets.

Therefore:

```text
Production build
→ bundled JavaScript
→ bundled assets
→ native Android application
```

If a release build opens a blank screen or behaves differently from development, check the release bundle and native configuration before changing application code blindly.

---

# 10. Build the release AAB

The recommended React Native release command documented by React Native is:

```bash
npx react-native build-android --mode=release
```

This creates an Android App Bundle using the release configuration. citeturn0search0

The resulting artifact is normally:

```text
android/app/build/outputs/bundle/release/app-release.aab
```

An Android App Bundle uses the `.aab` extension.

Google Play uses the bundle to generate device-specific APKs for distribution. citeturn0search4turn0search9

---

# 11. Direct Gradle build

The React Native command wraps the underlying Gradle build.

The equivalent Gradle task is:

```bash
cd android
./gradlew bundleRelease
```

On Windows:

```powershell
cd android
.\gradlew.bat bundleRelease
```

Use the React Native command when you want the framework-level workflow.

Use Gradle directly when you need:

```text
specific Gradle tasks
custom variants
debugging
CI control
advanced Android build configuration
```

Do not maintain two different release procedures unless both are intentionally supported.

---

# 12. Windows command example

From the repository root:

```powershell
npx react-native build-android --mode=release
```

Or:

```powershell
cd android
.\gradlew.bat bundleRelease
```

Expected artifact:

```text
android\app\build\outputs\bundle\release\app-release.aab
```

The exact output path can differ for custom variants.

---

# 13. Android App Bundle

Google Play uses Android App Bundles as the standard publishing format for new apps.

The bundle:

```text
app-release.aab
```

is uploaded to Google Play.

Google Play then generates optimized APKs for users based on their device configuration. citeturn0search2turn0search4turn0search9

For a normal Google Play release:

```text
React Native
→ Gradle
→ AAB
→ Google Play
```

Do not treat an APK as the default production Play Store artifact.

An APK is still useful for:

```text
local installation
device testing
specific alternative stores
debugging
```

---

# 14. Signing model

Android release signing has two important concepts:

```text
Upload key
+
Google Play App Signing
```

For Google Play App Bundles:

```text
Developer
→ signs upload artifact with upload key
→ uploads to Play Console
→ Google Play App Signing handles distribution signing
```

Android's official documentation describes this model and recommends protecting the signing credentials. citeturn0search8

React Native's official Android publishing guide also documents using an upload key for the release build. citeturn0search0

See:

```text
signing/android/
```

for the shared signing process.

---

# 15. Generate an upload keystore

If the project does not already have an upload key, follow the Android signing documentation to generate one.

Do not create a new production upload key simply because you cannot find the existing one.

First determine:

```text
Which key is currently registered?
Who owns it?
Where is it stored?
Can it be recovered?
```

For a new application, the upload-key setup can be performed before the first Play submission.

For an existing application, key changes must follow the appropriate Google Play process.

---

# 16. Store signing credentials outside Git

React Native's official publishing guide recommends keeping sensitive Gradle variables outside the repository when possible. citeturn0search0

For example, credentials can be stored in:

```text
~/.gradle/gradle.properties
```

rather than:

```text
android/gradle.properties
```

when the project setup supports that model.

Never commit:

```text
*.keystore
*.jks
store passwords
key passwords
private keys
```

unless a repository has a very unusual, explicitly reviewed encrypted-secret workflow.

For a public repository, assume everything committed to Git can become public.

---

# 17. Gradle signing configuration

A typical React Native project can load signing values from Gradle properties.

Example pattern:

```gradle
android {
    signingConfigs {
        release {
            if (project.hasProperty('MYAPP_UPLOAD_STORE_FILE')) {
                storeFile file(MYAPP_UPLOAD_STORE_FILE)
                storePassword MYAPP_UPLOAD_STORE_PASSWORD
                keyAlias MYAPP_UPLOAD_KEY_ALIAS
                keyPassword MYAPP_UPLOAD_KEY_PASSWORD
            }
        }
    }

    buildTypes {
        release {
            signingConfig signingConfigs.release
        }
    }
}
```

This pattern is documented in the React Native Android publishing guide. citeturn0search0

Use the project's existing signing structure if one already exists.

Do not overwrite working signing configuration just to make it look like an example.

---

# 18. Verify signing

Before submission, verify that the release artifact is signed correctly.

At minimum confirm:

```text
[ ] Release signing configuration is selected
[ ] Upload key is the expected key
[ ] No debug signing is used
[ ] Play App Signing configuration is understood
[ ] Credentials are not committed
[ ] CI has access to the credentials when required
```

For important releases, inspect the generated artifact using Android tooling and verify the signing identity.

Do not rely only on:

```text
Gradle build succeeded
```

---

# 19. Google Play App Signing

Google Play App Signing separates:

```text
upload key
```

from:

```text
app signing key
```

The upload key authenticates the upload.

Google manages the app-signing key used to sign distributed APKs when Play App Signing is enabled.

Android's official documentation recommends Play App Signing for Google Play distribution. citeturn0search8

Do not confuse:

```text
upload key
```

with:

```text
app signing key
```

They have different recovery and rotation procedures.

---

# 20. Target API level

Google Play's target API requirements are time-sensitive.

As of the current Android documentation:

```text
Starting August 31, 2026:

New apps and app updates:
→ Android 16 / API 36 or higher

Existing apps:
→ Android 15 / API 35 or higher
  to remain available to new users on newer Android versions
```

There are platform-specific exceptions for:

```text
Wear OS
Android Automotive OS
Android TV
Android XR
```

and Google documents an extension process in applicable cases. citeturn0search1

Because this requirement changes over time:

> **Do not hard-code a target API number into this playbook as a permanent rule.**

Check the current Google Play target API requirements before every production release.

---

# 21. Compile SDK vs target SDK

Do not confuse:

```text
compileSdk
```

with:

```text
targetSdk
```

Conceptually:

```text
compileSdk
→ Android APIs available while compiling

targetSdk
→ Android behavior/API level the application declares it targets
```

Google Play's submission requirement is based on the target API level.

Both still need to be compatible with the project's:

```text
React Native version
Android Gradle Plugin
Gradle version
JDK
dependencies
```

Do not upgrade these independently without checking the React Native compatibility matrix and project constraints.

---

# 22. Minimum SDK

The project also defines a minimum supported Android API level.

This controls:

```text
oldest supported Android platform
```

Changing it can remove support for existing users.

Before changing it, check:

```text
Current user/device distribution
React Native support
Native dependency requirements
Business support policy
Store requirements
```

Do not increase the minimum SDK just because a dependency supports a higher API level unless the product actually accepts the compatibility loss.

---

# 23. Android Gradle Plugin and Gradle

React Native Android builds depend on a chain:

```text
React Native
    ↓
Android Gradle Plugin
    ↓
Gradle
    ↓
JDK
    ↓
Android SDK
```

These versions are coupled.

When a release build fails after a tooling upgrade, inspect:

```text
React Native version
Android Gradle Plugin version
Gradle wrapper version
JDK version
compileSdk
targetSdk
```

Do not randomly upgrade all of them.

Change the smallest incompatible component first.

---

# 24. Gradle wrapper

The project should use the Gradle wrapper:

```text
gradlew
gradlew.bat
```

This makes the intended Gradle version explicit.

Prefer:

```bash
./gradlew bundleRelease
```

over relying on a globally installed Gradle version.

On Windows:

```powershell
.\gradlew.bat bundleRelease
```

This reduces environment drift between:

```text
developer machine
CI
release machine
```

---

# 25. JDK compatibility

React Native Android builds depend on a compatible Java/JDK version.

Check the React Native version and its supported Android toolchain before changing JDK versions.

Useful checks:

```bash
java -version
```

and:

```bash
cd android
./gradlew -version
```

Windows:

```powershell
java -version
```

```powershell
cd android
.\gradlew.bat -version
```

The goal is not to use the newest JDK automatically.

The goal is:

```text
React Native
+
Gradle
+
Android Gradle Plugin
+
JDK
```

that are known to work together.

---

# 26. Environment configuration

A production React Native build must use production configuration.

Verify:

```text
API base URL
Authentication configuration
Analytics configuration
Push notification configuration
Payment configuration
Feature flags
OAuth redirect configuration
Deep-link configuration
```

Do not assume:

```text
release
```

automatically means:

```text
production environment
```

A release build can still point at a staging backend if the configuration is wrong.

See:

```text
foundations/release-environments.md
```

---

# 27. Client-side secrets

React Native code runs on the user's device.

Anything bundled into the application should be treated as potentially observable.

Do not put:

```text
database passwords
private API keys
service account credentials
signing keys
admin tokens
```

into:

```text
BuildConfig
React Native constants
bundled JavaScript
public environment variables
```

Use a backend for privileged operations.

The repository security system explicitly requires secrets to stay out of source, Git history, frontend/client bundles, and logs. fileciteturn27file2

---

# 28. Native modules

React Native applications commonly depend on native Android modules.

Examples:

```text
Push notifications
Camera
Maps
Payments
Biometrics
Bluetooth
Location
Storage
Background processing
```

When adding or changing a native dependency:

```text
Dependency change
    ↓
Android native project changes
    ↓
Development build
    ↓
Release build
```

Do not assume a JavaScript-only update can deliver a native module.

If the native runtime changes, create a new native build.

---

# 29. React Native New Architecture

React Native projects may use the New Architecture depending on their version and configuration.

Before release, know whether the project uses:

```text
New Architecture
```

and whether its native dependencies support that configuration.

If a release breaks after enabling or changing architecture settings:

```text
Check native module compatibility
Check build logs
Check runtime behavior
```

Do not disable or enable the architecture as a blind troubleshooting step.

Make the change intentionally and test the affected native modules.

---

# 30. Hermes

React Native projects may use Hermes for JavaScript execution.

Before release, verify:

```text
Hermes configuration
React Native version
Native dependencies
Release behavior
```

If JavaScript behaves differently only in release:

```text
Check Hermes
Check minification
Check release-only configuration
Check native module initialization
```

Do not assume a JavaScript bug is caused by Hermes without evidence.

---

# 31. Minification and shrinking

Release builds may use:

```text
R8
ProGuard rules
resource shrinking
```

The exact configuration depends on the project and React Native version.

React Native's current Android publishing guide notes that enabling ProGuard can reduce size but may require rules specific to native libraries, so the release must be tested thoroughly. citeturn0search0

If a release works in debug but crashes in release:

```text
Check R8/minification
Check ProGuard rules
Check reflection-based libraries
Check native module configuration
```

Do not disable shrinking permanently just because it exposes a release-only issue.

First identify the missing rule or incompatible library.

---

# 32. Release-only failures

A common pattern:

```text
Debug works
Release fails
```

Possible causes:

```text
Different signing
Different environment
Bundled JS
Minification
R8
ProGuard
Build flags
Manifest
Permissions
Native initialization
Missing resource
Missing environment variable
```

Debug systematically:

```text
Compare debug vs release
        ↓
Identify first difference
        ↓
Reproduce in release
        ↓
Fix
        ↓
Verify release artifact
```

Do not make unrelated configuration changes.

---

# 33. Release build testing

React Native's official publishing guide recommends testing the release build before uploading it to Google Play. citeturn0search0

At minimum:

```text
[ ] Fresh install
[ ] Upgrade from previous production version
[ ] Login/signup
[ ] Main user journey
[ ] API requests
[ ] Push notifications
[ ] Deep links
[ ] Permissions
[ ] Payments if applicable
[ ] Offline behavior
[ ] Background/foreground transitions
[ ] Crash reporting
[ ] Analytics
```

Use:

```text
testing/android.md
testing/release-smoke-tests.md
```

for the broader testing process.

---

# 34. Test the actual release configuration

Do not test only:

```bash
npx react-native run-android
```

and assume production is safe.

Test the actual release build.

For example:

```bash
npm run android -- --mode="release"
```

or:

```bash
yarn android --mode release
```

React Native's current Android publishing guide documents release-mode installation/testing and notes that the release application contains the bundled JavaScript and does not require a running Metro server. citeturn0search0

---

# 35. Fresh install test

Before Play submission:

```text
Uninstall existing application
        ↓
Install release build
        ↓
Launch
        ↓
Complete critical flows
```

This catches issues hidden by:

```text
existing local storage
existing permissions
existing cached data
existing login session
```

Also test upgrade behavior separately.

---

# 36. Upgrade test

A production release should be tested as an upgrade:

```text
Previous production build
        ↓
Install
        ↓
Create realistic state
        ↓
Install new release
        ↓
Verify state survives
```

Check:

```text
Authentication
Local database
Secure storage
Cached state
Notifications
Deep links
Migrations
Feature flags
```

Do not assume:

```text
fresh install works
→ upgrade works
```

---

# 37. Android manifest

React Native dependencies can modify:

```text
AndroidManifest.xml
```

Review release-impacting permissions and components.

Check:

```text
Permissions
Services
Receivers
Providers
Intent filters
Exported components
Deep links
Queries
```

Do not accept every permission added by a dependency without reviewing why it is needed.

Unnecessary permissions can create:

```text
privacy risk
store review risk
security risk
user trust issues
```

---

# 38. Deep links

If the app uses deep links, verify:

```text
Android intent filters
App Links
HTTPS domains
Digital Asset Links
Authentication redirects
```

Test:

```text
Cold start
Warm start
Existing app
App not installed
```

A release can build successfully while deep links are broken because the problem is configuration rather than compilation.

See:

```text
testing/deep-links.md
integrations/deep-links/
integrations/app-links/
```

---

# 39. Push notifications

For push notifications, test:

```text
Fresh install
Permission request
Token registration
Foreground notification
Background notification
Terminated app
Notification tap
Deep link from notification
Token refresh
```

Verify the production Firebase/project configuration where applicable.

Do not test only with development credentials and assume production will behave identically.

See:

```text
testing/push-notifications.md
integrations/push-notifications/
```

---

# 40. Payments

If the application has payments:

```text
Test real release configuration
Test purchase initiation
Test success
Test failure
Test cancellation
Test restoration
Test backend entitlement verification
```

The client must not be the final authority for valuable entitlements.

Use the backend/platform transaction state as the source of truth where required.

See:

```text
testing/payments.md
monetization/
```

---

# 41. ProGuard/R8 verification

If release shrinking is enabled:

```text
[ ] Release build succeeds
[ ] App launches
[ ] Login works
[ ] Navigation works
[ ] Reflection-based libraries work
[ ] Push notifications work
[ ] Deep links work
[ ] Payments work
[ ] Crash reporting works
```

If something fails only in release:

```text
Check R8 mapping
Check missing keep rules
Check native library documentation
```

Do not blindly add broad keep rules for the entire application.

Broad keep rules reduce the value of shrinking and can hide the actual compatibility problem.

---

# 42. Native dependency upgrades

Before upgrading a native dependency:

```text
Check React Native compatibility
Check Android requirements
Check JDK/Gradle requirements
Check manifest changes
Check permissions
Check release notes
```

Then:

```text
Upgrade
 ↓
Debug build
 ↓
Release build
 ↓
Real-device test
 ↓
Upgrade test
```

Do not combine a large number of native dependency upgrades with a production release unless necessary.

---

# 43. React Native upgrade

A React Native upgrade is a release-risk event.

Treat it separately from normal feature releases where possible.

Recommended flow:

```text
React Native upgrade
        ↓
Resolve native changes
        ↓
Resolve Gradle/toolchain changes
        ↓
Run tests
        ↓
Debug release build
        ↓
Real-device testing
        ↓
Upgrade testing
        ↓
Preview/Play testing
        ↓
Production
```

Check the official React Native upgrade guidance for the specific version.

Do not copy a configuration from an unrelated React Native version.

---

# 44. Gradle cache

Gradle caching is useful, but cache deletion is not a universal fix.

If a build fails:

```text
Read first meaningful error
        ↓
Determine whether cache is involved
        ↓
Clear cache only when justified
```

Potential commands include:

```bash
cd android
./gradlew clean
```

or, on Windows:

```powershell
cd android
.\gradlew.bat clean
```

Do not make:

```text
clean
+
delete node_modules
+
delete Gradle caches
+
reinstall everything
```

the default troubleshooting sequence.

That destroys useful diagnostic state.

---

# 45. Common failure: Gradle build fails

Check:

```text
React Native version
Gradle version
Android Gradle Plugin
JDK
Android SDK
Native dependencies
```

Then identify the first meaningful error.

Useful commands:

```bash
cd android
./gradlew bundleRelease --stacktrace
```

Windows:

```powershell
cd android
.\gradlew.bat bundleRelease --stacktrace
```

Use `--info` or `--debug` only when additional logs are actually needed.

---

# 46. Common failure: JDK mismatch

Symptoms may include:

```text
Unsupported class file version
Gradle JVM error
Android Gradle Plugin incompatibility
```

Check:

```bash
java -version
```

and:

```bash
cd android
./gradlew -version
```

Then compare with the React Native project's supported toolchain.

Do not upgrade JDK, Gradle, and Android Gradle Plugin together without understanding the compatibility chain.

---

# 47. Common failure: signing fails

Symptoms:

```text
Keystore not found
Alias not found
Wrong password
Signing config missing
```

Check:

```text
Keystore path
Alias
Store password
Key password
Gradle property source
CI secret injection
Release signingConfig
```

Do not regenerate the production key because of a configuration typo.

First verify the existing credential.

---

# 48. Common failure: release app opens but JavaScript is missing

Symptoms:

```text
Blank screen
Metro connection error
JS bundle not found
```

Check:

```text
Release bundling
Gradle configuration
React Native build task
Assets
Build variant
```

The official React Native release command bundles JavaScript into the release AAB. citeturn0search0

Do not run Metro in production as a workaround.

---

# 49. Common failure: debug works, release crashes

Check in this order:

```text
1. Production environment
2. Release signing
3. JavaScript bundle
4. R8/ProGuard
5. Native module initialization
6. Manifest
7. Permissions
8. Resource configuration
9. Hermes
10. Release-only code paths
```

Collect the actual crash stack before changing configuration.

---

# 50. Common failure: Google Play rejects target API

Check the current Google Play target API requirement.

As of the current official Android documentation, starting August 31, 2026:

```text
New apps + updates
→ target API 36+
```

for standard Android applications. citeturn0search1

Do not hard-code this number into long-lived scripts without a maintenance mechanism.

A future requirement may change it.

---

# 51. Common failure: Play rejects version code

If Google Play reports that the version code already exists:

```text
Check Play Console
        ↓
Check release configuration
        ↓
Increase versionCode
        ↓
Build new AAB
        ↓
Verify
        ↓
Submit
```

Do not change `versionName` only.

Google Play uses the version code to distinguish Android application releases.

---

# 52. Common failure: wrong application uploaded

Check:

```text
applicationId
Google Play application
release variant
Gradle product flavor
CI environment
```

If flavors exist, verify that CI is building the intended variant.

Example:

```text
productionRelease
```

is not necessarily the same as:

```text
release
```

when product flavors are configured.

---

# 53. Common failure: native module works in debug but not release

Check:

```text
Native module compatibility
R8/ProGuard
Manifest
Permissions
Release initialization
JSC/Hermes
New Architecture
```

Look at the module's official Android setup.

Do not assume:

```text
npm install succeeded
→ native module is production-ready
```

---

# 54. CI/CD

React Native Android release automation should have a clear pipeline:

```text
Pull Request
    ↓
Install dependencies
    ↓
Lint / typecheck
    ↓
Unit tests
    ↓
Android validation
    ↓
Merge
    ↓
Production release trigger
    ↓
Build AAB
    ↓
Verify artifact
    ↓
Human approval
    ↓
Google Play submission
    ↓
Monitor
```

The repository architecture places React Native release automation under:

```text
release-engineering/
```

while the framework directory explains React Native-specific build behavior. fileciteturn28file10

---

# 55. GitHub Actions

A React Native Android workflow can use GitHub Actions to:

```text
Install Node
Install Java
Restore dependency caches
Install dependencies
Run tests
Build AAB
Upload artifact
Submit to Play
```

Do not hard-code a toolchain that is not supported by the project's React Native version.

Keep production credentials in protected GitHub secrets/environments.

Never expose:

```text
keystore passwords
upload keys
Google service-account credentials
EXPO_TOKEN
```

in logs.

---

# 56. CI toolchain checks

CI should make the toolchain explicit.

At minimum record/check:

```text
Node version
Package manager
Java version
React Native version
Android Gradle Plugin
Gradle wrapper
compileSdk
targetSdk
minSdk
```

A useful diagnostic step:

```bash
node --version
java -version
```

and:

```bash
cd android
./gradlew -version
```

Do not rely on the CI runner's default Java or Android SDK configuration.

---

# 57. Build caching

Cache carefully.

Useful candidates:

```text
npm/yarn/pnpm cache
Gradle cache
Android SDK layers
```

But caching must not hide dependency or build corruption.

If a cache causes inconsistent builds:

```text
invalidate
rebuild
identify cause
```

Do not permanently disable caching without measuring the impact.

---

# 58. Artifact handling

After CI creates:

```text
app-release.aab
```

record:

```text
Git commit
React Native version
Android application ID
versionName
versionCode
build variant
CI run
artifact
```

For production, the exact artifact that is approved should be the one submitted.

Avoid:

```text
Build A
→ later build B
→ submit "latest"
```

when the release process requires exact traceability.

---

# 59. AI-assisted Android release audit

AI can help inspect the React Native Android project.

Useful inputs:

```text
package.json
android/app/build.gradle
android/build.gradle
gradle.properties
gradle wrapper
settings.gradle
AndroidManifest.xml
React Native version
lockfile
CI workflow
```

Ask the agent to check:

```text
Application ID
Version
Version code
Build variant
Target SDK
Signing references
Release configuration
Native dependencies
R8/ProGuard
Environment configuration
Manifest permissions
Deep links
Push notifications
Google Play readiness
```

The repository's AI architecture treats AI as an accelerator and requires human verification for high-impact actions. fileciteturn28file2L1-L35

---

# 60. AI release audit prompt

```text
Audit this React Native Android project for production release readiness.

Inspect:

- package.json
- lockfile
- android/app/build.gradle
- android/build.gradle
- gradle.properties
- gradle wrapper
- settings.gradle
- AndroidManifest.xml
- native dependencies
- CI workflows
- release configuration

Check:

1. Application ID
2. versionName
3. versionCode
4. minSdk
5. compileSdk
6. targetSdk
7. React Native/toolchain compatibility
8. JDK/Gradle/AGP compatibility
9. Release signing configuration
10. R8/ProGuard configuration
11. Production environment
12. Permissions
13. Deep links
14. Push notifications
15. Release-only risks
16. Google Play submission blockers

Return:

- READY
- BLOCKED
- NEEDS VERIFICATION

For every finding provide:

- evidence
- file
- risk
- minimal fix
- verification step

Rules:

- Do not modify files.
- Do not request or print secrets.
- Do not invent Google Play requirements.
- Verify volatile platform requirements against official sources.
- Do not recommend unrelated dependency upgrades.
```

---

# 61. AI build failure debugging

```text
Analyze this React Native Android release build failure.

Project:
<project>

React Native:
<version>

Android:
<version/toolchain>

JDK:
<version>

Build command:
<command>

Failure:
<relevant logs>

Return:

1. First meaningful error
2. Failure category
3. Evidence
4. Most likely root cause
5. Alternative causes
6. Minimal fix
7. Whether a clean build is actually required
8. Verification steps
9. Release risk

Rules:

- Do not request secrets.
- Do not recommend upgrading the entire Android toolchain without evidence.
- Do not treat the last error line as automatically being the root cause.
- Separate confirmed facts from hypotheses.
```

---

# 62. Security boundaries

A public React Native repository must treat these as sensitive:

```text
Android keystores
Upload keys
Keystore passwords
Google Play credentials
CI secrets
Production environment secrets
API credentials
```

Never commit them.

The repository security guidance explicitly requires least privilege, secret isolation, protected CI/CD, and secure handling of private keys. fileciteturn27file0turn27file3

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

Do not simply delete the file and assume the secret is safe because it disappeared from the working tree.

Git history must also be considered.

---

# 63. Production observability

A release is not complete when Google Play accepts the AAB.

After rollout, monitor:

```text
Crash rate
ANR rate
API failures
Login failures
Critical user flows
Performance
Push notification delivery
Deep-link success
Payment failures
```

The production operations system requires deployment visibility, structured logs, metrics, error tracking, and rollback readiness. fileciteturn27file5turn27file7

Use the project's observability stack and application-specific telemetry.

---

# 64. Rollback thinking

Google Play does not provide a simple "undo the uploaded AAB" operation equivalent to reverting a Git commit.

Your recovery plan should therefore distinguish:

```text
Release has not rolled out
→ stop/limit rollout where possible

Release is already distributed
→ publish a corrected release

JavaScript-only issue and an OTA system is intentionally supported
→ use the compatible update path

Native issue
→ new native build + store release
```

Do not assume a React Native Android application can always be recovered through an OTA mechanism.

For Expo applications, use:

```text
frameworks/expo/updates.md
```

when the project actually uses EAS Update.

---

# 65. Release evidence

For each production Android release, retain:

```text
Git commit
React Native version
Android application ID
versionName
versionCode
build variant
Gradle version
JDK version
AAB artifact
CI run
Google Play submission
Release date
```

This makes failures reproducible.

Do not store:

```text
keystore password
private key
service account secret
API secret
```

inside the release record.

---

# 66. Recommended release workflow

For a normal React Native Android release:

```text
1. Confirm release commit
        ↓
2. Confirm application ID
        ↓
3. Confirm versionName
        ↓
4. Increment versionCode
        ↓
5. Verify production environment
        ↓
6. Run tests
        ↓
7. Build release AAB
        ↓
8. Verify signing
        ↓
9. Install/test release build
        ↓
10. Test upgrade from previous release
        ↓
11. Upload to Play testing
        ↓
12. Validate production-like behavior
        ↓
13. Human approval
        ↓
14. Production rollout
        ↓
15. Monitor
```

---

# 67. Pre-submission checklist

```text
[ ] Application ID is correct
[ ] Production variant is correct
[ ] versionName is correct
[ ] versionCode is new
[ ] target SDK meets current Google Play requirements
[ ] min SDK is intentional
[ ] React Native/toolchain versions are compatible
[ ] Release signing is configured
[ ] Upload key is correct
[ ] Google Play App Signing state is understood
[ ] Production API configuration is correct
[ ] No debug endpoints are bundled
[ ] Release AAB was generated successfully
[ ] AAB was inspected
[ ] Fresh-install test passed
[ ] Upgrade test passed
[ ] Critical user journeys passed
[ ] Push notifications tested if applicable
[ ] Deep links tested if applicable
[ ] Payments tested if applicable
[ ] Crash reporting verified
[ ] Store metadata is ready
[ ] Human approval completed
```

---

# 68. Definition of done

A React Native Android release is ready when:

```text
Correct project
      +
Correct application identity
      +
Correct version
      +
Compatible Android toolchain
      +
Production configuration
      +
Correct signing
      +
Valid AAB
      +
Real-device testing
      +
Upgrade testing
      +
Play testing
      +
Human approval
      +
Production monitoring
      ↓
ANDROID RELEASE READY
```

The goal is not merely:

```text
Gradle build succeeded.
```

The goal is:

```text
The exact artifact is known,
correctly configured,
correctly signed,
tested on real devices,
accepted by the intended Play workflow,
and observable after rollout.
```

---

# Related documentation

### Core

- `foundations/mobile-release-lifecycle.md`
- `foundations/project-configuration.md`
- `foundations/release-environments.md`
- `foundations/identifiers.md`
- `foundations/versioning.md`
- `foundations/device-support.md`
- `foundations/dependency-management.md`

### Android

- `checklists/android.md`
- `testing/android.md`
- `publishing/android/`
- `signing/android/`

### React Native

- `frameworks/react-native/README.md`
- `frameworks/react-native/ios-release.md`
- `frameworks/react-native/signing.md`
- `frameworks/react-native/fastlane.md`
- `frameworks/react-native/common-failures.md`

### Expo

- `frameworks/expo/`
- `frameworks/expo/build.md`
- `frameworks/expo/eas.md`
- `frameworks/expo/updates.md`
- `frameworks/expo/submit.md`

### Release engineering

- `release-engineering/build-systems.md`
- `release-engineering/release-configurations.md`
- `release-engineering/github-actions/`
- `release-engineering/environment-management.md`
- `release-engineering/release-pipelines.md`

### Release strategy

- `release-strategy/beta-testing.md`
- `release-strategy/staged-rollouts.md`
- `release-strategy/hotfixes.md`
- `release-strategy/emergency-release.md`

### Post-release

- `post-release/monitoring.md`
- `post-release/crash-analysis.md`
- `post-release/performance.md`
- `post-release/rollout-monitoring.md`
- `post-release/incident-response.md`
- `post-release/rollback.md`
- `post-release/hotfix.md`

### AI

- `ai/README.md`
- `ai/tools/`
- `ai/agents/release-auditor.md`
- `ai/agents/qa-agent.md`
- `ai/agents/debugging-agent.md`
- `ai/workflows/release-audit.md`
- `ai/workflows/debugging.md`
- `ai/orchestration/human-approval.md`
- `ai/security/secret-protection.md`
- `ai/security/agent-permissions.md`

---

# Official sources

Use official sources as the authority for current platform and framework behavior.

## React Native

- Publishing to Google Play: https://reactnative.dev/docs/signed-apk-android.html
- React Native documentation: https://reactnative.dev/docs/getting-started
- React Native releases: https://reactnative.dev/versions

## Android

- Android app signing: https://developer.android.com/studio/publish/app-signing
- Android App Bundles: https://developer.android.com/guide/app-bundle
- Build and test App Bundles: https://developer.android.com/guide/app-bundle/test
- Google Play target API requirements: https://developer.android.com/google/play/requirements/target-sdk
- Android behavior changes: https://developer.android.com/about/versions

## Google Play

- Google Play Console Help: https://support.google.com/googleplay/android-developer/
- Google Play app publishing: https://support.google.com/googleplay/android-developer/topic/7072031

## Gradle

- Gradle documentation: https://docs.gradle.org/

## Android Studio

- Android Studio documentation: https://developer.android.com/studio

**Last verified:** August 11, 2026

React Native, Android Gradle Plugin, Gradle, JDK, Android SDK, Google Play target API requirements, signing behavior, and Play Console policies change over time. Verify the current React Native and Android documentation before every production release.
