# Release Context

Release Context is the structured information an AI workflow needs to understand a specific mobile release.

It prevents an agent from starting with vague instructions such as:

```text
"Check if the app is ready."
```

and instead gives it a concrete release:

```text
Application
→ Version
→ Build
→ Commit
→ Platform
→ Environment
→ Release type
→ Scope
→ Configuration
→ Tests
→ Audit results
→ Store status
→ Known risks
→ Approval state
```

The purpose is simple:

> Give AI enough verified context to reason about the current release without making it guess.

This file defines the recommended context model for AI-assisted release workflows.

---

# Purpose

Use Release Context with:

```text
Release Auditor
QA Agent
Security Auditor
Privacy Auditor
Dependency Auditor
Metadata Agent
Debugging Agent
Rejection Analyzer
Release Manager
AI release workflows
AI prompts
AI orchestration
```

It should make the current release understandable without requiring every agent to reconstruct the release from repository history, chat messages, CI logs, and store dashboards.

---

# What Release Context contains

A release context should describe:

```text
1. Application
2. Release identity
3. Source
4. Platforms
5. Environment
6. Release type
7. Scope
8. Configuration
9. Build
10. Testing
11. Audits
12. Store state
13. Rollout
14. Monitoring
15. Risks
16. Unknowns
17. Approvals
18. Current state
19. Next action
```

Not every release needs every field.

Missing information should be represented as:

```text
UNKNOWN
NOT_APPLICABLE
NOT_PROVIDED
```

Do not invent values.

---

# Design principles

## 1. Release Context is evidence, not a memory dump

Only include information useful to the current release.

Good:

```text
Version: 2.4.0
iOS Build: 104
Commit: abc1234
Environment: production
```

Avoid:

```text
Three months of unrelated Git history
Old release notes
Unrelated tickets
Unused configuration
```

---

## 2. Verified information comes first

Separate:

```text
Verified
Inferred
Unknown
```

Example:

```text
Verified:
Android build 104 was generated from commit abc1234.

Unknown:
Whether the final production artifact was tested on a physical Android device.
```

An AI agent must not turn an inference into a verified fact.

---

## 3. Current release state must be explicit

A release context should always contain:

```text
current_state
```

Examples:

```text
DRAFT
PLANNED
VALIDATING
BUILDING
BUILT
TESTING
READY_FOR_APPROVAL
APPROVED
SUBMITTED
IN_REVIEW
RELEASED
MONITORING
STABLE
BLOCKED
REJECTED
HOTFIX_REQUIRED
CANCELLED
```

Use the project's actual lifecycle states when they already exist.

Do not silently introduce a second state model.

---

## 4. Platform state can differ

Do not assume iOS and Android have the same status.

Example:

```text
Overall:
BLOCKED

iOS:
READY_FOR_APPROVAL

Android:
TESTING
```

The context must preserve platform-specific state.

---

## 5. Unknown is a real state

Use:

```text
UNKNOWN
```

when evidence is missing.

Example:

```text
Physical device test:
UNKNOWN

Reason:
No result was provided.
```

Do not convert missing evidence into:

```text
PASS
```

---

# Canonical context structure

Use this structure as the default.

