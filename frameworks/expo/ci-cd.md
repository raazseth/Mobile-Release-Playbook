# Expo CI/CD

CI/CD for Expo should make releases **repeatable, visible, and safe**.

For an Expo project using EAS, the practical model is:

```text
Pull Request
    ↓
Validation
    ↓
Merge
    ↓
Build
    ↓
Artifact verification
    ↓
Human approval
    ↓
Store submission
    ↓
Monitor
```

The important distinction is:

```text
CI
→ prove the change is buildable and testable

CD
→ produce and deliver an approved release
```

This guide focuses on **Expo + EAS + GitHub Actions**, while keeping the workflow simple enough for a public open-source project.

It does not turn the repository into a CI/CD platform. The playbook architecture deliberately favors documentation, executable examples, small scripts, AI workflows, and community contributions over unnecessary infrastructure. The repository's documentation rules also require workflows to be executable, commands to be verified, secrets to be protected, and production actions to have a human approval boundary. fileciteturn19file4L1-L30 fileciteturn19file3L1-L45

---

# 1. What this workflow should solve

A good Expo CI/CD setup should answer:

- Does the change pass automated checks?
- Can the application build?
- Is the build using the intended environment?
- Is the artifact identifiable?
- Can the release be traced to a Git commit?
- Are production credentials protected?
- Can production submission happen only after approval?
- Can a failed release be diagnosed?

It should **not** try to automate everything from day one.

Prefer:

```text
Small workflow
+
Explicit environments
+
Protected secrets
+
Traceable builds
+
Human approval
```

over:

```text
Complex release platform
+
Many custom scripts
+
Unrestricted automation
+
Automatic production publishing
```

The project explicitly rejects unnecessary CI/CD infrastructure and prefers the simplest solution that solves the real release problem. fileciteturn19file2L1-L35

---

# 2. Recommended architecture

For most Expo applications:

```text
GitHub
  │
  ├── Pull Request
  │      ↓
  │   Validation
  │
  ├── main
  │      ↓
  │   Build / integration checks
  │
  └── Release trigger
         ↓
      EAS Build
         ↓
      Artifact
         ↓
   Human approval
         ↓
   EAS Submit / Store
```

The build itself can run on EAS rather than requiring GitHub Actions to maintain Android and iOS build environments.

Expo documents GitHub Actions as a supported way to trigger EAS builds. A successful local EAS setup is required first so the project has the necessary EAS project configuration, build profiles, and native identifiers. fileciteturn0search4

This keeps the responsibilities clear:

```text
GitHub Actions
→ orchestration and validation

EAS
→ mobile build infrastructure

App Store Connect / Google Play
→ distribution
```

---

# 3. Recommended release pipeline

A practical pipeline is:

```text
1. Pull Request
      ↓
2. Install dependencies
      ↓
3. Validate Expo project
      ↓
4. Run tests
      ↓
5. Build preview when needed
      ↓
6. Merge
      ↓
7. Release trigger
      ↓
8. Production EAS Build
      ↓
9. Inspect artifact
      ↓
10. Human approval
      ↓
11. Submit
      ↓
12. Monitor
```

Do not make every pull request create a production iOS and Android build.

Cloud builds consume time and resources.

Use the cheapest validation that gives useful confidence.

---

# 4. Before CI/CD

The repository should first work locally.

Run:

```bash
npm ci
```

Then validate the Expo project:

```bash
npx expo-doctor
```

Check dependencies where appropriate:

```bash
npx expo install --check
```

Inspect the resolved configuration:

```bash
npx expo config
```

For public runtime configuration:

```bash
npx expo config --type public
```

Then prove that EAS can build the application from a developer machine.

Expo's current CI guidance requires a successful EAS build locally before relying on non-interactive CI builds. That initial setup establishes the EAS project, `eas.json`, and critical native configuration. citeturn0search4

---

# 5. Required repository files

A typical Expo CI/CD repository may contain:

```text
mobile-app/
├── app.config.ts
├── eas.json
├── package.json
├── package-lock.json
├── src/
├── scripts/
│   └── build-validation/
│
├── .github/
│   └── workflows/
│       ├── ci.yml
│       ├── eas-build.yml
│       └── release.yml
│
└── ...
```

Do not create all three workflows automatically.

Start with:

```text
ci.yml
```

and add build/release workflows when the release process actually needs them.

---

# 6. Pull request CI

Pull request CI should answer:

> "Is this change safe enough to merge?"

A typical flow:

```text
Pull Request
    ↓
Checkout
    ↓
Install dependencies
    ↓
Lint
    ↓
Typecheck
    ↓
Unit tests
    ↓
Expo validation
    ↓
Pass / Fail
```

Example:

```yaml
name: CI

on:
  pull_request:
    branches:
      - main

jobs:
  validate:
    name: Validate
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v5

      - name: Setup Node
        uses: actions/setup-node@v6
        with:
          node-version: 24
          cache: npm

      - name: Install dependencies
        run: npm ci

      - name: Expo Doctor
        run: npx expo-doctor

      - name: Typecheck
        run: npm run typecheck

      - name: Lint
        run: npm run lint

      - name: Test
        run: npm test
```

The exact Node version should match the project's supported toolchain. Do not copy a version blindly from another repository.

Expo's current CI example uses `actions/checkout@v5`, `actions/setup-node@v6`, Node 24, and `expo/expo-github-action@v8`; verify current tool versions against the project's Expo SDK and official documentation before adopting them. citeturn0search4

---

# 7. Do not put production credentials in PR CI

Pull requests can come from contributors you do not fully trust.

Do not expose:

```text
EXPO_TOKEN
Apple credentials
Google Play credentials
Signing credentials
Production API secrets
```

to arbitrary pull-request workflows.

A PR workflow should normally operate without production secrets.

Use:

```text
PR
→ untrusted validation

Release
→ protected credentials
```

This is one of the most important boundaries in mobile CI/CD.

GitHub's documentation recommends using protected secrets and environment controls for sensitive deployment workflows. citeturn0search6turn0search3

---

# 8. GitHub Actions permissions

Use minimum permissions.

For a validation workflow, prefer:

```yaml
permissions:
  contents: read
```

Example:

```yaml
name: CI

on:
  pull_request:
    branches:
      - main

permissions:
  contents: read

jobs:
  validate:
    ...
```

Do not grant:

```text
write-all
```

unless a specific job requires it.

The goal is:

```text
CI job
→ minimum GitHub permissions
```

not:

```text
CI job
→ repository administrator
```

---

# 9. Main branch CI

After merge, you may run a stronger validation workflow.

Example:

```text
main
 ↓
Install
 ↓
Lint
 ↓
Typecheck
 ↓
Tests
 ↓
Expo validation
 ↓
Optional preview build
```

This gives the team confidence that the branch remains releasable.

Do not automatically publish a production application merely because `main` is green.

---

# 10. Preview builds

Preview builds are useful when automated tests cannot prove native behavior.

Example:

```text
Pull Request
   ↓
CI
   ↓
Merge
   ↓
Preview build
   ↓
QA
```

Triggering a preview EAS build can be useful for:

- native dependency changes
- permissions
- deep links
- push notifications
- authentication
- payments
- release-only behavior
- device testing

Do not create a preview build for every tiny documentation change.

Use change-sensitive triggers when useful.

---

# 11. EAS build from GitHub Actions

Expo's current official GitHub Actions example uses:

```yaml
- uses: actions/checkout@v5

- uses: actions/setup-node@v6
  with:
    node-version: 24
    cache: npm

- name: Setup Expo and EAS
  uses: expo/expo-github-action@v8
  with:
    eas-version: latest
    token: ${{ secrets.EXPO_TOKEN }}

- name: Install dependencies
  run: npm ci

- name: Build on EAS
  run: eas build --platform all --non-interactive --no-wait
```

Expo documents this as a way to trigger EAS builds from GitHub Actions. citeturn0search4

For production repositories, prefer an explicit build profile:

```bash
eas build \
  --profile production \
  --platform all \
  --non-interactive \
  --no-wait
```

This makes the release intent clear.

---

# 12. Why `--non-interactive` matters

CI cannot safely depend on an interactive terminal.

Use:

```bash
--non-interactive
```

for automated EAS commands.

The workflow should either:

```text
have everything configured
```

or:

```text
fail clearly
```

It should not wait for a human to answer a terminal prompt.

---

# 13. Why `--no-wait` can be useful

EAS builds run remotely.

Using:

```bash
--no-wait
```

lets GitHub Actions trigger the build without waiting for the full cloud build to finish.

This is useful when:

```text
GitHub Actions
→ trigger EAS
→ finish orchestration job

EAS
→ continues build
```

If the CI system must wait for the build result, use the appropriate EAS workflow instead.

Do not use `--no-wait` if the next CI step assumes that the artifact already exists.

The correct choice depends on the workflow.

---

# 14. Explicit build profiles

Keep `eas.json` understandable.

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
      "distribution": "store",
      "environment": "production"
    }
  }
}
```

This is an example structure, not a universal configuration.

The important relationship is:

```text
development
→ development

preview
→ preview

production
→ production
```

EAS supports explicit `environment` values in build profiles. If the field is omitted, EAS can infer the environment from profile configuration, but explicit configuration is easier to review and reason about. citeturn0search7

---

# 15. EAS environment variables

Do not confuse GitHub Actions environment variables with EAS environment variables.

This matters because the EAS builder runs remotely.

Expo explicitly notes that environment variables need to be defined on EAS servers to be available to EAS Build builders. Setting a variable only inside GitHub Actions does not automatically make it available inside the EAS build environment. citeturn0search0

Recommended model:

```text
EAS environment variables
→ values needed by EAS Build

