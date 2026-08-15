# Release Environments

A release environment defines **where an app is running, which services it talks to, which configuration it uses, and how safely it can move toward production**.

For most mobile applications, you do not need a large environment system.

A practical model is:

```text
Development
    ↓
Preview
    ↓
Production
```

But the names are less important than the boundaries.

The important rule is:

> **A production build must be able to prove that it is using production configuration.**

This guide focuses on Expo and React Native first, while keeping the concepts useful for other mobile frameworks.

---

## 1. What is a release environment?

An environment is a defined set of configuration and services used by a particular stage of the application lifecycle.

For example:

```text
Development
→ developer machine
→ development API
→ test accounts
→ local/debug tooling

Preview
→ shared testing
→ staging/test API
→ realistic release configuration
→ internal testers

Production
→ real users
→ production API
→ production services
→ store distribution
```

An environment is more than an API URL.

It can include:

- API endpoints
- authentication configuration
- application identifiers
- feature flags
- analytics
- crash reporting
- push notification configuration
- payment configuration
- OAuth configuration
- deep-link configuration
- native build settings
- environment variables
- signing configuration
- update channels

---

# 2. Why environments exist

Without environment separation, developers eventually get problems like:

```text
Production app
    ↓
staging API
```

or:

```text
Preview build
    ↓
production payments
```

or:

```text
Production build
    ↓
development analytics
```

or:

```text
CI build
    ↓
different configuration from local release testing
```

These failures can be much worse than a normal application bug because the application may appear healthy while operating against the wrong system.

---

# 3. The smallest useful environment model

For most small and medium mobile projects:

```text
Development
Preview
Production
```

is enough.

### Development

Used by individual developers.

Typical properties:

- fast iteration
- local debugging
- development backend
- development credentials
- development analytics
- development feature flags
- development app identity where useful

### Preview

Used for shared validation.

Typical properties:

- release-like build
- shared backend
- staging/test services
- internal testers
- realistic configuration
- production-like native behavior

### Production

Used by real users.

Typical properties:

- production backend
- production services
- production credentials
- production analytics
- production crash reporting
- store distribution
- strict access control

Do not create more environments unless there is a real workflow that needs them.

---

# 4. Environment vs build profile

These are related but not identical.

An **environment** answers:

> Which configuration and services should this app use?

A **build profile** answers:

> How should this application binary be built?

For example:

```text
Environment:
preview

Build profile:
preview
```

is common, but they do not have to have the same name.

You can have:

```text
Build profile:
internal-ios

Environment:
preview
```

The important thing is that the relationship is explicit.

For EAS, `eas.json` build profiles can specify the environment used during the build. Expo currently supports `development`, `preview`, and `production` as the default EAS environments, and custom environment names are available on eligible plans.

---

# 5. Environment vs application identity

A separate environment does not automatically require a separate application identity.

For example:

```text
Development
com.example.app.dev

Preview
com.example.app.preview

Production
com.example.app
```

This is useful when all three builds need to coexist on the same device.

But another project may use:

```text
Development
com.example.app

Production
com.example.app
```

with different backend configuration.

Both approaches can be valid.

Choose based on the actual workflow.

Do not create multiple store applications just to make environment separation look cleaner.

See:

```text
foundations/identifiers.md
```

for identifier strategy.

---

# 6. Recommended environment model for Expo

A practical Expo/EAS setup is:

```text
development
    ↓
development build
    ↓
development environment

preview
    ↓
internal distribution build
    ↓
preview environment

production
    ↓
store build
    ↓
production environment
```

Example:

```text
Development
API:
https://dev-api.example.com

Preview
API:
https://staging-api.example.com

Production
API:
https://api.example.com
```

The actual domains will depend on the project.

The important thing is that the mapping is explicit.

---

# 7. EAS environments

Expo EAS currently provides three default environments:

```text
development
preview
production
```

Environment variables can be scoped to these environments and used by EAS Build, EAS Update, EAS Workflows, and local development workflows where supported.

Example:

```bash
eas env:list --environment production
```

To pull an environment locally:

```bash
eas env:pull --environment development
```

Expo documents `eas env:pull` as a way to retrieve environment variables for local development. Generated `.env` files should remain ignored by Git when they contain sensitive or machine-specific values.

---

# 8. Explicitly map build profiles to environments

For production releases, make the relationship obvious.

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

