# Expo Credentials

Expo credentials are the credentials that allow an Expo / React Native application to be **built, signed, and submitted** to Apple and Google.

They are not all the same thing.

A useful mental model is:

```text
Application identity
 ↓
Signing credentials
 ↓
Build artifact
 ↓
Store credentials
 ↓
Submission
```

For Expo projects using EAS, credentials can be managed by EAS or supplied locally. EAS defaults to remotely managed credentials when `credentialsSource` is not specified.

The most important rule is:

> **Do not treat every credential as interchangeable, and never commit production credentials to Git.**

This guide covers:

- EAS-managed credentials
- local credentials
- Android signing
- iOS signing
- push notification credentials
- App Store Connect submission credentials
- Google Play submission credentials
- credential recovery
- rotation
- CI/CD
- AI-assisted credential workflows
- common credential failures

---

# 1. Credential types

An Expo production workflow can involve several different credentials.

| Credential | Platform | Main purpose |
|---|---|---|
| Android keystore | Android | Signs Android builds |
| Android upload key | Android | Authenticates uploads to Google Play when Play App Signing is used |
| Google service account key | Android | Allows EAS Submit / automation to access Google Play |
| iOS distribution certificate | iOS | Signs iOS distribution builds |
| iOS provisioning profile | iOS | Authorizes the signed app for its distribution method |
| Apple Push Notification key | iOS | Authorizes push notification delivery |
| App Store Connect API key | iOS | Allows API-based App Store Connect operations |
| Expo token | CI/CD | Authenticates EAS CLI from automation |

These credentials have different lifecycles and should be managed separately.

For example:

```text
iOS distribution certificate
≠
Apple Push Notification key
≠
App Store Connect API key
```

Expo's current credential documentation explicitly separates iOS distribution certificates, provisioning profiles, and push notification keys.

---

# 2. EAS credential management

EAS provides two primary ways to supply app signing credentials.

## Remote credentials

EAS stores and manages the credentials for the project.

```text
Developer
 ↓
EAS
 ↓
Signing credentials
 ↓
EAS Build
```

This is the simplest option for most Expo projects.

## Local credentials

The project provides a local:

```text
credentials.json
```

file containing paths to signing material and associated passwords.

```text
Developer machine / CI
 ↓
credentials.json
 ↓
EAS Build
```

Expo documents `credentialsSource` as:

```json
{
 "credentialsSource": "remote"
}
```

or:

```json
{
 "credentialsSource": "local"
}
```

If it is not specified, the default is `remote`.

For most teams:

```text
remote
```

is the simpler starting point.

---

# 3. Recommended default

For a normal Expo application:

```text
EAS-managed signing credentials
+
EAS environment variables
+
protected CI secrets
```

is usually enough.

Use local credentials when there is a concrete requirement such as:

- existing organization-controlled signing material
- custom credential-management requirements
- builds that must use credentials outside EAS
- a migration from an existing native release system
- a specific compliance/security requirement

Do not choose local credentials simply because:

```text
"local sounds more secure."
```

Security depends on the entire credential lifecycle:

```text
storage
+
access
+
backup
+
rotation
+
CI exposure
+
recovery
```

---

# 4. Inspect EAS credentials

Use:

```bash
eas credentials
```

You can target a platform:

```bash
eas credentials --platform android
```

or:

```bash
eas credentials --platform ios
```

The current EAS CLI provides `eas credentials` for managing credentials and `eas credentials:configure-build` for configuring build credentials.

Start here before creating or replacing credentials.

Do not generate a new production credential just because you cannot immediately find the existing one.

---

# 5. EAS credential configuration

You can explicitly configure the credential source per build profile.

Example:

```json
{
 "build": {
 "production": {
 "credentialsSource": "remote"
 }
 }
}
```

A local credential profile could use:

```json
{
 "build": {
 "production-local": {
 "credentialsSource": "local"
 }
 }
}
```

Keep production credential configuration explicit when the project has more than one credential strategy.

---

# 6. Android credentials

Android signing generally involves:

```text
Keystore
 ↓
Private signing key
 ↓
Signed AAB/APK
 ↓
Google Play
```

A keystore contains the private key and certificate used for signing.

