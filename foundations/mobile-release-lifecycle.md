# Mobile Release Lifecycle

The mobile release lifecycle is the complete path from a finished app change to a safely released, monitored, and maintainable production version.

The Mobile Release Playbook is built around this lifecycle:

```text
App Built
 ↓
Release Readiness
 ↓
Configuration & Signing
 ↓
Testing
 ↓
Store Preparation
 ↓
Production Build
 ↓
TestFlight / Play Testing
 ↓
Submission
 ↓
Review / Rejection
 ↓
Production Release
 ↓
Monitoring
 ↓
Update / Hotfix
```

This is the core model of the playbook.

The goal is not to make every release complicated.

The goal is to make the important steps visible, repeatable, verifiable, and safe.

---

## 1. What this lifecycle covers

A mobile release is more than uploading an APK, AAB, or IPA.

A complete release involves:

- application configuration
- identifiers
- versioning
- dependencies
- signing
- testing
- store metadata
- privacy declarations
- production builds
- beta testing
- submission
- review
- rollout
- monitoring
- incident response
- hotfixes
- future updates

A useful mental model is:

```text
Code change
 ↓
Release candidate
 ↓
Verified build
 ↓
Store submission
 ↓
Approved release
 ↓
Production users
 ↓
Production evidence
 ↓
Next release
```

Each stage produces evidence for the next stage.

---

# 2. The lifecycle at a glance

| Stage | Main question | Output |
|---|---|---|
| 1. App Built | Is the change complete? | Candidate change |
| 2. Release Readiness | Is it ready to become a release? | Release candidate |
| 3. Configuration | Is the production identity/config correct? | Release configuration |
| 4. Signing | Can the build be trusted and distributed? | Signed build inputs |
| 5. Testing | Does the release work? | Validation evidence |
| 6. Store Preparation | Is the store submission complete? | Submission package |
| 7. Production Build | Is the exact artifact ready? | Production binary |
| 8. Beta Testing | Does the store-distributed build work? | Beta evidence |
| 9. Submission | Has the correct build been submitted? | Store submission |
| 10. Review | Did the platform accept it? | Accepted / rejected submission |
| 11. Production Release | Is it available to users? | Production release |
| 12. Monitoring | Is it behaving correctly? | Production evidence |
| 13. Update / Hotfix | What needs to happen next? | New release or mitigation |

---

# 3. Stage 1: App Built

The lifecycle starts when the application change itself is ready.

This can be:

- a new feature
- a bug fix
- a performance improvement
- a dependency update
- a native configuration change
- a security fix
- a platform compatibility update

Before calling the work "release ready":

- [ ] The intended change is implemented.
- [ ] Unrelated changes are excluded.
- [ ] Required tests are present.
- [ ] Known issues are understood.
- [ ] Backend changes are compatible where required.
- [ ] Dependencies are intentional.
- [ ] The release scope is known.

Do not start store publishing while the actual application change is still moving.

---

# 4. Stage 2: Release Readiness

A release candidate is a specific version of the application that you intend to validate and potentially ship.

The key difference is:

```text
Development
 ↓
Many possible states

Release candidate
 ↓
One specific state
```

Record:

```text
Commit:
Version:
iOS build:
Android version code:
Build profile:
Environment:
Release scope:
```

The release candidate should be reproducible.

A release should not depend on:

```text
"whatever is currently on my machine"
```

It should depend on a known source state and known build configuration.

---

# 5. Stage 3: Configuration

Production configuration determines how the application behaves when released.

Verify:

- application identifiers
- production API endpoints
- environment variables
- feature flags
- analytics
- crash reporting
- push notifications
- deep links
- payments
- OAuth
- platform capabilities
- native configuration

For Expo projects, configuration commonly includes:

```text
app.json / app.config.*
+
eas.json
+
native configuration where applicable
```

For React Native projects, also inspect:

```text
ios/
android/
```

when native projects are part of the repository.

The actual build configuration matters more than what a developer assumes is configured.

---

# 6. Stage 4: Signing

Signing establishes that the released binary is associated with the expected application identity and distribution credentials.

### iOS

The release flow generally involves:

```text
Bundle ID
 ↓
Apple Developer configuration
 ↓
Signing credentials
 ↓
Provisioning / distribution configuration
 ↓
Signed application
```

### Android

The release flow generally involves:

```text
Application ID
 ↓
Release signing
 ↓
Upload / Play App Signing
 ↓
Signed application
```

