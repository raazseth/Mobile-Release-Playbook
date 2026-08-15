# React Native with Fastlane

Fastlane is an automation layer for mobile release work.

For a React Native project, it can automate parts of:

```text
Testing
Versioning
Signing
Building
TestFlight uploads
App Store uploads
Google Play uploads
Store metadata
Screenshots
Release notes
CI/CD
```

Fastlane does not replace:

```text
React Native
Xcode
Gradle
Apple Developer
App Store Connect
Google Play Console
```

It coordinates these tools and services.

The repository should therefore keep Fastlane guidance focused on **release automation**, while platform-specific release behavior remains in:

```text
frameworks/react-native/android-release.md
frameworks/react-native/ios-release.md

signing/
publishing/
testing/
release-engineering/
```

The current fastlane documentation describes fastlane as a tool for automating beta deployments and releases for iOS and Android. Its React Native documentation points developers to the general iOS/Android workflows because fastlane does not maintain a separate full React Native release implementation. citehttps://docs.fastlane.tools/ citehttps://docs.fastlane.tools/getting-started/cross-platform/react-native/

---

# 1. Where Fastlane fits

Without Fastlane:

```text
Developer
   ↓
Run tests
   ↓
Run Gradle / Xcode
   ↓
Handle signing
   ↓
Create artifact
   ↓
Upload
   ↓
Update metadata
   ↓
Release
```

With Fastlane:

```text
Developer / CI
      ↓
Fastlane lane
      ↓
Tests
      ↓
Build
      ↓
Signing
      ↓
Artifact
      ↓
Store upload
      ↓
Release workflow
```

The important difference is:

> Fastlane automates a known release process. It does not decide what a safe release process should be.

---

# 2. When Fastlane is useful

Fastlane is useful when the project repeatedly performs the same release operations.

Good use cases:

```text
[ ] Repeatable iOS builds
[ ] Repeatable Android builds
[ ] TestFlight uploads
[ ] Google Play uploads
[ ] Signing automation
[ ] Store metadata management
[ ] Screenshot automation
[ ] Release notes
[ ] CI/CD
[ ] Release validation
```

It becomes especially useful when:

```text
Manual release steps
        ↓
Repeated mistakes
        ↓
Need for CI
        ↓
Need for one reproducible command
```

Do not introduce Fastlane just because the project is a mobile app.

For a very small project with a simple release process, native commands or EAS may already be sufficient.

---

# 3. Fastlane is not required for React Native

A React Native application can be released without Fastlane.

Possible release stacks include:

```text
React Native
+
Xcode / Gradle
```

or:

```text
React Native
+
EAS
```

or:

```text
React Native
+
Fastlane
+
Xcode / Gradle
```

Choose the smallest release stack that solves the actual problem.

Do not use both EAS and Fastlane for the same release operation unless there is a clear reason.

For example:

```text
EAS Build
+
Fastlane store automation
```

can be valid in a deliberate workflow.

But:

```text
EAS Build
+
Fastlane Build
+
another CI build
```

for the same artifact usually creates unnecessary complexity.

---

# 4. Fastlane architecture

A simple React Native repository may look like:

```text
mobile-app/
├── android/
├── ios/
├── src/
├── package.json
├── Gemfile
├── Gemfile.lock
└── fastlane/
    ├── Appfile
    ├── Fastfile
    ├── Matchfile
    ├── Deliverfile
    └── metadata/
```

Not every project needs every file.

Start with:

```text
Gemfile
fastlane/Fastfile
fastlane/Appfile
```

Add other files only when they provide real value.

---

# 5. Install Fastlane

Fastlane's current setup documentation recommends using Bundler with a `Gemfile` so the project pins its Fastlane dependency and its Ruby dependencies. citehttps://docs.fastlane.tools/getting-started/android/setup/

Create:

```ruby
source "https://rubygems.org"

gem "fastlane"
```

Then:

```bash
bundle install
```

Run Fastlane through Bundler:

```bash
bundle exec fastlane <lane>
```

This is preferable to relying on an unknown globally installed Fastlane version.

For CI:

```bash
bundle install
bundle exec fastlane <lane>
```

Commit:

```text
Gemfile
Gemfile.lock
```

so the release tooling is reproducible.

---

# 6. Check the installation

Run:

```bash
bundle exec fastlane --version
```

Then:

```bash
bundle exec fastlane lanes
```

The first command confirms the installed version.

The second shows the lanes defined by the project.

Do not start by running a production lane.

First verify that Fastlane itself loads correctly.

---

# 7. Initialize Fastlane

From the project root:

```bash
bundle exec fastlane init
```

Fastlane creates its configuration under:

```text
fastlane/
```

For React Native, review the generated files before committing them.

Do not accept generated configuration blindly.

Confirm:

```text
Bundle identifier
Android package name
Apple team
Google Play configuration
Authentication method
```

---

# 8. `Appfile`

`Appfile` contains application-level identifiers and account configuration.

Example:

```ruby
app_identifier("com.example.app")
```