```yaml
release:
  id:
  application:
  version:
  release_type:
  environment:
  current_state:
  owner:
  planned_release_window:

source:
  repository:
  branch:
  commit:
  tag:
  working_tree:
  changed_files:

platforms:
  ios:
    state:
    build:
    bundle_identifier:
    artifact:
  android:
    state:
    version_code:
    application_id:
    artifact:

scope:
  included:
  excluded:
  user_facing_changes:
  technical_changes:

configuration:
  environment:
  api:
  authentication:
  payments:
  push_notifications:
  deep_links:
  analytics:
  crash_reporting:
  permissions:

build:
  status:
  profiles:
  artifacts:
  created_at:
  source_commit:
  reproducible:

testing:
  unit:
  integration:
  e2e:
  device:
  accessibility:
  performance:
  network:
  offline:
  deep_links:
  push_notifications:
  payments:
  smoke:

audits:
  release:
  security:
  privacy:
  dependencies:
  metadata:

store:
  ios:
    status:
    version:
    build:
    submission:
    review:
  android:
    status:
    version:
    build:
    submission:
    review:

rollout:
  strategy:
  status:
  percentage:
  started_at:

monitoring:
  crash_rate:
  error_rate:
  critical_flows:
  incidents:
  health:

risks:
  critical:
  high:
  medium:
  low:

unknowns:

approvals:
  required:
  status:
  approved_by:
  approved_at:

next_action:

evidence:
  - type:
    source:
    reference:
    status:
```

This is a conceptual schema.

The repository does not require YAML storage unless a workflow needs machine-readable context.

---

# Release identity

The minimum release identity is:

```text
Application
Version
Platform
Build
Commit
Environment
Release type
```

Example:

```yaml
release:
  id: "2026-08-11-01"
  application: "Example App"
  version: "2.4.0"
  release_type: "FEATURE_RELEASE"
  environment: "production"
  current_state: "TESTING"
```

---

# Source context

Source context establishes exactly what code is being released.

Track:

```text
Repository
Branch
Commit
Tag
Working tree state
Changed files
```

Example:

```yaml
source:
  repository: "example/mobile-app"
  branch: "main"
  commit: "abc1234"
  tag: null
  working_tree: "clean"
  changed_files:
    - "src/onboarding/"
    - "src/notifications/"
```

A release should be traceable to a specific source revision.

---

# Working tree state

Prefer:

```text
clean
```

for production releases.

If the build contains uncommitted changes:

```text
working_tree: dirty
```

This should normally be a release blocker unless the workflow explicitly supports and records it.

The important rule is:

> The released artifact must be traceable to the exact source used to create it.

---

# Platform context

Each platform should have its own state.

## iOS

Track:

```text
State
Bundle identifier
Version
Build number
Artifact
Build profile
Signing state
Submission state
Review state
```

## Android

Track:

```text
State
Application ID
Version
Version code
Artifact
Build profile
Signing state
Submission state
Review state
```

Example:

```yaml
platforms:
  ios:
    state: "READY_FOR_APPROVAL"
    build: "104"
    bundle_identifier: "com.example.app"

  android:
    state: "TESTING"
    version_code: 104
    application_id: "com.example.app"
```

---

# Scope context

Define what changed.

Use:

```yaml
scope:
  included:
    - "New onboarding flow"
    - "Push notification fixes"

  excluded:
    - "Subscription redesign"

  user_facing_changes:
    - "Updated onboarding"

  technical_changes:
    - "Updated notification handling"
```

The scope should drive the validation plan.

Example:

```text
Payment changes
→ payment testing

Deep-link changes
→ deep-link testing

Permission changes
→ permission/privacy review

Dependency changes
→ dependency audit
```

---

# Configuration context

Configuration is part of the release.

Track relevant configuration:

```text
Environment
API
Authentication
Payments
Push notifications
Deep links
Analytics
Crash reporting
Permissions
Feature flags
Remote configuration
```

Do not include secrets.

Bad:

```yaml
api_key: "actual-secret"
```

Correct:

```yaml
api_key:
  status: "configured"
  source: "CI secret"
  exposed_to_ai: false
```

The AI needs to know that a secret exists and how it is supplied, not the secret value.

---

# Environment context

Use explicit environments:

```text
development
preview
staging
production
```

The context should identify which environment the release targets.

Example:

```yaml
environment:
  name: "production"
  api: "production"
  analytics: "production"
  crash_reporting: "production"
```

Do not assume that a production build automatically uses production services.

Verify it.

---

# Build context

Track the exact artifact being validated.