Signing credentials are sensitive.

Never commit:

- private keys
- signing passwords
- service-account secrets
- API tokens
- production credentials

Use protected secret storage and least-privilege access.

---

# 7. Stage 5: Testing

Testing should answer:

> Can this release safely reach real users?

Test the application at several levels.

```text
Unit
 ↓
Integration
 ↓
End-to-end
 ↓
Real device
 ↓
Production-like build
```

Focus on critical user journeys first.

At minimum, verify where applicable:

- application launch
- authentication
- primary workflow
- API requests
- error handling
- permissions
- navigation
- deep links
- push notifications
- payments
- offline behavior
- upgrades
- accessibility
- localization
- performance

Do not use test count as the definition of release quality.

A release with many tests can still fail if the critical production path is untested.

---

# 8. Stage 6: Store Preparation

Store preparation is separate from building the application.

The store may require:

- app name
- description
- screenshots
- app previews
- privacy information
- content ratings
- data safety information
- age rating
- target audience
- support URLs
- privacy policy
- review notes
- release notes

Store metadata must describe the actual application.

Do not write claims that the production build cannot demonstrate.

---

# 9. Stage 7: Production Build

Create the exact binary that will be distributed.

For Expo and React Native, EAS Build is a common path for creating production-ready Android and iOS binaries. Expo documents EAS Build as a hosted build service for Expo and React Native projects and supports production builds for both platforms.

Typical Expo commands include:

```bash
eas build --platform ios
```

and:

```bash
eas build --platform android
```

or:

```bash
eas build --platform all
```

The exact command depends on the project's configured build profiles.

A production build should be traceable to:

```text
Source commit
+
Dependency state
+
Build profile
+
Environment
+
Version
+
Build number
```

---

# 10. Verify the actual artifact

Do not stop when the build service says:

```text
Build succeeded
```

Verify the resulting artifact.

### iOS

Check:

- Bundle ID
- version
- build number
- signing
- entitlements
- production configuration

### Android

Check:

- Application ID
- version
- version code
- target SDK
- signing
- production configuration

The binary is what users receive.

Configuration files are inputs, not proof of the final output.

---

# 11. Stage 8: Beta Testing

The store testing layer gives you another validation point before public release.

### iOS

TestFlight is Apple's beta distribution system.

Typical flow:

```text
Production build
 ↓
App Store Connect
 ↓
Build processing
 ↓
TestFlight
 ↓
Testing
 ↓
App Review
 ↓
Production release
```

App Store Connect supports uploading builds, distributing beta versions through TestFlight, and submitting versions for review.

### Android

Google Play provides testing tracks such as internal, closed, and other release/testing tracks depending on the application's situation.

The exact track should match the release need.

Use beta testing to validate:

- real installation
- production-like configuration
- upgrade behavior
- critical workflows
- device compatibility
- store-distributed behavior

---

# 12. Expo internal distribution vs store testing

These are different things.

Expo EAS internal distribution can provide installable builds for a development team or testers. Expo documents internal distribution as a way to share builds directly with testers.

Store testing happens through:

```text
TestFlight
+
Google Play testing tracks
```

A useful sequence is:

```text
Local development
 ↓
Internal build
 ↓
Real-device validation
 ↓
Store testing
 ↓
Production release
```

Do not assume an internal build provides the exact same distribution experience as the store.

---

# 13. Stage 9: Submission

Submission means sending the selected production build and required store information into the platform's review/distribution workflow.

### iOS

Apple's current workflow requires required metadata and the correct build to be selected before adding the version for review. The app is then submitted for App Review.

A simplified flow:

```text
Prepare metadata
 ↓
Select correct build
 ↓
Add for Review
 ↓
Submit for Review
```

### Android

The flow generally involves:

```text
Prepare release
 ↓
Select artifact
 ↓
Configure release
 ↓
Submit / rollout
 ↓
Google review / processing
```

Exact Play Console behavior depends on the app's account, testing history, release track, and current Google requirements.

---

# 14. Expo EAS Submit

For Expo and React Native projects, EAS Submit can upload Android and iOS binaries to the respective store systems.

Examples:

```bash
eas submit --platform android
```

```bash
eas submit --platform ios
```

You can also connect build and submission:

```bash
eas build --platform ios --auto-submit
```

Expo documents `--auto-submit` as a way to pass a completed build to EAS Submit automatically.

Important:

> Uploading a binary is not always the same as publishing it.

