# Expo Updates

Expo Updates lets an Expo application receive compatible JavaScript and asset changes after a native binary has already been installed.

The practical model is:

```text
Native build
    ↓
Runtime version
    ↓
Update channel
    ↓
EAS Update
    ↓
Compatible JavaScript/assets
    ↓
Users
```

It is useful for:

- JavaScript bug fixes
- UI fixes
- copy changes
- translations
- compatible asset changes
- controlled rollouts
- production hotfixes that do not require native changes

It is **not** a replacement for a new native build.

The most important rule in this guide is:

> **An update is only safe when the installed native runtime can support the code being published.**

---

# 1. What Expo Updates is

EAS Update is the cloud service that serves updates to applications using `expo-updates`.

An update can contain non-native application code such as:

```text
JavaScript
TypeScript compiled output
React UI
Styles
Images/assets
Other compatible bundled resources
```

A native application binary remains installed on the device.

The update layer can change independently when the update is compatible with that binary.

Official documentation:

https://docs.expo.dev/eas-update/introduction/

---

# 2. Build vs Update

Keep this distinction clear.

## Native build

Creates or changes:

```text
Android/iOS native code
Native dependencies
Permissions
Entitlements
Native SDKs
Native configuration
Application identity
```

Use:

```bash
eas build
```

## EAS Update

Changes compatible:

```text
JavaScript
UI
Styles
Assets
Other supported non-native application code
```

Use:

```bash
eas update
```

The decision rule is:

```text
Does the change require a different native runtime?
        │
        ├── Yes → New native build
        │
        └── No
             ↓
        Is the update compatible?
             │
             ├── Yes → EAS Update may be appropriate
             │
             └── No → New native build
```

Expo explicitly requires a new build when native code changes because updates must match the native runtime.

---

# 3. What usually requires a new build

Treat these as native changes unless the project's actual configuration proves otherwise:

```text
Native dependency
Native module
Native SDK
Native permission
iOS entitlement
Android manifest change
Config plugin affecting native code
Bundle identifier
Android application ID
Native capability
Native URL scheme
Push notification native configuration
App icon / native assets
Native build configuration
```

Examples:

```text
Install expo-camera
→ new build

Add a native permission
→ new build

Change an iOS entitlement
→ new build

Change Android native configuration
→ new build
```

Do not try to use an OTA update to bypass a required native build.

---

# 4. What is commonly suitable for EAS Update

Examples:

```text
Fix a JavaScript bug
Change a React component
Fix navigation logic
Change UI styling
Update copy
Update translations
Change compatible assets
Fix a JavaScript crash
```

Example:

```text
Checkout button does nothing
        ↓
Fix JavaScript handler
        ↓
Run tests
        ↓
Build/export update
        ↓
Publish EAS Update
```

This can avoid waiting for store review when the change is genuinely compatible with the installed native runtime.

That does not mean every JavaScript change should bypass normal release controls.

---

# 5. Install and configure Expo Updates

Install the package:

```bash
npx expo install expo-updates
```

Then configure EAS Update:

```bash
eas update:configure
```

The project must have a native build containing `expo-updates` before that build can receive EAS Updates.

That means the first setup requires:

```text
Install expo-updates
        ↓
Configure EAS Update
        ↓
Create new Android/iOS build
        ↓
Install build
        ↓
Publish compatible update
```

Official documentation:

https://docs.expo.dev/eas-update/getting-started/

---

# 6. Basic publish command

A production update can be published with:

```bash
eas update \
  --channel production \
  --message "Fix checkout button"
```

For current EAS environments, also specify the intended environment:

```bash
eas update \
  --channel production \
  --environment production \
  --message "Fix checkout button"
```

The `--environment` flag is important for projects using EAS environment variables. Expo documents that in current SDKs the update command should explicitly select the EAS environment so the update uses the intended server-side environment variables.

---

# 7. The three concepts you must understand

EAS Update uses:

```text
Runtime version
+
Channel
+
Branch
```

They solve different problems.

```text
Runtime version
→ Which native runtimes can safely run this update?

Channel
→ Which builds should receive updates from this deployment path?

Branch
→ Which ordered set of updates is being managed?
```

Do not treat them as interchangeable.

---

# 8. Runtime version

A runtime version defines compatibility between:

```text
Native build
```

and:

```text
EAS Update
```

For example:

```text
Build
runtimeVersion = 1.4.0

Update
runtimeVersion = 1.4.0

→ compatible
```

But:

```text
Build
runtimeVersion = 1.4.0

Update
runtimeVersion = 1.5.0

→ not targeted to that build
```

Expo describes runtime versions as the compatibility boundary between the native layer inside a build and the update layer served to that build.

---

# 9. Why runtime versions matter

Imagine:

```text
Production build
runtimeVersion = 1.0.0
```

Then you install a native dependency:

```text
expo-camera
```

and publish JavaScript that calls the new native module without creating a new build.

If the runtime version remains incorrectly compatible:

```text
Old native binary
        +
JavaScript expecting new native module
        ↓
Broken application
```

Runtime versioning exists to prevent this class of mistake.

Expo documents that when native code changes, a new build is required before publishing an update for that new native runtime.

---

# 10. Runtime version policies

Expo supports runtime version policies.

Common policies include:

```text
appVersion
nativeVersion
sdkVersion
fingerprint
```

You can also set a custom runtime version.

Example:

```json
{
  "expo": {
    "runtimeVersion": {
      "policy": "appVersion"
    }
  }
}
```

The current Expo deployment guide recommends `appVersion` for the simplest common release process. The `fingerprint` policy is more automatic but is currently described by Expo as experimental/not broadly recommended for the default workflow.

---

# 11. Recommended runtime strategy

For a normal Expo application, start simple:

```json
{
  "expo": {
    "runtimeVersion": {
      "policy": "appVersion"
    }
  }
}
```

Then make the release process enforce:

```text
Native runtime change
→ app version change
→ new native build
```

This keeps the relationship easy to understand.

If the project later needs a different runtime strategy, document the reason.

Do not introduce custom runtime versioning just because it is more flexible.

---

# 12. When `appVersion` is not enough

The `appVersion` policy depends on the application version being changed when the native runtime changes.

That means this is dangerous:

```text
Native code changed
+
App version not changed
+
Update published
```

If the team frequently changes native dependencies without bumping the app version, the release process needs stronger controls.

Possible solutions include:

```text
Stricter versioning rules
+
CI validation
+
Fingerprint-based runtime strategy
+
Explicit runtimeVersion management
```

Choose the smallest solution that prevents the actual failure mode.

---

# 13. Channels

A channel is the deployment path embedded in a native build.

A common setup is:

```text
development
preview
production
```

For example:

```json
{
  "build": {
    "preview": {
      "channel": "preview",
      "distribution": "internal"
    },
    "production": {
      "channel": "production"
    }
  }
}
```

Then:

```text
Preview build
→ preview channel

Production build
→ production channel
```

When you publish:

```bash
eas update --channel production --environment production
```

the update is associated with the production deployment path.

Expo's deployment documentation recommends a simple channel-focused workflow for many applications.

---

# 14. Channels are not environments

Do not confuse:

```text
EAS Update channel
```

with:

```text
EAS environment
```

For example:

```text
Channel:
production

Environment:
production
```

is a common mapping.

But they answer different questions.

```text
Channel
→ which update deployment path?

Environment
→ which configuration values are used while producing the update/build?
```

A production channel with staging configuration is possible.

That is exactly why the mapping must be explicit.

---

# 15. Branches

EAS Update also uses branches.

A branch contains an ordered set of updates.

Conceptually:

```text
Branch
    ↓
Update 1
    ↓
Update 2
    ↓
Update 3
```

The most recent active update on a branch is the update clients can receive through the associated deployment configuration.

Channels can point to branches.

Expo describes branches as similar to Git branches in that they contain an ordered sequence of updates.

---

# 16. Do not overuse branches

For most projects, you do not need a complicated branch model.

A simple setup can be:

```text
production channel
→ production branch

preview channel
→ preview branch
```

You can think of the channel and matching branch as one deployment concept.

Expo's current deployment guide specifically recommends a simple channel-focused process for most applications and says teams can mostly ignore branches until they have a reason to use them directly.

---

# 17. When branches become useful

Branches can help when you need:

```text
Previewing updates from different development lines
Testing a release candidate
Managing multiple application versions
Supporting staged deployment workflows
Separating update histories
```

Example:

```text
production
    ↓
version-1.4

preview
    ↓
version-1.5
```

This can be useful when different native versions remain in production.

Do not create a branch for every Git branch unless the workflow actually needs it.

---

# 18. Multiple native versions in production

This is where EAS Update becomes more important.

Imagine:

```text
Users:
30% on 1.3.0
50% on 1.4.0
20% on 1.5.0
```

The application fleet contains multiple native runtimes.

Your update system must know:

```text
Which update is compatible with which build?
```

