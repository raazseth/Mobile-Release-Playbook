# Release Audit Prompt

The Release Audit Prompt provides a copy-pasteable prompt template for running a comprehensive release candidate audit across all technical, operational, security, and store compliance dimensions.

Engineered following 2026 Context Engineering standards, it incorporates prompt caching layouts, single-mount variable blocks, explicit XML tag isolation, and a reasoning step (`<thinking>`) to aggregate reports from domain sub-auditors (QA, Security, Privacy, Metadata, Dependencies) and issue a final release verdict (`READY`, `CONDITIONAL`, or `NOT READY`).

This guide is **not**:

- an automated release submission bot (human sign-off is mandatory before store submission)
- a substitute for running automated test suites and security scanners
- a mechanism to waive P0/P1 release blockers without explicit human sign-off

---

# 1. Purpose & Usage Scenarios

Use this prompt when evaluating:

- **Release Candidates (RC)**: Final builds targeted for App Store Connect submission, Google Play production release, or major Over-The-Air (OTA) updates.
- **Pre-Submission Audits**: Aggregating sub-auditor findings across Security, Privacy, QA, Dependencies, and Store Metadata.
- **Human Approval Preparation**: Generating the structured **Evidence Package** required for human release manager sign-off.

---

# 2. Production Prompt Contract

Copy and paste the prompt template below into your AI tool. Populate the single-mount variables at the bottom with your release candidate metadata and sub-auditor reports.

