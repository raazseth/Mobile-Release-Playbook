# Expo EAS

Expo Application Services (EAS) is the cloud service layer around Expo and React Native projects.

For a mobile release workflow, EAS can provide:

```text
Build
+
Submit
+
Update
+
Workflows
+
Credentials
+
Environment management
```

A useful mental model is:

```text
Source code
    ↓
Expo configuration
    ↓
EAS configuration
    ↓
Build
    ↓
Artifact
    ↓
Test
    ↓
Submit
    ↓
Store
```

And for JavaScript-only compatible changes:

```text
Source code
    ↓
EAS Update
    ↓
Compatible installed binaries
```

EAS is not a replacement for understanding the mobile release process.

It is the execution layer.

You still need to understand:

- application identity
- environments
- signing
- versioning
- testing
- store requirements
- release approval
- rollback and recovery

The repository therefore treats EAS as one part of the release system rather than the release system itself.

---

# 1. What EAS provides

The current EAS CLI supports workflows around:

```text
eas build
eas submit
eas update
eas workflow
eas credentials
eas env
```

EAS CLI can build, update, submit, and run workflows for Expo and React Native projects. The current CLI also exposes commands for inspecting builds, managing versions, environment variables, metadata, credentials, and EAS Workflows.

Official CLI reference:

https://docs.expo.dev/eas/cli/

EAS Workflows adds cloud CI/CD capabilities for builds, updates, submissions, tests, and related automation.

Official EAS Workflows documentation:

https://docs.expo.dev/eas/workflows/introduction/

---

# 2. What EAS does not replace

EAS does not remove the need for:

```text
Apple Developer account
Google Play Console
App Store Connect
Android signing
iOS signing
Store metadata
Privacy declarations
Testing
Release approval
Production monitoring
```

The boundary is:

```text
EAS
→ executes release operations

Apple / Google
→ control store distribution and platform rules

Your application
→ must still be correct
```

A successful EAS build does not mean:

```text
the app is ready for production.
```

It only proves that the configured build process produced an artifact successfully.

---

# 3. EAS services in simple terms

| EAS capability | What it does | Typical use |
|---|---|---|
| EAS Build | Builds Android/iOS binaries | APK/AAB/IPA builds |
| EAS Submit | Uploads binaries to stores | Play Store / App Store submission |
| EAS Update | Publishes compatible JavaScript/assets updates | OTA fixes/features |
| EAS Workflows | Automates release workflows | CI/CD |
| EAS Credentials | Manages signing credentials | Android/iOS signing |
| EAS Environment Variables | Manages build/runtime configuration inputs | Development/preview/production |
| EAS Metadata | Manages supported store metadata workflows | Store listing automation |

Use only the services that solve a real problem.

Do not adopt every EAS capability just because it exists.

---

# 4. Recommended architecture

For a typical Expo application:

```text
                    Git repository
                         │
                         ▼
                  Expo configuration
                         │
                         ▼
                    eas.json
                         │
             ┌───────────┼───────────┐
             ▼           ▼           ▼
        Development    Preview    Production
             │           │           │
             ▼           ▼           ▼
          EAS Build    EAS Build   EAS Build
             │           │           │
             ▼           ▼           ▼
        Internal      Internal     Store artifact
        testing       testing          │
                                      ▼
                               Human approval
                                      │
                              ┌───────┴───────┐
                              ▼               ▼
                         EAS Submit      Manual store
                              │
                              ▼
                       App Store / Play
```

For JavaScript-only updates:

```text
Production binary
       │
       │ compatible runtime
       ▼
    EAS Update
       │
       ▼
Users receive compatible update
```

The important boundary is:

```text
Build
→ creates native binary

Update
→ changes compatible JavaScript/assets
```

---

# 5. The three main EAS operations

Most Expo release workflows can be understood through three commands:

```bash
eas build
```

```bash
eas submit
```

```bash
eas update
```

They solve different problems.

## Build

Creates an application binary.

```text
Source
→ native build
→ APK/AAB/IPA
```

## Submit

Uploads an existing application binary to a store workflow.

```text
Artifact
→ App Store Connect / Google Play
```

## Update

Publishes a JavaScript/assets update for compatible installed binaries.

```text
JS/assets
→ EAS Update
→ compatible app binaries
```

Do not use `eas update` as a substitute for a native rebuild.

---

# 6. Install EAS CLI

Expo currently documents these installation options.

Global installation:

```bash
npm install --global eas-cli
```

Or run it through the package manager:

```bash
npx eas-cli@latest
```

Verify:

```bash
eas --version
```

The exact CLI version can change. Pin or control the version in CI when reproducibility requires it.

Official documentation:

https://docs.expo.dev/eas/cli/

---

# 7. Authenticate with Expo

Interactive login:

```bash
eas login
```

Check the current account:

```bash
eas whoami
```

Log out:

```bash
eas logout
```

For CI/CD, prefer an appropriate Expo access token through protected CI secrets rather than interactive authentication.

Never commit:

```text
EXPO_TOKEN
```

or any other authentication token.

---

# 8. Configure EAS for a project

From the project root:

```bash
eas build:configure
```

This creates or updates:

```text
eas.json
```

The file lives next to:

```text
package.json
```

EAS Build configuration belongs under the `build` key.

Official reference:

https://docs.expo.dev/build/eas-json/

---

# 9. The role of `eas.json`

`eas.json` defines how EAS should perform different types of builds and submissions.

A simple setup:

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

This creates three named build profiles:

```text
development
preview
production
```

A build profile is a named set of configuration values describing a particular build type.

The names are conventions, not magic.

You can define custom profiles when the project has a real reason to do so.

---

# 10. Recommended profile model