GitHub Actions secrets
→ values needed by GitHub Actions itself
```

Keep the two systems separate.

---

# 16. EAS environment model

EAS provides environments such as:

```text
development
preview
production
```

Variables can be scoped to these environments and can have visibility such as:

```text
plain text
sensitive
secret
```

Expo's current documentation recommends EAS environment variables when you want one environment configuration source for builds, updates, and workflows. citeturn0search1turn0search5

Example:

```bash
eas env:create \
  --name EXPO_PUBLIC_API_URL \
  --value https://api.example.com \
  --environment production \
  --visibility plaintext
```

List variables:

```bash
eas env:list --environment production
```

Do not print secret values in CI logs.

---

# 17. Client-visible variables are not secrets

Anything bundled into a mobile application can potentially be inspected.

Therefore:

```text
EXPO_PUBLIC_*
→ public
```

and:

```text
extra
→ public
```

should be treated accordingly.

EAS secret variables protect values used by EAS jobs, but they do not make a value secret if the value is ultimately embedded in the client application. Expo explicitly documents this distinction. citeturn0search1turn0search7

Never put:

```text
Database password
Private API key
Payment secret
OAuth client secret
Signing private key
```

into client-side configuration.

---

# 18. GitHub Actions secrets

GitHub Actions secrets can be stored at:

```text
Repository
Organization
Environment
```

Use the narrowest scope that works.

For example:

```text
EXPO_TOKEN
→ production release environment
```

rather than exposing it to every workflow.

GitHub documents repository, organization, and environment secrets and supports required reviewers for protected environment access. citeturn0search6turn0search3

---

# 19. GitHub production environment

Create a GitHub environment such as:

```text
production
```

Use it for the production submission job.

Example:

```yaml
jobs:
  submit:
    environment: production
```

Configure the environment with:

```text
Required reviewers
Deployment branch restrictions
Environment secrets
Protection rules
```

GitHub environments can require approval before a job proceeds and can restrict access to environment secrets until approval is granted. citeturn0search2turn0search3

This creates the required boundary:

```text
Automation prepares release
        ↓
Human approves production
        ↓
Protected job gets production secrets
        ↓
Submission executes
```

---

# 20. Human approval is a security boundary

Production submission should not normally be:

```text
Push
→ automatic public release
```

A safer default is:

```text
Release trigger
→ build
→ verify
→ approval
→ submit
```

The repository's documentation rules require explicit human approval for production releases, signing credentials, secrets, store submission, destructive actions, and other high-impact operations. fileciteturn19file3L10-L25

GitHub environment required reviewers provide a native mechanism for this approval gate. citeturn0search2turn0search9

---

# 21. Release triggers

Use an intentional release trigger.

Good options include:

```text
Git tag
workflow_dispatch
release event
approved release branch
```

For example:

```yaml
on:
  workflow_dispatch:
```

This is often a good starting point because it makes production release intentional.

Later, the project may adopt:

```text
v1.4.0
```

tags or GitHub Releases.

Do not add automatic release triggers until the manual process is stable.

---

# 22. Example release workflow

A simplified release workflow:

```yaml
name: Release

on:
  workflow_dispatch:

permissions:
  contents: read

jobs:
  build:
    name: Production build
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v5

      - name: Setup Node
        uses: actions/setup-node@v6
        with:
          node-version: 24
          cache: npm

      - name: Setup Expo and EAS
        uses: expo/expo-github-action@v8
        with:
          eas-version: latest
          token: ${{ secrets.EXPO_TOKEN }}

      - name: Install dependencies
        run: npm ci

      - name: Validate Expo project
        run: npx expo-doctor

      - name: Build production
        run: |
          eas build \
            --profile production \
            --platform all \
            --non-interactive \
            --no-wait

  submit:
    name: Submit production
    needs: build
    runs-on: ubuntu-latest
    environment: production

    steps:
      - name: Submit approved release
        run: echo "Run the project's verified EAS submission workflow here."
```

The final submission step is intentionally left project-specific.

Do not copy store credentials, API keys, or submission commands into a generic public example without verifying the project's actual credential and submission model.

---

# 23. Build and submit should be separate jobs

Prefer:

```text
build
  ↓
submit
```

instead of one large job.

Why?

Because the approval boundary belongs between them.

```text
Build
→ artifact exists

Approval
→ human accepts artifact

Submit
→ protected action
```

This also makes failure diagnosis easier.

A build failure is different from a submission failure.

---

# 24. Build once, submit the same artifact

Do not:

```text
Build
→ test