Expo's current documentation states that the Android application's keystore must be kept private and must not be committed to the repository.

---

# 7. Android keystore

A production Android keystore is critical.

It may contain:

```text
Private key
Certificate
Key alias
Password
```

Treat all of these as sensitive.

Never commit:

```text
*.jks
*.keystore
credentials.json
```

when they contain production credentials.

Use:

```text
EAS-managed credentials
or
protected secret storage
```

instead.

---

# 8. Android Play App Signing

Google Play App Signing changes the credential model.

A common model is:

```text
Developer
 ↓
Upload key
 ↓
Google Play
 ↓
App signing key
 ↓
User-installed app
```

The important distinction is:

```text
Upload key
≠
App signing key
```

Do not rotate or replace one while assuming it is the other.

If the upload key is lost, Google provides a process for resetting it. Expo documents this as a recoverable scenario when Google Play App Signing is being used.

---

# 9. Android credential recovery

If an Android signing credential is missing:

```text
STOP
 ↓
Identify whether it is:
 upload key
 or
 app signing key
 ↓
Check EAS credentials
 ↓
Check Google Play configuration
 ↓
Determine recovery path
```

Do not immediately generate a new keystore.

A new signing identity can have serious consequences depending on how the app is configured in Google Play.

If the app uses Google Play App Signing, recover or reset the upload key through the supported Google process when appropriate.

---

# 10. Android service account credentials

Google Play submission is separate from application signing.

For EAS Submit, Expo currently requires a Google Service Account key with access to the application in Google Play Console.

The model is:

```text
Android signing
→ creates trusted application artifact

Google Service Account
→ authorizes store submission
```

Do not confuse them.

A successful Android build does not prove that Google Play submission credentials are configured.

---

# 11. Google service account security

A Google service account key is highly sensitive.

Treat the JSON key as a secret.

Do not:

```text
commit it
email it casually
paste it into an AI tool
put it in frontend code
print it in CI logs
store it in public GitHub issues
```

Use:

```text
EAS credential storage
or
protected CI secret storage
```

and grant only the permissions required for the release workflow.

---

# 12. iOS credentials

The main iOS signing credentials used by EAS are:

```text
Distribution Certificate
Provisioning Profile
```

Push notifications use a separate:

```text
Apple Push Notification Key
```

Expo's current documentation identifies these as distinct credential types.

The build relationship is:

```text
Bundle ID
 ↓
Distribution Certificate
 +
Provisioning Profile
 ↓
Signed iOS application
```

---

# 13. iOS distribution certificate

A distribution certificate is used to sign a distributable iOS application.

It is associated with:

```text
Apple Developer account
+
Team
```

EAS can create and manage it or use an existing certificate supplied by the project.

Expo's current documentation notes that Apple Developer permissions capable of creating signing credentials are required when EAS needs to create them.

---

# 14. iOS provisioning profile

A provisioning profile connects the signed application to Apple's distribution configuration.

It depends on:

```text
App identifier
+
Certificates
+
Capabilities / entitlements
+
Distribution method
```

When relevant capabilities or entitlements change, the provisioning profile may need to be updated.

Expo's current Apple credential guidance specifically notes that provisioning profiles may need updating when capabilities change or when the profile expires.

---

# 15. iOS push notification key

Push notification credentials are separate from app signing.

Think:

```text
Build signing
→ distribution certificate
→ provisioning profile

Push delivery
→ Apple Push Notification key
```

Do not delete or rotate a push credential while assuming it affects signing.

Expo identifies Apple Push Notification keys as a separate iOS credential type.

---

# 16. App Store Connect API key

An App Store Connect API key is used for API-based operations against App Store Connect.

It is different from:

```text
iOS distribution certificate
```

and:

```text
Apple Push Notification key
```

Use the API key when the workflow needs programmatic App Store Connect access.

Keep its:

```text
Issuer ID
Key ID
Private key
```

protected.

Never commit the private key.

---

# 17. Apple account permissions

Not every Apple Developer user can create or modify every signing credential.

Expo's current documentation states that the permissions required depend on the Apple Developer account type and role. On organization accounts, Account Holder and Admin can create signing credentials, while App Manager can do so when the appropriate Certificates, Identifiers, and Profiles access is enabled.