For most projects:

```text
development
→ active development

preview
→ internal/release-like testing

production
→ store release
```

Example:

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

This matches the practical release environment model used elsewhere in this playbook:

```text
Development
→ Preview
→ Production
```

See:

```text
foundations/release-environments.md
```

---

# 11. Build profiles are not environments

Do not confuse:

```text
build profile
```

with:

```text
environment
```

A build profile controls how the artifact is produced.

An environment controls which configuration/services the application uses.

For example:

```text
Build profile:
internal-ios

Environment:
preview
```

This can be valid.

The relationship should be explicit.

---

# 12. Build a development client

A development build normally uses:

```json
{
  "developmentClient": true,
  "distribution": "internal"
}
```

and the project should have:

```bash
npx expo install expo-dev-client
```

Build:

```bash
eas build --profile development --platform android
```

or:

```bash
eas build --profile development --platform ios
```

or:

```bash
eas build --profile development --platform all
```

See:

```text
frameworks/expo/development-builds.md
```

for the development-build workflow.

---

# 13. Build a preview artifact

A preview build is intended for internal validation.

Example:

```bash
eas build --profile preview --platform android
```

```bash
eas build --profile preview --platform ios
```

or:

```bash
eas build --profile preview --platform all
```

Typical use:

```text
Feature complete
→ preview build
→ real-device testing
→ fix
→ production build
```

A preview build should be close enough to production to catch release-specific problems.

---

# 14. Build a production artifact

Production build:

```bash
eas build --profile production --platform android
```

```bash
eas build --profile production --platform ios
```

or:

```bash
eas build --profile production --platform all
```

Do not treat this command as the final release step.

The full flow is:

```text
Validate
→ build
→ inspect
→ install/test
→ approve
→ submit
```

---

# 15. Select a platform explicitly

Use:

```bash
--platform android
```

for Android.

Use:

```bash
--platform ios
```

for iOS.

Use:

```bash
--platform all
```

for both.

For debugging, build one platform at a time when only one platform is failing.

This shortens the feedback loop and makes the failure easier to isolate.

---

# 16. Wait for build completion

EAS supports waiting for the build:

```bash
eas build --profile production --platform android --wait
```

For automation:

```bash
eas build --profile production --platform android --non-interactive --wait
```

Use non-interactive mode in CI when the command and credentials are already configured.

Do not make production CI depend on an interactive terminal prompt.

---

# 17. Build messages

EAS supports a build message:

```bash
eas build \
  --profile production \
  --platform android \
  --message "Release 1.4.0"
```

Use messages that make build history easier to understand.

Good:

```text
Release 1.4.0
Hotfix payment crash
Release candidate 2
```

Avoid:

```text
test
build
new
asdf
```

A build record should help someone understand why it exists.

---

# 18. Clear build cache

EAS supports:

```bash
eas build --clear-cache
```

Use this deliberately.

Do not make:

```bash
--clear-cache
```

the first response to every build failure.

Correct order:

```text
Read logs
→ identify likely cause
→ determine whether cache is relevant
→ clear cache if justified
→ rebuild
```

A cache cannot fix:

```text
wrong dependency
wrong configuration
wrong signing
wrong environment
```

---

# 19. Local EAS builds

EAS can run builds locally:

```bash
eas build --local
```

The current CLI marks local builds as experimental.

Use local builds when they provide a useful debugging loop or when the project specifically needs local native compilation.

Do not assume local and cloud builds are identical.

Differences can include:

```text
toolchain
machine state
environment variables
credentials
source archive
build image
```

See:

```text
frameworks/expo/common-failures.md
```

---

# 20. Build inspection

When a build succeeds but the generated native project looks wrong, inspect the build stages.

Current EAS CLI supports:

```bash
eas build:inspect
```

For example:

```bash
eas build:inspect \
  --platform android \
  --stage pre-build \
  --output ./build-inspection \
  --profile production
```

Supported inspection stages include:

```text
archive
pre-build
post-build
```

This is useful for diagnosing:

```text
config plugin changes
native project generation
archive contents
native build state
```

Official CLI reference:

https://docs.expo.dev/eas/cli/

---

# 21. Inspect a build

View a build:

```bash
eas build:view
```

Or by ID:

```bash
eas build:view <BUILD_ID>
```

Use build IDs in release records.

A production release should be traceable to:

```text
Git commit
+
EAS build ID
+
platform
+
profile
+
environment
+
version
+
build number
```

---

# 22. Download a build artifact

EAS supports:

```bash
eas build:download
```

You can target a specific build:

```bash
eas build:download --build-id <BUILD_ID>
```

You can also download available build artifacts/logs when needed.

Use this for:

```text
manual inspection
real-device testing
artifact archiving
debugging
release evidence
```

Do not submit an artifact just because the build service reports success.

Inspect the artifact when the release is important.

---

# 23. Artifact verification

Before submission, verify the actual artifact.

### Android

Check:

```text
Application ID
Version name
Version code
Signing
Target SDK
Environment
Permissions
```

### iOS

Check:

```text
Bundle ID
Version
Build number
Signing
Entitlements
Environment
```

The key rule is:

> **The artifact is the release candidate. The configuration files are only its inputs.**

See:

```text
frameworks/expo/build.md
```

and:

```text
foundations/project-configuration.md
```

---

# 24. EAS credentials

EAS can manage signing credentials remotely or use local credentials.

The recommended default for many Expo projects is:

```text
EAS-managed credentials
```

unless there is a concrete reason to use local credentials.

Inspect credentials:

```bash
eas credentials
```

Platform-specific:

```bash
eas credentials --platform android
```

```bash
eas credentials --platform ios
```

See:

```text
frameworks/expo/credentials.md
```

and:

```text
signing/
```

Do not generate new production signing credentials casually.

---

# 25. Credential source

A build profile can use remote or local credentials.

Conceptually:

```json
{
  "build": {
    "production": {
      "credentialsSource": "remote"
    }
  }
}
```

or:

```json
{
  "build": {
    "production-local": {
      "credentialsSource": "local"
    }
  }
}
```

Remote credentials are the simpler default for most Expo projects.

Local credentials add operational work around:

```text
storage
backup
CI injection
file paths
rotation
recovery
```

---

# 26. EAS environment variables

EAS supports environment-specific variables.

The default environment model is:

```text
development
preview
production
```

Use:

```bash
eas env:list
```

or:

```bash
eas env:list --environment production
```

Do not print sensitive values.

EAS CLI also provides commands for pulling and pushing environment variables:

```bash
eas env:pull production
```

```bash
eas env:push production
```

Use these carefully because environment variables can contain sensitive configuration.

Official documentation:

https://docs.expo.dev/eas/environment-variables/

---

# 27. Environment boundary

Keep this distinction clear:

```text
GitHub environment
≠
EAS environment
```

A secret available to GitHub Actions is not automatically available to the remote EAS build.

Likewise, an EAS environment variable is not automatically a GitHub Actions secret.

Make the flow explicit:

```text
GitHub
→ CI credentials

EAS
→ build environment

Application
→ public runtime configuration only
```

---

# 28. Public vs secret variables

Anything bundled into client-side application code should be treated as public.

Do not embed:

```text
database passwords
private API keys
service-account credentials
Apple private keys
Google private keys
```

Use:

```text
App
→ authenticated backend
→ privileged service
```

instead.

EAS environment-variable management can help keep build-time secrets out of source control, but it cannot make a secret safe after it has been embedded in a client binary.

See:

```text
signing/security/secret-storage.md
```

---

# 29. EAS and versioning

EAS supports version-management workflows.

Current CLI commands include:

```bash
eas build:version:get
```

```bash
eas build:version:set
```

```bash
eas build:version:sync
```

EAS can also automatically increment platform build versions through build configuration.

Example:

```json
{
  "build": {
    "production": {
      "autoIncrement": true
    }
  }
}
```

Do not enable automatic version management without deciding which system owns version state.

See:

```text
foundations/versioning.md
```

---

# 30. Version ownership

Choose one clear source of truth.

Possible models include:

```text
Repository configuration
```

or:

```text
EAS-managed version state
```

The dangerous model is:

```text
developer edits version
+
CI edits version
+
EAS edits version
+
store state differs
```

That creates drift.

Before enabling EAS version automation, document:

```text
Who changes marketing version?
Who changes build number?
Where is current version stored?
How is CI expected to behave?
```

---

# 31. EAS Submit

EAS Submit uploads a built binary to the store platform.

Examples:

```bash
eas submit --platform android
```

```bash
eas submit --platform ios
```

You can submit the latest build:

```bash
eas submit --platform android --latest
```

or target a specific build:

```bash
eas submit --platform android --id <BUILD_ID>
```

The current EAS CLI also supports submitting a local artifact using `--path`.

Official documentation:

https://docs.expo.dev/submit/introduction/

---

# 32. Build vs submit

Keep the two operations separate:

```text
eas build
→ creates artifact

eas submit
→ uploads artifact
```

This separation is valuable because you can:

```text
Build
→ inspect
→ test
→ approve
→ submit
```

instead of:

```text
Build
→ immediately submit
```

For first releases and high-risk releases, keep the approval boundary explicit.

---

# 33. Auto-submit

EAS can connect build and submission.

For example:

```bash
eas build \
  --profile production \
  --platform ios \
  --auto-submit
```

Or specify a submission profile:

```bash
eas build \
  --profile production \
  --platform ios \
  --auto-submit-with-profile production
```

The current EAS CLI supports these options.

Automatic submission should be introduced only when:

```text
artifact verification is reliable
+
store metadata is ready
+
environment is correct
+
credentials are protected
+
human approval is intentional
```

Do not enable automatic production submission simply because it saves one command.

---

# 34. Submission profiles

`eas.json` can contain submit configuration.

Conceptually:

```json
{
  "submit": {
    "production": {
      "android": {},
      "ios": {}
    }
  }
}
```

Keep submission configuration separate from build configuration.

This makes the intent clear:

```text
build.production
→ how to create the artifact

submit.production
→ how to deliver it to the stores
```

---

# 35. Store credentials

Submission credentials are different from signing credentials.

### Android submission

Typically uses:

```text
Google Play service account
```

### iOS submission

Can use:

```text
App Store Connect credentials / API key
```

Do not confuse:

```text
Android keystore
```

with:

```text
Google Play submission credentials
```

or:

```text
iOS distribution certificate
```

with:

```text
App Store Connect API key
```

See:

```text
frameworks/expo/credentials.md
```

---

# 36. EAS Metadata

EAS CLI currently includes metadata commands:

```bash
eas metadata:pull
```

```bash
eas metadata:push
```

```bash
eas metadata:lint
```

These can support a source-controlled store metadata workflow.

Use metadata automation only if the team actually benefits from it.

Store metadata can be sensitive to:

```text
platform rules
localization
review requirements
screenshots
legal information
privacy information
```

Do not blindly push metadata from CI.

Validate first.

---

# 37. EAS Update

EAS Update publishes JavaScript and asset updates to installed application binaries.

Basic command:

```bash
eas update
```

A message:

```bash
eas update --message "Fix checkout crash"
```

A channel:

```bash
eas update --channel production --message "Fix checkout crash"
```