Build again
→ submit
```

unless there is a specific reason.

Prefer:

```text
Build
→ identify artifact
→ verify artifact
→ submit same artifact
```

The artifact should be traceable to:

```text
Git commit
Build profile
Environment
Version
Build number
EAS build ID
```

This avoids the dangerous situation where the artifact tested is not the artifact submitted.

---

# 25. Production artifact identity

For every release, record:

```text
Git commit
EAS build ID
Platform
Profile
Environment
Version
Build number
Artifact
```

Example:

```text
Commit:
8f3a1c2

EAS build:
abc123

Platform:
iOS

Profile:
production

Environment:
production

Version:
1.4.0

Build:
42
```

This becomes the release evidence.

Do not store secrets in the release record.

---

# 26. Concurrency

Production releases should not accidentally overlap.

GitHub Actions supports concurrency controls.

Example:

```yaml
concurrency:
  group: production-release
  cancel-in-progress: false
```

This can prevent multiple production release workflows from running simultaneously.

This matters when:

```text
Release A
```

and:

```text
Release B
```

could otherwise race to submit different artifacts.

GitHub documents concurrency as a way to ensure only one workflow/job in a concurrency group runs at a time. citeturn0search11

Use it where overlapping releases would create a real operational problem.

---

# 27. Do not cancel a production release blindly

For development workflows:

```yaml
cancel-in-progress: true
```

may be reasonable.

For production releases:

```yaml
cancel-in-progress: false
```

is usually safer.

You do not want a new workflow silently cancelling an already approved production operation.

If a release must be stopped:

```text
Stop intentionally
→ identify current state
→ verify store/build state
→ decide next action
```

---

# 28. CI validation matrix

A practical CI baseline:

| Check | PR | Main | Release |
|---|---:|---:|---:|
| Install dependencies | Yes | Yes | Yes |
| Typecheck | Yes | Yes | Yes |
| Lint | Yes | Yes | Yes |
| Unit tests | Yes | Yes | Yes |
| Expo Doctor | Yes | Yes | Yes |
| Dependency check | Optional | Yes | Yes |
| Resolved config | Optional | Yes | Yes |
| Preview build | Conditional | Conditional | No |
| Production build | No | No | Yes |
| Store submission | No | No | Approved release only |

The exact matrix should match project size and release risk.

Do not turn every check into a mandatory expensive CI job without evidence that it improves release confidence.

---

# 29. Native changes should trigger stronger validation

Not every change has the same release risk.

Examples of higher-risk changes:

```text
app.config.ts
eas.json
ios/
android/
native dependencies
permissions
entitlements
deep links
push
payments
authentication
Expo SDK
React Native version
```

For these changes, consider:

```text
CI
+
Preview build
+
Real-device testing
```

A useful rule is:

```text
JavaScript-only change
→ normal CI

Native/release configuration change
→ stronger release validation
```

The repository's project configuration guidance explicitly treats configuration-only changes as potentially release-affecting and recommends build/device validation where appropriate. fileciteturn19file12L1-L40

---

# 30. Expo SDK upgrades

An Expo SDK upgrade should not use the same lightweight path as a documentation change.

Use:

```text
Upgrade
→ dependency validation
→ native build
→ iOS testing
→ Android testing
→ release smoke tests
```

At minimum verify:

```text
Expo SDK
React Native
Native dependencies
Node
Xcode
Android tooling
Build profiles
Config plugins
```

Do not merge an SDK upgrade solely because JavaScript tests pass.

---

# 31. CI caching

GitHub Actions can cache package-manager dependencies.

For npm:

```yaml
- uses: actions/setup-node@v6
  with:
    node-version: 24
    cache: npm
```

The project should still use:

```bash
npm ci
```

for deterministic installation from the lockfile.

Do not cache arbitrary build outputs unless the cache behavior is understood.

EAS itself also has its own build/cache behavior. Keep GitHub Actions caching and EAS build caching conceptually separate.

---

# 32. Lockfile enforcement

CI should fail when dependency state is inconsistent.

Use:

```bash
npm ci
```

rather than:

```bash
npm install
```

for normal CI installation.

This ensures the lockfile is used as the dependency source.

For other package managers, use their equivalent deterministic CI installation command.

Do not update dependencies automatically during a production build.

---

# 33. Build environment consistency

The CI system should build the same application you expect to release.

Verify:

```text
Node
Package manager
Lockfile
Expo SDK
EAS CLI
Build profile
Environment
Native toolchain
Signing
```

The project configuration guidance explicitly requires CI to avoid silently using a different dependency or build environment from the one used during release validation. fileciteturn19file12L1-L20

---

# 34. GitHub Actions vs EAS Workflows

Both can automate Expo release work.

## GitHub Actions

Useful when:

```text
Repository already uses GitHub Actions
PR checks live in GitHub
Team wants GitHub-native approval
Custom CI tooling is needed
```

## EAS Workflows

Useful when:

```text
The workflow is tightly coupled to EAS
Build/update/submit jobs should live with Expo tooling
The team wants EAS-native workflow definitions
```

Do not use both for the same job without a clear reason.

For example, avoid:

```text
GitHub Actions
→ triggers EAS Workflow
→ which triggers another GitHub Action
→ which triggers EAS Build
```

That adds orchestration without adding useful control.

Pick one primary orchestration layer for each workflow.

---

# 35. Recommended starting point

For a small or medium Expo project:

```text
GitHub Actions
→ CI validation