For example, an iOS upload through EAS Submit appears in App Store Connect/TestFlight and still requires the App Store submission workflow for public release.

---

# 15. Stage 10: Review

Store review is an external gate.

The platform may:

- accept the release
- reject the release
- request additional information
- identify metadata issues
- identify policy issues
- identify functional problems
- require another build

Do not treat rejection as a random event.

Treat it as a release signal.

When rejected:

```text
Read exact reason
 ↓
Identify affected requirement
 ↓
Reproduce if possible
 ↓
Fix
 ↓
Test
 ↓
Update metadata / review notes
 ↓
Resubmit
```

Do not blindly resubmit the same build without addressing the stated issue.

Apple exposes submission and app statuses such as Ready for Review, Waiting for Review, In Review, Accepted, and Rejected.

---

# 16. Review rejection workflow

A rejection should produce a concrete engineering action.

Record:

```text
Platform:
Version:
Build:
Rejection reason:
Affected feature:
Evidence:
Required fix:
Owner:
New build required:
Metadata change required:
Resubmission date:
```

Then classify the issue:

### Code issue

Requires:

```text
Fix
→ Test
→ New build
→ Resubmit
```

### Metadata issue

May require:

```text
Correct metadata
→ Verify
→ Resubmit
```

### Configuration issue

Requires:

```text
Correct configuration
→ Rebuild if necessary
→ Test
→ Resubmit
```

### Missing reviewer information

Requires:

```text
Provide clear instructions
→ Verify
→ Resubmit
```

---

# 17. Stage 11: Production Release

Once the store accepts the release, production distribution can begin.

The exact rollout model depends on the platform.

Possible approaches:

```text
Immediate release
```

or:

```text
Controlled rollout
```

or:

```text
Phased / staged release
```

The choice should depend on:

- release risk
- user impact
- monitoring quality
- ability to stop rollout
- ability to ship a hotfix
- confidence in the change

Do not choose staged rollout merely because it sounds safer.

A staged rollout is useful only when the team can observe the rollout and act on what it sees.

---

# 18. Stage 12: Production Verification

A release is not finished when the store says:

```text
Available
```

Verify the public version.

Check:

- [ ] App is available in the expected region.
- [ ] Correct version is visible.
- [ ] Installation works.
- [ ] Existing users can update.
- [ ] App launches.
- [ ] Authentication works.
- [ ] Primary workflow works.
- [ ] Production API works.
- [ ] Analytics is receiving data.
- [ ] Crash reporting is receiving data.
- [ ] Push notifications work where applicable.
- [ ] Deep links work where applicable.

Production verification should happen after release, not only before it.

---

# 19. Stage 13: Monitoring

Once users receive the application, production becomes the strongest source of evidence.

Monitor:

- crashes
- error rates
- API failures
- latency
- authentication failures
- payment failures
- push notification failures
- deep-link failures
- important business events
- support reports
- store reviews

The goal is not to monitor everything.

The goal is to detect meaningful user-impacting problems quickly.

A useful production loop is:

```text
Release
 ↓
Observe
 ↓
Compare
 ↓
Investigate
 ↓
Act
```

---

# 20. Release health

Compare the new release with the previous stable version when possible.

Look for:

```text
Crash rate ↑
Error rate ↑
Latency ↑
Conversion ↓
Authentication failures ↑
Payment failures ↑
Support complaints ↑
```

The important question is:

> Did something change after this release?

Do not treat every production change as a release regression.

Use evidence.

---

# 21. Incident and rollback thinking

Mobile rollback is different from server rollback.

A server deployment can often be reverted immediately.

A mobile binary may already be installed on users' devices.

Therefore:

```text
Mobile release
 ↓
Users install version
 ↓
Version remains on devices
```

If the release is broken, possible responses include:

- stop a staged rollout
- disable a risky feature
- change backend behavior
- use a server-side mitigation
- release a hotfix
- communicate with affected users

Do not assume:

```text
Rollback
```

always means:

```text
Return every device to the previous binary immediately
```

It usually does not.

---

# 22. Hotfix lifecycle

When a production problem requires a new mobile build:

```text
Incident
 ↓
Contain
 ↓
Root cause
 ↓
Focused fix
 ↓
Regression test
 ↓
Build
 ↓
Real-device validation
 ↓
Store submission
 ↓
Review
 ↓
Production
 ↓
Monitor
```

Keep hotfixes focused.

Avoid combining:

```text
Critical fix
+
dependency upgrade
+
UI redesign
+
unrelated refactor
```