``=`markdown
<system_instructions>
# Persona & Role
You are acting as the Lead Mobile Release Auditor responsible for conducting the final release candidate evaluation for an iOS and Android application.

# Objective
Review the aggregated sub-auditor reports, git diff summary, test matrix evidence, and build configuration, assess overall release risk, and issue a definitive Release Readiness Verdict.

# Decision & Audit Rules
1. Verdict Vocabulary:
   - READY: Zero P0/P1 blockers exist across all sub-auditing domains. Build and signing are verified. All automated tests passed.
   - CONDITIONAL: Non-blocking P2/P3 warnings exist. Safe to submit ONLY IF the Human Release Manager explicitly acknowledges and signs off on warnings.
   - NOT READY: One or more P0 or P1 blockers exist. Release MUST HALT.

2. Severity Vocabulary:
   - P0 (Release Blocker): Guaranteed crash, key secret leakage, missing privacy manifest API declaration, or failing critical test.
   - P1 (High Risk): Major functionality issue or unverified device matrix gap. Requires explicit human sign-off.
   - P2 (Medium Warning): Minor non-critical issue or unoptimized asset.
   - P3 (Low Warning): Non-functional suggestion or cosmetic cleanup.

3. Confidence Vocabulary:
   - Classify findings strictly as FACT (proven by code/test log evidence), INFERENCE (deduced), or UNKNOWN (cannot be verified from context). Do NOT guess missing test data.
</system_instructions>

<output_formatting_rules>
Provide your response in two parts:
1. Conduct your cross-domain risk evaluation inside a `<thinking>` block.
2. Output the final structured Markdown report matching the exact template below.
</output_formatting_rules>

<single_mount_variables>
<release_candidate_context>
- App Name: <APP_NAME>
- App Version: <APP_VERSION: e.g., 1.4.0>
- iOS Build Number: <BUILD_NUMBER_IOS: e.g., 140>
- Android Version Code: <VERSION_CODE_ANDROID: e.g., 1400>
- Target Environment: <ENVIRONMENT: Production Store Release>
- Git Commit: <GIT_COMMIT_HASH: e.g., a1b2c3d4e5f6>
- Framework: <FRAMEWORK: e.g., Expo SDK 51 | React Native 0.74>
</release_candidate_context>

<git_diff_summary>
<PASTE_SUMMARY_OF_CHANGES_OR_GIT_DIFF_STAT_HERE>
</git_diff_summary>

<sub_auditor_reports>
- QA Agent Report: <PASTE_QA_AGENT_SUMMARY_OR_VERDICT>
- Security Auditor Report: <PASTE_SECURITY_AUDITOR_SUMMARY_OR_VERDICT>
- Privacy Auditor Report: <PASTE_PRIVACY_AUDITOR_SUMMARY_OR_VERDICT>
- Dependency Auditor Report: <PASTE_DEPENDENCY_AUDITOR_SUMMARY_OR_VERDICT>
- Metadata Agent Report: <PASTE_METADATA_AGENT_SUMMARY_OR_VERDICT>
</sub_auditor_reports>

<build_and_signing_evidence>
- Build Status: <PASS | FAIL>
- iOS Signing Verified: <TRUE | FALSE>
- Android Signing Verified: <TRUE | FALSE>
- Rollback Strategy Available: <TRUE | FALSE>
</build_and_signing_evidence>
</single_mount_variables>
``=
````

---

# 3. Complete Execution Example

### Input Context Provided to AI:

```text
App: PayFast v1.4.0 (140)
Environment: Production Store Release
Git Commit: b8e9a7c
QA Report: READY (100% tests passed)
Security Report: READY (No hardcoded secrets)
Privacy Report: NOT READY (Missing `NSPrivacyAccessedAPICategoryFileTimestamp` in `PrivacyInfo.xcprivacy`)
Dependency Report: READY
Metadata Report: READY
Build Status: PASS | Signing Verified: TRUE
```

### Expected AI Output:

````markdown
<thinking>
1. Evaluating sub-auditor reports:
   - QA: READY (100% pass)
   - Security: READY
   - Dependency: READY
   - Metadata: READY
   - Build & Signing: PASS / Verified
   - Privacy: NOT READY -> Missing `NSPrivacyAccessedAPICategoryFileTimestamp` in `PrivacyInfo.xcprivacy`.
2. Severity of Privacy finding: P0 Release Blocker (will trigger ITMS-91053 store rejection).
3. Overall Verdict: NOT READY due to P0 Privacy blocker.
</thinking>

# Release Audit Verdict

- **Overall Verdict**: NOT READY
- **Target Release**: PayFast v1.4.0 (140 / 1400)
- **Total Blockers (P0/P1)**: 1 (P0)
- **Total Warnings (P2/P3)**: 0
- **Human Approval Required**: NO (Workflow Halted due to P0 Blocker)

# Sub-System Audit Matrix

| Domain | Sub-Auditor Verdict | Blockers | Warnings | Status |
|---|---|---|---|---|
| QA & Test Evidence | READY | 0 | 0 | PASS |
| Security & Secrets | READY | 0 | 0 | PASS |
| Privacy Compliance | NOT READY | 1 | 0 | FAIL |
| Dependencies & SDKs | READY | 0 | 0 | PASS |
| Store Metadata | READY | 0 | 0 | PASS |
| Build & Signing | READY | 0 | 0 | PASS |

# Risk & Blocker Breakdown

### Finding 1: Privacy Compliance - Missing Apple Privacy Manifest Declaration
- **Severity**: P0 (Release Blocker)
- **Confidence**: FACT (Confirmed from Privacy Auditor report)
- **Description**: App source code invokes file timestamp APIs (`stat`), but `ios/PrivacyInfo.xcprivacy` lacks the required `NSPrivacyAccessedAPICategoryFileTimestamp` declaration.
- **Remediation**: Add the file timestamp API category declaration and valid reason code to `PrivacyInfo.xcprivacy` and regenerate the iOS build candidate.

# Evidence Package for Human Review

Summary of evidence compiled for the Human Release Manager:
- [x] Git commit `b8e9a7c` is clean.
- [x] Build compiled successfully for iOS and Android.
- [x] Automated tests passed (Pass Rate: 100%).
- [x] Signing credentials verified.
- [ ] **FAILED**: Privacy declarations match code capabilities.
- [x] Rollback strategy defined.

# Final Recommendation

The release candidate PayFast v1.4.0 (140) is **NOT READY** for App Store submission. While QA, Security, Build, and Metadata checks passed, the iOS binary lacks a mandatory Apple Privacy Manifest declaration. Add the missing API declaration to `PrivacyInfo.xcprivacy`, re-compile build 141, and re-run the release audit.
````

---

# 4. Operational Verification Checklist

- [ ] **Static Instructions First**: System instructions precede single-mount variable blocks.
- [ ] **Conservative Verdict**: Any P0 or P1 blocker forces the overall verdict to `NOT READY`.
- [ ] **Reasoning Block Active**: Output features a `<thinking>` evaluation phase.
- [ ] **Evidence Package Assembled**: Output generates a clear verification checklist for the Human Release Manager.

---

# 5. Related Documentation

- [Release Auditor Agent](../agents/release-auditor.md) - Specialized Release Auditor definition.
- [Human Approval](../orchestration/human-approval.md) - Human review protocols and schemas.
- [Release Audit Workflow](../workflows/release-audit.md) - Pre-release audit topologies.

---

# 6. Official Sources

- Apple App Store Connect Release Help: https://developer.apple.com/help/app-store-connect/
- Google Play Console Release Management: https://support.google.com/googleplay/android-developer/answer/9859348

---

**Last verified:** August 13, 2026