For Android, the package name may also be configured depending on the setup.

Keep identifiers consistent with:

```text
android/app/build.gradle
iOS project
App Store Connect
Google Play Console
```

Do not make Fastlane a second source of truth for application identity.

The actual application configuration must agree across the project.

See:

```text
foundations/identifiers.md
foundations/project-configuration.md
```

---

# 9. `Fastfile`

The `Fastfile` defines lanes.

A lane should represent a meaningful release operation.

Example:

```ruby
default_platform(:ios)

platform :ios do
  lane :beta do
    build_app
    upload_to_testflight
  end
end
```

The important idea is:

```text
lane
=
repeatable release workflow
```

Not:

```text
lane
=
random collection of shell commands
```

Keep lanes small enough to understand.

---

# 10. Lane naming

Prefer names based on intent:

```text
test
build
beta
release
screenshots
submit
```

For larger projects:

```text
android_beta
android_release
ios_beta
ios_release
```

Avoid:

```text
do_everything
final_final_release
production_new_v2
magic
```

A lane name should tell the developer what it does.

---

# 11. Separate build from submission

Prefer:

```text
build
   ↓
verify
   ↓
submit
```

rather than making every lane automatically publish.

For example:

```ruby
lane :build_release do
  gradle(task: "bundle", build_type: "Release")
end

lane :submit do
  upload_to_play_store(track: "internal")
end
```

This makes it possible to:

```text
Build
→ inspect artifact
→ test
→ approve
→ submit
```

without rebuilding unnecessarily.

---

# 12. Human approval boundary

A production lane should not silently turn:

```text
git push
```

into:

```text
production release
```

Prefer:

```text
CI
 ↓
Build
 ↓
Validation
 ↓
Artifact
 ↓
Human approval
 ↓
Production submission
```

Fastlane can automate the execution.

It should not remove the approval boundary.

The repository architecture explicitly requires human approval for high-impact release actions.

---

# 13. Android release flow

A typical Android Fastlane flow is:

```text
Fastlane
   ↓
Gradle
   ↓
AAB
   ↓
Google Play
```

Fastlane delegates Android builds to Gradle and can upload the resulting artifact to Google Play. citehttps://docs.fastlane.tools/getting-started/android/release-deployment/

Example:

```ruby
platform :android do
  lane :build_release do
    gradle(
      task: "bundle",
      build_type: "Release"
    )
  end

  lane :internal do
    gradle(
      task: "bundle",
      build_type: "Release"
    )

    upload_to_play_store(
      track: "internal"
    )
  end
end
```

Adapt the task and variant to the actual React Native Android project.

---

# 14. Prefer AAB for Google Play

For Google Play production workflows, the Android artifact is normally:

```text
.aab
```

Fastlane's `upload_to_play_store` / `supply` supports uploading Android App Bundles. citehttps://docs.fastlane.tools/actions/supply/

Example:

```ruby
upload_to_play_store(
  aab: "android/app/build/outputs/bundle/release/app-release.aab",
  track: "internal"
)
```

The exact output path may differ for:

```text
flavors
custom variants
custom Gradle configuration
```

Do not hard-code an artifact path without checking the project.

---

# 15. Android testing tracks

Fastlane can upload to Google Play tracks.

Examples include:

```text
internal
alpha
beta
production
```

A safer release flow is:

```text
Build
 ↓
Internal testing
 ↓
Validation
 ↓
Production
```

For gradual rollout, Fastlane's `upload_to_play_store` supports rollout configuration. citehttps://docs.fastlane.tools/actions/supply/

Example:

```ruby
upload_to_play_store(
  track: "production",
  rollout: "0.1"
)
```

Do not use gradual rollout automatically for every application.

Use it when the product's release strategy benefits from staged exposure.

See:

```text
release-strategy/staged-rollouts.md
```

---

# 16. Android store metadata

Fastlane's `supply` can manage Google Play metadata and store assets.

A typical structure is:

```text
fastlane/
└── metadata/
    └── android/
        ├── en-US/
        │   ├── title.txt
        │   ├── short_description.txt
        │   ├── full_description.txt
        │   └── changelogs/
        └── ...
```

Fastlane documents `supply init` for downloading existing metadata and `supply` for uploading local metadata. citehttps://docs.fastlane.tools/actions/supply/

Treat store metadata as release content.

Review it before publishing.

---

# 17. Google Play authentication

Fastlane's current Android setup uses a Google Developer service account for Play API access.

The general flow is:

```text
Google Cloud project
        ↓
Google Play Developer API
        ↓
Service account
        ↓
Play Console access
        ↓
Fastlane
```

Fastlane documents service-account configuration for `supply`. citehttps://docs.fastlane.tools/getting-started/android/setup/

Do not commit the service-account JSON key.

Store it in protected CI secret storage or another approved secret manager.

---

# 18. Android credentials in CI

A CI workflow should provide Google Play credentials without committing them.

Conceptually:

```text
GitHub secret
      ↓
CI environment
      ↓
Fastlane
      ↓
Google Play API
```