unless there is a strong reason.

Every additional change increases release risk.

---

# 23. Update lifecycle

Most mobile applications go through this cycle repeatedly.

```text
Existing production
 ↓
New code
 ↓
Release candidate
 ↓
Validation
 ↓
Build
 ↓
Testing
 ↓
Submission
 ↓
Review
 ↓
Production
 ↓
Monitoring
 ↓
Next change
```

This means release engineering should be designed for repetition.

A process that works once but requires a developer to remember twenty manual steps is not a good long-term release process.

---

# 24. OTA updates and native releases

Some frameworks support over-the-air JavaScript/content updates.

For Expo projects, `expo-updates` can be part of the release strategy, but OTA updates do not replace native binary releases for every change.

A useful distinction is:

```text
JavaScript / compatible update
 ↓
OTA may be appropriate
```

versus:

```text
Native code
permissions
entitlements
native dependency
OS/build configuration
 ↓
New binary required
```

Always verify the framework's current runtime/versioning rules before using OTA updates for production changes.

Do not use OTA as a way to bypass store review for changes that require a new native binary.

---

# 25. AI across the lifecycle

AI is an execution layer across the release lifecycle.

The playbook explicitly treats AI as a practical accelerator rather than a separate product. It can help with audits, debugging, metadata, release notes, repetitive checks, repository inspection, and workflow preparation.

A useful model is:

```text
 AI
 │
 ┌─────────────┼─────────────┐
 ↓ ↓ ↓
 Audit Build QA
 ↓ ↓ ↓
 Metadata Debug Release
 ↓ ↓ ↓
 Rejection Automate Monitor
```

AI should help developers move through the lifecycle faster.

It should not become an uncontrolled production operator.

---

# 26. AI-assisted release workflow

A practical AI workflow:

```text
Developer
 ↓
Ask AI to inspect release
 ↓
AI finds candidate issues
 ↓
Developer verifies findings
 ↓
Developer fixes issues
 ↓
Automated tests
 ↓
Production build
 ↓
Real-device testing
 ↓
Human approval
 ↓
Submission
```

Good AI tasks include:

- dependency audit
- configuration audit
- release-readiness review
- test generation
- debugging
- metadata drafting
- release-note drafting
- rejection analysis
- checklist generation
- repository inspection
- build failure analysis

---

# 27. Agents

Agents can perform larger bounded workflows.

Examples:

```text
Release Auditor
→ inspect repository and release configuration

QA Agent
→ execute defined validation checks

Debugging Agent
→ investigate a reproducible build/runtime failure

Metadata Agent
→ prepare store metadata drafts

Privacy Auditor
→ inspect data collection and declarations

Security Auditor
→ identify release security risks

Dependency Auditor
→ inspect dependency changes

Rejection Analyzer
→ convert store rejection feedback into actions
```

Agents should have:

- clear scope
- explicit inputs
- bounded permissions
- visible actions
- failure handling
- human approval where needed

Avoid creating an agent for every small task.

---

# 28. AI approval boundary

The core rule is:

```text
AI assists
 +
Human verifies
 +
Human approves
```

This is especially important for:

- store submission
- signing
- credentials
- production deployment
- financial changes
- account changes
- permissions
- destructive actions
- irreversible operations

The documentation rules explicitly require human approval boundaries for sensitive or irreversible actions.

Do not give an AI agent unrestricted access to production systems.

---

# 29. Automation progression

Automation should grow with confidence.

A practical progression is:

```text
Manual
 ↓
Documented
 ↓
Scripted
 ↓
CI validated
 ↓
AI-assisted
 ↓
Bounded automation
 ↓
Selective automation
```

Do not jump directly to:

```text
Commit
 ↓
AI agent
 ↓
Production release
```

without strong validation and approval controls.

The playbook is a GitHub repository and knowledge system, not a SaaS release-management platform. It should prefer documentation, executable examples, small scripts, AI workflows, and community contributions over unnecessary infrastructure.

---

# 30. Release evidence

Every important lifecycle stage should leave enough evidence to answer:

```text
What was released?
Why was it released?
Which source commit produced it?
Which build was used?
Which tests passed?
Who approved it?
What happened after release?
```

Useful evidence includes:

- commit SHA
- version
- build number/versionCode
- build profile
- test results
- device test results
- store submission status
- review feedback
- production metrics
- incidents
- hotfixes

Do not create paperwork for its own sake.

