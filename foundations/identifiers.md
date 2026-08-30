# Identifiers

Application identifiers are the permanent names that connect your mobile app to its platform, store listing, signing configuration, native project, and many platform services.

Get them right before the first production release.

A useful mental model is:

```text
Your app
 ↓
Platform identifier
 ↓
Store app record
 ↓
Signing / capabilities
 ↓
Production binary
```

The two main identifiers are:

| Platform | Main identifier | Common configuration |
|---|---|---|
| iOS | Bundle ID | `com.example.myapp` |
| Android | Application ID | `com.example.myapp` |

Expo exposes these through `ios.bundleIdentifier` and `android.package`. EAS Build uses these values when identifying the app for Apple and Google distribution.

> **Important:** An identifier is not a secret. It is public application metadata. Credentials, private keys, API secrets, and signing secrets are different and must be protected.

---

## 1. Why identifiers matter

Identifiers are used across the release lifecycle.

```text
Identifier
 ↓
Native project
 ↓
Signing
 ↓
Capabilities / permissions
 ↓
Store record
 ↓
Build
 ↓
Updates
 ↓
Production services
```

A wrong identifier can cause:

- build failures
- signing failures
- inability to upload a build
- the build being associated with the wrong store app
- broken push notification configuration
- broken deep links
- broken authentication configuration
- broken associated domains
- incorrect app updates
- accidental creation of a second app

Treat production identifiers as release-critical configuration.

---

## 2. iOS Bundle ID

The iOS Bundle ID uniquely identifies an app in Apple's ecosystem.

Example:

```text
com.example.myapp
```

Apple describes the bundle ID as a unique identifier for an app. The bundle ID in the app's `Info.plist` must match the bundle ID registered for the app, and App Store Connect uses it to associate uploaded builds with the app.

Apple's Bundle ID system also connects the identifier to capabilities and provisioning profiles.

### Typical format

A common convention is reverse-DNS notation:

```text
com.company.product
```

Examples:

```text
com.acme.shop
com.acme.mobile
com.acme.customer
```

The format is a convention. The important requirement is that the identifier is valid and uniquely represents the intended application.

---

## 3. Android Application ID

Android uses the application ID to uniquely identify the application on the device and in Google Play.

Example:

```text
com.example.myapp
```

In a standard Android project, the application ID is configured through the Gradle build configuration.

Expo exposes it as:

```json
{
 "expo": {
 "android": {
 "package": "com.example.myapp"
 }
 }
}
```

Expo documents `android.package` as the package name used for the Android standalone app and identifies it as the Android Application ID used for Google Play.

---

## 4. Bundle ID vs Application ID

They serve a similar purpose but belong to different platforms.

| | iOS | Android |
|---|---|---|
| Identifier | Bundle ID | Application ID |
| Example | `com.acme.app` | `com.acme.app` |
| Native location | `CFBundleIdentifier` | Gradle `applicationId` |
| Expo config | `ios.bundleIdentifier` | `android.package` |
| Store | App Store Connect | Google Play |
| Signing relationship | Apple App ID / profiles | Android signing / Play App Signing |
| Platform-specific | Yes | Yes |

You may choose the same string on both platforms:

```text
iOS: com.acme.app
Android: com.acme.app
```

That is convenient but not required.

Do not assume that using the same identifier makes the two platforms one application internally. They still have separate store records, signing systems, builds, and platform configuration.

---

## 5. Choose the identifier before publishing

Choose production identifiers before the first store submission.

Recommended process:

```text
Company / product name
 ↓
Choose identifier
 ↓
Check platform availability / registration
 ↓
Configure native project
 ↓
Configure store record
 ↓
Build
 ↓
Verify
 ↓
Publish
```

Before choosing one:

- [ ] Product name is reasonably stable.
- [ ] Company/domain naming is understood.
- [ ] Identifier follows the platform's accepted format.
- [ ] Identifier is not already being used for another app you own.
- [ ] Identifier is intended for the production app.
- [ ] Team members know it is the production identifier.
- [ ] CI/CD uses the same identifier.
- [ ] Store configuration uses the same identifier.

Do not choose an identifier based only on what is shortest.

Changing a production identifier later can create a new application identity instead of a normal app update.

---

## 6. Production identifiers are effectively permanent

Treat a published identifier as a long-term decision.

For iOS, Apple states that the Bundle ID in App Store Connect cannot be changed after a build has been uploaded.

Apple also states that the bundle ID in the app's information property list must match the App Store Connect bundle ID.

For Android, changing the application ID creates a different application identity rather than a normal update to the existing application.

Therefore:

```text
Before first release
→ choose carefully

After production release
→ treat as fixed
```