Runtime versions solve the compatibility side.

Channels and branches help manage deployment targeting.

Expo's deployment documentation specifically calls out multiple binary versions in production as a reason to use channels, branches, and runtime versions deliberately.

---

# 19. Production update flow

A safe production update flow:

```text
Code change
    ↓
Tests
    ↓
Check native compatibility
    ↓
Confirm runtimeVersion
    ↓
Build preview if needed
    ↓
Publish to preview channel
    ↓
Test
    ↓
Publish to production channel
    ↓
Monitor
```

Do not make:

```text
Developer changes code
        ↓
eas update --channel production
```

the default production workflow.

---

# 20. Preview updates

A preview update is useful for testing an update before production.

For example:

```bash
eas update \
  --channel preview \
  --environment preview \
  --message "Test checkout fix"
```

Then test with:

```text
Preview build
+
same compatible runtime
+
preview channel
```

This is stronger than testing the JavaScript only in a development environment.

---

# 21. Production updates

After preview verification:

```bash
eas update \
  --channel production \
  --environment production \
  --message "Fix checkout button"
```

Record:

```text
Update ID
Runtime version
Platform
Channel
Environment
Git commit
Message
Publisher
```

This creates an auditable release record.

---

# 22. Platform matching

Updates are platform-specific.

An Android build must receive an Android-compatible update.

An iOS build must receive an iOS-compatible update.

Expo's update matching rules require the update's target platform to match the build platform and the runtime version to match the build runtime version.

Do not assume:

```text
one update
→ identical behavior across Android and iOS
```

Test both platforms when the change affects both.

---

# 23. Environment variables and updates

This is a common source of mistakes.

A build may use:

```text
production EAS environment
```

while the update command accidentally uses:

```text
local .env
```

That can produce:

```text
Production binary
+
staging API URL
```

or the reverse.

For current EAS Update workflows, explicitly specify:

```bash
eas update --environment production
```

Expo documents this behavior and recommends using the explicit environment so updates and builds use consistent EAS-managed environment variables.

---

# 24. Public environment variables

Remember:

```text
EXPO_PUBLIC_*
```

values are intended for client-side code.

They are not secrets.

Anything bundled into the application can potentially be inspected by a user.

Never put:

```text
Database password
Private API key
Service account secret
Signing private key
Admin token
```

into a public application environment variable.

Use a backend for privileged operations.

---

# 25. How users receive updates

For normal non-development builds:

```text
App launches
    ↓
Checks for compatible update
    ↓
Downloads update
    ↓
Applies according to update configuration
    ↓
App restarts / launches with update
```

Expo's current documentation explains that preview and production builds can download updates in the background and apply them after a subsequent launch/restart.

Do not design critical product behavior around the assumption that every user receives an update immediately.

---

# 26. Embedded update

The native binary contains an embedded update.

This gives the app a known-good fallback.

Conceptually:

```text
Native binary
    │
    ├── Embedded update
    │
    └── Downloaded update
```

If the downloaded update is broken, Expo Updates has recovery behavior designed to prevent the application from becoming permanently unusable.

Do not disable these safety mechanisms casually.

---

# 27. Update checking

The `expo-updates` library provides APIs for inspecting and controlling update behavior.

Examples include:

```text
useUpdates()
checkForUpdateAsync()
fetchUpdateAsync()
```

Use these APIs only when the product actually needs custom update behavior.

For a normal application, the default update flow is usually simpler and safer.

Do not build a custom update manager when the standard behavior already works.

---

# 28. Custom update UI

A custom update UI can be useful when:

```text
You need to tell the user a new update is ready
You need controlled reload behavior
You need product-specific messaging
```

For example:

```text
New version available.

Restart the app to apply the update.
```

But avoid forcing update downloads or reloads at arbitrary times.

A user can be:

```text
filling a form
checking out
on a call
using navigation
```

An unexpected reload can destroy state.

---

# 29. Critical updates

For a severe JavaScript bug:

```text
Broken production update
        ↓
Stop rollout / rollback
        ↓
Publish known-good update
        ↓
Monitor
```

If the issue is native:

```text
Broken native build
        ↓
Store release / hotfix
```

Do not assume EAS Update can recover from every production issue.

---

# 30. Rollback

EAS Update supports two important rollback paths:

```text
Rollback to a previous published update
```

and:

```text
Rollback to the update embedded in the build
```

The current CLI provides:

```bash
eas update:rollback
```

Expo documents both rollback types and explains that a rollback republishes or points clients back to a known-good update path.

