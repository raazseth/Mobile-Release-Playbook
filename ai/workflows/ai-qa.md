# Automated QA & Device Matrix Audit Workflow

This document details the AI-assisted QA & Device Matrix Audit Workflow—the procedure for auditing mobile test suite execution logs (Jest, Vitest, Maestro, Detox), evaluating critical user journey pass rates, triaging flaky tests versus real regressions, and validating target OS/device matrix coverage.

Engineered in alignment with **2026 Context Engineering standards** and the **OWASP GenAI Security Project (2026)**—specifically addressing **LLM05: Improper Output Handling**—it specifies how the [QA Agent](../agents/qa-agent.md) processes test artifacts while enforcing zero-blocker quality gates for critical path user flows.

This guide is **not**:

- an automated test runner (tests must be executed by Jest, Maestro, Detox, or cloud device farms)
- a substitute for physical device smoke testing on target iOS and Android hardware
- a permission mechanism to bypass failing automated test suites

---

# 1. Workflow Architecture & QA Lifecycle

The Automated QA Workflow ingests automated test execution logs, categorizes test failures into critical vs non-critical paths, evaluates flaky test patterns, and assesses OS/device matrix coverage.

```text
[ Test Suite Execution Complete ]
(Jest Unit Tests, Maestro / Detox E2E Flows)
               │
               ▼
[ Single-Mount Test Evidence Injection ]
               │
               ▼
┌────────────────────────────────────────────────────────┐
│               QA AGENT REASONING ENGINE                │
│  - Evaluate Failure Logs inside `<thinking>` Block     │
│  - Classify Critical Paths (Auth, Checkout, IAP)      │
│  - Distinguish Reproducible Regressions vs Flaky Tests│
│  - Calculate OS Matrix Coverage (Android 16, iOS 18)   │
└──────────────────────────┬─────────────────────────────┘
               │
               ▼
┌────────────────────────────────────────────────────────┐
│                 QA VERDICT ASSIGNMENT                  │
│  READY (100% Critical Pass) | NOT READY (Blocker)      │
└────────────────────────────────────────────────────────┘
```

---

# 2. Step-by-Step Workflow Execution

## Step 1: Ingesting Test Evidence & Context Injection

The workflow parses test report files (`jest-results.json`, `maestro-report.xml`, `detox-output.log`) and injects context into single-mount XML tags:

```markdown
<system_instructions>
# Persona & Mandate
You are an expert Mobile QA Lead evaluating automated test suite evidence.

# Assessment Rules
1. Zero Blocker Policy: Any failure in critical user flows (Auth, Payment, Core Navigation) is a P0 Release Blocker.
2. Execute step-by-step evaluation inside a <thinking> block before generating final output.
3. Verify device matrix coverage across target iOS versions (iOS 17/18) and Android API levels (API 31–36).
</system_instructions>

<single_mount_variables>
<test_execution_data>
<PASTE_TEST_REPORT_LOGS_HERE>
</test_execution_data>
</single_mount_variables>
```

## Step 2: Critical Path Evaluation

The [QA Agent](../agents/qa-agent.md) classifies test failures by user flow criticality:

- **P0 Blocker**: Failures in Onboarding, Authentication, Payment/Checkout, In-App Purchase, or Core Navigation.
- **P1 High**: Failures in non-critical features with available workarounds.
- **P2/P3 Warning**: Minor UI glitch or non-functional test assertion failure.

## Step 3: Flaky Test Triage

The agent analyzes test failure stack traces to distinguish flaky infrastructure issues from reproducible code regressions:

- **Flaky Infrastructure**: Network timeout on test farm, simulator launch delay.
- **Reproducible Regression**: Deterministic assertion failure, missing UI element, crashing native binary.

## Step 4: Device & OS Matrix Audit

Verifies test execution across required platform matrix bounds:

- **iOS Targets**: Verified on iOS 17.5 and iOS 18.0 simulator/device instances.
- **Android Targets**: Verified on target API levels up to Android 16 (API Level 36).

---

# 3. Machine-Readable QA Schema

```yaml
qa_workflow_report:
  workflow_run_id: "qa-20260813-v1.4.0"
  status: "COMPLETED"
  qa_verdict: "READY" # Options: READY, CONDITIONAL, NOT READY

  summary:
    total_tests: 145
    passed: 145
    failed: 0
    skipped: 0
    pass_rate_percentage: 100.0

  critical_paths_status:
    authentication: "PASS"
    checkout_and_payment: "PASS"
    in_app_purchase: "PASS"

  device_matrix_coverage:
    ios_versions_tested: ["17.5", "18.0"]
    android_api_levels_tested: [31, 33, 36] # Android 16 target verified
    form_factors: ["phone", "tablet"]

  human_approval_required: true
```

---

# 4. Operational Verification Checklist

- [ ] **100% Critical Flow Pass**: Zero failures in authentication, checkout, or core navigation.
- [ ] **Context Engineering Structured**: System instructions use static caching layouts and XML tag shielding.
- [ ] **Device Matrix Verified**: Target OS versions include Android 16 (API 36) and iOS 18.
- [ ] **Flaky Tests Justified**: Any skipped or retried test has documented diagnostic evidence.

---

# 5. Related Documentation

- [QA Agent Definition](../agents/qa-agent.md) - QA agent definition.
- [QA Review Prompt](../prompts/qa.md) - Copy-paste QA audit prompt.
- [Device Support Matrix](../../foundations/device-support.md) - Playbook device requirements.

---

# 6. Official Sources

- OWASP GenAI Security Project (2026 Standard): https://genai.owasp.org/
- Maestro Mobile Testing Framework: https://maestro.mobile.dev/
- Detox End-to-End Testing: https://wix.github.io/Detox/

---

**Last verified:** August 13, 2026