This is easier to reason about than relying entirely on implicit behavior.

Expo documents that EAS can automatically determine an environment based on build configuration, but explicitly setting `environment` gives the project direct control over the mapping.

For release-critical configuration:

> Prefer explicit mapping over hidden assumptions.

---

# 9. Environment variables

Environment variables are one of the main mechanisms for separating configuration.

Example:

```text
development
API_URL=https://dev-api.example.com

preview
API_URL=https://staging-api.example.com

production
API_URL=https://api.example.com
```

But environment variables are not automatically secrets.

Every variable should be classified.

```text
PUBLIC
SENSITIVE
SECRET
```

For EAS, Expo currently provides plain-text, sensitive, and secret visibility levels. Secret variables are intended for values that should remain on EAS servers and not be readable outside them.

---

# 10. Client environment variables are public

This is one of the most important mobile rules.

If a value is bundled into the mobile application:

```text
User
    ↓
Downloads app
    ↓
Inspects application
    ↓
Can potentially recover the value
```

Therefore:

```text
EXPO_PUBLIC_API_URL
```

is public configuration.

Do not put:

```text
Database password
Private API key
Service-account private key
OAuth client secret
Signing secret
Payment private key
```

into client-side environment variables.

Expo explicitly warns that values included in client-side code are readable by users.

---

# 11. Public vs sensitive vs secret

A useful classification:

| Type | Example | Can be in client bundle? |
|---|---|---|
| Public | API URL | Yes |
| Public | App identifier | Yes |
| Public | Feature configuration | Usually |
| Sensitive | CI token | No |
| Sensitive | Source-map upload token | No |
| Secret | Private API credential | No |
| Secret | Signing private key | No |
| Secret | Service-account credential | No |

The key question is:

> **Would exposing this value allow someone to gain privileged access?**

If yes, it does not belong in the mobile client.

---

# 12. Environment variables in Expo

Expo uses the `EXPO_PUBLIC_` prefix for variables intended to be available directly in application JavaScript.

Example:

```text
EXPO_PUBLIC_API_URL=https://api.example.com
```

Application code can read:

```ts
const apiUrl = process.env.EXPO_PUBLIC_API_URL;
```

Expo documents that `EXPO_PUBLIC_*` values are inlined into the client bundle and therefore must not contain secrets.

Do not use:

```text
EXPO_PUBLIC_SECRET_KEY
```

as a secret.

The prefix does not provide security.

It explicitly indicates that the value is intended for client-side use.

---

# 13. Dynamic app configuration

Some environment values need to affect native configuration.

For example:

```text
Development
→ com.example.app.dev

Preview
→ com.example.app.preview

Production
→ com.example.app
```

A dynamic Expo configuration can select these values based on the environment.

Example:

```ts
const variant = process.env.APP_VARIANT;

const identifiers = {
  development: 'com.example.app.dev',
  preview: 'com.example.app.preview',
  production: 'com.example.app',
};

export default {
  name:
    variant === 'production'
      ? 'Example'
      : `Example (${variant})`,

  ios: {
    bundleIdentifier: identifiers[variant],
  },

  android: {
    package: identifiers[variant],
  },
};
```

Keep this logic small.

Do not turn `app.config.ts` into a general application configuration engine.

---

# 14. Local `.env` files

Local development commonly uses:

```text
.env
.env.local
```

These can contain:

```text
Development API URL
Local configuration
Developer-specific values
Local service settings
```

If a file contains secrets or machine-specific configuration:

```text
.gitignore
```

should prevent it from being committed.

Example:

```gitignore
.env
.env.local
.env.*.local
```

Adjust the pattern to the project's actual environment strategy.

Do not assume `.env` is automatically private.

If it is committed to Git, it is no longer a secret store.

---

# 15. EAS environment variables vs local `.env`

These solve different problems.

### Local `.env`

Useful for:

```text
Developer machine
Local development
Local testing
```

### EAS environment variables

Useful for:

```text
Cloud builds
EAS Update
EAS Workflows
Shared release configuration
Protected build values
```

Expo recommends EAS environment variables for cloud builds and updates when using EAS, while local `.env` files remain useful for local development.

Avoid having two conflicting sources of truth.

---

# 16. Avoid configuration precedence surprises

A common problem is:

```text
Local .env
      +
EAS environment
      +
eas.json env
      +
app.config.ts
      +
CI variables
```

all defining the same variable.