If you discover a naming problem after publishing, do not simply change the identifier and ship it.

First understand the migration and store consequences.

---

## 7. Naming strategy

A simple convention is usually enough.

Example:

```text
com.<company>.<product>
```

For example:

```text
com.acme.store
com.acme.finance
com.acme.mobile
```

For multiple products:

```text
com.acme.producta
com.acme.productb
```

For separate applications within one product:

```text
com.acme.product.consumer
com.acme.product.business
```

Keep identifiers:

- stable
- understandable
- unique
- consistent
- boring

Do not encode temporary information into a production identifier.

Avoid identifiers such as:

```text
com.acme.newapp
com.acme.test
com.acme.v2
com.acme.final
com.acme.final2
```

unless the identifier genuinely represents a separate application.

---

## 8. Do not confuse names and identifiers

A mobile application can have several names.

For example:

```text
Product name:
Acme

Store name:
Acme

Home-screen name:
Acme

iOS Bundle ID:
com.acme.mobile

Android Application ID:
com.acme.mobile
```

These are different properties.

Changing the visible app name is not the same operation as changing the application identifier.

The identifier should not be treated as a user-facing branding field.

---

## 9. Store identity vs platform identity

The store contains additional identifiers.

### Apple

Apple has several identifiers associated with an app, including:

- Bundle ID
- App ID
- Apple ID
- SKU

They do different jobs.

Apple documents the Bundle ID as the identifier used throughout the system, while the Apple ID is generated when the app is added to the account and the SKU is an internal tracking identifier.

Do not use:

```text
Apple ID
```

when you actually mean:

```text
Bundle ID
```

### Google

Google Play also has application/store metadata associated with the application ID.

For release work, distinguish:

```text
Android Application ID
vs
Google Play listing/account information
vs
versionCode
```

They are not interchangeable.

---

## 10. Expo configuration

For Expo, identifiers normally live in the app configuration.

Example:

```json
{
 "expo": {
 "name": "Acme",
 "slug": "acme",
 "ios": {
 "bundleIdentifier": "com.acme.mobile"
 },
 "android": {
 "package": "com.acme.mobile"
 }
 }
}
```

The important fields are:

```text
ios.bundleIdentifier
android.package
```

Expo documents `ios.bundleIdentifier` as the iOS standalone app's bundle identifier and `android.package` as the Android package/application ID.

EAS Build can prompt for these identifiers when they are not configured yet.

---

## 11. React Native configuration

For a standard React Native project, verify the identifiers in the native projects.

### iOS

The Bundle ID is associated with:

```text
CFBundleIdentifier
```

in the application's property-list/build configuration.

Apple requires the identifier used by the app to match the identifier used in App Store Connect.

### Android

The application ID is normally configured in:

```text
android/app/build.gradle
```

or the equivalent Gradle configuration.

Expo's documentation notes that existing React Native projects use the Android `applicationId` and the iOS `CFBundleIdentifier` rather than relying only on Expo app configuration.

---

## 12. Native project vs Expo configuration

This is an important source of mistakes.

An Expo project can have:

```text
app.json / app.config.*
+
ios/
+
android/
```

Depending on the project's workflow, not every app-config value is automatically synchronized into native project directories.

Expo documents that when native `ios` or `android` directories are present, EAS Build does not automatically sync app-config properties into those native projects in every setup. Expo Doctor can warn about app-config fields that are not synchronized.

Therefore, verify the actual release configuration.

Do not assume:

```text
app.json says X
```

means:

```text
native project builds X
```

when native projects are checked into the repository.

---

## 13. Verify identifiers before building

Before creating the production build:

### iOS

Check:

```text
Bundle ID
Apple Developer identifier
App Store Connect app
Native project
Build configuration
```

All should point to the same application.

### Android

Check:

```text
Application ID
Play Console app
Gradle configuration
Build configuration
```

All should point to the same application.

Use:

```text
One app
 ↓
One intended identifier
 ↓
One intended store record
```

for each platform.

---

## 14. Verify the actual production artifact

Configuration files are not enough.

After building:

- [ ] iOS artifact contains the expected Bundle ID.
- [ ] Android artifact contains the expected Application ID.
- [ ] Version is correct.
- [ ] Build number/versionCode is correct.
- [ ] Signing is correct.
- [ ] Store destination is correct.

The release artifact is the final source of truth for what you are actually shipping.

---

## 15. Identifier verification matrix

Use this before a first release.

