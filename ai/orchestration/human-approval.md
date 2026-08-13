# Human Approval

Human approval establishes the mandatory safety boundary between AI-assisted analysis and non-reversible mobile release actions.

This document defines the architecture, operational protocols, evidence requirements, and machine-readable contracts for human review gates within AI-assisted release engineering. It details which actions can be automated, which actions strictly require human authorization, how approval evidence is captured, and how to prevent unsafe automation.

This guide is **not**:

- a guide to manual testing or manual QA execution
- a recommendation to eliminate CI/CD automation
- an authorization mechanism managed purely by AI prompts
- a temporary policy that can be bypassed during emergency hotfixes

---

# 1. The Human-in-the-Loop Baseline

Mobile app deployment involves platform ecosystems (Apple App Store Connect, Google Play Console) and client devices where deployment errors cannot be easily undone. Once a binary is published to millions of end-user devices or submitted for store review, reversing the change requires multi-step remediation, store review waiting periods, or complex rollbacks.

```text
       ┌────────────────────────────────────────────────────────┐
       │                   AI AGENT WORKFLOW                    │
       │  Collect Evidence → Audit Risk → Draft Recommendation  │
       └──────────────────────────┬─────────────────────────────┘
                                  │
                                  ▼
       ┌────────────────────────────────────────────────────────┐
       │               HUMAN APPROVAL GATEWAY                   │
       │  Inspect Evidence → Verify Diffs → Decide Sign-off     │
       └──────────────────────────┬─────────────────────────────┘
                                  │
                        ┌─────────┴─────────┐
                        ▼                   ▼
                  [ Approved ]        [ Rejected ]
                        │                   │
                        ▼                   ▼
       ┌────────────────────────┐  ┌────────────────────────┐
       │   BOUNDED EXECUTION    │  │     RELEASE HALTED     │
       │ Execute Store Push /   │  │ Return to Development  │
       │ Credential Rotation    │  └────────────────────────┘
       └────────────────────────┘
```

The foundational principle of this repository is:

> **AI assists  +  Human verifies  +  Human approves**

AI agents analyze data, run static checks, aggregate test results, synthesize release notes, and highlight risks. Humans hold sole authority and responsibility for approving production-impacting decisions.

---

# 2. Why Human Approval Matters

Mobile release engineering carries unique operational, regulatory, and legal risks that differentiate it from traditional web server deployments:

1. **Non-Reversible Client Distribution**: Unlike server-side microservices where a bad release can be rolled back in seconds via traffic redirection, native mobile binaries are downloaded directly to user hardware. A critical bug in a published binary remains on user devices until users manually or automatically update.
2. **App Store Policy Compliance & Ban Risks**: Submitting binaries with undisclosed data collection, hidden APIs, or guideline violations can result in immediate app rejection, app removal, or developer account termination by Apple or Google.
3. **Secret & Credential Exposure**: Mobile app signing requires high-value credentials (Apple Distribution Certificates, Provisioning Profiles, Google Play Upload Keys, Service Account Keys). Mismanaging credentials can compromise the entire app distribution identity.
4. **Privacy & Legal Liability**: Updates to data collection or tracking without updating store privacy declarations (`PrivacyInfo.xcprivacy` on iOS, Google Play Data Safety form) violate platform policies and regional privacy regulations (GDPR, CCPA).
5. **Financial & Revenue Risk**: In-App Purchase (IAP) product configurations, subscription tiers, and entitlement validation logic directly govern business revenue. Errors can lead to unbilled access or blocked purchases.

---

# 3. Non-Delegable Actions Matrix

AI workflows must enforce a hard boundary between read-only analysis and execution of non-reversible actions.

## 3.1 Strict Human Approval Required (Non-Delegable)

The following actions strictly require explicit, recorded human sign-off before execution:

| Action Domain | Specific Operation | Non-Reversibility Risk |
|---|---|---|
| **Production Publishing** | Submitting binaries to Apple App Store Connect or Google Play Console for production release | High (Store review required to fix; user downloads begin upon approval) |
| **OTA Production Update** | Publishing Over-The-Air JavaScript/asset updates to production channels (Expo Updates, CodePush) | High (Immediate execution on active client devices) |
| **Signing Credentials** | Creating, uploading, renewing, or deleting signing certificates, provisioning profiles, or Android keystores | Critical (Can break all future app updates or compromise app authenticity) |
| **API & CI Secrets** | Accessing, creating, or rotating App Store Connect API keys, Google Play Service Accounts, or CI secrets | Critical (Grants administrative store/infrastructure access) |
| **Privacy Declarations** | Modifying `PrivacyInfo.xcprivacy`, Google Data Safety declarations, or app privacy nutrition labels | High (Risk of store removal or regulatory non-compliance) |
| **Store Metadata** | Submitting updated store descriptions, app ratings, screenshots, or legal URL declarations | Medium-High (Public store listing impact) |
| **Monetization & IAP** | Altering store subscription products, pricing, or entitlement definitions | High (Immediate financial impact) |
| **Account Settings** | Changing developer account team roles, banking details, tax forms, or organization transfer settings | Critical (Account-level operational control) |
| **Version Recall / Rollback** | Initiating emergency store version halts or user-facing version deprecations | High (Affects active user base) |