The exact branch/channel model must match the project's Expo Updates configuration.

Official documentation:

https://docs.expo.dev/eas-update/introduction/

---

# 38. Build vs Update decision

Use this rule:

```text
Does the change require native code/configuration?
        │
        ├── Yes → EAS Build
        │
        └── No
             ↓
       Is the update compatible
       with the installed runtime?
             │
             ├── Yes → EAS Update may be appropriate
             │
             └── No → EAS Build
```

Examples that generally require a new native build:

```text
Native dependency
Config plugin
Native permission
iOS entitlement
Android manifest change
Bundle ID
Application ID
Native SDK
Native code
```

Examples that may be suitable for EAS Update:

```text
JavaScript logic
React UI
Styles
Compatible assets
Some configuration values that are intentionally runtime/update-safe
```

Always verify the project's runtime compatibility before publishing an update.

---

# 39. Runtime compatibility

The installed native binary defines what JavaScript can safely use.

Think:

```text
Native runtime
        +
Compatible update
```

not:

```text
Any binary
        +
Any JavaScript
```

A JavaScript update that calls native functionality unavailable in the installed binary can fail.

Use runtime-version boundaries to keep incompatible updates away from incompatible binaries.

See:

```text
release-strategy/
```

and the Expo Updates documentation.

---

# 40. EAS Update channels and branches

EAS Update uses concepts including:

```text
branches
channels
update groups
runtime versions
```

Keep the model simple.

A practical production model can be:

```text
development channel
preview channel
production channel
```

with the actual branch/channel mapping documented in the repository.

Do not create dozens of channels without a real release workflow that needs them.

---

# 41. Rollouts for updates

EAS Update supports rollout controls for update groups.

The current CLI includes:

```bash
eas update:edit
```

with rollout percentage support.

Use gradual rollout when:

```text
change is high impact
+
you have monitoring
+
rollback/update controls are understood
```

A rollout without monitoring is not useful.

---

# 42. Rollback strategy for updates

An OTA rollback is different from a store rollback.

Store rollback:

```text
release artifact
→ store rollout / hotfix
```

Update rollback:

```text
EAS Update
→ stop/redirect affected update path
→ publish or restore compatible known-good update
```

The exact mechanism depends on the project's update strategy.

Before using EAS Update in production, document:

```text
How to identify update
How to stop rollout
How to publish a replacement
How to verify recovery
```

---

# 43. EAS Workflows

EAS Workflows is EAS's cloud CI/CD system.

It can automate:

```text
Builds
Updates
Submissions
Tests
Other project workflows
```

Workflows can be triggered through GitHub integration, schedules, or manually through EAS CLI.

Official documentation:

https://docs.expo.dev/eas/workflows/introduction/

---

# 44. When to use EAS Workflows

Use EAS Workflows when:

```text
Repeated manual release work is real
+
the workflow is understood
+
automation removes meaningful effort
```

Good candidates:

```text
Development builds
Preview builds
Release candidate builds
E2E tests
Preview updates
Production build preparation
```

Do not introduce EAS Workflows just because:

```text
"CI/CD should be automated."
```

Manual release steps are often useful until the process is stable.

---

# 45. EAS Workflows vs GitHub Actions

Both can automate release processes.

### EAS Workflows

Good for:

```text
Expo/EAS-native workflows
Mobile build/update/submit jobs
Expo-managed CI
```

### GitHub Actions

Good for:

```text
Repository-wide CI
Lint
Typecheck
Tests
Custom scripts
Non-Expo automation
GitHub-specific controls
```

A project can use both:

```text
GitHub Actions
→ repository validation

EAS Workflows
→ mobile release operations
```

Do not build duplicate pipelines for the same job.

Choose one owner for each automation step.

---

# 46. EAS Workflow example

A simple conceptual workflow:

```text
GitHub push
    ↓
Install dependencies
    ↓
Run tests
    ↓
Build preview
    ↓
Test artifact
```

A production workflow:

```text
Release trigger
    ↓
Validate
    ↓
Build
    ↓
Artifact verification
    ↓
Human approval
    ↓
Submit
    ↓
Monitor
```

Production approval should remain explicit unless the organization intentionally accepts fully automated submission.

---

# 47. EAS Workflows and GitHub

When the EAS project is linked to GitHub, workflows can respond to repository events.

Possible triggers include:

```text
push
pull request
labels
schedule
manual execution
```

The exact trigger and workflow syntax should be taken from current EAS Workflows documentation.

Do not copy old workflow examples without checking the current schema.

Official documentation:

https://docs.expo.dev/eas/workflows/

---

# 48. Create an EAS Workflow

Current EAS CLI supports:

```bash
eas workflow:create
```

Templates can be used for:

```text
build
update
deploy
custom
```

For example:

```bash
eas workflow:create --template build
```

Then inspect the generated workflow before committing it.

Do not treat generated workflow files as trusted production configuration without review.

---

# 49. Run a workflow manually

EAS CLI supports:

```bash
eas workflow:run <workflow-file>
```

This is useful for:

```text
testing workflow logic
manual release preparation
debugging
controlled production runs
```

Use the exact workflow file configured in:

```text
.eas/workflows/
```

where applicable.

---

# 50. Workflow logs

Use:

```bash
eas workflow:logs
```

to inspect workflow runs.

A failed workflow should be debugged like any other production system:

```text
Identify run
→ identify failed job
→ identify failed step
→ inspect first meaningful error
→ reproduce
→ fix
→ rerun
```

Do not hide failures behind retries.

Retries are useful only when the failure is actually transient.

---

# 51. Retry behavior

Be careful with automatic retries.

Safe-ish candidates:

```text
temporary network failure
temporary service unavailability
```