```yaml
build:
  status: "PASS"
  profiles:
    ios: "production"
    android: "production"
  artifacts:
    ios: "build-104.ipa"
    android: "build-104.aab"
  source_commit: "abc1234"
  reproducible: true
```

If artifact-to-commit traceability is unknown:

```text
reproducible:
  UNKNOWN
```

Do not claim it is reproducible without evidence.

---

# Artifact integrity

Where practical, track:

```text
Artifact
Build ID
Checksum
Source commit
Build profile
Build timestamp
Signing identity/reference
```

Do not store private signing material in the context.

Example:

```yaml
artifact:
  name: "production-ios-104.ipa"
  build: "104"
  source_commit: "abc1234"
  checksum: "<recorded checksum>"
```

---

# Testing context

Testing should record results, not just test intentions.

Example:

```yaml
testing:
  unit:
    status: "PASS"
    evidence: "CI run 1821"

  e2e:
    status: "PASS"
    evidence: "CI run 1822"

  device:
    status: "UNKNOWN"
    reason: "No physical-device result provided"

  smoke:
    status: "PASS"
    evidence: "Release smoke test report"
```

Use:

```text
PASS
FAIL
BLOCKED
UNKNOWN
SKIPPED
NOT_APPLICABLE
```

---

# Test scope should match release scope

The Release Context should help agents understand why a test is required.

Example:

```text
Changed:
Push notification handling

Required:
Push notification test

Changed:
Deep-link routing

Required:
Deep-link test

Changed:
Payment entitlement logic

Required:
Payment validation
```

Do not blindly run every possible test for every small release.

---

# Audit context

Track specialist results separately.

Example:

```yaml
audits:
  release:
    status: "PASS"

  security:
    status: "PASS"

  privacy:
    status: "NOT_APPLICABLE"

  dependencies:
    status: "PASS"

  metadata:
    status: "PASS"
```

Where possible include:

```text
review ID
timestamp
commit
scope
findings
verdict
```

---

# Audit evidence

A status without evidence is weak.

Prefer:

```yaml
security:
  status: "PASS"
  evidence:
    report: "security-audit-2026-08-11"
    commit: "abc1234"
    reviewed_at: "2026-08-11T12:00:00Z"
```

Do not include secrets or sensitive personal data.

---

# Security context

For security-sensitive releases include:

```text
Authentication changes
Authorization changes
Sensitive data changes
Permissions
Secrets
Dependencies
Third-party SDKs
AI capabilities
Production access
```

Example:

```yaml
security:
  changes:
    - "New OAuth callback"
    - "New document access endpoint"

  status: "CONDITIONAL"

  critical:
    - "Resource ownership verification pending"

  high: []
```

The Security Auditor remains the source of security findings.

Release Context only carries the relevant result.

---

# Privacy context

When relevant, record:

```text
New data collection
New permissions
Tracking
Analytics
Advertising
Sensitive data
Deletion
Retention
Store privacy declarations
```

Example:

```yaml
privacy:
  status: "PASS"
  changes:
    - "Added camera permission"
  evidence: "privacy-audit-2026-08-11"
```

Do not copy the entire privacy policy into release context.

---

# Dependency context

Track changes that can affect release risk.

Example:

```yaml
dependencies:
  changed: true
  packages:
    - name: "expo"
      from: "old-version"
      to: "new-version"
    - name: "react-native"
      from: "old-version"
      to: "new-version"

  audit:
    status: "PASS"
```

Use the Dependency Auditor for detailed analysis.

---

# Store context

Store status should be platform-specific.

Example:

```yaml
store:
  ios:
    status: "IN_REVIEW"
    version: "2.4.0"
    build: "104"
    submission: "SUBMITTED"
    review: "IN_REVIEW"

  android:
    status: "RELEASED"
    version: "2.4.0"
    build: "104"
    submission: "COMPLETE"
    review: "APPROVED"
```

Do not infer store status from local build status.

A successful build does not mean:

```text
Submitted
```

and submission does not mean:

```text
Approved
```

---

# Rollout context

Track:

```text
Strategy
Status
Percentage
Start time
Pause state
```

Example:

```yaml
rollout:
  strategy: "staged"
  status: "ACTIVE"
  percentage: 25
```

If rollout information is unavailable:

```yaml
rollout:
  status: "UNKNOWN"
```

---

# Monitoring context

Post-release monitoring should capture only information useful for the current release.

Example:

```yaml
monitoring:
  crash_rate:
    status: "STABLE"

  error_rate:
    status: "STABLE"

  critical_flows:
    status: "PASS"

  incidents: []

  health: "HEALTHY"
```

Monitoring data should be linked to the release where possible.

---

# Incident context

If an incident exists:

```yaml
monitoring:
  incidents:
    - id: "INC-104"
      severity: "HIGH"
      status: "OPEN"
      impact: "Login failures"
      release_related: true
```

Do not include unnecessary user data.

---

# Risk context

Use risk levels:

```text
CRITICAL
HIGH
MEDIUM
LOW
```

Example:

```yaml
risks:
  critical: []
  high:
    - "New OAuth callback path"
  medium:
    - "New native dependency"
  low:
    - "Metadata changes"
```

Risks should come from evidence.

Do not create a risk simply because a component exists.

---

# Unknowns

Unknowns are first-class context.

Example:

```yaml
unknowns:
  - "Physical iOS device test result was not provided."
  - "Android rollout percentage is not available."
```

This allows the AI to reason safely.

A good agent response should say:

```text
Not enough evidence yet.
```

when an unknown affects the decision.

---

# Approval context

Track human approval explicitly.

Example:

```yaml
approvals:
  required: true
  status: "PENDING"
  approved_by: null
  approved_at: null
```

After approval:

```yaml
approvals:
  required: true
  status: "APPROVED"
  approved_by: "<human>"
  approved_at: "<timestamp>"
```

Do not treat:

```text
AI recommendation = approval
```

Approval is a human decision for high-impact release actions.

---

# Current state

The context should make the next decision obvious.

Example:

```yaml
release:
  current_state: "READY_FOR_APPROVAL"
```

Then:

```text
Required action:
Human approval
```

Another example:

```yaml
release:
  current_state: "BLOCKED"
```

with:

```yaml
risks:
  high:
    - "Android physical-device validation failed"
```

Then:

```text
Required action:
Fix or assess the Android validation failure.
```

---

# Next action

Always provide a concrete next action when one is known.

Good:

```yaml
next_action:
  owner: "release-owner"
  action: "Complete Android physical-device smoke test."
  blocking: true
```

Bad:

```yaml
next_action:
  action: "Continue release process."
```

The next action should be executable.

---

# Evidence

Evidence should identify where a fact came from.

Example:

```yaml
evidence:
  - type: "ci"
    source: "GitHub Actions"
    reference: "run-1821"
    status: "VERIFIED"

  - type: "build"
    source: "EAS"
    reference: "build-104"
    status: "VERIFIED"

  - type: "security"
    source: "Security Audit"
    reference: "security-audit-2026-08-11"
    status: "VERIFIED"
```

Prefer references over copying large reports into context.

---

# Evidence status

Use:

```text
VERIFIED
UNVERIFIED
STALE
UNKNOWN
```

If a result was generated against an older commit, it may be stale.

Example:

```text
Security audit:
PASS

Audit commit:
abc1234

Current release commit:
def5678

Result:
STALE
```

Do not reuse an old PASS as current evidence without checking whether the changed scope makes it invalid.

---

# Freshness

Release Context should identify when important evidence was created.

Useful fields:

```text
created_at
updated_at
reviewed_at
commit
build
```

Do not treat a recent timestamp as proof that the content itself is current.

The actual release version and commit matter.

---

# Context freshness rules

A result can become stale when:

```text
Code changed
Dependencies changed
Configuration changed
Build changed
Platform target changed
Release scope changed
Security-sensitive behavior changed
```