Do not:

```text
credentials.json
```

in the repository.

Do not print the JSON key into CI logs.

Use the least privilege required by the release workflow.

---

# 19. Android signing

Fastlane does not eliminate Android signing.

The underlying flow remains:

```text
Keystore
 ↓
Gradle signing configuration
 ↓
AAB
 ↓
Google Play
```

Keep the signing model documented in:

```text
signing/android/
```

Fastlane should orchestrate the release.

It should not become the only place where the team understands how signing works.

---

# 20. React Native Android build

Fastlane delegates to the native Android build system.

That means React Native Android failures should be debugged through:

```text
Gradle
Android Gradle Plugin
JDK
Android SDK
Native modules
```

Use:

```text
frameworks/react-native/android-release.md
frameworks/react-native/common-failures.md
```

for the actual build/debugging model.

Fastlane should not hide the underlying Gradle command.

---

# 21. iOS release flow

A typical iOS Fastlane flow is:

```text
Fastlane
   ↓
Xcode
   ↓
IPA
   ↓
TestFlight / App Store Connect
```

Fastlane's iOS workflow uses `build_app` to create the IPA and `upload_to_testflight` or `deliver` to distribute it. citehttps://docs.fastlane.tools/getting-started/ios/beta-deployment/

Example:

```ruby
platform :ios do
  lane :beta do
    build_app(
      scheme: "MyApp"
    )

    upload_to_testflight
  end
end
```

Replace:

```text
MyApp
```

with the actual project scheme.

---

# 22. iOS workspace vs project

React Native applications commonly use CocoaPods.

The build may therefore need:

```text
.xcworkspace
```

rather than:

```text
.xcodeproj
```

Example:

```ruby
build_app(
  workspace: "ios/MyApp.xcworkspace",
  scheme: "MyApp"
)
```

Verify the actual project structure.

Do not hard-code `.xcodeproj` when the application depends on Pods.

---

# 23. iOS signing

Fastlane provides several signing tools.

Important ones include:

```text
match
cert
sigh
```

`match` is commonly used to synchronize signing certificates and provisioning profiles across developers and CI. Fastlane's current documentation supports API-key authentication for `match` as well. citehttps://docs.fastlane.tools/actions/match/

A typical workflow is:

```text
match
 ↓
Certificates + profiles
 ↓
Xcode build
 ↓
IPA
```

See:

```text
signing/ios/
```

before choosing a signing strategy.

---

# 24. `match`

`match` can centralize signing assets for a team.

Example:

```ruby
lane :certificates do
  match(type: "appstore")
end
```

A project may use different signing types for:

```text
development
adhoc
appstore
```

Do not create signing repositories or credential systems without deciding:

```text
Who owns them?
Who can access them?
Where are they stored?
How are they recovered?
How are they rotated?
```

Fastlane documents multiple storage modes for `match`, including Git and cloud storage options. citehttps://docs.fastlane.tools/actions/match/

---

# 25. `match` security

If using Git storage for signing assets:

```text
Signing repository
```

must be protected.

The fact that the repository is encrypted by Fastlane does not mean its access can be unrestricted.

Protect:

```text
Match encryption password
Repository credentials
Certificates
Provisioning profiles
```

Never put the match password directly into:

```text
Fastfile
public Git repository
CI logs
AI prompts
```

---

# 26. App Store Connect API authentication

For App Store Connect automation, Fastlane currently recommends API-key authentication when possible.

The official Fastlane documentation states that API-key authentication:

```text
Does not require 2FA
Uses the documented API
Provides better performance/reliability
```

and is supported by actions including:

```text
pilot
deliver
sigh
cert
match
precheck
```

with some differences between tools. citehttps://docs.fastlane.tools/app-store-connect-api/

Prefer API keys for CI when the required action supports them.

---

# 27. App Store Connect API key

Fastlane's `app_store_connect_api_key` action accepts:

```ruby
app_store_connect_api_key(
  key_id: ENV["APP_STORE_CONNECT_KEY_ID"],
  issuer_id: ENV["APP_STORE_CONNECT_ISSUER_ID"],
  key_filepath: ENV["APP_STORE_CONNECT_KEY_PATH"]
)
```

Do not hard-code real credentials.

The `.p8` private key is sensitive.

Fastlane's current documentation notes that the downloaded `.p8` key cannot be downloaded again after the relevant page is refreshed, so protect the original securely. citehttps://docs.fastlane.tools/app-store-connect-api/

---

# 28. TestFlight with `pilot`

Fastlane calls TestFlight automation:

```text
pilot
```

or:

```text
upload_to_testflight
```

Example:

```ruby
lane :beta do
  build_app(
    workspace: "ios/MyApp.xcworkspace",
    scheme: "MyApp"
  )

  upload_to_testflight(
    changelog: "Bug fixes and performance improvements"
  )
end
```

Fastlane's current documentation supports API-key authentication for TestFlight uploads. citehttps://docs.fastlane.tools/actions/pilot/