Dangerous candidates:

```text
invalid credentials
wrong configuration
failed migration
wrong store target
bad artifact
authorization failure
```

A retry can turn one mistake into:

```text
multiple submissions
multiple builds
multiple notifications
```

Production automation should be idempotent where possible.

---

# 52. Idempotency

A release operation should be safe to retry where practical.

Examples:

```text
Build
→ creates identifiable build

Metadata validation
→ repeatable

Tests
→ repeatable

Submission
→ should target a specific artifact

Update publication
→ should have a traceable update group
```

Avoid scripts that:

```text
guess latest artifact
+
blindly submit it
```

when a specific build ID can be used.

Prefer:

```text
Release
→ build ID
→ verify
→ submit build ID
```

---

# 53. Production approval boundary

A safe release flow is:

```text
Automation
→ prepare

Human
→ verify

Human
→ approve

Automation
→ execute approved action
```

Keep human approval for:

```text
Production store submission
Signing credential changes
Store account changes
Destructive operations
Irreversible configuration
High-impact production changes
```

AI or automation should not silently bypass these boundaries.

---

# 54. AI + EAS

AI is useful around EAS when it is used for:

```text
Inspection
Analysis
Preparation
Debugging
Validation
Documentation
```

Good examples:

```text
Inspect eas.json
→ find inconsistent profiles

Inspect build logs
→ classify failure

Inspect dependencies
→ identify likely native rebuild requirement

Inspect release config
→ identify production risks

Generate release notes
→ human reviews

Prepare workflow
→ human reviews
```

AI should not automatically own:

```text
production credentials
store permissions
signing rotation
production submission
destructive EAS operations
```

---

# 55. AI release workflow

A practical workflow:

```text
Repository
    ↓
AI audit
    ↓
Find blockers
    ↓
Developer reviews
    ↓
AI prepares changes
    ↓
Tests
    ↓
EAS preview build
    ↓
Real-device verification
    ↓
Human approval
    ↓
Production build
    ↓
Artifact verification
    ↓
Human approval
    ↓
Submit
```

This is much safer than:

```text
AI
→ build
→ submit
→ hope
```

---

# 56. AI prompt for EAS audit

```text
Audit this Expo project for EAS release readiness.

Inspect:

- package.json
- app.json / app.config.*
- eas.json
- native directories if present
- EAS-related CI/workflow files
- environment configuration
- build profiles
- submit profiles
- update configuration
- versioning configuration

Return:

1. EAS configuration summary
2. Build profiles
3. Environment mapping
4. Signing/credential dependencies
5. Submission configuration
6. EAS Update configuration
7. CI/CD integration
8. Release blockers
9. Security risks
10. Missing documentation
11. Minimal recommended changes

Rules:

- Do not modify files.
- Do not request or print secrets.
- Separate confirmed facts from hypotheses.
- Use current official Expo documentation for volatile behavior.
- Do not recommend unrelated upgrades.
- Do not invent EAS configuration properties.
- Do not recommend automatic production submission without an approval boundary.
```

---

# 57. AI build-failure debugging

Use:

```text
EAS build ID
Platform
Profile
Expo SDK
Recent changes
Exact command
First meaningful error
Relevant logs
```

Prompt:

```text
Analyze this Expo EAS Build failure.

Project:
Expo / React Native

Platform:
<android / ios>

Profile:
<profile>

Build ID:
<id>

Expo SDK:
<version>

Recent change:
<change>

Logs:
<logs>

Return:

1. FIRST REAL ERROR
2. FAILURE CATEGORY
3. DIRECT CAUSE
4. EVIDENCE
5. ROOT-CAUSE HYPOTHESIS
6. ALTERNATIVE HYPOTHESES
7. MINIMAL FIX
8. VERIFICATION STEPS
9. RELEASE RISK
10. WHAT NOT TO CHANGE

Rules:

- Do not invent requirements.
- Do not recommend unrelated upgrades.
- Do not modify production configuration.
- Do not request secrets.
- If evidence is insufficient, say NEEDS MORE EVIDENCE.
```

---

# 58. EAS project configuration

An EAS-enabled project generally has:

```text
package.json
app.json / app.config.*
eas.json
```

and an EAS project association.

The project should have stable:

```text
iOS bundle identifier
Android application ID
Expo project identity
```

Do not casually change application identifiers after a store application exists.

See:

```text
foundations/identifiers.md
```

---

# 59. EAS project identity

The Expo project itself has an EAS project identity.

Keep:

```text
Expo project
+
repository
+
application identifiers
```

aligned.

If a project is moved, renamed, or split, verify:

```text
EAS project association
CI secrets
credentials
environment variables
build profiles
store applications
```

Do not assume changing the GitHub repository automatically changes the EAS project correctly.

---

# 60. EAS and native projects

Expo projects can use different native strategies.

You may have:

```text
Managed/CNG-style workflow
```

or:

```text
Committed ios/ and android/ projects
```

EAS can build both, but the source of truth differs.

Before modifying native files, determine:

```text
Who owns native project state?
```

If native projects are generated:

```text
app config
+
config plugins
→ native project
```

If native projects are manually maintained:

```text
ios/
android/
```

may be part of the committed source of truth.

See:

```text
frameworks/expo/app-config.md
```

---

# 61. EAS and config plugins

Config plugins can modify native projects during prebuild.

This means:

```text
Package
+
plugin
+
configuration
→ native changes
→ EAS build
```

If a native build suddenly changes after a dependency/configuration update:

```text
Inspect config plugins
```

before editing generated native files blindly.

Use:

```bash
eas build:inspect
```

when the generated state needs investigation.

---

# 62. EAS and environment configuration

A good model:

```text
Development
→ development EAS environment

Preview
→ preview EAS environment

Production
→ production EAS environment
```

Then map build profiles deliberately.

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

Use the current EAS schema and project requirements when configuring this.

Do not assume every project needs explicit `environment` values in every profile if its current EAS configuration already establishes the correct environment behavior.

---

# 63. Build environment reproducibility

A release build should be reproducible enough to answer:

```text
What source produced this?
What configuration was used?
Which profile?
Which environment?
Which Expo SDK?
Which dependencies?
Which build number?
Which credentials?
Which EAS build?
```

Keep:

```text
lockfile
+
build profile
+
environment
+
commit
```

traceable.

Do not make production builds from uncommitted source unless the workflow explicitly supports and records that state.

---

# 64. EAS and dependency upgrades

When upgrading Expo or React Native:

```text
Upgrade
→ validate
→ Expo Doctor
→ development build
→ preview build
→ production verification
```

Do not combine:

```text
Expo upgrade
+
React Native upgrade
+
10 native packages
+
release
```

into one opaque change.

If something fails, you lose the ability to identify the cause.

---

# 65. EAS and release candidates

A release candidate should have:

```text
Known commit
Known version
Known build number
Known environment
Known EAS build ID
Known artifact
Known test result
```

Example:

```text
Version:
1.5.0

Android:
build 52

iOS:
build 52

Commit:
abc123

Profile:
production

Environment:
production

EAS Build:
android <id>
ios <id>
```

Then:

```text
Test
→ approve
→ submit
```

---

# 66. EAS release evidence

For each important production release, record:

```text
Git commit
EAS build ID
Platform
Profile
Environment
Version
Build number
Artifact
Submission ID
Release date
```

This gives you a simple audit trail.

Do not store:

```text
passwords
private keys
tokens
service-account secrets
```

in the release record.

---

# 67. Common EAS failure: project not configured

Symptom:

```text
EAS project not configured
```

Check:

```bash
eas build:configure
```

Then inspect:

```text
eas.json
app configuration
project association
```

Do not create a second EAS project because the first configuration is unclear.

---

# 68. Common EAS failure: wrong build profile

Symptom:

```text
Expected preview
but production was built
```

Check:

```bash
eas build --profile preview
```

and inspect:

```text
eas.json
```

Remember:

```text
profile name
→ configuration selection
```

Do not assume:

```text
"preview"
```

automatically means:

```text
staging environment
```

Verify the environment mapping.

---

# 69. Common EAS failure: wrong environment

Symptom:

```text
production artifact
→ staging API
```

Check:

```text
EAS environment
eas.json
environment variables
app.config.*
build profile
```

Then inspect the resolved configuration and actual artifact.

See:

```text
foundations/release-environments.md
```

---

# 70. Common EAS failure: missing environment variable

Check:

```bash
eas env:list --environment production
```

Verify:

```text
variable name
environment
scope
visibility
```

If the value is needed during EAS Build, make sure it is available to that build environment.

Do not paste the secret value into the repository just to make the build work.

---

# 71. Common EAS failure: signing

Symptoms:

```text
certificate
provisioning
keystore
credentials
```

Check:

```bash
eas credentials
```

Then verify:

```text
application identity
credential source
platform credentials
Apple/Google permissions
```

See:

```text
frameworks/expo/credentials.md
```

---

# 72. Common EAS failure: build works locally but fails on EAS

Compare:

```text
Node
package manager
Expo SDK
dependencies
environment variables
source files
native tooling
credentials
```

Use:

```bash
eas build --local
```

when appropriate.

Also check whether the local build is using:

```text
uncommitted files
local environment variables
cached state
```

that the EAS builder does not have.

---

# 73. Common EAS failure: EAS Update breaks production

Check:

```text
runtimeVersion
native capabilities
update channel
branch
environment
published update
```

If the update requires native support:

```text
build a new native binary
```

Do not keep publishing JavaScript updates until the runtime mismatch is understood.

---

# 74. Common EAS failure: submission authentication

For Android:

```text
Google Play service account
→ correct application
→ correct permissions
```

For iOS:

```text
App Store Connect credentials
→ correct team
→ correct app
→ correct permissions
```

Build credentials and submission credentials are separate.

See:

```text
frameworks/expo/credentials.md
```

---

# 75. Common EAS failure: automatic submission chose the wrong artifact

Avoid ambiguous release scripts such as:

```bash
eas submit --latest
```

when multiple builds exist and the release matters.

Prefer:

```bash
eas submit --id <BUILD_ID>
```

when the exact production artifact is known.

This creates a stronger relationship:

```text
Approved release
→ exact build ID
→ exact submission
```

---

# 76. Common EAS failure: workflow does not start

Check:

```text
workflow file
trigger
GitHub integration
branch
event
permissions
EAS project
```

If using EAS Workflows, confirm the workflow is in the expected:

```text
.eas/workflows/
```

location and matches the current EAS workflow schema.

Do not copy an old workflow example without validating it against current Expo documentation.

---

# 77. Common EAS failure: workflow starts but job fails

Use:

```bash
eas workflow:logs
```

Then:

```text
Identify failed job
→ identify failed step
→ inspect first meaningful error
```

Do not increase retries until you know whether the error is transient.

---

# 78. Common EAS failure: credentials change unexpectedly

If an automated build changes credentials unexpectedly:

```text
Stop
→ inspect credential behavior
→ identify which account/job performed the change
→ protect production credentials
```

For non-interactive production builds, consider whether credential updates should be frozen when supported:

```bash
eas build --freeze-credentials
```

Use this deliberately and only when it matches the project's credential-management model.

---

# 79. Common EAS failure: build number collision