Example:

```text
QA PASS on build 101
```

does not automatically validate:

```text
build 104
```

---

# Context merging

Multiple sources may contribute to one Release Context.

Example:

```text
Git
+
CI
+
EAS
+
QA
+
Security
+
Store
+
Monitoring
```

When merging:

```text
Prefer newer verified evidence
Prefer exact release/build/commit matches
Preserve conflicting values
Do not silently overwrite disagreements
```

If two sources disagree:

```yaml
conflict:
  field: "current_state"
  values:
    - source: "CI"
      value: "BUILT"
    - source: "Store"
      value: "IN_REVIEW"
```

The correct interpretation may be that the sources describe different stages.

Do not flatten them into one inaccurate value.

---

# Context conflict handling

When evidence conflicts:

```text
1. Identify the field.
2. Identify the sources.
3. Check timestamps.
4. Check release/build/commit.
5. Determine whether the sources describe different stages.
6. Preserve the conflict if unresolved.
7. Mark the affected value UNKNOWN if necessary.
```

Never resolve conflicts by guessing.

---

# AI context boundaries

Only provide an agent the data it needs.

For example:

```text
Metadata Agent
→ store metadata
→ scope
→ platform
→ version/build
```

It normally does not need:

```text
Production database credentials
Private signing keys
Unrelated user data
```

Security-sensitive context should follow least privilege.

---

# Secret handling

Never put:

```text
API keys
Passwords
Private keys
Signing secrets
OAuth client secrets
Database credentials
Store credentials
AI provider secrets
```

inside Release Context.

Instead record:

```yaml
secret:
  configured: true
  source: "CI secret store"
  exposed_to_agent: false
```

---

# Sensitive data handling

Avoid putting raw:

```text
User records
Authentication tokens
Payment details
Health information
Private documents
Personal identifiers
```

into general release context.

Use:

```text
Aggregated results
Redacted evidence
References
Counts
Statuses
```

where possible.

---

# AI authorization context

When an AI agent can perform actions, Release Context should identify:

```text
Available tools
Permissions
Allowed actions
Approval requirements
```

Example:

```yaml
ai_permissions:
  repository:
    read: true
    write: false

  ci:
    read: true
    trigger_build: true

  store:
    read: true
    submit: false

  production:
    read: false
    write: false
```

Do not use Release Context as a mechanism to grant permissions.

Actual tool authorization must remain enforced by the execution system.

---

# Human approval boundary

Release Context should make high-impact actions explicit.

Examples:

```text
Store submission
Production release
Signing changes
Credential changes
Destructive actions
Account changes
Financial actions
Permission changes
```

Represent them as:

```yaml
approval:
  required: true
```

The context informs the agent.

The execution layer enforces the boundary.

---

# Agent-specific context

Agents should receive a filtered context.

## Release Auditor

Needs:

```text
Release identity
Source
Platforms
Configuration
Build
Testing
Audits
Store
Risks
Unknowns
```

## QA Agent

Needs:

```text
Release scope
Platforms
Build
Changed flows
Configuration
Required tests
Previous failures
```

## Security Auditor

Needs:

```text
Release scope
Changed files
Authentication changes
Authorization changes
Dependencies
Permissions
Secrets configuration
AI changes
Relevant architecture
```

## Metadata Agent

Needs:

```text
Application
Version
Platform
Release scope
User-facing changes
Store state
Localization
Screenshots
Release notes
```

## Dependency Auditor

Needs:

```text
Lockfile
Dependency changes
Framework versions
Native dependencies
Build environment
Known compatibility constraints
```

## Debugging Agent

Needs:

```text
Failure
Logs
Stack trace
Build
Commit
Environment
Recent changes
Reproduction
```

## Rejection Analyzer

Needs:

```text
Store
Version
Build
Rejection message
Review metadata
Submitted artifact
Previous response
```