Before troubleshooting an apparent credential failure, check:

```text
Apple account
+
Team
+
Role
+
Certificates / Identifiers / Profiles permission
```

Do not solve a permissions problem by sharing the Account Holder password.

---

# 18. Apple federated accounts

Apple organizations may use federated authentication.

Expo's current documentation notes that EAS CLI cannot use a federated Apple Developer account's email/password login for certain credential updates. An App Store Connect API token with appropriate access can be used for supported credential-management operations instead.

If your organization uses federated Apple accounts:

```text
Do not assume
Apple email/password
=
EAS credential automation
```

Use the supported API-based workflow where required.

---

# 19. Local credentials with `credentials.json`

If you intentionally use local credentials, EAS can read:

```text
credentials.json
```

from the project.

The file can reference:

```text
Android keystore
iOS distribution certificate
iOS provisioning profile
```

and their associated passwords.

Expo documents this local credential model explicitly.

A conceptual structure is:

```json
{
 "android": {
 "keystore": {
 "keystorePath": "path/to/keystore",
 "keystorePassword": "<secret>",
 "keyAlias": "<secret>",
 "keyPassword": "<secret>"
 }
 },
 "ios": {
 "provisioningProfilePath": "path/to/profile",
 "distributionCertificate": {
 "path": "path/to/certificate",
 "password": "<secret>"
 }
 }
}
```

**Do not replace the placeholders with real credentials in public documentation.**

---

# 20. Never commit `credentials.json`

If using local credentials:

```text
credentials.json
```

should be protected from Git.

Add it to:

```text
.gitignore
```

along with local credential files and private signing material.

For example:

```gitignore
credentials.json
*.jks
*.keystore
*.p12
*.p8
*.mobileprovision
```

Review the repository's actual credential requirements before adding patterns.

Do not ignore a file simply because its extension looks sensitive if the project intentionally needs a non-secret public fixture.

---

# 21. Local credentials in CI

Local credentials can be used in CI, but this increases operational complexity.

The CI system needs access to:

```text
credential files
+
passwords
```

and must reconstruct the expected file paths securely.

Before choosing this model, ask:

```text
Why can EAS-managed credentials not solve the problem?
```

If there is no strong answer, remote credentials are usually simpler.

---

# 22. Remote credential storage

With EAS-managed credentials:

```text
EAS
→ stores credential material
→ makes it available to authorized build jobs
```

Expo documents that EAS encrypts stored credentials and uses KMS-based encryption for credentials at rest.

That does not mean credentials are harmless.

Your security boundary still includes:

```text
Expo account
+
project permissions
+
CI tokens
+
local developer machines
+
store accounts
```

Protect all of them.

---

# 23. Credential access is a permission problem

A production release system should follow:

```text
Need access?
 ↓
Grant minimum required permission
 ↓
Use for intended task
 ↓
Remove/revoke when no longer needed
```

Avoid:

```text
Everyone gets Account Holder access
```

or:

```text
Every CI workflow gets every secret
```

Use project/team permissions and protected environments.

The repository's security guidance requires least privilege, protected secrets, explicit authorization, and human approval for high-impact release actions.

---

# 24. Credential lifecycle

Treat credentials as lifecycle-managed assets.

```text
Create
 ↓
Store
 ↓
Use
 ↓
Monitor
 ↓
Rotate / renew
 ↓
Revoke
 ↓
Recover
```

Do not wait until release day to discover:

```text
certificate expired
```

or:

```text
credential is inaccessible
```

Record:

```text
Credential type
Owner
Purpose
Platform
Expiry
Storage location
Recovery path
Rotation procedure
```

Never record the secret itself.

---

# 25. Credential inventory

Maintain a non-secret inventory.

Example:

| Credential | Owner | Used for | Expiry / rotation | Recovery |
|---|---|---|---|---|
| Android signing | Release owner | Build | Project-specific | Google/EAS path |
| Google service account | Release owner | Play submission | Rotate per policy | Recreate/revoke |
| iOS distribution certificate | Apple release owner | Build | Apple lifecycle | Recreate/renew |
| iOS provisioning profile | Apple release owner | Build | Apple lifecycle | Regenerate |
| Apple Push key | Notification owner | Push | Apple lifecycle | Recreate |
| ASC API key | Release automation | App Store Connect | Policy-based | Revoke/recreate |
| EXPO_TOKEN | CI owner | EAS automation | Policy-based | Revoke/recreate |

This inventory is safe to keep in public documentation if it contains no secrets or identifying private account information.

---

# 26. Credential expiry

When a credential expires:

```text
Identify credential
 ↓
Check whether it affects:
 build
 submission
 push
 runtime
 ↓
Renew / replace
 ↓
Build/test
 ↓
Verify
```

Do not regenerate every credential because one credential expired.

Credential types have different lifecycles.

---

# 27. Credential rotation

Rotation should be deliberate.

For example:

```text
Create replacement
 ↓
Configure replacement
 ↓
Test
 ↓
Deploy
 ↓
Confirm
 ↓
Revoke old credential
```

Avoid:

```text
Revoke first
 ↓
Hope the new credential works
```

when the platform supports overlap.

For production signing, first understand the platform's credential model before rotating anything.

---

# 28. Emergency credential rotation

If a credential is exposed:

```text
1. Stop affected automation.
2. Identify exactly what was exposed.
3. Revoke the credential where appropriate.
4. Create replacement credentials.
5. Update EAS / CI / store configuration.
6. Verify a non-production or controlled build.
7. Verify production build/submission.
8. Review logs and access history.
9. Document the incident.
```

Do not simply delete the leaked file from Git.

If it was committed, assume it may have been copied.

---

# 29. If a signing key is leaked

Treat a leaked production signing key as a security incident.

Do not immediately:

```text
generate random new key
```

First determine:

```text
Android:
Is Google Play App Signing enabled?
Is the leaked key the upload key?
Is it the app signing key?

iOS:
Which certificate was exposed?
Is it still valid?
What applications are affected?
```

Then follow the platform-supported recovery/rotation process.

For Android, Google Play App Signing can provide a different recovery path for an upload-key compromise than for an app-signing-key compromise. Expo documents the upload-key reset path for Play App Signing.

---

# 30. Credential backup

Backups are useful only if they are protected.

A backup should have:

```text
restricted access
+
encryption
+
known owner
+
recovery instructions
+
tested restoration
```

Do not create:

```text
credentials-backup.zip
```

and upload it to a public GitHub repository.

Do not store production signing material in ordinary shared folders.

---

# 31. Credential recovery drill

A mature release process should answer:

```text
If the release owner disappears,
can another authorized person release the app?
```

Verify:

```text
Who has access?
Where are credentials managed?
How is access granted?
How are credentials recovered?
How is a replacement created?
Can a build be performed?
Can a store submission be performed?
```

Test this before an emergency.

---

# 32. EAS credential synchronization

EAS can synchronize credentials between remote and local storage.

Use:

```bash
eas credentials
```

and select the credential synchronization options.

Expo documents workflows for:

```text
EAS → local credentials.json
```

and:

```text
credentials.json → EAS
```

for supported credentials.

This is useful when:

```text
migrating credential management
running local builds
backing up credentials through an approved secure process
```

Do not use synchronization as an excuse to keep unnecessary credential copies everywhere.

---

# 33. Credential source and build profile

Keep the relationship explicit:

```text
Build profile
 ↓
Credential source
 ↓
Platform credentials
```

Example:

```json
{
 "build": {
 "production": {
 "credentialsSource": "remote"
 },
 "internal-local": {
 "credentialsSource": "local"
 }
 }
}
```

This makes the build intent reviewable.

---

# 34. CI/CD and EAS credentials

A CI pipeline typically needs:

```text
GitHub Actions
 ↓
EXPO_TOKEN
 ↓
EAS
 ↓
Build credentials
```

The CI job should not normally need direct access to:

```text
Apple private signing material
Android keystore
```

when EAS-managed credentials can handle the signing step.

This keeps the credential surface smaller.

---

# 35. `EXPO_TOKEN`

For non-interactive EAS commands in CI, Expo documents using an Expo access token through:

```text
EXPO_TOKEN
```

The token authenticates the CI process with Expo/EAS.

Treat it as a secret.

Never:

```text
echo $EXPO_TOKEN
```

or print it in logs.

Use protected GitHub environment/repository secrets according to the workflow.

---

# 36. Production GitHub environment

For production release automation:

```text
GitHub workflow
 ↓
production environment
 ↓
required reviewer
 ↓
production secrets
 ↓
EAS submission
```

This is preferable to exposing store credentials to every workflow.

The repository's CI/CD guidance requires production credentials to be protected and production submission to have a human approval boundary.

---

# 37. Credential separation in CI

A good workflow separates:

```text
PR CI
→ no production credentials

Preview
→ preview/test credentials only

Production build
→ required signing/build access

Production submission
→ store submission credentials
```

Do not make one global:

```text
MOBILE_RELEASE_SECRET
```

that contains everything.

Separate credentials by purpose.

---

# 38. First production build

For the first Expo production build:

```text
1. Verify identifiers.
2. Verify Apple / Google application records.
3. Configure EAS.
4. Inspect existing credentials.
5. Create or import signing credentials.
6. Verify environment.
7. Build Android.
8. Build iOS.
9. Test artifacts.
10. Configure store submission credentials.
11. Submit through the intended path.
```

The first build is not the time to experiment with credential rotation.

---

# 39. Existing application migration

If an application already exists in the stores:

```text
STOP
```

before generating new signing credentials.

Determine:

```text
Current Android signing identity
Current Play App Signing state
Current iOS distribution identity
Current App Store Connect application
Current bundle/application IDs
Current EAS credential state
```

Then migrate deliberately.

A new credential can be harmless in one context and destructive in another.

---

# 40. Migrating Android credentials

If moving from an existing native Android workflow to EAS:

```text
Existing keystore
 ↓
Verify identity
 ↓
Configure local credentials
 ↓
Upload/sync to EAS if desired
 ↓
Build
 ↓
Verify signing identity
```

Expo documents using `eas credentials` to upload existing local credentials to EAS-managed storage.

Do not generate a replacement keystore before verifying the existing one.

---

# 41. Migrating iOS credentials

For an existing iOS application:

```text
Existing Bundle ID
 ↓
Existing distribution certificate
 ↓
Existing provisioning profile
 ↓
EAS
```

If using existing credentials, EAS supports uploading them.

Expo documents that pre-generated iOS credentials can be added using `eas credentials` or through the EAS dashboard.

Preserve the application's identity.

---

# 42. Build credentials vs submission credentials

Keep these separate conceptually.

### Build

```text
Android keystore
iOS distribution certificate
iOS provisioning profile
```

### Submission

```text
Google Service Account
App Store Connect API key / Apple submission credentials
```

### Runtime integrations

```text
Push credentials
Third-party service credentials
```

One successful layer does not prove the others work.

---

# 43. Push notification credentials

Push systems may involve:

```text
Apple Push Notification credentials
+
Firebase / FCM credentials
+
Expo Push infrastructure
```

These are not necessarily build-signing credentials.

When push breaks:

```text
Do not start by regenerating signing certificates.
```

Check:

```text
push credential
+
environment
+
app identifier
+
notification provider
+
device token
```

Expo's credential documentation explicitly distinguishes push notification credentials from app signing credentials.

---

# 44. Store submission credentials

EAS Submit uses different credentials from the signing process.

For Android:

```text
Google Play
→ Google Service Account
```

For iOS:

```text
App Store Connect
→ Apple credentials / API key
```

Expo's current EAS Submit documentation describes these separately from build signing.

This separation should also exist in your documentation and secret storage.

---

# 45. Submission credential verification

Before a production submission:

```text
Credential exists
 ↓
Credential has required permission
 ↓
Credential targets correct app/account
 ↓
Credential is not expired/revoked
 ↓
Test submission path
 ↓
Human approval
```

Do not test a production credential by submitting a real production release just to see whether it works.

Use the least-impact validation available.

---

# 46. Credential failure: EAS cannot find Android credentials

Check:

```bash
eas credentials --platform android
```

Then verify:

```text
project
application ID
build profile
credential source
```

If using local credentials:

```text
credentials.json
keystore path
```

If using remote credentials:

```text
EAS project credentials
```

Do not create a new keystore until you know why the existing one is unavailable.

---

# 47. Credential failure: EAS cannot find iOS credentials

Check:

```bash
eas credentials --platform ios
```

Verify:

```text
Bundle ID
Apple team
distribution certificate
provisioning profile
credential source
```

If EAS needs to create credentials, verify the Apple account has sufficient permissions.

---

# 48. Credential failure: Apple permission denied

Possible causes:

```text
Wrong Apple team
Insufficient role
Certificates/Identifiers/Profiles access disabled
Federated account limitation
Expired/revoked credential
```

Check the account role before changing credentials.

Expo's current documentation provides the role-specific requirements for creating signing credentials.

---

# 49. Credential failure: Android upload rejected

Check:

```text
Application ID
Upload certificate
Upload key
Play App Signing state
```

If the upload key is incorrect or lost:

```text
Use Google's supported upload-key reset process
```

Do not change the application signing identity without understanding the Play App Signing configuration.

---

# 50. Credential failure: store submission authentication fails

For Android:

```text
Google Service Account
→ correct Play Console app access
→ correct project
```

For iOS:

```text
App Store Connect API key
→ correct issuer
→ correct key
→ correct role
→ correct app access
```

Do not reuse a signing credential as a store API credential.

---

# 51. Credential failure: CI cannot authenticate to EAS

Check:

```text
EXPO_TOKEN
GitHub secret scope
GitHub environment
Expo project
workflow permissions
```

Run the equivalent command locally with a valid authenticated account before changing the workflow.

Do not put the token into:

```text
eas.json
app.config.ts
package.json
```

---

# 52. Credential failure: credentials work locally but not in CI

This usually means:

```text
local credential source
≠
CI credential source
```

Check:

```text
credentials.json availability
file paths
CI secret injection
EAS credential source
environment
permissions
```

If using remote credentials, confirm the CI account has permission to access the EAS project.

---

# 53. Credential failure: credentials were deleted from EAS

Important distinction:

```text
Delete from EAS
≠
delete from Apple / Google
```

Expo's current documentation states that deleting credentials through `eas credentials` removes them from Expo's servers; it does not necessarily revoke them at the platform provider.

If a credential must actually be revoked:

```text
Revoke/delete it at the platform provider
```

as well.

---

# 54. Do not confuse deletion with revocation

This is critical.

```text
Remove from EAS
→ EAS no longer stores/uses it

Revoke at Apple/Google
→ credential no longer has platform authority
```

Both may be necessary during incident response.

---

# 55. Credential security with AI

AI coding tools can inspect:

```text
package.json
app.config.ts
eas.json
GitHub workflows
```

That can be useful.

Do not give them:

```text
private keys
keystores
service-account JSON
Apple private keys
production passwords
```

AI should operate on:

```text
metadata
configuration structure
sanitized errors
credential type
credential status
```

not the secret value itself.

---

# 56. AI credential audit

A safe AI audit can ask:

```text
Inspect this Expo repository for credential-management risks.

Check:

- credentials.json
- .gitignore
- GitHub Actions
- eas.json
- app.config.*
- environment configuration
- package scripts
- CI secret usage

Look for:

1. committed credential files
2. hard-coded secrets
3. production secrets exposed to PR workflows
4. excessive CI permissions
5. credentials mixed with public client configuration
6. store credentials exposed to build jobs unnecessarily
7. unsafe logging

Do not request or print secret values.

Return:

- confirmed findings
- likely risks
- evidence
- recommended fixes
- verification steps
```

This is a good use of AI because the agent is auditing structure rather than handling secrets.

---

# 57. AI-assisted credential rotation

AI can help prepare a rotation plan:

```text
Identify credential
 ↓
Find where it is referenced
 ↓
List affected workflows
 ↓
Prepare replacement steps
 ↓
Human verifies
 ↓
Create replacement
 ↓
Test
 ↓
Revoke old credential
```

Do not allow an AI agent to independently:

```text
create production signing credentials
revoke production credentials
change store permissions
submit a release
```