EAS
→ mobile builds

GitHub Environment
→ production approval

EAS Submit
→ store submission
```

This is usually enough.

Do not add:

```text
Jenkins
CircleCI
GitLab CI
custom release server
Kubernetes
message queues
```

unless the repository has a real requirement for them.

The playbook explicitly prefers simple documentation, scripts, AI workflows, and community contributions over unnecessary infrastructure. fileciteturn19file2L1-L35

---

# 36. Submission workflow

A safe release flow is:

```text
Release trigger
    ↓
Production EAS build
    ↓
Build succeeds
    ↓
Identify artifact
    ↓
Verify artifact
    ↓
Human approval
    ↓
EAS Submit
    ↓
Store processing
    ↓
TestFlight / Play testing
    ↓
Production release
```

Do not assume:

```text
EAS build succeeded
```

means:

```text
Store submission succeeded
```

or:

```text
App Store / Play review passed
```

Those are separate stages.

---

# 37. Store submission credentials

Store credentials are high-impact credentials.

Examples:

```text
Apple App Store Connect API credentials
Google Play service-account credentials
```

Protect them using:

```text
GitHub environment secrets
EAS secrets
official credential storage
```

depending on the workflow.

Never commit:

```text
*.p8
service-account.json
private keys
```

to the repository.

The security baseline explicitly requires secrets to stay out of source code, Git history, frontend bundles, and logs. fileciteturn19file7L1-L35

---

# 38. Production approval

A production job should reference:

```yaml
environment: production
```

and the GitHub repository should configure:

```text
Required reviewer
```

for that environment.

The approval should happen after:

```text
Build
+
Validation
+
Artifact identification
```

and before:

```text
Store submission
```

GitHub's environment protection model ensures protected environment secrets are not available to the job until the required protection rules pass. citeturn0search2turn0search3

---

# 39. AI-assisted CI/CD

AI can help with CI/CD without owning production.

Useful tasks:

```text
Read CI failure
→ identify first meaningful error

Inspect PR
→ identify release-affecting changes

Audit configuration
→ find environment mismatch

Review dependency change
→ identify native risk

Generate release notes
→ summarize verified changes

Prepare release
→ check missing steps
```

A safe model is:

```text
AI
→ inspect

AI
→ propose

Developer
→ verify

CI
→ test

Human
→ approve

Automation
→ execute
```

The repository's documentation rules explicitly state that AI output must be treated as untrusted until inspected, verified, tested, and approved. fileciteturn19file10L1-L30

---

# 40. AI should not own production credentials

Do not give an AI agent unrestricted access to:

```text
Apple production account
Google Play production account
Signing keys
Production secrets
Production infrastructure
```

Prefer:

```text
AI
→ read-only inspection
→ diagnostics
→ draft changes
→ prepare release

Human
→ approval

Protected CI
→ production action
```

This keeps the AI useful without making it the final authority.

---

# 41. AI release audit workflow

A useful workflow before production:

```text
AI reads:
- git diff
- app.config.ts
- eas.json
- package.json
- lockfile
- CI workflow
- release metadata

AI checks:
- identifiers
- environment
- version
- dependencies
- native changes
- secrets
- CI permissions
- release blockers

AI returns:
- confirmed issues
- likely issues
- missing evidence
- recommended checks

Developer verifies

CI runs

Human approves
```

Do not let the agent automatically modify production configuration based only on its own findings.

---

# 42. AI prompt for CI failure analysis

```text
Analyze this Expo CI/CD failure.

Repository:
<repository>

Workflow:
<workflow name>

Commit:
<commit>

Platform:
<android / ios / both>

Profile:
<development / preview / production>

Environment:
<environment>

Failure logs:
<paste logs>

Return:

1. FIRST REAL ERROR
2. DIRECT CAUSE
3. EVIDENCE
4. LIKELY ROOT CAUSE
5. HYPOTHESES THAT ARE NOT YET PROVEN
6. MINIMAL FIX
7. VERIFICATION STEPS
8. RELEASE RISK
9. WHETHER A NEW BUILD IS REQUIRED

Rules:

- Do not invent Expo commands.
- Do not invent platform requirements.
- Do not recommend unrelated changes.
- Separate evidence from inference.
- Treat production configuration as sensitive.
- Do not modify files.
- If evidence is insufficient, say NEEDS MORE EVIDENCE.
```

This follows the project's AI reliability rule:

```text
AI suggestion
→ inspect
→ verify
→ test
→ approve
```

not:

```text
AI says it works
→ ship
```

fileciteturn19file10L1-L30

---

# 43. AI pull-request release-risk review

AI can classify changes:

```text
LOW
MEDIUM
HIGH
```

Example:

```text
README change
→ LOW

React component change
→ LOW / MEDIUM

API configuration change
→ MEDIUM

app.config.ts
→ HIGH

eas.json
→ HIGH

ios/
android/
→ HIGH

Signing configuration
→ HIGH

Payment configuration
→ HIGH
```

This should be advisory.

Do not make AI the sole authority for release risk.

The repository's governance rules require AI-generated changes to remain aligned with architecture, maintainability, operational safety, and human review. fileciteturn19file16L1-L35

---

# 44. Security review of CI/CD

Review:

```text
Workflow triggers
Permissions
Secrets
Environment protection
Third-party actions
EAS token
Store credentials
Artifact access
Logs
Pull requests
Forks
Self-hosted runners
```

Important questions:

```text
Can an untrusted PR access production secrets?

Can a contributor trigger production submission?

Can a workflow modify the repository?

Can a compromised action access credentials?

Can two releases race?

Can logs expose secrets?

Can AI trigger a high-impact action?
```

Prioritize realistic attack paths rather than theoretical security theater. The security system requires least privilege, protected secrets, explicit authorization, and testable controls. fileciteturn19file8L1-L45

---

# 45. Third-party GitHub Actions

Every third-party action is part of your release supply chain.

Before adding an action:

```text
Who maintains it?
What permissions does it need?
What secrets can it access?
Is it necessary?
Is the version pinned?
```

Avoid adding an action for trivial shell commands.

For example, if:

```bash
npm ci
```

solves the problem, do not add a third-party action simply to run npm.

The security baseline explicitly treats dependencies, CI/CD permissions, build secrets, and untrusted scripts as supply-chain concerns. fileciteturn19file13L1-L35

---

# 46. Pinning actions

For critical release workflows, consider pinning third-party actions to a known version or immutable reference according to the team's security policy.

Avoid blindly depending on:

```yaml
uses: some/action@latest
```

for high-impact production operations.

However, do not make the workflow unreadable through excessive pinning without a maintenance process.

The goal is:

```text
Known dependency
+
Reviewable updates
+
Controlled change
```

---

# 47. Fork safety

Public repositories must assume pull requests can come from forks.

Do not allow forked PRs to access:

```text
Production secrets
EAS production token
Store credentials
Signing credentials
```

Use separate workflows for:

```text
Untrusted PR validation
```

and:

```text
Trusted release automation
```

Never solve fork security by giving all PRs access to production credentials.

---

# 48. Self-hosted runners

Avoid self-hosted runners unless there is a real requirement.

A self-hosted runner can have access to:

```text
filesystem
network
credentials
cached data
```

and may not provide the same isolation assumptions as a managed runner.

For a normal Expo release pipeline:

```text
GitHub-hosted runner
+
EAS cloud build
```

is simpler.

The repository's architecture explicitly avoids unnecessary infrastructure and operational burden. fileciteturn19file19L1-L35

---

# 49. Observability for CI/CD

Every release should be traceable.

Record:

```text
Workflow run
Commit
Build ID
Platform
Profile
Environment
Version
Build number
Submission status
```

A failed release should answer:

```text
What changed?
Which commit?
Which workflow?
Which build?
Which environment?
Which artifact?
Where did it fail?
```

Do not rely on screenshots or memory.

---

# 50. Release failure handling

If the production build fails:

```text
No submission
↓
Inspect logs
↓
Identify first meaningful error
↓
Fix
↓
Re-run validation
↓
Build again
```

If submission fails:

```text
Do not automatically rebuild

↓
Identify whether failure is:
- credentials
- metadata
- store processing
- platform validation
- artifact problem

↓
Fix the actual problem
↓
Reuse the correct artifact where possible
```

Do not rebuild just because submission failed.

---

# 51. Partial failure

Suppose:

```text
iOS build
→ SUCCESS

Android build
→ FAIL
```

Do not throw away the successful iOS artifact automatically.

Record:

```text
iOS:
ready

Android:
failed
```

Then determine whether:

```text
iOS can proceed independently
```

or:

```text
release policy requires both platforms
```

Do not introduce cross-platform orchestration just to force both platforms to succeed together.

---

# 52. Rollback

CI/CD should not pretend that mobile rollback is identical to a server rollback.

For a mobile release:

```text
Previous binary
→ remains installed