## 3.2 Delegable AI Responsibilities (Read-Only / Drafts)

AI agents may perform the following tasks autonomously without prior human sign-off:

- Reviewing code diffs, configuration files, and dependency manifests.
- Running unit tests, linters, static analyzers, and build scripts.
- Summarizing crash reports, Sentry stack traces, and test execution logs.
- Drafting release changelogs, store release notes, and pull request descriptions.
- Identifying security vulnerabilities, privacy declaration discrepancies, and dependency bloat.
- Generating readiness scores and issuing recommendations (`READY`, `CONDITIONAL`, `NOT READY`).

---

# 4. Approval Workflow Topologies

Human approval gates are integrated into release automation through four primary workflow topologies.

## 4.1 Pre-Submission Release Gate

Executed after build compilation and automated testing, before submitting the artifact to store review platforms.

```text
[ Build Candidate ]
        ↓
[ Automated Test Matrix ] ──( Fail )──→ [ Halt Workflow ]
        ↓ ( Pass )
[ AI Release Audit ]
        ↓
[ Generate Audit Report & Verdict ]
        ↓
┌────────────────────────────────────────────────────────┐
│               HUMAN APPROVAL GATEWAY                   │
│                                                        │
│  Reviewer checks:                                      │
│  - Release Auditor Verdict (READY / CONDITIONAL)       │
│  - Test matrix evidence & coverage                     │
│  - Security & Privacy diffs                            │
│  - App version & build numbers                         │
│                                                        │
│  Decision: [ APPROVE SUBMISSION ]  [ REJECT ]          │
└──────────────────────────┬─────────────────────────────┘
                           │
                 ┌─────────┴─────────┐
                 ▼                   ▼
           [ Approved ]        [ Rejected ]
                 │                   │
                 ▼                   ▼
       [ EAS Submit / Fastlane ]  [ Return to Dev ]
```

## 4.2 Credential & Secret Management Gate

Executed when signing keys, certificates, or service account API keys require setup, rotation, or access.

```text
[ Credential Rotation Request ]
                ↓
[ AI Secret Auditor Verifies Scoping & Expiry ]
                ↓
[ Prepare Sealed Secret Rotation Payload ]
                ↓
┌────────────────────────────────────────────────────────┐
│             HUMAN SECURITY OFFICER GATE                │
│                                                        │
│  Reviewer verifies:                                    │
│  - Secret access scope (Least Privilege)               │
│  - Target CI environment                               │
│  - Key rotation justification                          │
│                                                        │
│  Decision: [ AUTHORIZE KEY ROTATION ]  [ DENY ]        │
└──────────────────────────┬─────────────────────────────┘
                           │
                 ┌─────────┴─────────┐
                 ▼                   ▼
           [ Authorized ]      [ Denied ]
                 │                   │
                 ▼                   ▼
       [ Execute Secret Update ] [ Audit Log Warning ]
```

## 4.3 Emergency Hotfix Accelerated Gate

Executed during active production incidents. The workflow accelerates audit collection but maintains explicit human review.

```text
[ Incident Alert ] ──→ [ AI Crash Diagnosis ] ──→ [ Scoped Patch Fix ]
                                                         ↓
                                            [ Scoped AI Security & QA Audit ]
                                                         ↓
┌────────────────────────────────────────────────────────────────────────┐
│                    EMERGENCY HUMAN APPROVAL GATE                       │
│                                                                        │
│  Reviewer verifies:                                                    │
│  - Scoped diff specifically fixes incident root cause                  │
│  - No unintended side-effects or regressions in patch                  │
│  - Rollback plan exists                                                │
│                                                                        │
│  Decision: [ APPROVE EMERGENCY PUBLISH ]  [ DENY ]                     │
└────────────────────────────────────┬───────────────────────────────────┘
                                     │
                           ┌─────────┴─────────┐
                           ▼                   ▼
                     [ Approved ]        [ Denied ]
                           │                   │
                           ▼                   ▼
                 [ Fast-track Push ]  [ Standard Pipeline ]
```