| Check | iOS | Android |
|---|---|---|
| Production identifier chosen | [ ] | [ ] |
| Native project configured | [ ] | [ ] |
| Store app record created | [ ] | [ ] |
| Identifier matches store record | [ ] | [ ] |
| Signing configuration matches | [ ] | [ ] |
| Production build verified | [ ] | [ ] |
| Real-device install verified | [ ] | [ ] |
| Production release approved | [ ] | [ ] |

---

## 16. Identifiers and signing

Identifiers are tightly connected to signing.

### iOS

The Bundle ID is associated with an Apple App ID and its capabilities. Provisioning profiles and signing configuration depend on this relationship.

For example:

```text
Bundle ID
 ↓
App ID
 ↓
Capabilities
 ↓
Provisioning profile
 ↓
Signed application
```

If the identifier is wrong, signing can fail or the resulting application can have the wrong capabilities.

### Android

The application identity is also tied to the Android signing and Google Play distribution model.

For an existing Play application, do not change the application ID simply because the package name looks inconvenient.

---

## 17. Identifiers and capabilities

Some platform services are tied to the application identifier.

Examples:

### iOS

- Push Notifications
- Associated Domains
- Sign in with Apple
- App Groups
- Apple Pay
- Keychain sharing
- other entitlements

### Android

- Push notification configuration
- App Links
- OAuth client configuration
- Google APIs
- Firebase application configuration
- other package/application-specific services

When adding a capability:

```text
Application identifier
 ↓
Platform configuration
 ↓
Capability
 ↓
Build
 ↓
Test
```

Do not configure the service against a different application identifier by mistake.

---

## 18. Push notifications

Push configuration is a common identifier-related failure.

The notification system may depend on the exact application identity.

If notifications stop working after a release, verify:

- [ ] Bundle ID/application ID is unchanged.
- [ ] Production push credentials/configuration match.
- [ ] Correct Firebase/Apple project is being used.
- [ ] Correct build was installed.
- [ ] Token registration is working.
- [ ] Environment is production.
- [ ] Notification provider is targeting the correct application.

Do not regenerate credentials first.

Verify the application identity first.

---

## 19. Deep links

Deep links can also depend on the application identity and associated platform configuration.

### iOS

Check:

- Bundle ID
- Associated Domains entitlement
- associated domain configuration
- production domain

### Android

Check:

- application ID
- intent filters
- App Links configuration
- asset links configuration where required

A mismatch can result in:

```text
Link opens browser
instead of
Link opens app
```

or the wrong application handling the link.

---

## 20. Authentication and OAuth

Some OAuth providers use platform-specific application identifiers.

When configuring mobile OAuth:

- [ ] iOS client is associated with the correct Bundle ID where required.
- [ ] Android client is associated with the correct package/application ID where required.
- [ ] SHA certificate fingerprints are correct where Android providers require them.
- [ ] Redirect URIs are correct.
- [ ] Production configuration is separate from development.
- [ ] The correct provider project is used.

Do not copy a development OAuth configuration into production without checking the application identity.

---

## 21. Firebase and Google services

If Firebase or another Google service is used:

Verify that the mobile application registration matches the production app.

For Android, commonly check:

```text
Application ID
+
Firebase Android app registration
+
google-services configuration
```

For iOS:

```text
Bundle ID
+
Firebase iOS app registration
+
GoogleService-Info.plist
```

Do not assume the Firebase project is correct simply because the app builds.

Verify the application registration.

---

## 22. Multiple environments

If the application has:

```text
development
staging
production
```

you need an intentional identifier strategy.

One common approach is:

```text
Production
com.acme.app

Development
com.acme.app.dev

Staging
com.acme.app.staging
```

This can allow separate applications to coexist on the same device.

However, environment-specific identifiers introduce additional:

- store records
- signing configuration
- credentials
- push configuration
- OAuth configuration
- deep-link configuration
- CI/CD configuration

Do not create separate identifiers unless the development workflow benefits from them.

---

## 23. Environment identifier strategy

Before creating environment-specific identifiers, decide:

| Environment | iOS Bundle ID | Android Application ID | Store distribution |
|---|---|---|---|
| Development | `<dev-id>` | `<dev-id>` | Usually internal |
| Staging | `<staging-id>` | `<staging-id>` | Optional |
| Production | `<prod-id>` | `<prod-id>` | Public |

There is no requirement that every project needs separate staging store applications.

For smaller projects, a simpler setup may be better:

```text
Development build
→ development backend

Production build
→ production backend
```

with one production store identity.

Choose the smallest setup that solves the real workflow.

---

## 24. White-label applications

If one codebase produces multiple separately published apps:

```text
Shared codebase
 ↓
Product A
Product B
Product C
```

each published application normally needs its own production identity.

For example:

```text
com.acme.product.a
com.acme.product.b
com.acme.product.c
```

Do not reuse one production identifier for separate store applications.

Keep the identifier mapping explicit in the build configuration.

---

## 25. App extensions and companion applications

Some platforms allow related application targets.

Examples include iOS:

- widgets
- notification extensions
- share extensions
- watch applications

These targets can have their own Bundle IDs and must follow the platform's identifier relationships.

Do not treat every native target as if it were the main application.

Document:

```text
Main application
 ↓
Extension / companion target
 ↓
Identifier
 ↓
Required relationship
```

when the project uses them.

---

## 26. Identifier security

Identifiers are not secrets.

It is normal for them to appear in:

- application binaries
- store URLs
- configuration
- logs
- public documentation
- OAuth configuration
- platform dashboards

Do not try to hide an application identifier as a security mechanism.

Security comes from:

- authentication
- authorization
- signing
- server-side validation
- protected credentials
- least privilege

Do not treat an unpredictable identifier as authorization. The security system should still validate access server-side.

---

## 27. Common identifier mistakes

### Mistake 1: Changing the production Bundle ID

This can create a different app identity.

**Better:**

Treat the production Bundle ID as fixed.

---

### Mistake 2: Changing Android `applicationId`

Changing it creates a different application identity.

**Better:**

Keep the production application ID stable.

---

### Mistake 3: Store record and native project do not match

Example:

```text
App Store Connect
com.acme.app

Xcode
com.acme.newapp
```

The build will not belong to the intended app.

**Fix:**

Make the native project and store record agree.

---

### Mistake 4: Expo config and native project disagree

Example:

```text
app.json
com.acme.app

ios project
com.acme.oldapp
```

**Fix:**

Determine which configuration is authoritative for the project's workflow and verify the actual build output.

---

### Mistake 5: Development identifier reaches production

Example:

```text
com.acme.app.dev
```

is accidentally used for the public release.

**Fix:**

Verify the production build identifier before submission.

---

### Mistake 6: Different identifier across a service integration

Example:

```text
App
com.acme.app

OAuth
com.acme.mobile
```

**Fix:**

Review the platform service registration and map it to the actual production application identity.

---

### Mistake 7: Using identifier secrecy as security

An identifier being hard to guess does not make authorization secure.

**Fix:**

Enforce authorization at the trusted backend boundary.

---

## 28. Troubleshooting

### "This build cannot be uploaded to the intended app"

Check:

1. Bundle ID/application ID.
2. Store app record.
3. Version.
4. Build number/versionCode.
5. Signing.
6. Build configuration.

For iOS, App Store Connect associates the build with the app using the Bundle ID and version/build information.

---

### "The app appears as a new app"

Check whether the production identifier changed.

For Android:

```text
Old:
com.acme.app

New:
com.acme.mobile
```

Those represent different application identities.

For iOS, verify the Bundle ID against the App Store Connect record.

Do not continue publishing until the identity is understood.

---

### "Push notifications stopped after release"

Check:

```text
Identifier
 ↓
Platform service registration
 ↓
Credentials
 ↓
Build configuration
 ↓
Token registration
 ↓
Provider delivery
```

Start with the identifier before rotating credentials.

---

### "Deep links work in development but not production"

Check:

- production identifier
- production domain
- associated domains/App Links
- signing/build configuration
- production service files
- installed production build

Do not assume a development configuration is equivalent to production.

---

## 29. AI-assisted identifier audit

AI can help inspect a repository for identifier inconsistencies.

Useful checks:

- compare Expo config with native projects
- compare iOS Bundle ID with App Store Connect configuration
- compare Android application ID with Play configuration
- find development identifiers in production configuration
- inspect environment-specific identifiers
- find hard-coded service configurations
- identify push/deep-link/OAuth configuration that may reference the wrong app
- prepare an identifier mapping table

A safe workflow:

```text
Repository
 ↓
AI scans configuration
 ↓
Find candidate mismatches
 ↓
Developer verifies against native/store configuration
 ↓
Build
 ↓
Inspect artifact
 ↓
Human approval
```

Example prompt:

```text
Audit this mobile repository for application-identifier consistency.

Check:

1. iOS Bundle ID
2. Android Application ID
3. Expo ios.bundleIdentifier
4. Expo android.package
5. iOS native CFBundleIdentifier
6. Android applicationId
7. environment-specific identifiers
8. push notification configuration
9. deep-link configuration
10. OAuth configuration
11. Firebase configuration
12. CI/CD build configuration
13. store configuration references

Return:

IDENTIFIER MAP
MISMATCHES
PRODUCTION RISKS
ENVIRONMENT DIFFERENCES
FILES TO VERIFY
RECOMMENDED FIXES

Do not modify files.

Do not invent store configuration.

Clearly mark anything that cannot be verified from the repository.
```