This makes debugging difficult.

For important variables, define one clear source of truth.

For example:

```text
Production API
→ EAS production environment

Development API
→ EAS development environment

Local overrides
→ explicit local development only
```

Document exceptions.

Do not rely on developers remembering undocumented precedence rules.

---

# 17. Environment-specific services

An environment can contain more than an API URL.

Example:

### Development

```text
API
Development backend

Database
Development database

Auth
Development provider/project

Analytics
Development analytics

Push
Development configuration

Payments
Sandbox
```

### Preview

```text
API
Staging backend

Database
Staging database

Auth
Staging provider/project

Analytics
Test analytics

Push
Test configuration

Payments
Sandbox
```

### Production

```text
API
Production backend

Database
Production database

Auth
Production provider/project

Analytics
Production analytics

Push
Production configuration

Payments
Production configuration
```

The exact setup depends on the product.

The principle is:

> **Do not mix environments across critical services without a deliberate reason.**

---

# 18. Backend environment separation

Mobile environment separation is only useful if the backend understands the same boundary.

For example:

```text
Mobile Development
        ↓
Development API
        ↓
Development database

Mobile Preview
        ↓
Staging API
        ↓
Staging database

Mobile Production
        ↓
Production API
        ↓
Production database
```

Avoid:

```text
Preview mobile
    ↓
Production database
```

unless the workflow explicitly requires it and access is tightly controlled.

A staging environment should not become a backdoor into production.

---

# 19. Data isolation

Prefer separate data stores for environments when practical.

For example:

```text
development DB
staging DB
production DB
```

This prevents:

- test data appearing in production
- production data being modified by tests
- accidental destructive operations
- environment confusion

If infrastructure cost makes separate systems impractical, use strong logical isolation and explicit access controls.

For production data, never rely only on a client-provided environment label.

The trusted backend must enforce the correct environment and access boundary.

---

# 20. Authentication separation

Authentication is especially important.

Avoid:

```text
Development app
→ production OAuth client

Preview app
→ production accounts

Production app
→ staging auth backend
```

Prefer:

```text
Development
→ development auth

Preview
→ staging/test auth

Production
→ production auth
```

This makes testing safer and reduces accidental access to real accounts.

---

# 21. Push notification separation

Push notification configuration should also be environment-aware.

Example:

```text
Development
→ development/test notification target

Preview
→ internal testers

Production
→ real users
```

Be careful when using the same application identity across environments.

If a development build can register against the same production push infrastructure, developers can accidentally receive or trigger production notifications.

Separate the configuration where the workflow requires it.

---

# 22. Analytics separation

Analytics environments should make test traffic distinguishable.

Options include:

```text
Separate analytics projects
```

or:

```text
Shared project
+
environment property
```

The right choice depends on the analytics provider and team size.

At minimum:

- [ ] Test events can be identified.
- [ ] Production dashboards are not polluted by developer activity.
- [ ] Sensitive data is not collected unnecessarily.
- [ ] Production analytics credentials/configuration are correct.

---

# 23. Crash reporting separation

Crash reporting should preserve release visibility.

A practical setup can use:

```text
One project
+
environment/release metadata
```

or separate projects where isolation is useful.

Always preserve:

```text
Environment
Version
Build number
Commit
```

where the provider supports it.

This makes it possible to answer:

> Is this crash happening in development, preview, or production?

---

# 24. Payment environment separation

Payments deserve stronger boundaries.

Use:

```text
Development
→ sandbox

Preview
→ sandbox/test

Production
→ production
```

Do not let test builds access real payment credentials or production payment operations unless the workflow explicitly requires it and the access is tightly controlled.

For store-based purchases, verify the platform's sandbox/test behavior separately from production behavior.

---

# 25. Feature flags by environment

Feature flags are useful for controlling release behavior.

Example:

```text
Development
NEW_CHECKOUT=true

Preview
NEW_CHECKOUT=true

Production
NEW_CHECKOUT=false
```

This can allow:

```text
Build
→ release
→ enable feature
```

without rebuilding when the flag is server-controlled.

But do not put security boundaries in client-side feature flags.

A client can modify or inspect client configuration.

For sensitive authorization:

```text
Backend authorization
```

must remain authoritative.

---

# 26. Build-time vs runtime configuration

This distinction is important.

### Build-time configuration

Changes the binary.

Examples:

- Bundle ID
- Application ID
- native permissions
- entitlements
- native dependencies
- API URL bundled into client code
- app name
- icons

Changing these generally requires a new native build.

### Runtime configuration

Can change without rebuilding when the configuration is fetched from a trusted backend or compatible update system.

Examples:

- feature flags
- remote configuration
- rollout percentage
- non-sensitive UI configuration

Use runtime configuration only when it genuinely improves release operations.

Do not build a remote configuration system for a few static values.

---

# 27. Environment and OTA updates

If using Expo Updates, environment handling becomes especially important.

A native build establishes the native runtime and application identity.

An OTA update changes compatible application code/configuration within the boundaries supported by the runtime.

A useful model is:

```text
Native build
→ establishes runtime

OTA update
→ changes compatible application code
```

Do not use OTA updates to bypass a required native release.

For EAS Update, specify the intended environment when publishing updates so the update uses the correct environment variables. Expo currently documents the `--environment` option for this purpose.

Example:

```bash
eas update --environment production
```

Only use OTA updates when the update is compatible with the installed native runtime.

---

# 28. Environment and signing

Signing belongs to the release pipeline, but environment separation affects which build gets signed.

A useful model:

```text
Development
→ development build
→ development signing/distribution

Preview
→ internal distribution
→ preview configuration

Production
→ store distribution
→ production signing
```

Do not allow a developer workflow to accidentally produce a production-signed store artifact without the expected controls.

---

# 29. Environment and application identifiers

If environments use separate identifiers:

```text
Development
com.example.app.dev

Preview
com.example.app.preview

Production
com.example.app
```

verify every dependent service.

For example:

```text
Identifier
    ↓
Push configuration
    ↓
OAuth configuration
    ↓
Deep links
    ↓
Firebase
    ↓
Analytics
    ↓
Store record
```

Changing only the Bundle ID or Application ID can break the rest of the environment.

---

# 30. Environment matrix

Maintain a small environment matrix.

| Configuration | Development | Preview | Production |
|---|---|---|---|
| API | Dev | Staging | Production |
| Database | Dev | Staging | Production |
| Auth | Dev | Staging | Production |
| Analytics | Test | Test | Production |
| Crash reporting | Dev | Preview | Production |
| Payments | Sandbox | Sandbox | Production |
| Push | Test | Test | Production |
| App identity | Optional separate | Optional separate | Fixed |
| Store distribution | No | Internal | Public |
| Debug tools | Yes | Limited | No |

This table should describe the actual project.

Do not copy the example blindly.

---

# 31. Environment matrix for Expo/EAS

A typical setup:

| Build profile | EAS environment | Distribution | Purpose |
|---|---|---|---|
| `development` | `development` | Internal | Developer workflow |
| `preview` | `preview` | Internal | Shared testing |
| `production` | `production` | Store | Public release |

Example:

```json
{
  "build": {
    "development": {
      "developmentClient": true,
      "distribution": "internal",
      "environment": "development"
    },
    "preview": {
      "distribution": "internal",
      "environment": "preview"
    },
    "production": {
      "environment": "production"
    }
  }
}
```

Expo's current EAS Build documentation uses `development`, `preview`, and `production` as common build profile patterns and supports explicit `environment` mapping.

---

# 32. Environment naming

Keep names boring and consistent.

Recommended:

```text
development
preview
production
```

Avoid:

```text
dev2
qa-final
staging-new
prod-v2
testing-real
temporary
```

If an environment needs a special name, document why.

Names should describe purpose, not history.

---

# 33. When to add staging

You do not always need staging.

Add it when there is a real need such as:

- multiple developers need a shared test backend
- releases need production-like validation
- backend changes need integrated testing
- QA needs a stable target
- production data must remain isolated
- deployment needs a pre-production gate

If the project is small:

```text
Development
→ Production
```

may be enough.

Do not create staging because every tutorial says you should.

---

# 34. When to add more environments

Additional environments may be justified for:

- enterprise customer testing
- regulatory testing
- regional deployments
- long-running QA
- parallel release trains
- large integration testing
- dedicated partner environments

But every environment adds:

```text
Configuration
+
Credentials
+
Infrastructure
+
Monitoring
+
Debugging
+
Maintenance
```

Use the smallest environment model that solves the workflow.

---

# 35. Environment access control

Production should have the strongest access controls.

A practical model:

```text
Development
→ most developers

Preview
→ developers + QA

Production
→ limited release operators
```

