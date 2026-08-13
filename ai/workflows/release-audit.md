# End-to-End Release Audit Workflow

This document details the AI-assisted Release Audit Workflow—the comprehensive evaluation procedure executed after compiling a production release candidate binary and before submitting the artifact to Apple App Store Connect or Google Play Console.

Engineered in alignment with **2026 Context Engineering standards** and the **OWASP GenAI Security Project (2026)**—specifically mitigating **LLM06: Excessive Agency** and **LLM01: Prompt Injection**—it specifies how orchestrators dispatch specialized sub-auditor agents ([QA Agent](../agents/qa-agent.md), [Security Auditor](../agents/security-auditor.md), [Privacy Auditor](../agents/privacy-auditor.md), [Dependency Auditor](../agents/dependency-auditor.md), [Metadata Agent](../agents/metadata-agent.md)), aggregate findings, and present an **Evidence Package** to human release managers.

This guide is **not**:

- an automated store submission bot
- a replacement for running automated test suites or security scans
- an authorization mechanism to bypass release blockers without explicit human sign-off

---

# 1. Workflow Topology & Scatter-Gather Architecture

The Release Audit Workflow uses a **Parallel Scatter-Gather Pattern** to minimize audit wall-clock time while maintaining domain isolation.

```text
                        [ Release Candidate Built ]
                                     │
                                     ▼
                        [ Load Context & Artifacts ]
                                     │
                                     ▼
                       [ Dispatch Sub-Auditor Agents ]
     ┌───────────────────────────────┼───────────────────────────────┐
     ▼                               ▼                               ▼
[ QA Agent ]                [ Security Auditor ]            [ Privacy Auditor ]
(Test matrix & logs)        (Code & secret scanning)        (PrivacyInfo & Data Safety)
[ Metadata Agent ]          [ Dependency Auditor ]
(Store listing text)        (Native SDK locks)
     │                               │                               │
     └───────────────────────────────┼───────────────────────────────┘
                                     │
                                     ▼
                       [ Release Auditor Aggregation ]
                                     │
                                     ▼
                       [ Evaluate Overall Verdict ]
                                     │
                 ┌───────────────────┴───────────────────┐
                 ▼                                       ▼
      Verdict: NOT READY                         Verdict: READY / CONDITIONAL
                 │                                       │
                 ▼                                       ▼
       [ Halt & Fix Blockers ]                 [ Human Approval Gate ]
                                                         │
                                               ┌─────────┴─────────┐
                                               ▼                   ▼
                                          [ Approved ]        [ Rejected ]
                                               │                   │
                                               ▼                   ▼
                                      [ EAS Submit / Push ]   [ Halt Release ]
```

---

# 2. Step-by-Step Workflow Execution

## Step 1: Candidate Verification & Single-Mount Data Injection

The orchestrator verifies that the binary artifact was compiled cleanly from a tagged, clean git commit and injects context into single-mount XML tags:

```markdown
<system_instructions>
# Persona & Mandate
You are the Lead Release Auditor evaluating a mobile release candidate.

# Assessment Rules
1. Ground all verdicts strictly in sub-auditor reports provided in <single_mount_variables>.
2. Execute step-by-step risk evaluation inside a <thinking> block before generating final report.
3. Classify verdicts as READY (0 blockers), CONDITIONAL (P2/P3 warnings only), or NOT READY (1+ P0/P1 blockers).
</system_instructions>

<single_mount_variables>
<candidate_metadata>
  app_name: "PayFast"
  version: "1.4.0"
  ios_build: 140
  android_version_code: 1400
  git_commit: "b8e9a7c"
</candidate_metadata>

<sub_auditor_reports>
<PASTE_SUB_AUDITOR_REPORTS_HERE>
</sub_auditor_reports>
</single_mount_variables>
```

## Step 2: Parallel Sub-Audit Execution

The orchestrator dispatches independent auditor subagents:

1. **QA Agent**: Evaluates test logs (Jest, Maestro, Detox) and checks OS version coverage across target device matrices.
2. **Security Auditor**: Scans source diffs for hardcoded secrets, ATS exceptions, insecure storage, and known vulnerabilities (OWASP LLM02).
3. **Privacy Auditor**: Verifies `PrivacyInfo.xcprivacy` API categories (Apple Guideline 5.1.1) and checks Google Data Safety alignment.
4. **Dependency Auditor**: Verifies third-party native library licenses, lockfile sync, and binary size impact.
5. **Metadata Agent**: Checks store titles, subtitles, keywords, support URLs, and changelog text against platform guidelines.

## Step 3: Synthesis & Verdict Assignment

The [Release Auditor](../agents/release-auditor.md) evaluates aggregated subagent findings and issues a verdict:

- **`READY`**: Zero P0/P1 blockers. All automated tests passed. Binary and metadata verified.
- **`CONDITIONAL`**: Non-blocking P2/P3 warnings exist. Human release manager must explicitly sign off on warnings.
- **`NOT READY`**: One or more P0 or P1 blockers exist. Workflow halts immediately.

## Step 4: Human Review & Submission Authorization

If the verdict is `READY` or `CONDITIONAL`, the orchestrator formats the **Evidence Package** and halts at the [Human Approval Gate](../orchestration/human-approval.md). Store submission proceeds only upon explicit human sign-off.

---

# 3. Machine-Readable Audit Schema

```yaml
release_audit_workflow_report:
  workflow_run_id: "audit-20260813-v1.4.0"
  target_candidate: "PayFast v1.4.0 (140)"
  overall_verdict: "CONDITIONAL" # Options: READY, CONDITIONAL, NOT READY

  domain_verdicts:
    qa: "READY"
    security: "READY"
    privacy: "CONDITIONAL"
    dependencies: "READY"
    metadata: "READY"
    signing: "READY"

  total_blockers: 0
  total_warnings: 1
  warnings:
    - id: "PRIV-002"
      severity: "P2"
      domain: "privacy"
      summary: "Privacy policy URL uses 301 redirect; direct URL recommended."

  human_approval_required: true
```

---

# 4. Operational Verification Checklist

- [ ] **Parallel Execution**: Sub-auditors run in parallel isolated contexts.
- [ ] **Context Engineering Layout**: System prompt uses cached static instructions and single-mount XML tags.
- [ ] **Conservative Verdict**: Any P0 or P1 blocker forces the overall verdict to `NOT READY`.
- [ ] **Evidence Package Assembled**: Build logs, test reports, and diff summaries are compiled for human review.
- [ ] **Human Sign-Off Verified**: Store submission commands are executed only after a valid human approval record is created.

---

# 5. Related Documentation

- [Release Preparation Workflow](release-preparation.md) - Candidate preparation workflow.
- [Release Auditor Agent](../agents/release-auditor.md) - Release candidate risk aggregator.
- [Human Approval](../orchestration/human-approval.md) - Mandatory human review boundaries.
- [Agent Workflows](../orchestration/agent-workflows.md) - Multi-agent topologies.

---

# 6. Official Sources

- OWASP GenAI Security Project (2026 Standard): https://genai.owasp.org/
- Apple App Store Connect Release Guidance: https://developer.apple.com/help/app-store-connect/
- Google Play Console Production Releases: https://support.google.com/googleplay/android-developer/answer/9859348

---

**Last verified:** August 13, 2026