---

# 29. TestFlight processing

Uploading an IPA is not the same as:

```text
Build available to testers
```

App Store Connect still needs to process the build.

A release workflow should account for:

```text
Build upload
 ↓
Processing
 ↓
Build availability
 ↓
Tester distribution
```

Do not treat:

```text
upload succeeded
```

as:

```text
TestFlight validation completed
```

---

# 30. App Store submission with `deliver`

Fastlane's:

```text
deliver
```

can upload:

```text
Metadata
Screenshots
App previews
Binary
```

and can submit the app for review. citehttps://docs.fastlane.tools/actions/appstore/

Example:

```ruby
lane :submit do
  deliver(
    submit_for_review: true,
    skip_metadata: true,
    skip_screenshots: true
  )
end
```

Be careful with:

```text
submit_for_review
automatic_release
phased_release
```

These are production-impacting operations.

Prefer explicit approval before executing them.

---

# 31. Do not auto-submit by default

Avoid making:

```ruby
deliver(
  submit_for_review: true
)
```

the default production lane unless the team has intentionally chosen that workflow.

A safer pattern is:

```text
build
 ↓
TestFlight
 ↓
Validation
 ↓
Human approval
 ↓
submit
```

This prevents an accidental CI run from submitting a release before the team has reviewed it.

---

# 32. App Store metadata

Fastlane can keep metadata in the repository.

Example:

```text
fastlane/
├── metadata/
│   ├── en-US/
│   ├── en-GB/
│   └── ...
└── screenshots/
```

Store metadata can include:

```text
Description
Keywords
What's New
Promotional text
Review information
```

Do not place secrets inside metadata.

Review generated or AI-written metadata before publishing.

---

# 33. Screenshots

Fastlane supports automated screenshot workflows.

For Android, `screengrab` can generate screenshots that can then be uploaded through `supply`. citehttps://docs.fastlane.tools/getting-started/android/screenshots/

For iOS, Fastlane provides screenshot automation through its screenshot tooling.

Use screenshot automation when:

```text
Many locales
Many device sizes
Frequent releases
Stable UI
```

Do not automate screenshots just to add another tool.

---

# 34. Release notes

Fastlane can generate or accept release notes.

A useful pattern is:

```text
Git commits
 ↓
Draft changelog
 ↓
Developer review
 ↓
Store release notes
```

Fastlane documents `changelog_from_git_commits` for generating changelog content from Git history. citehttps://docs.fastlane.tools/getting-started/android/beta-deployment/

AI can also draft release notes, but the final content should be reviewed.

Do not expose internal implementation details in public store notes.

---

# 35. Testing with Fastlane

Fastlane can run existing platform tests.

Android example:

```ruby
lane :test do
  gradle(task: "test")
end
```

Fastlane's Android testing documentation shows this pattern and supports running Fastlane lanes in CI. citehttps://docs.fastlane.tools/getting-started/android/running-tests/

For React Native, keep actual test ownership in:

```text
testing/
```

Fastlane should orchestrate tests rather than redefine the project's test strategy.

---

# 36. Fastlane and E2E testing

Fastlane can orchestrate external E2E tooling.

For example:

```text
Fastlane
   ↓
Build
   ↓
Install
   ↓
Detox / other E2E tool
   ↓
Results
```

Do not force E2E testing into Fastlane if the existing CI workflow already handles it cleanly.

Use Fastlane as the coordinator only when that makes the release path simpler.

---

# 37. Pre-release lane

A useful pre-release lane can perform deterministic checks.

Example:

```ruby
lane :pre_release do
  gradle(task: "test")
end
```

For a larger project:

```text
Dependency validation
+
Tests
+
Build
+
Artifact validation
```

Do not put subjective decisions into automation.

For example:

```text
"Is this feature ready?"
```

should not be reduced to a Fastlane boolean.

---

# 38. Release lane design

A production lane should make the critical sequence visible.

Example:

```ruby
lane :release do
  ensure_git_status_clean
  build_app
  upload_to_testflight
end
```

For Android:

```ruby
lane :release do
  ensure_git_status_clean
  gradle(task: "bundle", build_type: "Release")
  upload_to_play_store(track: "internal")
end
```

Do not make one giant lane that performs:

```text
version bump
credentials
build
testing
screenshots
metadata
submission
production release
notifications
rollback
```

Keep the workflow composable.

---

# 39. Shared lane helpers

If several lanes repeat the same operation, extract only the repeated logic that is genuinely useful.

Example:

```ruby
private_lane :build_android do
  gradle(
    task: "bundle",
    build_type: "Release"
  )
end
```

Then:

```ruby
lane :internal do
  build_android
  upload_to_play_store(track: "internal")
end
```

Do not create a custom abstraction layer around every Fastlane action.

Fastlane itself is already an abstraction.

---

# 40. Fastlane environment handling

Use environment variables for:

```text
Credentials
Tokens
Secret paths
Environment-specific values
```

Example:

```ruby
api_key = app_store_connect_api_key(
  key_id: ENV["ASC_KEY_ID"],
  issuer_id: ENV["ASC_ISSUER_ID"],
  key_filepath: ENV["ASC_KEY_PATH"]
)
```

Do not put:

```text
API keys
Passwords
Private key contents
Service-account credentials
```

directly into the `Fastfile`.

---

# 41. `.env` files

If the project uses environment files, be careful.

Never commit:

```text
.env
.env.production
```

when they contain secrets.

Prefer:

```text
.env.example
```

with placeholders:

```text
ASC_KEY_ID=
ASC_ISSUER_ID=
ASC_KEY_PATH=
```

CI should inject real values through protected secrets.

---

# 42. Fastlane CI architecture

A production CI pipeline can look like:

```text
Git tag
   ↓
Checkout exact commit
   ↓
Install Ruby
   ↓
bundle install
   ↓
Install JS dependencies
   ↓
Run tests
   ↓
Fastlane build lane
   ↓
Artifact verification
   ↓
Human approval
   ↓
Fastlane submit lane
   ↓
Store testing
   ↓
Production release
```

The exact sequence can differ by project.

The important properties are:

```text
Reproducible
Traceable
Safe
Observable
Recoverable
```

The repository's production rules require staged deployments, rollback capability, deployment verification, monitoring hooks, and environment isolation.

---

# 43. GitHub Actions example

A simplified iOS CI job:

```yaml
name: iOS Release

on:
  workflow_dispatch:

jobs:
  release:
    runs-on: macos-latest

    steps:
      - uses: actions/checkout@v4

      - uses: ruby/setup-ruby@v1
        with:
          bundler-cache: true

      - uses: actions/setup-node@v4
        with:
          node-version: <project-version>

      - run: npm ci

      - run: bundle exec fastlane ios beta
```

This is an example, not a production-ready workflow.

Before production use, define:

```text
Xcode version
Ruby version
Node version
Signing
Secrets
Environment
Approval
Artifact handling
Failure reporting
```

Do not rely on `macos-latest` forever if a specific Xcode version is required for reproducibility.

---

# 44. Android CI example

A simplified Android workflow:

```yaml
name: Android Release

on:
  workflow_dispatch:

jobs:
  release:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - uses: ruby/setup-ruby@v1
        with:
          bundler-cache: true

      - uses: actions/setup-node@v4
        with:
          node-version: <project-version>

      - run: npm ci

      - run: bundle exec fastlane android internal
```

The real workflow must also establish:

```text
Java
Android SDK
Signing
Google Play credentials
Environment
Artifact validation
```

See:

```text
release-engineering/github-actions/
```

---

# 45. Fastlane and GitHub Actions secrets

Use protected secrets for:

```text
Apple API key
Apple issuer ID
Google Play service account
Android signing credentials
Match password
CI tokens
```

Never print:

```text
$ASC_KEY
$GOOGLE_PLAY_KEY
$MATCH_PASSWORD
```

in logs.

If a secret is accidentally exposed:

```text
Stop
 ↓
Revoke / rotate
 ↓
Investigate
 ↓
Update CI
 ↓
Verify
```

Do not simply remove the secret from the latest commit.

---

# 46. Fastlane and AI

AI can help with Fastlane, but should not own the release.

Useful AI tasks:

```text
Review Fastfile
Find duplicated lanes
Check missing approval boundaries
Inspect release configuration
Explain build failures
Draft release notes
Review metadata
Generate test checklists
Compare CI workflows
Find unsafe secret handling
```

AI should not automatically:

```text
Rotate production signing credentials
Publish to production
Delete store releases
Modify Apple/Google accounts
Change signing infrastructure
```

The playbook's AI model is:

```text
AI assists
 ↓
Developer verifies
 ↓
Tests
 ↓
Human approval
 ↓
Release
```

---

# 47. AI Fastlane audit prompt

```text
Audit this React Native Fastlane setup for production release safety.

Inspect:

- Gemfile
- Gemfile.lock
- fastlane/Fastfile
- fastlane/Appfile
- fastlane/Matchfile
- fastlane/Deliverfile
- CI workflows
- Android release configuration
- iOS release configuration

Check:

1. Fastlane version pinning
2. Lane structure
3. Build commands
4. Android/iOS platform separation
5. Signing flow
6. App Store Connect authentication
7. Google Play authentication
8. Secret handling
9. Environment separation
10. Artifact traceability
11. Human approval boundaries
12. Store submission behavior
13. Rollout safety
14. Failure handling
15. CI reproducibility

Return:

- BLOCKER
- HIGH
- MEDIUM
- LOW
- OK

For each issue provide:

- evidence
- file
- risk
- minimal fix
- verification

Rules:

- Do not request secrets.
- Do not print secrets.
- Do not modify production configuration.
- Do not invent store requirements.
- Verify volatile Fastlane, Apple, and Google behavior against official documentation.
- Do not recommend EAS or another release system unless there is a concrete reason.
```

---

# 48. Fastlane troubleshooting

When a lane fails:

```text
Lane fails
   ↓
Identify action
   ↓
Read first meaningful error
   ↓
Determine platform
   ↓
Run underlying native command if needed
   ↓
Reproduce
   ↓
Fix
   ↓
Run lane again
```

For example:

```text
Fastlane
 ↓
gradle
 ↓
Gradle error
```

The fix may belong in:

```text
android/
```

not:

```text
Fastfile
```

Similarly:

```text
Fastlane
 ↓
build_app
 ↓
Xcode error
```

The fix may belong in:

```text
ios/
```

not in Fastlane.

---

# 49. Common failure: Fastlane command not found

Check:

```bash
bundle exec fastlane --version
```

If this works:

```text
Fastlane is installed correctly.
```

Use:

```bash
bundle exec fastlane <lane>
```

rather than relying on:

```bash
fastlane <lane>
```

when the project uses Bundler.

---

# 50. Common failure: Bundler dependency mismatch

Symptoms:

```text
Could not find gem
Gem version conflict
Bundle failed
```

Check:

```text
Gemfile
Gemfile.lock
Ruby version
Bundler version
Fastlane version
```

Do not delete `Gemfile.lock` as the first fix.

The lockfile exists to make the release tooling reproducible.

---

# 51. Common failure: Fastlane lane cannot find the project

Check:

```text
Working directory
Fastfile location
Appfile
Xcode workspace
Xcode scheme
Android project
Gradle task
```

Fastlane normally runs from the project containing:

```text
fastlane/
```

Do not solve path problems by adding a large collection of relative paths.

Make the expected project root explicit.

---

# 52. Common failure: iOS signing fails in Fastlane

Check:

```text
Bundle identifier
Apple team
Certificate
Provisioning profile
Entitlements
match state
API key
Xcode signing settings
```

Then determine whether the problem is:

```text
Credential
or
Project configuration
```

Do not rotate certificates just because Fastlane cannot find an existing profile.

---

# 53. Common failure: `match` fails

Check:

```text
Match repository
Match password
Repository access
Bundle identifier
Apple credentials
Certificate state
Provisioning profile state
```

Useful diagnostic:

```bash
bundle exec fastlane match appstore --verbose
```

Fastlane's current documentation recommends `--verbose` for more detailed `match` output. citehttps://docs.fastlane.tools/actions/match/

Never paste the verbose output into a public issue without reviewing it for sensitive information.

---

# 54. Common failure: App Store Connect authentication fails

Prefer API-key authentication for CI where supported.

Check:

```text
Key ID
Issuer ID
P8 file
Key role
App access
Environment variables
```

Fastlane documents the App Store Connect API key as the preferred authentication method where available. citehttps://docs.fastlane.tools/app-store-connect-api/

Do not generate a new key until you know the existing key is invalid or insufficient.

---

# 55. Common failure: TestFlight upload succeeds but build is unavailable

Remember:

```text
Upload
≠
Processing complete
```

Check:

```text
App Store Connect processing
Build status
Version/build number
Compliance
Missing metadata
```

Fastlane's `pilot` can upload builds and manage TestFlight information, but App Store Connect processing remains part of the platform workflow. citehttps://docs.fastlane.tools/actions/pilot/

---

# 56. Common failure: Google Play upload fails

Check:

```text
Package name
Service account
Google Play API
Track
Version code
AAB
Store permissions
```

If the error is:

```text
version code already exists
```

increase the Android `versionCode`.

Do not change the application ID.

---

# 57. Common failure: Fastlane uploads to the wrong track

Check:

```ruby
upload_to_play_store(
  track: "internal"
)
```

or:

```ruby
upload_to_play_store(
  track: "production"
)
```

Make the track explicit for important lanes.

Do not rely on the default production track in a lane intended for testing.

---

# 58. Common failure: Fastlane submits production unexpectedly

Inspect:

```text
deliver
submit_for_review
automatic_release
upload_to_play_store
track
release_status
rollout
```

Separate:

```text
upload
```

from:

```text
production release
```

where possible.

For example:

```text
upload → internal
```

should not silently become:

```text
production
```

because a default parameter changed.

---

# 59. Common failure: CI works locally but not in CI

Compare:

```text
Ruby
Bundler
Fastlane
Node
Package manager
Xcode
Java
Gradle
Android SDK
Environment variables
Secrets
Git commit
Working directory
```

Fastlane is only one part of the build environment.

Do not change the `Fastfile` until you know the CI environment differs from local development.

---

# 60. Common failure: release lane is too large

Symptoms:

```text
One lane does everything
Hard to debug
Cannot test individual steps
Production submission is automatic
```

Refactor into:

```text
test
build
beta
submit
release
```

Example:

```text
test
 ↓
build
 ↓
beta
 ↓
submit
```

Keep production actions explicit.

---

# 61. Common failure: Fastlane duplicates EAS

This is an architecture problem rather than a Fastlane bug.

Example:

```text
EAS Build
+
Fastlane build
```

Ask:

```text
Which tool owns the build?
```