If the store rejects a build because the version/build number already exists:

```text
Check current store version
→ check EAS version state
→ determine version owner
→ increment correctly
→ rebuild
```

Do not randomly increase version numbers until the build passes.

See:

```text
foundations/versioning.md
```

---

# 80. EAS command reference

## Authentication

```bash
eas login
eas whoami
eas logout
```

## Project setup

```bash
eas build:configure
eas new
```

## Build

```bash
eas build
eas build:view
eas build:download
eas build:inspect
```

## Submit

```bash
eas submit
eas build:submit
```

## Version

```bash
eas build:version:get
eas build:version:set
eas build:version:sync
```

## Credentials

```bash
eas credentials
```

## Environment

```bash
eas env:list
eas env:pull
eas env:push
eas env:update
```

## Update

```bash
eas update
eas update:configure
eas update:list
eas update:edit
eas update:insights
```

## Workflows

```bash
eas workflow:create
eas workflow:run
eas workflow:logs
```

The exact command flags can change. Use the current EAS CLI reference before scripting production workflows.

---

# 81. Recommended command progression

For a new Expo project:

```bash
npx expo install expo-dev-client
```

```bash
eas login
```

```bash
eas build:configure
```

Then:

```bash
eas build --profile development --platform android
```

Test.

Then:

```bash
eas build --profile preview --platform android
```

Test.

Then:

```bash
eas build --profile production --platform android
```

Inspect.

Then:

```bash
eas submit --platform android --id <BUILD_ID>
```

Repeat the corresponding iOS workflow.

Do not automate the entire process until the manual path is proven.

---

# 82. Recommended release flow

For a normal release:

```text
1. Confirm release commit
2. Confirm version
3. Validate configuration
4. Run tests
5. Build preview
6. Test real devices
7. Fix blockers
8. Build production
9. Inspect artifact
10. Record EAS build ID
11. Human approval
12. Submit exact build
13. Monitor store processing
14. Monitor production
```

For an OTA-safe JavaScript-only change:

```text
1. Confirm native runtime compatibility
2. Validate update configuration
3. Test
4. Publish EAS Update
5. Monitor
6. Roll back or replace if required
```

---

# 83. Recommended CI/CD maturity

## Stage 1

```text
GitHub PR
→ tests

Developer
→ EAS build manually

Developer
→ submit manually
```

## Stage 2

```text
GitHub PR
→ validation

Merge
→ preview build

Developer
→ test

Production
→ manual build
→ manual approval
→ submit
```

## Stage 3

```text
Release tag
→ validation
→ EAS production build
→ artifact verification
→ protected approval
→ submission
→ monitoring
```

## Stage 4

Add only proven automation:

```text
AI release audit
Automated metadata checks
Automated smoke tests
Controlled EAS Update rollout
Release reporting
```

Do not jump directly to Stage 4.

---

# 84. Security model

The minimum security model is:

```text
Least privilege
+
Protected credentials
+
Environment separation
+
Human production approval
+
Auditability
+
Recovery
```

Never allow:

```text
AI
→ unrestricted EAS access
→ production credentials
→ store account administration
→ automatic signing rotation
```

unless the organization has explicitly designed and accepted that risk.

---

# 85. EAS + AI safe boundary

A good model:

```text
AI
→ inspect
→ analyze
→ prepare
→ suggest

Human
→ verify
→ approve

EAS
→ execute
```

AI can prepare:

```text
eas.json changes
workflow files
release notes
metadata drafts
build commands
debugging plans
```

But high-impact actions need explicit approval.

---

# 86. EAS release audit prompt

```text
Audit this Expo EAS release before production.

Inspect:

- eas.json
- app.config.*
- package.json
- lockfile
- EAS environments
- build profiles
- submit profiles
- update configuration
- CI/workflow files
- versioning
- application identifiers
- credential references

Check:

1. Correct environment
2. Correct build profile
3. Correct version
4. Correct build number
5. Correct application identifiers
6. Native dependency readiness
7. Signing dependency
8. Submission dependency
9. EAS Update compatibility
10. CI security
11. Production approval boundary
12. Artifact verification readiness

Return:

- PASS
- BLOCKED
- NEEDS VERIFICATION

For every finding provide:

- evidence
- risk
- exact file
- minimal fix
- verification step

Do not request or print secrets.
Do not modify files.
Do not invent requirements.
```

---

# 87. EAS production checklist

## Project

- [ ] EAS project is correct.
- [ ] `eas.json` is committed.
- [ ] Build profile is explicit.
- [ ] Submit profile is explicit where needed.
- [ ] Environment mapping is understood.
- [ ] Application identifiers are correct.

## Dependencies

- [ ] Expo SDK is known.
- [ ] Dependencies are consistent.
- [ ] Lockfile is committed.
- [ ] Native changes are understood.
- [ ] Development build has been tested.

## Build

- [ ] Release commit is known.
- [ ] Production profile is correct.
- [ ] Production environment is correct.
- [ ] Signing is correct.
- [ ] Version is correct.
- [ ] Build number is correct.
- [ ] Production build succeeds.
- [ ] EAS build ID is recorded.

## Artifact

- [ ] Artifact is downloaded/inspectable.
- [ ] Identifier is correct.
- [ ] Version is correct.
- [ ] Environment is correct.
- [ ] Signing is correct.
- [ ] Real-device smoke test passes.

## Submission

- [ ] Store metadata is ready.
- [ ] Submission credentials are available.
- [ ] Exact approved build is identified.
- [ ] Human approval is complete.
- [ ] Submission target is correct.

## Update

If using EAS Update:

- [ ] Runtime compatibility is verified.
- [ ] Channel/branch is correct.
- [ ] Environment is correct.
- [ ] Rollout plan exists.
- [ ] Monitoring exists.
- [ ] Recovery path is known.

---

# 88. What not to do

Do not:

```text
Build production from random local changes
```

Do not:

```text
Submit "latest" when multiple candidate builds exist
```

Do not:

```text
Use EAS Update for native changes
```

Do not:

```text
Commit credentials
```

Do not:

```text
Let AI rotate production signing keys without approval
```

Do not:

```text
Let CI silently choose production environment
```

Do not:

```text
Create many EAS profiles without real use cases
```

Do not:

```text
Add EAS Workflows before the manual process is understood
```

Do not:

```text
Treat a successful build as proof of release readiness
```

Do not:

```text
Retry blindly when a workflow fails
```

Do not:

```text
Assume current CLI behavior from an old tutorial
```

---

# 89. Definition of done

An EAS setup is production-ready when:

```text
Project is correctly linked
        +
eas.json is explicit
        +
Environments are clear
        +
Credentials are protected
        +
Development build works
        +
Preview build works
        +
Production build works
        +
Artifact is verified
        +
Submission path works
        +
EAS Update strategy is understood if used
        +
CI/CD has a clear owner
        +
Human production approval exists
        +
Recovery path is documented
        ↓
       EAS READY
```

The key rule is:

> **Use EAS to make release execution easier, not to hide release complexity.**

---

# Related documentation

### Foundations

- `foundations/mobile-release-lifecycle.md`
- `foundations/project-configuration.md`
- `foundations/release-environments.md`
- `foundations/identifiers.md`
- `foundations/versioning.md`
- `foundations/dependency-management.md`

### Expo

- `frameworks/expo/app-config.md`
- `frameworks/expo/build.md`
- `frameworks/expo/ci-cd.md`
- `frameworks/expo/credentials.md`
- `frameworks/expo/development-builds.md`
- `frameworks/expo/common-failures.md`
- `frameworks/expo/submit.md`

### Signing

- `signing/ios/certificates.md`
- `signing/ios/provisioning-profiles.md`
- `signing/ios/api-keys.md`
- `signing/android/keystore.md`
- `signing/android/upload-key.md`
- `signing/android/play-app-signing.md`
- `signing/security/secret-storage.md`
- `signing/security/ci-secrets.md`

### Testing

- `testing/release-smoke-tests.md`
- `testing/device-testing.md`
- `testing/device-matrix.md`
- `testing/upgrade-migrations.md`
- `testing/deep-links.md`
- `testing/push-notifications.md`
- `testing/payments.md`

### Release engineering

- `release-engineering/eas/`
- `release-engineering/github-actions/`
- `release-engineering/environment-management.md`
- `release-engineering/app-store-connect-api.md`
- `release-engineering/google-play-api.md`
- `release-engineering/release-pipelines.md`

### Release strategy

- `release-strategy/beta-testing.md`
- `release-strategy/staged-rollouts.md`
- `release-strategy/phased-release.md`
- `release-strategy/feature-flags.md`
- `release-strategy/hotfixes.md`
- `release-strategy/emergency-release.md`

### Post-release

- `post-release/monitoring.md`
- `post-release/crash-analysis.md`
- `post-release/rollout-monitoring.md`
- `post-release/incident-response.md`
- `post-release/rollback.md`
- `post-release/hotfix.md`

### AI

- `ai/workflows/release-audit.md`
- `ai/workflows/ai-qa.md`
- `ai/workflows/debugging.md`
- `ai/workflows/release-preparation.md`
- `ai/orchestration/agent-workflows.md`
- `ai/orchestration/human-approval.md`
- `ai/security/secret-protection.md`
- `ai/security/agent-permissions.md`
- `ai/security/destructive-actions.md`
- `ai/evals/hallucination-tests.md`

---

# Official sources

Use official Expo documentation as the authority for current EAS behavior.

### Expo

- EAS CLI reference: https://docs.expo.dev/eas/cli/
- EAS Build introduction: https://docs.expo.dev/build/introduction/
- Configure EAS Build with `eas.json`: https://docs.expo.dev/build/eas-json/
- EAS Build configuration reference: https://docs.expo.dev/eas/json/
- EAS Build troubleshooting: https://docs.expo.dev/build-reference/troubleshooting/
- EAS local builds: https://docs.expo.dev/build-reference/local-builds/
- EAS Submit introduction: https://docs.expo.dev/submit/introduction/
- EAS Update introduction: https://docs.expo.dev/eas-update/introduction/
- EAS Update runtime versions: https://docs.expo.dev/eas-update/runtime-versions/
- EAS environment variables: https://docs.expo.dev/eas/environment-variables/
- EAS Workflows introduction: https://docs.expo.dev/eas/workflows/introduction/
- EAS Workflows getting started: https://docs.expo.dev/eas/workflows/get-started/
- Automating EAS CLI commands: https://docs.expo.dev/eas/workflows/automating-eas-cli/
- EAS credentials: https://docs.expo.dev/app-signing/managed-credentials/
- EAS metadata: https://docs.expo.dev/eas/metadata/

### Apple

- Apple Developer: https://developer.apple.com/
- App Store Connect Help: https://developer.apple.com/help/app-store-connect/

### Android

- Android Developers: https://developer.android.com/
- Google Play Console Help: https://support.google.com/googleplay/android-developer/

### React Native

- React Native documentation: https://reactnative.dev/docs/getting-started

**Last verified:** August 11, 2026

EAS CLI commands, build images, EAS Workflows, environment behavior, signing systems, EAS Update behavior, and store requirements can change. Re-check the official Expo documentation for the project's Expo SDK and EAS CLI version before changing a production workflow.