## Release Manager

Needs:

```text
Full release state
Gate results
Risks
Unknowns
Approvals
Store status
Monitoring
Next action
```

---

# Context lifecycle

Release Context should evolve with the release.

```text
Created
   ↓
Updated during planning
   ↓
Updated during build
   ↓
Updated during testing
   ↓
Updated during approval
   ↓
Updated during submission
   ↓
Updated during rollout
   ↓
Updated during monitoring
   ↓
Archived after closure
```

Do not create a completely new context for every step.

Update the existing release context while preserving important history.

---

# Context versioning

If Release Context becomes machine-readable, version its schema.

Example:

```yaml
context_schema:
  version: "1"
```

A schema change should be explicit.

Do not silently rename important fields if workflows depend on them.

---

# Context immutability

Some fields should be treated as historical facts once recorded.

Examples:

```text
Submitted artifact
Source commit
Approval decision
Submission timestamp
Store response
Incident record
```

If a correction is required:

```text
Preserve the original
+
record the correction
```

Do not rewrite history without traceability.

---

# Release Context and repository state

The repository remains the source of truth for code.

Release Context is a structured view of the release.

Do not copy the entire repository into context.

Prefer:

```text
Context
→ references
→ summaries
→ verified states
→ targeted evidence
```

The agent can retrieve more detail when needed.

---

# Release Context and CI

CI can provide:

```text
Commit
Build
Tests
Artifacts
Environment
Status
Logs
```

The context should record the result and reference the CI run.

Do not copy huge CI logs into the context.

---

# Release Context and EAS

For Expo workflows, context may include:

```text
EAS project
Build profile
Platform
Build ID
Build status
Artifact
Source commit
Submission status
Update channel
```

Keep Expo-specific implementation details in:

```text
frameworks/expo/
```

The release context should remain generic enough for other frameworks.

---

# Release Context and React Native

For React Native workflows, context may include:

```text
React Native version
Native changes
iOS build
Android build
Signing state
Fastlane state
EAS state where used
```

Keep framework-specific instructions in:

```text
frameworks/react-native/
```

---

# Release Context and community frameworks

The core context model should not require Expo or React Native.

A future framework can provide:

```text
platform
build
artifact
source
testing
submission
store
```

through the same conceptual model.

Framework-specific fields should be namespaced or added only when necessary.

---

# Example: Expo release

```yaml
context_schema:
  version: "1"

release:
  id: "2026-08-11-01"
  application: "Example App"
  version: "2.4.0"
  release_type: "FEATURE_RELEASE"
  environment: "production"
  current_state: "READY_FOR_APPROVAL"

source:
  repository: "example/mobile-app"
  branch: "main"
  commit: "abc1234"
  working_tree: "clean"

platforms:
  ios:
    state: "READY_FOR_APPROVAL"
    build: "104"
    bundle_identifier: "com.example.app"

  android:
    state: "READY_FOR_APPROVAL"
    version_code: 104
    application_id: "com.example.app"

scope:
  included:
    - "New onboarding"
    - "Push notification improvements"

  excluded:
    - "Subscription redesign"

build:
  status: "PASS"
  profiles:
    ios: "production"
    android: "production"
  source_commit: "abc1234"

testing:
  e2e:
    status: "PASS"
  device:
    status: "PASS"
  smoke:
    status: "PASS"

audits:
  release:
    status: "PASS"
  security:
    status: "PASS"
  privacy:
    status: "PASS"
  dependencies:
    status: "PASS"
  metadata:
    status: "PASS"

store:
  ios:
    status: "READY"
    build: "104"

  android:
    status: "READY"
    build: "104"

risks:
  critical: []
  high: []
  medium: []
  low: []

unknowns: []

approvals:
  required: true
  status: "PENDING"

next_action:
  owner: "release-owner"
  action: "Review release evidence and approve production submission."
  blocking: true
```

---

# Example: blocked release