New binary
→ may already be distributed
```

Possible responses include:

```text
Stop rollout
Pause staged rollout
Fix server behavior
Release hotfix
Use compatible OTA update where appropriate
```

The correct response depends on the failure and whether the change is native or JavaScript-only.

See:

```text
release-strategy/rollback.md
post-release/rollback.md
release-strategy/hotfixes.md
```

Do not design a custom rollback system inside CI/CD unless the product actually needs one.

---

# 53. OTA updates and CI/CD

If the project uses Expo Updates:

```text
Native build
→ establishes runtime compatibility

OTA update
→ updates compatible JavaScript
```

CI/CD must keep:

```text
build environment
+
update environment
+
runtimeVersion
```

consistent.

Expo's current environment documentation notes that EAS Update uses an explicit environment and that build/workflow environments should be kept in sync where they are paired. citeturn0search7turn0search8

Do not let CI publish an update against the wrong environment.

---

# 54. Build vs update

A useful decision rule:

```text
JavaScript-only compatible change
→ EAS Update may be appropriate

Native change
→ new EAS Build
```

Examples that generally require a new native build include changes to:

```text
Native dependencies
Permissions
Entitlements
Bundle ID
Application ID
Native plugins
Native capabilities
```

Always verify compatibility with the project's actual Expo Updates/runtime configuration.

---

# 55. Release tagging

A mature project can use Git tags:

```text
v1.4.0
```

as the production release trigger.

Example:

```yaml
on:
  push:
    tags:
      - 'v*.*.*'
```

This creates a useful relationship:

```text
Git tag
→ release version
→ workflow
→ EAS build
→ artifact
→ store submission
```

Only use tag-based production releases after the release process is stable.

---

# 56. Manual release workflow

For a smaller project, this is often enough:

```text
Developer
→ merge to main

Developer
→ GitHub Actions CI passes

Developer
→ GitHub Actions "Run workflow"

CI
→ production EAS build

Developer
→ inspect result

GitHub
→ production environment approval

CI
→ submit
```

This is not a weakness.

It is often the safest level of automation for a small team.

---

# 57. Recommended maturity path

## Stage 1

```text
PR CI
+
manual EAS builds
+
manual store submission
```

## Stage 2

```text
PR CI
+
preview EAS builds
+
production EAS build workflow
+
human approval
```

## Stage 3

```text
PR CI
+
release tag
+
production build
+
artifact verification
+
protected submission
+
post-release monitoring
```

## Stage 4

Add only proven improvements:

```text
automated release notes
AI release audit
automated metadata checks
automated smoke tests
release dashboards
```

Do not skip directly to Stage 4.

---

# 58. What not to automate

Keep human approval for:

```text
Production submission
Signing credential changes
Store account changes
Irreversible configuration
High-impact financial changes
Destructive actions
```

The project documentation explicitly requires human approval for high-impact release actions. fileciteturn19file3L10-L25

---

# 59. What is safe to automate

Good candidates:

```text
Dependency installation
Linting
Typechecking
Unit tests
Expo Doctor
Configuration validation
Build triggering
Artifact collection
Release-note drafting
PR summaries
Failure classification
Non-production preview builds
```

Automation should reduce repetitive work without removing control over consequential actions.

---

# 60. CI/CD troubleshooting

## Workflow never starts

Check:

```text
Workflow trigger
Branch
Path filters
Permissions
GitHub Actions enabled
```

---

## EAS authentication fails

Check:

```text
EXPO_TOKEN
Secret scope
Workflow environment
EAS account/project
Token validity
```

Do not print the token.

---

## EAS build cannot see environment variables

Check:

```text
EAS environment
eas.json
environment field
EAS variable scope
variable visibility
```

Remember:

```text
GitHub Actions environment
≠
EAS Build environment
```

Expo explicitly documents this distinction. citeturn0search0

---

## Production job cannot access secrets

Check:

```text
GitHub environment
Required approval
Secret scope
Job environment declaration
```

Protected environment secrets become available only after the environment's protection rules pass. citeturn0search2turn0search3

---

## Build succeeds but app uses staging

Check:

```text
EAS environment
app.config.ts
EXPO_PUBLIC_API_URL
eas.json
resolved config
```

Then rebuild.

---

## CI passes but production build fails

Check:

```text
EAS build logs
Native dependencies
Build profile
Environment
Signing
Node/toolchain
```

Do not assume PR CI proves the native build will succeed.

---

# 61. CI/CD release checklist

## Repository

- [ ] Lockfile committed.
- [ ] CI workflow exists.
- [ ] Production workflow is separate from PR validation.
- [ ] Workflow permissions are minimal.
- [ ] Third-party actions are reviewed.
- [ ] Release triggers are intentional.

## Expo

- [ ] EAS project initialized.
- [ ] `eas.json` reviewed.
- [ ] Build profiles are explicit.
- [ ] Environment mapping is correct.
- [ ] Resolved configuration is validated.
- [ ] Expo Doctor passes.

## Environments

- [ ] Development environment exists.
- [ ] Preview environment exists if needed.
- [ ] Production environment exists.
- [ ] Environment variables are assigned correctly.
- [ ] Client-visible values are treated as public.

## Secrets

- [ ] Production secrets are protected.
- [ ] PR workflows cannot access production secrets.
- [ ] GitHub production environment is protected.
- [ ] EAS secrets are used where appropriate.
- [ ] No credentials are committed.
- [ ] No credentials are printed in logs.

## Build

- [ ] Correct profile selected.
- [ ] Correct environment selected.
- [ ] Production build is explicit.
- [ ] Build ID recorded.
- [ ] Artifact identified.
- [ ] Build logs accessible.

## Approval

- [ ] Artifact has been verified.
- [ ] Human approval is required.
- [ ] Production environment protection is enabled.
- [ ] Submission credentials are only available after approval.

## Submission

- [ ] Correct artifact is submitted.
- [ ] Store metadata is ready.
- [ ] Submission result is recorded.
- [ ] Post-release monitoring is ready.

---

# 62. Definition of done

Expo CI/CD is production-ready when:

```text
Pull requests
→ validated