without an explicit human approval boundary.

The repository's AI guidance requires human verification and approval for signing credentials, secrets, store submission, account changes, and other high-impact operations.

---

# 58. Credential exposure in Git

If a secret was committed:

```text
1. Assume it is compromised.
2. Revoke/rotate it.
3. Remove it from the working tree.
4. Remove it from repository history where appropriate.
5. Inspect CI logs and artifacts.
6. Review access.
7. Add prevention controls.
```

Do not rely on:

```bash
git rm secret-file
```

alone.

Git history may still contain the credential.

---

# 59. GitHub secret scanning

For public repositories, use GitHub's available secret-scanning and push-protection capabilities where appropriate.

The goal is:

```text
Developer mistake
 ↓
Detection
 ↓
Block or alert
 ↓
Rotate credential
```

Secret scanning is a safety net.

It is not a replacement for:

```text
good credential storage
+
least privilege
+
human review
```

---

# 60. Credential files and artifacts

Do not accidentally include credentials in:

```text
build artifacts
logs
release bundles
GitHub Actions artifacts
debug archives
support bundles
```

Before uploading diagnostics publicly:

```text
Inspect
→ redact
→ verify
→ upload
```

A build log can contain sensitive paths, identifiers, tokens, or environment information.

---

# 61. Credential checklist

## EAS

- [ ] EAS project is correct.
- [ ] Credential source is intentional.
- [ ] `eas credentials` has been checked.
- [ ] Remote credentials are accessible to authorized users.
- [ ] Local credentials are used only when needed.

## Android

- [ ] Production keystore is identified.
- [ ] Play App Signing state is known.
- [ ] Upload key is identified.
- [ ] Keystore is not committed.
- [ ] Google Service Account is separate from signing.
- [ ] Google Play permissions are correct.
- [ ] Recovery path is documented.

## iOS

- [ ] Bundle ID is correct.
- [ ] Distribution certificate is identified.
- [ ] Provisioning profile is valid.
- [ ] Required capabilities are aligned.
- [ ] Apple Developer permissions are sufficient.
- [ ] Push credentials are separate from signing.
- [ ] App Store Connect submission credentials are separate from signing.

## CI/CD

- [ ] `EXPO_TOKEN` is protected.
- [ ] Production secrets are not available to PR workflows.
- [ ] GitHub production environment is protected.
- [ ] Required reviewers are configured.
- [ ] Store credentials are only exposed to submission jobs.
- [ ] Secrets are never printed.

## Security

- [ ] No private keys are committed.
- [ ] No keystores are committed.
- [ ] No service-account JSON is committed.
- [ ] No credentials are embedded in the app.
- [ ] Credential owners are documented.
- [ ] Recovery procedures are documented.
- [ ] Rotation procedures are documented.

---

# 62. First-release credential gate

Before the first production release:

```text
Application identity
 ↓
Signing credentials
 ↓
Build
 ↓
Artifact verification
 ↓
Store submission credentials
 ↓
Test submission path
 ↓
Human approval
 ↓
Release
```

Do not combine these into one opaque step.

You should be able to answer:

```text
What credential signs the app?
Who owns it?
Where is it stored?
What does it authorize?
When does it expire?
How is it recovered?
How is it rotated?
```

---

# 63. Production credential ownership

Every production credential should have an owner.

The owner does not necessarily need to be the only person who can use it.

Use:

```text
Owner
+
Authorized users
+
Recovery path
```

rather than:

```text
One developer's laptop
```

being the only place where the release can happen.

---

# 64. Credential architecture

For a typical Expo project:

```text
 ┌─────────────────────┐
 │ GitHub │
 │ CI/CD + approval │
 └──────────┬──────────┘
 │
 EXPO_TOKEN
 │
 ▼
 ┌─────────────────────┐
 │ EAS │
 │ │
 │ Build credentials │
 └──────┬───────┬──────┘
 │ │
 Android iOS
 │ │
 Keystore Distribution
 Certificate
 + Profile
 │ │
 ▼ ▼
 Signed application
 │
 ┌──────────┴──────────┐
 │ │
 ▼ ▼
 Google Play App Store Connect
 │ │
 Service Account API / Apple
 submission auth submission auth
```