---

# 31. Rollback to a previous update

Use this when:

```text
Current update is broken
+
previous published update is known-good
```

Conceptually:

```text
Update A
   ↓
Update B
   ↓
Update C ← broken

Rollback
   ↓
Update B
```

The rollback is itself a deployment action.

Verify the result after performing it.

---

# 32. Rollback to embedded update

This can be useful when:

```text
The embedded binary update is known-good
```

The result is:

```text
Native build
    ↓
Embedded update
```

This gives the installed binary a known baseline.

Again:

```text
Rollback
→ verify
→ monitor
```

Do not assume rollback succeeded merely because the CLI command completed.

---

# 33. Rollouts

EAS Update supports gradual rollouts.

Instead of:

```text
0%
 ↓
100%
```

you can use:

```text
1%
 ↓
5%
 ↓
25%
 ↓
50%
 ↓
100%
```

when the change warrants it.

Expo documents update-based and branch-based rollout mechanisms and provides CLI tooling to control rollout percentages.

---

# 34. When to use a rollout

Use gradual rollout for changes with meaningful risk:

```text
Authentication
Payments
Navigation
Large refactors
Critical user journeys
Performance-sensitive changes
High-impact UI changes
```

A small copy change may not need a rollout.

The risk should determine the release control.

---

# 35. Rollout requires monitoring

Do not roll out gradually if you cannot observe the result.

You need signals such as:

```text
Crash rate
Update failure rate
App errors
API errors
Conversion failures
User reports
Performance regression
```

EAS provides update adoption and failure information through its deployment/update views. Expo documents metrics for users who have run an update and failed installs.

Combine EAS data with your application's normal observability.

---

# 36. A practical rollout

Example:

```text
Update:
Fix checkout JavaScript crash

Runtime:
1.4.0

Channel:
production
```

Rollout:

```text
5%
 ↓
Monitor crash rate
 ↓
No regression
 ↓
25%
 ↓
Monitor
 ↓
50%
 ↓
Monitor
 ↓
100%
```

If the error rate increases:

```text
Stop
 ↓
Rollback
 ↓
Verify
 ↓
Investigate
```

Do not continue rollout because:

```text
"only a small percentage is affected."
```

---

# 37. Rollout controls

The current EAS CLI provides rollout tooling such as:

```bash
eas channel:rollout
```

and update rollout/revert commands.

The exact command and rollout model depend on whether you are using branch-based or update-based rollout.

Always check current Expo CLI documentation before automating rollout commands.

Official documentation:

https://docs.expo.dev/eas-update/rollouts/

---

# 38. Update adoption

An update can be:

```text
published
```

without being:

```text
fully adopted
```

Users may:

```text
not open the app
remain offline
have an incompatible runtime
be on an old binary
```

Track:

```text
Published
→ Downloaded
→ Installed
→ Running
```

where the available telemetry allows it.

Do not treat publish success as user adoption.

---

# 39. Update debugging

When an update does not appear:

```text
1. Check platform.
2. Check runtime version.
3. Check channel.
4. Check branch/channel relationship.
5. Check update environment.
6. Check whether the build contains expo-updates.
7. Check update configuration.
8. Check network access.
9. Restart the application.
10. Inspect update details.
```

A common mistake is:

```text
Published to preview
```

while the installed build is:

```text
production channel
```

The update will not appear through the normal channel-based flow.

---

# 40. Update debugging matrix

| Symptom | Check first |
|---|---|
| Update never appears | Channel |
| Update appears on wrong build | Runtime version |
| Update has wrong API URL | EAS environment |
| Development build cannot load update | `expo-dev-client` / runtime / channel |
| Production update breaks app | Runtime compatibility / native dependency |
| Rollout cannot be changed | Existing rollout state |
| Old update still runs | App restart / update adoption |
| Update command fails | EAS authentication / environment / project |
| Update publishes but users do not receive it | Channel, runtime, network, app launch behavior |

---

# 41. Native dependency change

Example:

```text
Current production:
runtimeVersion 1.4.0
```

You add:

```text
expo-location
```

and configure native permissions.

Correct flow:

```text
Change native dependency
        ↓
Increase app version / runtime
        ↓
Build new native binary
        ↓
Test
        ↓
Submit to stores
        ↓
Users install new binary
        ↓
Publish compatible updates
```

Do not do:

```text
Install native dependency
        ↓
eas update
```

without a compatible native runtime.

---

# 42. JavaScript-only bug fix

Example:

```text
Current production:
runtimeVersion 1.4.0
```

Bug:

```text
Checkout button handler fails
```

No native code changed.

Correct flow can be:

```text
Fix JavaScript
        ↓
Tests
        ↓
Preview update
        ↓
Test
        ↓
Production EAS Update
        ↓
Monitor
```

This is where EAS Update can save significant release time.

---

# 43. OTA update safety rule

Before every production update, ask:

```text
1. Did native code change?
2. Did native dependencies change?
3. Did config plugins change native output?
4. Did permissions/capabilities change?
5. Did application identity change?
6. Does the JavaScript depend on a native API unavailable in old binaries?
7. Is the runtimeVersion correct?
8. Is the target channel correct?
9. Is the environment correct?
```

If any answer creates uncertainty:

```text
Do not publish yet.
```

Resolve compatibility first.

---

# 44. Release record

For every production EAS Update, record:

```text
Git commit
Update ID
Platform
Runtime version
Channel
Environment
Message
Publisher
Published time
Rollout percentage
Final status
```

Example:

```text
Version:
1.4.0

Runtime:
1.4.0

Channel:
production

Environment:
production

Commit:
abc1234

Update:
<update-id>

Change:
Fix checkout button

Rollout:
100%

Status:
Healthy
```

This makes production debugging much easier.

---

# 45. CI/CD

EAS Update works well with CI/CD.

A safe workflow is:

```text
Pull Request
    ↓
Tests
    ↓
Merge
    ↓
Preview update
    ↓
Testing
    ↓
Production approval
    ↓
Production update
    ↓
Monitoring
```

Do not let every merge automatically publish to the production channel.

Production update publication should be an intentional release action.

---

# 46. GitHub Actions

A CI job can publish an update.

Conceptually:

```yaml
name: Publish EAS Update

on:
  workflow_dispatch:

jobs:
  update:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: 20

      - run: npm ci

      - run: npx eas-cli@latest update \
          --channel production \
          --environment production \
          --message "Production update"
        env:
          EXPO_TOKEN: ${{ secrets.EXPO_TOKEN }}
```

Treat this as a pattern, not a copy-paste production workflow.

Pin versions where reproducibility matters, and use protected environments for production.

Never expose:

```text
EXPO_TOKEN
```

in logs.

---

# 47. EAS Workflows

EAS Workflows can publish updates.

Conceptually:

```yaml
jobs:
  publish_update:
    type: update
    params:
      message: "Fix login button"
      channel: production
```

Use EAS Workflows when the workflow naturally belongs with EAS.

Use GitHub Actions when the workflow is primarily repository-wide.

Do not duplicate the same production update job in both systems.

---

# 48. Production approval

A safe production OTA process is:

```text
Developer
→ prepares update

CI
→ tests

Preview
→ verifies

Human
→ approves

CI/EAS
→ publishes production update

Monitoring
→ verifies
```

Do not give an AI agent unrestricted permission to publish production updates.

An OTA update can affect users quickly.

---

# 49. AI-assisted update workflow

AI is useful for:

```text
Update compatibility analysis
Release audit
Change classification
Changelog generation
Test planning
Build/update decision
Failure analysis
Rollback analysis
```

Example:

```text
AI
→ inspect git diff
→ classify native vs JavaScript changes
→ inspect dependencies
→ inspect Expo config
→ recommend build or update
→ identify tests
```

Then:

```text
Developer
→ verifies recommendation
```

The AI must not be treated as the authority on native compatibility.

---

# 50. AI update audit prompt

```text
Determine whether this Expo change can safely ship through EAS Update.

Inspect:

- git diff
- package.json
- lockfile
- app.json / app.config.*
- eas.json
- native directories if present
- config plugins
- Expo SDK version
- React Native version
- expo-updates configuration
- runtimeVersion configuration

Return:

1. BUILD or UPDATE
2. Native changes detected
3. Native dependencies changed
4. Config-plugin changes
5. Permission/capability changes
6. Runtime-version impact
7. Target channel
8. Target EAS environment
9. Required tests
10. Main release risk

Rules:

- Do not guess.
- Separate confirmed changes from inferred risk.
- If native compatibility cannot be established, return BUILD or NEEDS VERIFICATION.
- Do not publish anything.
- Do not request secrets.
- Do not modify files.
```

---

# 51. AI update failure analysis

Use:

```text
Update ID
Runtime version
Platform
Channel
Environment
Git commit
Relevant logs
Crash/error
```

Prompt:

```text
Analyze this Expo EAS Update failure.

Determine whether the failure is caused by:

- runtime incompatibility
- JavaScript error
- native dependency mismatch
- environment configuration
- channel/branch targeting
- asset problem
- update download failure
- update application failure
- backend/API incompatibility

Return:

1. Failure category
2. Evidence
3. Most likely cause
4. Alternative causes
5. Whether rollback is appropriate
6. Whether a native build is required
7. Minimal fix
8. Verification steps

Do not request secrets.
Do not publish or rollback automatically.
```

---

# 52. Security considerations

Treat OTA publishing as a production deployment capability.

Protect:

```text
EXPO_TOKEN
EAS account access
Production update permissions
CI workflows
GitHub environments
Store credentials
```

Use:

```text
Least privilege
Protected secrets
Protected environments
Human approval
Audit logs
Rollback capability
```

Never allow untrusted pull requests to access production update credentials.

---

# 53. Fork and pull-request security

Public repositories need extra care.

Do not allow a pull request from an untrusted fork to publish:

```text
Production EAS Update
```

or access:

```text
Production EXPO_TOKEN
```

Use separate workflows:

```text
Untrusted PR
→ tests only

Trusted release workflow
→ production update
```

The repository's CI/CD guidance follows this separation for production credentials and forked pull requests.

---

# 54. Secrets in update code

An OTA update can change client-side code very quickly.

That does **not** make secrets safe.

Never publish:

```text
Database credentials
Admin tokens
Private signing keys
Service account secrets
Internal API secrets
```

through JavaScript.

Anything shipped to the client should be considered potentially observable by the client.

---

# 55. Anti-bricking behavior

Expo Updates includes safety behavior intended to help recover from broken updates.

Do not disable anti-bricking protections in production without a strong reason.

Expo documents that overriding update configuration and disabling anti-bricking measures can remove the ability to safely recover from a bad update.

The default production path should preserve recovery.

---

# 56. Custom update URL/channel overrides

Expo supports runtime overrides for update URL and request headers.

This can be useful for advanced cases such as:

```text
Testing a specific update
Switching channels for internal tooling
Custom update infrastructure
```

But it changes the normal update-selection model.

Do not add runtime channel switching to production simply because it is technically possible.

Expo documents this capability and warns about the interaction with anti-bricking behavior.

---

# 57. Channel surfing

Channel surfing allows an installed build to request updates from a different channel at runtime.

This can be useful for:

```text
Internal QA
Previewing updates
Controlled testing
```

The important constraint remains:

```text
Channel change
≠
runtime compatibility bypass
```

The selected update still needs to match:

```text
Platform
+
Runtime version
```

Expo documents channel surfing as an advanced feature available in supported Expo SDK versions.

---

# 58. Do not build your own update service too early

EAS Update is usually enough for:

```text
Small teams
Expo applications
Standard release channels
Basic rollouts
Production hotfixes
```

A custom update server becomes relevant only when you have concrete requirements that EAS Update cannot satisfy.

Examples:

```text
Custom deployment topology
Special compliance requirements
Custom update selection logic
Self-hosted infrastructure requirements
```

Expo supports the Expo Updates protocol and documents how a custom update service can be used as an alternative.

Do not build one just to gain theoretical control.

---

# 59. Monitoring

After publishing:

```text
Publish
 ↓
Observe
 ↓
Compare against baseline
 ↓
Increase rollout
or
 ↓
Rollback
```

Watch:

```text
Update adoption
Update install failures
Crashes
JavaScript errors
API failures
Performance
Critical business flows
```

For high-risk updates, define the rollback threshold before publishing.

Example:

```text
If checkout crash rate increases materially:
→ stop rollout
→ rollback
→ investigate
```

---

# 60. Production rollback decision

Use this decision tree:

```text
Is the problem JavaScript-only?
        │
        ├── Yes
        │    ↓
        │  Can compatible EAS Update fix it?
        │    │
        │    ├── Yes → publish fix / rollback
        │    └── No  → native build
        │
        └── No
             ↓
        Native build required
```

If the current update is broken:

```text
Known-good previous update?
        │
        ├── Yes → rollback to previous update
        │
        └── No
             ↓
        Embedded update acceptable?
             │
             ├── Yes → rollback to embedded
             └── No  → build/release hotfix
```

---

# 61. Example: bad JavaScript update

Initial state:

```text
Production build:
1.5.0

Runtime:
1.5.0

Update:
A
```

You publish:

```text
Update B
```

Users report:

```text
App crashes after opening checkout.
```