Then:

```text
Build owner
+
Store submission owner
+
Credential owner
```

should be clear.

Possible valid model:

```text
EAS
→ build

Fastlane
→ store metadata/submission
```

Possible valid model:

```text
Fastlane
→ build
→ submit
```

Invalid by default:

```text
EAS
→ build

Fastlane
→ build again

CI
→ build again
```

unless multiple artifacts are intentionally required.

---

# 62. Common failure: release artifact is not traceable

Every production artifact should map to:

```text
Git SHA
Fastlane lane
Fastlane version
Build profile/configuration
Environment
Version
Build number/version code
CI run
```

Do not release an artifact named only:

```text
latest.ipa
latest.aab
```

without knowing which source produced it.

---

# 63. Fastlane scripts should be safe by default

A good lane:

```text
Fails clearly
Uses explicit inputs
Does not expose secrets
Does not silently deploy production
Produces useful output
Can be rerun safely
```

Avoid:

```text
rm -rf
force production
automatic credential rotation
hidden shell commands
unbounded retries
```

Fastlane should reduce release risk, not create another source of operational risk.

---

# 64. Recommended project structure

For a React Native project using Fastlane:

```text
mobile-app/
├── android/
├── ios/
├── src/
│
├── fastlane/
│   ├── Appfile
│   ├── Fastfile
│   ├── Matchfile
│   ├── Deliverfile
│   └── metadata/
│       ├── android/
│       └── ios/
│
├── Gemfile
├── Gemfile.lock
├── package.json
├── package-lock.json
└── ...
```

Only keep the files that the project actually uses.

---

# 65. Recommended lane model

A practical starting point:

```text
test
android_build
android_internal
android_release

ios_build
ios_beta
ios_release

screenshots
metadata
```

Do not create all of these immediately.

Start with:

```text
test
android_internal
ios_beta
```

and add production lanes once the testing workflow is stable.

---

# 66. Recommended production model

A mature release process can become:

```text
PR
 ↓
Tests
 ↓
Merge
 ↓
Release tag
 ↓
Build
 ↓
Artifact validation
 ↓
Internal/TestFlight
 ↓
QA
 ↓
Human approval
 ↓
Production submission
 ↓
Staged rollout
 ↓
Monitoring
```

Fastlane should automate the deterministic parts.

The team remains responsible for:

```text
Release decision
Risk assessment
Store readiness
Approval
Rollback/mitigation
```

---

# 67. Fastlane and release evidence

For every production release, record:

```text
Git commit
Fastlane version
Lane
Platform
Application ID / Bundle ID
Version
Build number/version code
Artifact
CI run
Store submission
Release time
```

This makes a failed release easier to reproduce.

Do not record:

```text
Private keys
Passwords
Tokens
Service-account credentials
```

---

# 68. Fastlane and rollback

Fastlane can help automate a new corrective release.

It does not make rollback automatic.

A practical recovery path is:

```text
Incident
 ↓
Stop/limit rollout
 ↓
Identify last known-good release
 ↓
Prepare fix
 ↓
Build
 ↓
Test
 ↓
Human approval
 ↓
Submit corrected release
 ↓
Monitor
```

For JavaScript-only changes, an OTA system such as EAS Update may provide another recovery path if the installed native runtime is compatible.

Do not assume Fastlane can "roll back" a store release like Git.

---

# 69. Fastlane and observability

Fastlane should emit enough information to identify:

```text
What ran
What version ran
What commit ran
What artifact was produced
Where it was uploaded
Whether submission succeeded
```

CI should preserve:

```text
Build logs
Fastlane logs
Artifact metadata
Failure output
Release evidence
```

Do not depend on a developer's terminal history as the release audit trail.

The production system requires deployment visibility and useful failure/recovery information.

---

# 70. What Fastlane should automate

Good candidates:

```text
Build commands
Test commands
Version updates
Signing synchronization
Artifact creation
TestFlight upload
Google Play upload
Store metadata
Screenshots
Release notes
Release notifications
Deterministic validation
```

---

# 71. What Fastlane should not decide

Do not make Fastlane responsible for deciding:

```text
Is this release safe?
Should this feature ship?
Is the product ready?
Should production rollout continue?
Should a signing key be rotated?
Should a store account be changed?
Should a destructive recovery action happen?
```

Those require explicit engineering or human decisions.

---

# 72. AI-assisted Fastlane workflow

A useful AI workflow is:

```text
Repository
   ↓
AI Release Auditor
   ↓
Fastfile review
   ↓
Build/configuration review
   ↓
Signing review
   ↓
Store readiness review
   ↓
Find blockers
   ↓
Developer fixes
   ↓
Fastlane build
   ↓
Tests
   ↓
Human approval
   ↓
Fastlane submission
```

This matches the repository's broader AI model: AI assists with inspection and preparation, while high-impact release actions remain bounded and human-controlled.

---

# 73. AI release-note workflow

A practical workflow:

```text
Git diff
+
Git commits
+
Release scope
        ↓
AI draft
        ↓
Developer review
        ↓
Store-safe wording
        ↓
Fastlane metadata
```

Do not automatically publish AI-generated release notes without review.

The notes should describe user-visible changes rather than internal implementation details.

---

# 74. Fastlane validation checklist

Before using Fastlane for production:

```text
[ ] Fastlane version is pinned
[ ] Gemfile is committed
[ ] Gemfile.lock is committed
[ ] Fastfile is understandable
[ ] Application IDs are correct
[ ] Android build works
[ ] iOS build works
[ ] Signing is understood
[ ] Store credentials are protected
[ ] CI secrets are protected
[ ] Test lanes work
[ ] Internal/beta lanes work
[ ] Production lane is explicit
[ ] Production submission requires approval
[ ] Artifact is traceable
[ ] Failure logs are retained
[ ] Rollback/correction path is understood
```

---

# 75. Definition of done

Fastlane integration is ready when:

```text
Fastlane is pinned
       +
Lanes are understandable
       +
Native builds are reproducible
       +
Signing is secure
       +
Store credentials are protected
       +
Artifacts are traceable
       +
Testing is automated where useful
       +
Production submission is controlled
       +
CI can reproduce the workflow
       ↓
FASTLANE READY
```

The goal is not:

```text
"Everything is automated."
```

The goal is:

```text
"The repetitive release work is automated,
the important decisions remain visible,
and another developer can reproduce the release."
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

### React Native

- `frameworks/react-native/README.md`
- `frameworks/react-native/android-release.md`
- `frameworks/react-native/ios-release.md`
- `frameworks/react-native/signing.md`
- `frameworks/react-native/eas.md`
- `frameworks/react-native/common-failures.md`

### Expo

- `frameworks/expo/README.md`
- `frameworks/expo/eas.md`
- `frameworks/expo/submit.md`
- `frameworks/expo/credentials.md`
- `frameworks/expo/ci-cd.md`

### Signing

- `signing/ios/`
- `signing/android/`
- `signing/security/`

### Testing

- `testing/unit.md`
- `testing/integration.md`
- `testing/e2e.md`
- `testing/device-testing.md`
- `testing/android.md`
- `testing/ios.md`
- `testing/upgrade-migrations.md`
- `testing/release-smoke-tests.md`

### Publishing

- `publishing/ios/`
- `publishing/android/`
- `publishing/cross-platform/`

### Release engineering

- `release-engineering/build-systems.md`
- `release-engineering/release-configurations.md`
- `release-engineering/fastlane/`
- `release-engineering/github-actions/`
- `release-engineering/environment-management.md`
- `release-engineering/release-pipelines.md`

### AI

- `ai/agents/release-auditor.md`
- `ai/agents/debugging-agent.md`
- `ai/workflows/release-audit.md`
- `ai/workflows/debugging.md`
- `ai/orchestration/human-approval.md`
- `ai/security/secret-protection.md`
- `ai/security/agent-permissions.md`

---

# Official sources

Use official Fastlane and platform documentation as the authority for current tooling behavior.

## Fastlane

- Fastlane documentation: https://docs.fastlane.tools/
- React Native: https://docs.fastlane.tools/getting-started/cross-platform/react-native/
- Installation and Android setup: https://docs.fastlane.tools/getting-started/android/setup/
- Android beta deployment: https://docs.fastlane.tools/getting-started/android/beta-deployment/
- Android release deployment: https://docs.fastlane.tools/getting-started/android/release-deployment/
- Google Play `supply`: https://docs.fastlane.tools/actions/supply/
- iOS beta deployment: https://docs.fastlane.tools/getting-started/ios/beta-deployment/
- TestFlight `pilot`: https://docs.fastlane.tools/actions/pilot/
- App Store `deliver`: https://docs.fastlane.tools/actions/appstore/
- Code signing: https://docs.fastlane.tools/codesigning/getting-started/
- `match`: https://docs.fastlane.tools/actions/match/
- App Store Connect API: https://docs.fastlane.tools/app-store-connect-api/

## React Native

- React Native documentation: https://reactnative.dev/docs/getting-started
- Android publishing: https://reactnative.dev/docs/signed-apk-android
- iOS publishing: https://reactnative.dev/docs/publishing-to-app-store
- React Native upgrading: https://reactnative.dev/docs/upgrading

## Android

- Android Developers: https://developer.android.com/
- Android app signing: https://developer.android.com/studio/publish/app-signing
- Android App Bundles: https://developer.android.com/guide/app-bundle

## Apple

- Apple Developer: https://developer.apple.com/
- App Store Connect Help: https://developer.apple.com/help/app-store-connect/

## Google Play

- Google Play Console Help: https://support.google.com/googleplay/android-developer/

---

**Last verified:** August 11, 2026

Fastlane, React Native, Xcode, Gradle, Android SDK, Apple signing, Google Play APIs, App Store Connect APIs, and store workflows change over time. Verify the current official documentation for the exact versions and release workflow used by the project before production deployment.