Capture evidence that helps debugging, auditing, and future releases.

---

# 31. Release state model

A release can be represented as:

```text
DRAFT
 ↓
READY
 ↓
BUILDING
 ↓
BUILT
 ↓
TESTING
 ↓
SUBMITTED
 ↓
IN_REVIEW
 ↓
APPROVED
 ↓
RELEASED
 ↓
MONITORING
 ↓
STABLE
```

Failure paths:

```text
BUILDING
 ↓
BUILD_FAILED
```

```text
TESTING
 ↓
TEST_FAILED
```

```text
SUBMITTED
 ↓
REJECTED
```

```text
RELEASED
 ↓
INCIDENT
 ↓
HOTFIX
```

The exact labels are implementation choices. The important idea is that a release has an explicit state and transition rather than being treated as a single "published" event.

---

# 32. What can stop a release?

Stop the release when there is a meaningful unresolved risk.

Examples:

- critical security issue
- wrong production identifier
- wrong signing configuration
- broken primary workflow
- incompatible backend change
- serious data corruption risk
- exposed production secret
- failed production build
- unverified critical store requirement
- serious crash affecting the release
- no viable recovery path

Do not block a release because:

- code is not perfectly refactored
- every possible device was not tested
- coverage is not 100%
- there is unrelated technical debt

Release decisions should be based on actual risk and evidence.

---

# 33. Release decision

Use three outcomes:

```text
APPROVE
CONDITIONAL APPROVE
DENY
```

### APPROVE

Use when:

- critical workflows pass
- release evidence is sufficient
- no critical/high unresolved issue exists
- security boundaries are intact
- deployment risk is understood
- recovery path is understood

### CONDITIONAL APPROVE

Use when:

- no critical blocker exists
- remaining risk is bounded
- explicit conditions are defined
- the release owner accepts the conditions

### DENY

Use when:

- critical security/data issue exists
- core workflow is broken
- build or signing is unsafe
- production compatibility is broken
- release cannot be safely recovered

Do not use conditional approval to hide a real release blocker.

---

# 34. Release lifecycle checklist

Before production:

- [ ] Release scope is defined.
- [ ] Release candidate is identified.
- [ ] Versioning is correct.
- [ ] Identifiers are correct.
- [ ] Dependencies are verified.
- [ ] Production configuration is correct.
- [ ] Signing is correct.
- [ ] Tests pass.
- [ ] Real-device testing is complete.
- [ ] Store metadata is complete.
- [ ] Production build is verified.
- [ ] Store testing is complete.
- [ ] Review information is complete.
- [ ] Backend compatibility is confirmed.
- [ ] Monitoring is ready.
- [ ] Recovery/hotfix path is understood.
- [ ] Human approval is complete.

---

# 35. After production release

Do not close the release immediately after publication.

Run:

```text
Production release
 ↓
Public install/update
 ↓
Smoke test
 ↓
Observe crashes/errors
 ↓
Check critical workflow
 ↓
Check backend health
 ↓
Check store/support signals
 ↓
Continue rollout or intervene
```

Then record:

- release outcome
- important incidents
- hotfixes
- useful lessons
- documentation changes

Only add a new process rule when it prevents a realistic recurring problem.

---

# 36. The complete lifecycle

The complete model is:

```text
┌─────────────────────┐
│ APP BUILT │
└──────────┬──────────┘
 ↓
┌─────────────────────┐
│ RELEASE READINESS │
└──────────┬──────────┘
 ↓
┌─────────────────────┐
│ CONFIG + IDENTITIES │
└──────────┬──────────┘
 ↓
┌─────────────────────┐
│ SIGNING │
└──────────┬──────────┘
 ↓
┌─────────────────────┐
│ TESTING │
└──────────┬──────────┘
 ↓
┌─────────────────────┐
│ STORE PREPARATION │
└──────────┬──────────┘
 ↓
┌─────────────────────┐
│ PRODUCTION BUILD │
└──────────┬──────────┘
 ↓
┌─────────────────────┐
│ STORE TESTING │
└──────────┬──────────┘
 ↓
┌─────────────────────┐
│ SUBMISSION │
└──────────┬──────────┘
 ↓
 ┌────┴─────┐
 ↓ ↓
 ACCEPTED REJECTED
 ↓ ↓
 ↓ FIX + RESUBMIT
 ↓
┌─────────────────────┐
│ PRODUCTION RELEASE │
└──────────┬──────────┘
 ↓
┌─────────────────────┐
│ MONITORING │
└──────────┬──────────┘
 ↓
 ┌────┴─────┐
 ↓ ↓
 STABLE INCIDENT
 ↓ ↓
 ↓ HOTFIX
 ↓ ↓
 └──────┬───┘
 ↓
 NEXT RELEASE
```