Response:

```text
Stop rollout
    ↓
Check update metrics/logs
    ↓
Confirm update B is the trigger
    ↓
Rollback to A
    ↓
Verify recovery
    ↓
Fix B
    ↓
Test preview
    ↓
Publish fixed update C
```

Do not rebuild the native app if the root cause is genuinely JavaScript-only.

---

# 62. Example: native change mistakenly shipped as OTA

Suppose update B adds code requiring:

```text
new native module
```

but production users are still running:

```text
native runtime 1.5.0
```

Correct response:

```text
Stop update
    ↓
Rollback
    ↓
Create native build 1.6.0
    ↓
Test
    ↓
Store release
    ↓
Publish compatible updates for runtime 1.6.0
```

Do not keep trying JavaScript fixes around a missing native API.

---

# 63. Release checklist

Before publishing a production update:

```text
[ ] Change is classified as JavaScript/assets only
[ ] No native dependency changed
[ ] No native permission changed
[ ] No entitlement/capability changed
[ ] No native config/plugin change
[ ] Runtime version is correct
[ ] Platform targets are correct
[ ] Channel is correct
[ ] EAS environment is correct
[ ] Tests pass
[ ] Preview update tested
[ ] Production update message is clear
[ ] Rollback path is known
[ ] Monitoring is available
[ ] Production approval completed
```

---

# 64. Post-publish checklist

Immediately after publishing:

```text
[ ] Update ID recorded
[ ] Git commit recorded
[ ] Runtime version recorded
[ ] Channel recorded
[ ] Environment recorded
[ ] Update appears in EAS
[ ] Expected builds can receive it
[ ] Adoption is visible
[ ] No unexpected crash spike
[ ] Critical user flows work
```

For a gradual rollout:

```text
[ ] Initial percentage verified
[ ] Error rate checked
[ ] Rollout increased deliberately
[ ] Final rollout verified
```

---

# 65. Common failures

## Update does not appear

Check:

```text
Channel
Runtime version
Platform
Build configuration
Update environment
Update publication status
Network
App restart
```

---

## Update appears on one platform but not another

Check:

```text
Platform-specific update
Runtime version
Native build
Channel
```

Do not assume Android and iOS are using identical native runtimes.

---

## Update contains wrong API URL

Check:

```text
eas update --environment
EAS environment variables
EXPO_PUBLIC_* values
app configuration
```

For current SDKs, explicitly use:

```bash
eas update --environment production
```

when publishing production updates.

---

## Update crashes immediately

First check:

```text
Did native code change?
Did a native dependency change?
Did a config plugin change?
Does the runtime support the update?
```

If compatibility is wrong:

```text
Rollback
```

then create the required native build.

---

## Rollout cannot be changed

Check whether another rollout is already active.

EAS Update restricts concurrent rollouts on the same deployment path/branch in order to avoid conflicting rollout state.

End or revert the current rollout before starting another one.

---

## Users remain on the old update

Possible reasons:

```text
User has not restarted
User is offline
Update has not downloaded
Runtime does not match
Channel does not match
Update is still rolling out
```

Check adoption metrics before assuming the update system is broken.

---

# 66. What not to do

Do not:

```text
Use EAS Update for native changes
```

Do not:

```text
Publish directly to production without testing
```

Do not:

```text
Ignore runtimeVersion
```

Do not:

```text
Mix staging environment variables into production updates
```

Do not:

```text
Give production EXPO_TOKEN to forked PRs
```

Do not:

```text
Disable anti-bricking protections casually
```

Do not:

```text
Create a complicated branch/channel system without a real need
```

Do not:

```text
Assume publishing means every user is updated
```

Do not:

```text
Treat AI's build-vs-update decision as authoritative
```

Do not:

```text
Rebuild the native application just because an OTA submission failed
```

First identify the actual failure.

---

# 67. Recommended project configuration

A simple project can use:

```json
{
  "expo": {
    "runtimeVersion": {
      "policy": "appVersion"
    }
  }
}
```

and:

```json
{
  "build": {
    "preview": {
      "channel": "preview",
      "distribution": "internal",
      "environment": "preview"
    },
    "production": {
      "channel": "production",
      "environment": "production"
    }
  }
}
```

Then:

```text
Preview build
→ preview channel
→ preview environment

Production build
→ production channel
→ production environment
```

Treat this as a baseline, not a universal configuration.

The actual `eas.json` should match the project's environment, credential, and release strategy.

---

# 68. Recommended release model

For most Expo projects:

```text
Development
    ↓
Development Build
    ↓
Preview
    ↓
Preview Build
    ↓
EAS Update
    ↓
Test
    ↓
Production approval
    ↓
Production EAS Update
    ↓
Monitor
```

For native changes:

```text
Development
    ↓
Native change
    ↓
New runtime
    ↓
New native build
    ↓
Store testing
    ↓
Store release
    ↓
Compatible EAS Updates
```

This keeps the release model simple.

---

# 69. Definition of done

An Expo Updates setup is production-ready when:

```text
expo-updates configured
        +
native build contains update runtime
        +
runtime strategy documented
        +
channels documented
        +
environments mapped
        +
preview process works
        +
production update process works
        +
rollback tested/documented
        +
monitoring exists
        +
production credentials protected
        +
human approval exists
        ↓
EAS UPDATE READY
```

The real goal is not:

```text
"we can publish OTA updates."
```

It is:

```text
"we can safely ship compatible fixes quickly and recover when something goes wrong."
```

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

- `frameworks/expo/README.md`
- `frameworks/expo/app-config.md`
- `frameworks/expo/development-builds.md`
- `frameworks/expo/eas.md`
- `frameworks/expo/credentials.md`
- `frameworks/expo/build.md`
- `frameworks/expo/ci-cd.md`
- `frameworks/expo/submit.md`
- `frameworks/expo/common-failures.md`

### Release strategy

- `release-strategy/beta-testing.md`
- `release-strategy/staged-rollouts.md`
- `release-strategy/phased-release.md`
- `release-strategy/feature-flags.md`
- `release-strategy/hotfixes.md`
- `release-strategy/emergency-release.md`

### Release engineering

- `release-engineering/eas/`
- `release-engineering/github-actions/`
- `release-engineering/environment-management.md`
- `release-engineering/release-pipelines.md`

### Testing

- `testing/release-smoke-tests.md`
- `testing/device-testing.md`
- `testing/device-matrix.md`
- `testing/network.md`
- `testing/offline.md`
- `testing/performance.md`
- `testing/upgrade-migrations.md`

### Post-release

- `post-release/monitoring.md`
- `post-release/crash-analysis.md`
- `post-release/performance.md`
- `post-release/rollout-monitoring.md`
- `post-release/incident-response.md`
- `post-release/rollback.md`
- `post-release/hotfix.md`

### AI

- `ai/workflows/release-audit.md`
- `ai/workflows/release-preparation.md`
- `ai/workflows/debugging.md`
- `ai/orchestration/human-approval.md`
- `ai/security/secret-protection.md`
- `ai/security/agent-permissions.md`
- `ai/security/destructive-actions.md`
- `ai/evals/hallucination-tests.md`

---

# Official sources

Use official Expo documentation as the authority for current Expo Updates behavior.

### Expo

- EAS Update overview: https://docs.expo.dev/eas-update/introduction/
- EAS Update getting started: https://docs.expo.dev/eas-update/getting-started/
- How EAS Update works: https://docs.expo.dev/eas-update/how-it-works/
- Runtime versions: https://docs.expo.dev/eas-update/runtime-versions/
- Deploy updates: https://docs.expo.dev/eas-update/deployment/
- Downloading updates: https://docs.expo.dev/eas-update/download-updates/
- Rollouts: https://docs.expo.dev/eas-update/rollouts/
- Rollbacks: https://docs.expo.dev/eas-update/rollbacks/
- EAS CLI branch/channel management: https://docs.expo.dev/eas-update/eas-cli/
- Channel surfing: https://docs.expo.dev/eas-update/channel-surfing/
- Runtime configuration overrides: https://docs.expo.dev/eas-update/override/
- EAS environment variables: https://docs.expo.dev/eas/environment-variables/
- EAS Update environment variables: https://docs.expo.dev/eas/environment-variables/usage/
- EAS CLI: https://docs.expo.dev/eas/cli/

### React Native

- React Native documentation: https://reactnative.dev/docs/getting-started

### Apple

- Apple Developer: https://developer.apple.com/
- App Store Connect Help: https://developer.apple.com/help/app-store-connect/

### Android

- Android Developers: https://developer.android.com/
- Google Play Console Help: https://support.google.com/googleplay/android-developer/

**Last verified:** August 11, 2026

EAS Update behavior, Expo SDK APIs, runtime-version policies, rollout commands, environment handling, and platform requirements can change. Re-check the official Expo documentation for the project's Expo SDK and EAS CLI version before changing a production update workflow.