Production credentials should not be available to every developer simply because the app is mobile.

Use least privilege for:

- developers
- CI
- release automation
- build services
- backend services
- AI agents

---

# 36. CI/CD environment boundaries

CI should make environment selection explicit.

A safe pattern:

```text
Pull Request
→ tests
→ no production credentials

Main
→ build/test
→ preview where appropriate

Release tag/action
→ production build

Approval
→ production submission
```

Avoid workflows where:

```text
push to any branch
→ production build
→ production submission
```

unless the project intentionally accepts that risk.

---

# 37. Production credentials

Production credentials should be available only where required.

For example:

```text
Production build
→ production signing access

Production submission
→ store submission credentials

Application runtime
→ public client configuration only

Backend
→ privileged server credentials
```

Do not expose production credentials to the application runtime just because the build needs them.

Build-time access and runtime access are different permissions.

---

# 38. Secret storage

Never store production secrets in:

```text
Source code
Git history
Mobile bundle
Public app config
Logs
README
Screenshots
Issues
Pull requests
AI prompts
```

Use:

```text
EAS environment variables
CI secret storage
Managed secret systems
```

depending on the project.

For EAS, secret environment variables are not readable outside EAS servers and are intended for values needed by build/workflow jobs.

Remember:

> A secret is not protected if the build embeds it into the application.

---

# 39. Configuration drift

Environment drift happens when the documented environment differs from the real one.

Example:

```text
Documentation:
Production → production API

Actual:
Production → staging API
```

Or:

```text
EAS:
production environment

CI:
staging environment
```

Prevent this by checking:

```text
Documentation
        ↓
Build profile
        ↓
Environment variables
        ↓
Resolved config
        ↓
Artifact
        ↓
Backend
```

The final artifact and runtime behavior are the most important evidence.

---

# 40. Environment verification

Before a production build:

### Identity

- [ ] Correct Bundle ID.
- [ ] Correct Application ID.
- [ ] Correct store application.

### Backend

- [ ] Production API.
- [ ] Production backend.
- [ ] Correct database boundary.

### Services

- [ ] Production authentication.
- [ ] Production analytics.
- [ ] Production crash reporting.
- [ ] Production push.
- [ ] Production payments where applicable.

### Build

- [ ] Production build profile.
- [ ] Production environment.
- [ ] Correct signing.
- [ ] Correct version.

### Security

- [ ] No development credentials.
- [ ] No staging credentials.
- [ ] No privileged secrets in client code.

---

# 41. Verify the resolved environment

Do not stop at:

```text
eas.json looks correct
```

Verify what the build actually receives.

For EAS, build jobs expose information such as the build profile, build environment, platform, project ID, and Git commit to the job.

Useful release evidence:

```text
Environment:
production

Build profile:
production

Platform:
ios

Commit:
<sha>

Version:
1.4.0

Build:
42
```

Store this with the release record when practical.

---

# 42. Verify the actual application

After building:

```text
Install production build
        ↓
Open app
        ↓
Inspect environment-sensitive behavior
        ↓
Run critical workflow
        ↓
Verify backend
        ↓
Verify analytics/crash reporting
```

Do not rely only on source configuration.

A release is what the artifact does, not what the configuration file appears to say.

---

# 43. Environment smoke test

For every environment, test a small set of environment-sensitive behaviors.

### Development

- [ ] Development API works.
- [ ] Development authentication works.
- [ ] Debug tools work.
- [ ] Test data is isolated.

### Preview

- [ ] Preview API works.
- [ ] Shared authentication works.
- [ ] Real-device install works.
- [ ] Production-like native behavior works.
- [ ] Test data is isolated.

### Production

- [ ] Production API works.
- [ ] Production authentication works.
- [ ] Production analytics works.
- [ ] Crash reporting works.
- [ ] Push works where applicable.
- [ ] Critical user journey works.
- [ ] No development UI/configuration appears.

---

# 44. Common environment failures

## Production points to staging

### Symptoms

```text
Production build
→ staging data
```

### Check

```text
Build profile
→ EAS environment
→ environment variables
→ app config
→ actual bundle
```

### Fix

Correct the environment mapping and rebuild.

Do not assume changing a server variable fixes a URL already bundled into the application.

---

## Preview points to production

### Symptoms

Testers can affect real data.

### Check