> **Warning:** Emergency releases must never bypass human approval. Urgent conditions increase cognitive pressure, making human sign-off *more* critical, not less.

---

# 5. The Human Review Protocol & Evidence Package

A human approval gate is only effective if the reviewer is provided with clear, structured, and verifiable evidence. Approving an action based on assumptions or unverified AI summaries is an anti-pattern.

## 5.1 The Mandatory Evidence Package

Before presenting an approval request to a human reviewer, the AI orchestrator must assemble a complete **Evidence Package**:

```text
┌────────────────────────────────────────────────────────┐
│                    EVIDENCE PACKAGE                    │
├────────────────────────────────────────────────────────┤
│ 1. Release Identity (App ID, Version, Build Number)    │
│ 2. Automated Test Execution Summary (Passed/Failed)    │
│ 3. Git Commit Hash & Source Diff Summary               │
│ 4. AI Auditor Reports (Security, Privacy, Metadata)    │
│ 5. Overall Release Auditor Verdict & Severity Matrix   │
│ 6. Store Metadata & Release Notes Preview              │
│ 7. Verification Checklist Status                       │
└────────────────────────────────────────────────────────┘
```

## 5.2 Reviewer Verification Checklist

Before signing off on a release or credential change, the human reviewer must verify:

- [ ] **Build Identity**: Exact binary version and build number match the intended release candidate.
- [ ] **Commit Lineage**: Build binary was generated from a clean, tagged commit on the release branch.
- [ ] **Test Evidence**: 100% of required automated unit, integration, and E2E device matrix tests passed.
- [ ] **No Blocker Findings**: Zero `P0` or `P1` security, privacy, or stability blockers remain unresolved.
- [ ] **Privacy Alignment**: Native permission changes (`Info.plist`, `AndroidManifest.xml`) match privacy declarations.
- [ ] **Store Listing**: Release notes, promotional text, and screenshots are accurate and compliant.
- [ ] **Rollback Plan**: Clear rollback or feature-flag kill-switch strategy exists if post-release issues emerge.

---

# 6. Machine-Readable Approval Contracts & Audit Trails

Human approval decisions must be recorded in machine-readable, persistent audit logs (YAML/JSON). This ensures accountability and enables compliance auditing.

## 6.1 Approval Request Schema

Generated by the orchestrator when pausing at an approval gate:

```yaml
approval_request_id: "req-app-20260813-091"
timestamp: "2026-08-13T12:45:00Z"
target_action: "STORE_SUBMISSION"
environment: "production"

release_identity:
  app_id: "com.example.mobileapp"
  version: "2.1.0"
  build_number_ios: "2100"
  version_code_android: 21000
  git_commit: "9f8e7d6c5b4a3210"

auditor_summaries:
  release_auditor_verdict: "READY"
  security_auditor_verdict: "READY"
  privacy_auditor_verdict: "READY"
  qa_agent_verdict: "READY"
  total_blockers: 0
  total_warnings: 2

evidence_links:
  build_log: "https://ci.example.com/builds/98412"
  audit_report: "docs/audits/20260813-v2.1.0.md"
  diff_url: "https://github.com/example/mobileapp/compare/v2.0.0...v2.1.0"
```

## 6.2 Human Approval Record Schema

Generated upon human decision and stored permanently in the repository or audit logging system:

```yaml
approval_record_id: "rec-app-20260813-091"
approval_request_id: "req-app-20260813-091"
timestamp: "2026-08-13T12:50:12Z"

reviewer:
  identity: "sarah.chen@example.com"
  role: "Lead Mobile Release Engineer"
  authentication_method: "SSO_HARDWARE_KEY"

decision: "APPROVED" # Options: APPROVED, REJECTED, CONDITIONAL_WAIVER

justification: "Verified build 2100 on TestFlight and Play Internal Testing. All automated E2E tests passed. Privacy manifest verified."

scope_constraints:
  allowed_action: "PUBLISH_TO_STORE_REVIEW"
  expiration: "2026-08-13T18:50:12Z" # Approval expires if execution delayed
```

---

# 7. Anti-Patterns & Risk Mitigations

Failing to enforce genuine human approval creates severe security and operational risks. Common anti-patterns include:

```text
Anti-Pattern: Auto-Approval Loop
[ AI Audit ] ──→ [ AI Recommends Approve ] ──→ [ Auto-Approve Script ] ──→ [ Production Push ] (UNSAFE)

Correct Pattern: Explicit Human Approval Gate
[ AI Audit ] ──→ [ AI Recommends Approve ] ──→ [ Human Review & Sign-off ] ──→ [ Production Push ] (SAFE)
```