Main branch
→ remains buildable

Release trigger
→ intentional

EAS build
→ reproducible enough to trust

Environment
→ explicit

Secrets
→ protected

Artifact
→ identifiable

Production submission
→ human-approved

Failures
→ observable and diagnosable
```

The target is not maximum automation.

The target is:

> **The smallest CI/CD system that makes mobile releases safer, repeatable, and easier to debug.**

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
- `frameworks/expo/eas.md`
- `frameworks/expo/submit.md`
- `frameworks/expo/common-failures.md`

### Release engineering

- `release-engineering/build-systems.md`
- `release-engineering/release-configurations.md`
- `release-engineering/eas/`
- `release-engineering/github-actions/`
- `release-engineering/app-store-connect-api.md`
- `release-engineering/google-play-api.md`
- `release-engineering/environment-management.md`
- `release-engineering/release-pipelines.md`

### Signing

- `signing/ios/`
- `signing/android/`
- `signing/security/`

### Testing

- `testing/release-smoke-tests.md`
- `testing/device-testing.md`
- `testing/ios.md`
- `testing/android.md`
- `testing/upgrade-migrations.md`

### Release strategy

- `release-strategy/beta-testing.md`
- `release-strategy/staged-rollouts.md`
- `release-strategy/feature-flags.md`
- `release-strategy/hotfixes.md`
- `release-strategy/emergency-release.md`

### Post-release

- `post-release/monitoring.md`
- `post-release/incident-response.md`
- `post-release/rollback.md`
- `post-release/hotfix.md`

### AI

- `ai/workflows/release-audit.md`
- `ai/workflows/release-preparation.md`
- `ai/workflows/debugging.md`
- `ai/security/secret-protection.md`
- `ai/security/agent-permissions.md`
- `ai/security/destructive-actions.md`
- `ai/evals/`

---

# Official sources

These are the primary sources to verify current CI/CD behavior.

### Expo

- EAS Build on CI: https://docs.expo.dev/build/building-on-ci/
- EAS environment variables: https://docs.expo.dev/eas/environment-variables/
- Managing EAS environment variables: https://docs.expo.dev/eas/environment-variables/manage/
- Using EAS environment variables: https://docs.expo.dev/eas/environment-variables/usage/
- EAS Workflows environment variables: https://docs.expo.dev/eas/workflows/environment/
- EAS Build configuration: https://docs.expo.dev/build/eas-json/
- EAS CLI: https://docs.expo.dev/eas/cli/
- Expo app configuration: https://docs.expo.dev/versions/latest/config/app/

### GitHub

- GitHub Actions environments: https://docs.github.com/en/actions/concepts/workflows-and-actions/deployment-environments
- GitHub Actions secrets: https://docs.github.com/en/actions/concepts/security/secrets
- GitHub deployment controls: https://docs.github.com/en/actions/how-tos/deploy/configure-and-manage-deployments/control-deployments
- Reviewing deployments: https://docs.github.com/en/actions/how-tos/deploy/configure-and-manage-deployments/review-deployments

### Platform stores

- Apple Developer: https://developer.apple.com/
- App Store Connect Help: https://developer.apple.com/help/app-store-connect/
- Android Developers: https://developer.android.com/
- Google Play Console Help: https://support.google.com/googleplay/android-developer/

**Last verified:** August 11, 2026

CI/CD tooling, GitHub Actions behavior, Expo/EAS commands, build images, environment handling, and store requirements change. Verify the current official documentation before changing a production workflow.