- API URL
- authentication project
- database
- payment configuration
- push configuration

### Fix

Restore isolation.

Treat this as a release-blocking issue when testers can perform destructive or real-user operations.

---

## Local development uses production

### Symptoms

A developer accidentally changes real data.

### Check

- `.env`
- local app config
- backend URL
- authentication
- database configuration

### Fix

Make development defaults safe.

Do not rely on developers remembering which URL is safe.

---

## CI builds the wrong environment

### Symptoms

```text
Local build:
preview

CI build:
production
```

or the reverse.

### Check

- `eas.json`
- CI workflow
- EAS environment
- build command
- secrets
- environment variables

### Fix

Make the environment selection explicit in the release workflow.

---

## OTA update uses the wrong environment

### Symptoms

A production application receives an update configured for another environment.

### Check

- EAS Update environment
- update command
- runtime/update configuration
- channel/branch strategy where used

### Fix

Publish using the intended environment and verify compatibility before rollout.

Expo documents the `--environment` flag for EAS Update and recommends using the same environment mapping as the build workflow.

---

# 45. Environment migration

Changing an environment setup can affect:

- API endpoints
- databases
- authentication
- application identifiers
- push
- deep links
- analytics
- payments
- CI
- signing
- OTA updates

Before changing an environment:

```text
Map current state
        ↓
Define target state
        ↓
Identify dependencies
        ↓
Update configuration
        ↓
Test preview
        ↓
Build
        ↓
Verify
        ↓
Roll out
```

Do not rename or merge environments without understanding the downstream configuration.

---

# 46. Environment recovery

If the wrong environment is released:

### Wrong backend

```text
Stop rollout
        ↓
Assess impact
        ↓
Fix configuration
        ↓
Build/release if required
        ↓
Verify
        ↓
Monitor
```

### Secret exposure

```text
Revoke
   ↓
Rotate
   ↓
Investigate
   ↓
Rebuild if secret reached client
   ↓
Verify
```

### Production data touched by test build

```text
Contain access
   ↓
Identify affected operations
   ↓
Check data integrity
   ↓
Restore/fix where necessary
   ↓
Correct environment isolation
```

Environment mistakes can become security and data-integrity incidents.

---

# 47. AI-assisted environment audit

AI can help inspect a repository for environment mistakes.

Useful tasks:

- identify environment variables
- map build profiles
- find development/staging URLs
- compare Expo config
- inspect native configuration
- identify environment-specific application IDs
- detect production credentials in client configuration
- inspect CI environment selection
- identify inconsistent EAS Update/build environments
- generate an environment matrix

A safe workflow:

```text
Repository
    ↓
AI scans configuration
    ↓
Candidate mismatches
    ↓
Developer verifies
    ↓
Build
    ↓
Inspect artifact
    ↓
Real-device test
    ↓
Human approval
```

Do not allow an AI agent to silently switch production configuration.

AI can identify a likely mismatch.

A developer must verify it.

---

# 48. AI environment audit prompt

```text
Audit this mobile repository for release-environment consistency.

Primary framework:
Expo / React Native

Platforms:
iOS
Android

Identify:

1. Development environment
2. Preview/staging environment
3. Production environment
4. API endpoints
5. Environment variables
6. EAS build profiles
7. EAS environment mappings
8. Dynamic Expo configuration
9. iOS Bundle IDs
10. Android Application IDs
11. Authentication configuration
12. Push configuration
13. Analytics configuration
14. Crash-reporting configuration
15. Payment configuration
16. Deep-link configuration
17. CI/CD environment selection
18. EAS Update environment selection
19. Client-bundled values
20. Potential secrets

Return:

ENVIRONMENT MAP
CONFIGURATION SOURCES
MISMATCHES
SECURITY RISKS
DATA-ISOLATION RISKS
PRODUCTION RISKS
FILES TO VERIFY
RECOMMENDED FIXES

Do not modify files.

Do not invent missing configuration.

If a value cannot be verified from the repository, mark it as "NEEDS VERIFICATION".
```

---

# 49. Environment release gate

Before production:

- [ ] Environment model is documented.
- [ ] Production environment is explicit.
- [ ] Build profile maps to the correct environment.
- [ ] API endpoint is production.
- [ ] Authentication is production.
- [ ] Analytics is production.
- [ ] Crash reporting is production.
- [ ] Push configuration is correct.
- [ ] Payment configuration is correct where applicable.
- [ ] Deep links are correct.
- [ ] Application identifiers are correct.
- [ ] CI uses the intended environment.
- [ ] EAS Update uses the intended environment where applicable.
- [ ] No development/staging secrets are included.
- [ ] No privileged secrets are bundled into the app.
- [ ] Production data is isolated from test environments.
- [ ] Actual artifact has been verified.
- [ ] Real-device smoke test passes.
- [ ] Human approval is complete.

---

# 50. Recommended operating model

For most Expo/React Native projects:

```text
Development
    ↓
Preview
    ↓
Production
```

Use:

```text
app.config.ts
```

for environment-aware native configuration when needed.

Use:

```text
eas.json
```

for build profiles.

Use:

```text
EAS environment variables
```

for cloud build/update configuration when using EAS.

Use:

```text
.gitignored .env files
```

for local development where appropriate.

Use:

```text
CI secret storage
```

for CI-specific credentials.

Keep production access restricted.

This is enough for most teams.

---

# 51. What not to do

### Do not create five environments for a small app

More environments create more failure points.

### Do not use production data for normal testing

Keep test and production data isolated.

### Do not put secrets in `EXPO_PUBLIC_*`

Those values are client-visible.

### Do not assume `.env` means secret

Git can expose it.

### Do not rely on implicit environment selection for critical releases

Make production mapping explicit.

### Do not let CI choose an environment invisibly

The release should be traceable.

### Do not give every developer production credentials

Use least privilege.

### Do not let AI change environment configuration without review

AI should assist, not silently control production.

### Do not build a custom environment platform too early

Use the framework and CI tooling you already have until real complexity requires more.

---

# 52. Definition of done

Release environments are ready when:

```text
Environment model is simple
        +
Each environment has a clear purpose
        +
Build profiles map correctly
        +
Configuration is explicit
        +
Secrets are protected
        +
Data is isolated
        +
Production identity is stable
        +
CI is predictable
        +
OTA/update environment is controlled
        +
Actual artifact is verified
        ↓
   ENVIRONMENT READY
```

The key rule is:

> **The right build must use the right environment, and you should be able to prove it.**

---

# Related documentation

### Foundations

- `foundations/mobile-release-lifecycle.md`
- `foundations/project-configuration.md`
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
- `testing/network.md`
- `testing/offline.md`

### Release strategy

- `release-strategy/beta-testing.md`
- `release-strategy/staged-rollouts.md`
- `release-strategy/feature-flags.md`

### Post-release

- `post-release/monitoring.md`
- `post-release/incident-response.md`
- `post-release/rollback.md`
- `post-release/hotfix.md`

### AI

- `ai/workflows/release-audit.md`
- `ai/workflows/debugging.md`
- `ai/orchestration/agent-workflows.md`
- `ai/security/secret-protection.md`
- `ai/security/destructive-actions.md`

---

# Official sources

Use official documentation for current environment and tooling behavior.

### Expo

- Environment variables overview: https://docs.expo.dev/eas/environment-variables/
- Create and manage EAS environment variables: https://docs.expo.dev/eas/environment-variables/manage/
- Use EAS environment variables: https://docs.expo.dev/eas/environment-variables/usage/
- EAS environment variable FAQ: https://docs.expo.dev/eas/environment-variables/faq/
- Configure EAS Build with `eas.json`: https://docs.expo.dev/build/eas-json/
- EAS `eas.json` reference: https://docs.expo.dev/eas/json/
- Expo environment variables: https://docs.expo.dev/guides/environment-variables/
- Environment variables without EAS: https://docs.expo.dev/eas/environment-variables/without-eas/
- EAS CLI reference: https://docs.expo.dev/eas/cli/

### React Native

- React Native documentation: https://reactnative.dev/docs/getting-started

### GitHub

- GitHub Actions: https://docs.github.com/actions
- GitHub Actions secrets: https://docs.github.com/actions/security-guides/using-secrets-in-github-actions

### Apple

- Apple Developer: https://developer.apple.com/
- App Store Connect Help: https://developer.apple.com/help/app-store-connect/

### Android

- Android Developers: https://developer.android.com/
- Google Play Console Help: https://support.google.com/googleplay/android-developer/

**Last verified:** August 11, 2026

Environment tooling, EAS behavior, platform requirements, and CI/CD capabilities can change. Re-check the official source before changing production environment configuration or release workflows.