AI output is a review aid, not proof that the store configuration is correct.

---

## 30. Identifier change checklist

If you are considering changing a production identifier:

**Stop before changing it.**

First determine:

- [ ] Why is the identifier changing?
- [ ] Has the app already been published?
- [ ] Is the change intended to create a new application?
- [ ] What happens to existing users?
- [ ] What happens to existing installations?
- [ ] What happens to updates?
- [ ] What happens to push notifications?
- [ ] What happens to OAuth?
- [ ] What happens to deep links?
- [ ] What happens to analytics?
- [ ] What happens to subscriptions/purchases?
- [ ] What happens to signing?
- [ ] What happens to store metadata?
- [ ] What happens to backend mappings?
- [ ] Is a migration path possible?

Do not make the change until the consequences are understood.

---

## 31. Production identifier record

Keep a small record of production identifiers.

Example:

```text
Product: Acme

iOS
Bundle ID: com.acme.mobile
App Store Connect app: <record reference>

Android
Application ID: com.acme.mobile
Google Play app: <record reference>

Environment
Production: production
Development: development
```

Do not store secrets in this document.

This record is for identity mapping, not credential storage.

---

## 32. Identifier release gate

Before the first production release:

- [ ] iOS Bundle ID is finalized.
- [ ] Android Application ID is finalized.
- [ ] Identifiers are unique and intentional.
- [ ] Apple Developer registration is correct.
- [ ] App Store Connect record is correct.
- [ ] Android Play Console record is correct.
- [ ] Expo configuration is correct where applicable.
- [ ] Native projects are correct.
- [ ] Signing configuration uses the intended identifiers.
- [ ] Push configuration uses the intended identifiers.
- [ ] Deep-link configuration uses the intended identifiers.
- [ ] OAuth configuration uses the intended identifiers.
- [ ] Firebase configuration uses the intended identifiers where applicable.
- [ ] Production artifacts contain the expected identifiers.
- [ ] Real-device installation is verified.
- [ ] Human release approval is complete.

---

# 33. Recommended identifier workflow

For a new app:

```text
Choose production identity
 ↓
Register platform identifiers
 ↓
Create store records
 ↓
Configure Expo / native projects
 ↓
Configure capabilities and services
 ↓
Configure signing
 ↓
Build
 ↓
Inspect actual artifacts
 ↓
Test
 ↓
Submit
```

For an existing app:

```text
Identify current production IDs
 ↓
Treat them as fixed
 ↓
Audit configuration
 ↓
Keep services aligned
 ↓
Build
 ↓
Verify
 ↓
Release
```

---

# 34. Definition of done

Identifiers are ready when:

```text
Production IDs are intentional
 +
Native configuration matches
 +
Store records match
 +
Signing matches
 +
Platform services match
 +
Production artifact is verified
 +
No accidental environment ID is present
 ↓
 IDENTIFIER READY
```

The rule is simple:

> **Choose production identifiers carefully, keep them stable, and verify the actual build before publishing.**


---

# Related documentation

### Foundations

- `foundations/README.md`
- `foundations/dependency-management.md`
- `foundations/device-support.md`
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

Use official documentation when creating or changing application identifiers.

### Apple

- Bundle IDs: https://developer.apple.com/documentation/appstoreconnectapi/bundle-ids
- Bundle identifier (`CFBundleIdentifier`): https://developer.apple.com/documentation/BundleResources/Information-Property-List/CFBundleIdentifier
- App Store Connect app information: https://developer.apple.com/help/app-store-connect/reference/app-information/app-information
- Add a new app: https://developer.apple.com/help/app-store-connect/create-an-app-record/add-a-new-app/
- App ID glossary: https://developer.apple.com/help/glossary/app-id/

### Android

- Android application fundamentals: https://developer.android.com/guide/components/fundamentals
- Android app manifest: https://developer.android.com/guide/topics/manifest/manifest-intro
- Android build configuration: https://developer.android.com/build

### Expo

- App configuration: https://docs.expo.dev/versions/latest/config/app/
- EAS Build configuration: https://docs.expo.dev/build-reference/build-configuration/
- Expo Application: https://docs.expo.dev/versions/latest/sdk/application/

### React Native

- React Native documentation: https://reactnative.dev/docs/getting-started

**Last verified:** August 11, 2026

Application identifiers are stable concepts, but platform tooling and store workflows can change. Re-check the official documentation when creating a new app, changing identifiers, or debugging store/signing issues.