```yaml
release:
  current_state: "BLOCKED"

platforms:
  ios:
    state: "READY_FOR_APPROVAL"

  android:
    state: "BLOCKED"

testing:
  device:
    status: "FAIL"
    evidence: "android-device-test-104"

risks:
  critical: []
  high:
    - "Crash during login on Android physical device"

unknowns: []

approvals:
  required: true
  status: "PENDING"

next_action:
  owner: "engineering"
  action: "Investigate and fix Android login crash, then rerun device validation."
  blocking: true
```

The AI should not recommend submission.

---

# Example: insufficient evidence

```yaml
testing:
  device:
    status: "UNKNOWN"
    reason: "No physical device result available"

approvals:
  required: true
  status: "PENDING"

unknowns:
  - "Android physical-device validation is missing."

next_action:
  owner: "qa"
  action: "Complete required Android physical-device smoke tests."
  blocking: true
```

The correct AI response is:

```text
Not enough evidence yet.
```

if the missing test is required for approval.

---

# Example: store rejection

```yaml
release:
  current_state: "REJECTED"

store:
  ios:
    status: "REJECTED"
    version: "2.4.0"
    build: "104"
    review: "REJECTED"

risks:
  high:
    - "Store rejection requires metadata correction"

unknowns: []

next_action:
  owner: "release-owner"
  action: "Review rejection, apply the required metadata correction, and prepare resubmission."
  blocking: true
```

The Rejection Analyzer should provide the detailed analysis.

---

# Example: post-release incident

```yaml
release:
  current_state: "MONITORING"

rollout:
  strategy: "staged"
  status: "PAUSED"
  percentage: 25

monitoring:
  health: "DEGRADED"
  incidents:
    - id: "INC-104"
      severity: "HIGH"
      status: "OPEN"
      release_related: true

next_action:
  owner: "engineering"
  action: "Investigate release-related crash increase and determine mitigation."
  blocking: true
```

The context makes the operational state visible without copying the entire incident report.

---

# Context validation

Before giving Release Context to an AI agent, validate:

```text
[ ] Application identified
[ ] Version identified
[ ] Environment identified
[ ] Platform identified
[ ] Source commit identified where available
[ ] Build identified where available
[ ] Current state explicit
[ ] Scope explicit
[ ] Test states explicit
[ ] Audit states explicit
[ ] Store state explicit
[ ] Risks explicit
[ ] Unknowns explicit
[ ] Approval state explicit
[ ] Next action explicit
[ ] No secrets included
[ ] No unnecessary sensitive data included
[ ] Evidence references are traceable
[ ] Stale evidence identified
```

---

# Context quality rules

A good context is:

```text
Small enough to read
Specific enough to reason about
Current enough to trust
Traceable enough to verify
Safe enough to share with the intended agent
```

A bad context is:

```text
Huge
Duplicated
Stale
Secret-filled
Ambiguous
Missing release identity
Missing evidence
```

---

# AI reasoning rules

When using Release Context:

```text
1. Read release identity.
2. Read current state.
3. Read scope.
4. Check source/build alignment.
5. Check required gates.
6. Check risks.
7. Check unknowns.
8. Check approval state.
9. Determine whether evidence is current.
10. Recommend the smallest safe next action.
```

Do not:

```text
Invent missing values
Ignore UNKNOWN
Treat old evidence as current
Treat AI output as approval
Ignore platform-specific state
Expose secrets
```

---

# Context conflict rules

If the context says:

```text
Build:
104
```

but the store says:

```text
Build:
103
```

do not assume which one is correct.

Check:

```text
Submission record
Artifact
Store status
Build metadata
Timestamp
```

Then resolve or report:

```text
CONFLICT
```

---

# Context freshness rules

Evidence should be considered stale when it no longer matches the release.

At minimum compare:

```text
Release version
Build
Commit
Platform
Environment
Scope
```

Example:

```text
Security audit:
commit abc1234

Current release:
commit def5678
```