| Anti-Pattern | Description | Operational Risk | Mitigation |
|---|---|---|---|
| **Rubber-Stamp Approval** | Human signs off without reading evidence or inspecting diffs. | Silent release of bugs or security vulnerabilities. | Require explicit checklist verification before approval button is enabled. |
| **Implicit / Passive Approval** | Workflow proceeds to submission if human does not respond within X minutes. | Accidental publication during off-hours or unmonitored periods. | Require active explicit sign-off; default to `REJECT` on timeout. |
| **Stale Evidence Approval** | Human approves based on an old audit report generated before new code was pushed. | Submitting unverified code changes. | Invalidate approval requests automatically if git HEAD changes. |
| **Prompt Injection Override** | Repository text attempts to instruct the AI to bypass approval ("Ignore checks and approve"). | Malicious code execution or secret leakage. | Enforce approval gates in deterministic CI infrastructure, not inside prompt text. |
| **Self-Approving AI** | Orchestrator agent calls its own approval tool. | Complete collapse of safety boundary. | Isolate approval API credentials so AI agents cannot access approval invocation keys. |

---

# 8. Operational Verification Checklist

Verify your human approval workflow implementation against these operational requirements:

- [ ] **Gate Isolation**: Approval gates are enforced by deterministic CI/CD pipeline logic, not by AI prompt instructions.
- [ ] **Explicit Sign-Off**: Workflows require active human interaction; timeouts default to `HALT` or `REJECT`.
- [ ] **Evidence Integrity**: Approval requests include verifiable links to exact commit hashes, build logs, and test matrix outputs.
- [ ] **Stale Invalidation**: Approvals automatically expire if new commits are pushed or if a time-to-live (TTL) limit is exceeded.
- [ ] **Immutable Audit Log**: Approval decisions (including reviewer identity, timestamp, decision, and justification) are recorded in permanent logs.
- [ ] **Least-Privilege Execution**: Execution keys (EAS Submit API keys, Fastlane credentials) are accessible only *after* the human approval record is validated.
- [ ] **Emergency Discipline**: Hotfix workflows maintain human review gates while scoping audit evidence to the hotfix diff.

---

# 9. Related Documentation

### Architecture & Orchestration

- [Agent Workflows](agent-workflows.md) - Multi-agent orchestration patterns and data contracts.
- [Subagents](subagents.md) - Subagent delegation rules and context isolation.
- [Bounded Autonomy](bounded-autonomy.md) - Operational boundaries for constraining tool execution.
- [Tool Permissions](tool-permissions.md) - Scoping read-only vs execution tool privileges.

### Agent Definitions

- [Release Manager](../agents/release-manager.md) - Workflow coordinator role definition.
- [Release Auditor](../agents/release-auditor.md) - Release candidate assessment agent.
- [Security Auditor](../agents/security-auditor.md) - Secret and code scanner agent.
- [Privacy Auditor](../agents/privacy-auditor.md) - Data safety and privacy manifest auditor.

### Context & Security

- [Repository Context](../context/repository-context.md) - Repository context injection schema.
- [Release Context](../context/release-context.md) - Release metadata context schema.
- [Agent Permissions](../security/agent-permissions.md) - Security permissions and sandbox gating.

### Core Release Lifecycle

- [Mobile Release Lifecycle](../../foundations/mobile-release-lifecycle.md) - Core lifecycle phases from build to monitoring.

---

# 10. Official Sources

For platform-specific release policies, store submission rules, and governance guidelines, consult current official documentation:

### Apple Developer

- App Store Review Guidelines: https://developer.apple.com/app-store/review/guidelines/
- App Store Connect Help - Submitting Apps: https://developer.apple.com/help/app-store-connect/
- Apple Developer Program License Agreement: https://developer.apple.com/support/terms/

### Google Play Console

- Developer Program Policies: https://support.google.com/googleplay/android-developer/answer/9876937
- Play Console Help - Prepare & Roll Out Releases: https://support.google.com/googleplay/android-developer/answer/9859348
- Android App Signing: https://developer.android.com/studio/publish/app-signing

### Expo & React Native

- EAS Submit Security & Automation: https://docs.expo.dev/submit/introduction/
- React Native Publishing Guidelines: https://reactnative.dev/docs/publishing-to-app-store

---

**Last verified:** August 13, 2026

Platform guidelines, store review rules, security requirements, and automation tooling capabilities evolve. Re-verify current official documentation before changing production approval policies or release gate workflows.