---

# 37. Recommended operating model

For most Expo and React Native teams, keep the release system simple:

```text
Git
 ↓
Pull Request
 ↓
Automated checks
 ↓
Release candidate
 ↓
EAS Build / native build
 ↓
Real-device testing
 ↓
TestFlight / Play testing
 ↓
Human review
 ↓
Store submission
 ↓
Production
 ↓
Monitoring
```

Add automation only where it removes real repeated work.

Use GitHub Actions, EAS Workflows, or another CI/CD system when the team benefits from repeatable builds, tests, submissions, or release checks. Expo's current EAS Workflows can automate builds, submissions, updates, and tests and can be triggered by GitHub events or manually.

---

# 38. Definition of done

A mobile release is complete when:

```text
Code is ready
 +
Release candidate is known
 +
Configuration is verified
 +
Signing is verified
 +
Testing is complete
 +
Store package is complete
 +
Production artifact is verified
 +
Store testing is complete
 +
Submission is accepted
 +
Production release is verified
 +
Monitoring is healthy
 ↓
RELEASE COMPLETE
```

The lifecycle then starts again with the next change.

---

# Related documentation

Use this guide as the map to the rest of the playbook.

### Foundations

- `foundations/project-configuration.md`
- `foundations/identifiers.md`
- `foundations/versioning.md`
- `foundations/dependency-management.md`
- `foundations/device-support.md`
- `foundations/release-environments.md`

### Testing

- `testing/release-smoke-tests.md`
- `testing/device-testing.md`
- `testing/e2e.md`
- `testing/accessibility.md`
- `testing/performance.md`

### Publishing

- `publishing/ios/`
- `publishing/android/`
- `publishing/cross-platform/`

### Release engineering

- `release-engineering/eas/`
- `release-engineering/github-actions/`
- `release-engineering/release-pipelines.md`

### Strategy

- `release-strategy/beta-testing.md`
- `release-strategy/staged-rollouts.md`
- `release-strategy/phased-release.md`
- `release-strategy/hotfixes.md`

### Post-release

- `post-release/monitoring.md`
- `post-release/incident-response.md`
- `post-release/rollback.md`
- `post-release/hotfix.md`

### AI

- `ai/workflows/release-audit.md`
- `ai/workflows/ai-qa.md`
- `ai/workflows/debugging.md`
- `ai/orchestration/agent-workflows.md`
- `ai/security/human-approval.md`

---

# Official sources

Use official documentation as the authority for current platform behavior and requirements.

### Apple

- App Store Connect Help: https://developer.apple.com/help/app-store-connect/
- App Store Connect workflow: https://developer.apple.com/help/app-store-connect/get-started/app-store-connect-workflow
- Submit an app: https://developer.apple.com/help/app-store-connect/manage-submissions-to-app-review/submit-an-app
- Submission overview: https://developer.apple.com/help/app-store-connect/manage-submissions-to-app-review/overview-of-submitting-for-review
- App and submission statuses: https://developer.apple.com/help/app-store-connect/reference/app-information/app-and-submission-statuses

### Google

- Google Play Console Help: https://support.google.com/googleplay/android-developer/
- Prepare and roll out a release: https://support.google.com/googleplay/android-developer/answer/9859348
- App visibility and discovery: https://support.google.com/googleplay/android-developer/answer/9042516

### Expo

- Distribution overview: https://docs.expo.dev/distribution/introduction/
- EAS Build: https://docs.expo.dev/build/introduction/
- Build for app stores: https://docs.expo.dev/deploy/build-project/
- EAS Submit: https://docs.expo.dev/submit/introduction/
- Internal distribution: https://docs.expo.dev/build/internal-distribution/
- EAS Workflows: https://docs.expo.dev/eas/workflows/introduction/
- Automated submissions: https://docs.expo.dev/build/automate-submissions/

### React Native

- React Native documentation: https://reactnative.dev/docs/getting-started
- Publishing to Apple App Store: https://reactnative.dev/docs/publishing-to-app-store

**Last verified:** August 11, 2026

Platform requirements, store workflows, SDK requirements, review processes, and release tooling can change. Re-check the official source before executing a production release.