The security result may be stale.

Do not automatically reuse it.

---

# Context minimization

Do not include information just because it is available.

Ask:

```text
Does the agent need this?
Does it affect the current decision?
Can it be represented as a status or reference?
Is it sensitive?
Is it stale?
```

Prefer:

```text
security.status = PASS
```

with:

```text
security.evidence = "security-audit-104"
```

over copying the entire audit report.

---

# Machine-readable vs human-readable context

Use machine-readable context when:

```text
Multiple agents consume it
Automation needs it
Release state must be queried
CI updates it
```

Use Markdown when:

```text
Humans need to review it
The context is mostly narrative
A release handoff is being prepared
```

Do not introduce a database or service just to store release context.

A file, generated artifact, or existing CI/store metadata may be sufficient.

---

# Recommended storage

For an open-source playbook, prefer simple storage.

Possible options:

```text
Generated YAML/JSON artifact
Release metadata file
CI artifact
Release handoff Markdown
Existing release-management system
```

Do not require a backend service for the playbook itself.

The playbook is documentation and workflow guidance, not a release-management SaaS.

---

# Context generation

A practical generation flow is:

```text
Repository
   ↓
Git metadata
   ↓
Build system
   ↓
CI
   ↓
Testing
   ↓
Specialist audits
   ↓
Store
   ↓
Monitoring
   ↓
Release Context
```

Then:

```text
Release Context
→ filtered agent context
→ agent analysis
→ verified result
→ Release Context update
```

---

# Context update rules

When updating:

```text
Preserve release identity
Preserve source traceability
Update current state
Update platform state
Update gates
Add evidence
Record new risks
Resolve or preserve unknowns
Update next action
```

Do not overwrite historical approval or submission information.

---

# Context archive

After release closure, retain enough information to reconstruct:

```text
What was released
Which build
Which commit
Which platforms
Which checks passed
Who approved
What happened after release
What incidents occurred
What hotfixes followed
```

Do not retain sensitive data unnecessarily.

Follow the repository's privacy and retention requirements.

---

# Related agents

Release Context is consumed by:

```text
ai/agents/release-auditor.md
ai/agents/release-manager.md
ai/agents/qa-agent.md
ai/agents/security-auditor.md
ai/agents/privacy-auditor.md
ai/agents/dependency-auditor.md
ai/agents/metadata-agent.md
ai/agents/debugging-agent.md
ai/agents/rejection-analyzer.md
```

---

# Related workflows

Use with:

```text
ai/workflows/release-audit.md
ai/workflows/release-preparation.md
ai/workflows/ai-qa.md
ai/workflows/debugging.md
ai/workflows/rejection-analysis.md
```

---

# Related foundations

Release Context depends on the concepts in:

```text
foundations/mobile-release-lifecycle.md
foundations/release-environments.md
foundations/project-configuration.md
foundations/identifiers.md
foundations/versioning.md
foundations/dependency-management.md
foundations/device-support.md
```

---

# Security requirements

Release Context must follow the repository security model:

```text
Least privilege
Defense in depth
Server-side authorization
Secret protection
Sensitive data minimization
Input validation
Auditability
Human approval for high-impact actions
```

Never place:

```text
API keys
Passwords
Private keys
Signing secrets
Production tokens
Store credentials
```

inside the context.

AI-generated output remains untrusted until verified.

---

# Final rule

Release Context exists to answer one question:

> **What exactly is happening with this release right now?**

A useful context should let an engineer or AI agent quickly determine:

```text
What are we releasing?
From which code?
For which platform?
In which environment?
What changed?
What passed?
What failed?
What is unknown?
What is risky?
What has been approved?
What is the store doing?
What is happening in production?
What should happen next?
```

If the context cannot answer those questions, improve the context before asking an AI agent to make a release decision.

The goal is not to give AI more information.

The goal is to give it the **right verified information, at the right time, with the right permissions.**