Push credentials are a separate runtime integration:

```text
Application
 ↓
Push service
 ↓
Apple / Google push infrastructure
```

Do not mix them with signing credentials.

---

# 65. Definition of done

Expo credential management is production-ready when:

```text
Credential types identified
 +
Application identity verified
 +
Signing credentials configured
 +
Store submission credentials configured
 +
Secrets protected
 +
CI access restricted
 +
Recovery documented
 +
Rotation understood
 +
Real build verified
 +
Human approval required
 ↓
 CREDENTIALS READY
```

The goal is not to have the fewest credentials.

The goal is to have:

> **The smallest, clearly separated credential set that can safely build, submit, operate, and recover the application.**

---

# Related documentation

### Foundations

- `foundations/project-configuration.md`
- `foundations/release-environments.md`
- `foundations/identifiers.md`
- `foundations/versioning.md`
- `foundations/dependency-management.md`

### Signing

- `signing/ios/certificates.md`
- `signing/ios/provisioning-profiles.md`
- `signing/ios/distribution.md`
- `signing/ios/api-keys.md`
- `signing/ios/key-rotation.md`
- `signing/android/keystore.md`
- `signing/android/upload-key.md`
- `signing/android/play-app-signing.md`
- `signing/android/key-rotation.md`
- `signing/security/secret-storage.md`
- `signing/security/ci-secrets.md`
- `signing/security/access-control.md`
- `signing/security/recovery.md`

### Expo

- `frameworks/expo/app-config.md`
- `frameworks/expo/build.md`
- `frameworks/expo/ci-cd.md`
- `frameworks/expo/common-failures.md`
- `frameworks/expo/submit.md`

### Release engineering

- `release-engineering/eas/`
- `release-engineering/github-actions/`
- `release-engineering/environment-management.md`
- `release-engineering/app-store-connect-api.md`
- `release-engineering/google-play-api.md`
- `release-engineering/release-pipelines.md`

### Testing

- `testing/release-smoke-tests.md`
- `testing/device-testing.md`
- `testing/push-notifications.md`

### AI

- `ai/workflows/release-audit.md`
- `ai/workflows/release-preparation.md`
- `ai/workflows/debugging.md`
- `ai/security/secret-protection.md`
- `ai/security/agent-permissions.md`
- `ai/security/destructive-actions.md`

---

# Official sources

Use official documentation as the authority for current credential behavior.

### Expo

- App credentials: https://docs.expo.dev/app-signing/app-credentials/
- Automatically managed credentials: https://docs.expo.dev/app-signing/managed-credentials/
- Existing credentials: https://docs.expo.dev/app-signing/existing-credentials/
- Local credentials: https://docs.expo.dev/app-signing/local-credentials/
- Sync credentials: https://docs.expo.dev/app-signing/syncing-credentials/
- EAS credential security: https://docs.expo.dev/app-signing/security/
- Apple Developer roles and permissions: https://docs.expo.dev/app-signing/apple-developer-program-roles-and-permissions/
- EAS CLI: https://docs.expo.dev/eas/cli/
- EAS Build configuration: https://docs.expo.dev/build/eas-json/
- EAS Submit: https://docs.expo.dev/submit/introduction/

### Apple

- Apple Developer: https://developer.apple.com/
- Certificates, Identifiers & Profiles: https://developer.apple.com/help/account/
- App Store Connect Help: https://developer.apple.com/help/app-store-connect/

### Google

- Android Developers: https://developer.android.com/
- Google Play Console Help: https://support.google.com/googleplay/android-developer/
- Play App Signing: https://support.google.com/googleplay/android-developer/answer/9842756

### GitHub

- GitHub Actions secrets: https://docs.github.com/en/actions/security-for-github-actions/security-guides/using-secrets-in-github-actions
- GitHub environments: https://docs.github.com/en/actions/deployment/targeting-different-environments/using-environments-for-deployment

**Last verified:** August 11, 2026

Credential requirements, EAS behavior, Apple account permissions, Google Play signing, and CI/CD tooling can change. Re-check the official sources before creating, rotating, revoking, or migrating production credentials.
