# QA & Test Evidence Review Prompt

The QA & Test Evidence Review Prompt provides a copy-pasteable prompt template for auditing mobile test execution logs, E2E test matrix reports (Maestro, Detox, Appium), unit/integration coverage summaries, and device matrix validation records.

Engineered following 2026 Context Engineering standards, it incorporates prompt caching layouts, single-mount variable blocks, explicit XML tag isolation, and a reasoning step (`<thinking>`) to evaluate test evidence and issue a QA readiness verdict.

This guide is **not**:

- an automated test runner (tests must be executed by Jest, Maestro, Detox, or cloud test farms)
- a substitute for physical device smoke testing on target iOS and Android hardware
- a permission mechanism to bypass failing automated test suites

---

# 1. Purpose & Usage Scenarios

Use this prompt when evaluating:

- **E2E Test Execution Logs**: Maestro, Detox, or Appium end-to-end test execution reports for critical user flows (Onboarding, Authentication, Checkout, In-App Purchase).
- **Unit & Integration Coverage Summaries**: Jest, Vitest, or XCTest/JUnit code coverage metrics and regression test results.
- **Device & OS Matrix Coverage**: Verifying test execution across target iOS versions (e.g., iOS 17.5, iOS 18.0) and Android API levels (e.g., API 31–36).
- **Flaky Test Analysis**: Identifying intermittent test failures vs reproducible release-blocking regressions.

---

# 2. Production Prompt Contract

Copy and paste the prompt template below into your AI tool. Replace the single-mount variables at the bottom with your actual test execution logs and device matrix requirements.

``=`markdown
<system_instructions>
# Persona & Role
You are an expert Mobile QA Lead specializing in iOS and Android test automation, device matrix verification, and release quality gates.

# Objective
Audit the attached test execution evidence, verify device matrix coverage, evaluate failing or flaky tests, and issue an explicit QA readiness verdict.

# Evaluation Rules
1. Zero Blocker Policy: Any failing test in critical paths (Authentication, Checkout, In-App Purchase, Core Navigation) is a P0 (Release Blocker).
2. Device Matrix Completeness: Verify test execution covers target iOS versions and Android API levels.
3. Skipped Test Justification: Unexplained skipped critical tests degrade the verdict to CONDITIONAL or NOT READY.
4. Flaky Test Evaluation: Classify failures as reproducible regressions or intermittent infrastructure flakiness.
</system_instructions>

<output_formatting_rules>
Provide your response in two parts:
1. Conduct your detailed test evaluation inside a `<thinking>` block.
2. Output the final structured Markdown report matching the exact template below.
</output_formatting_rules>

<single_mount_variables>
<release_candidate_identity>
- App Version: <APP_VERSION: e.g., 1.4.2>
- Build Number: <BUILD_NUMBER: e.g., 142>
- Target Environment: <ENVIRONMENT: e.g., TestFlight | Play Internal>
</release_candidate_identity>

<test_execution_evidence>
<PASTE_JEST_MAESTRO_DETOX_TEST_LOGS_OR_SUMMARY_HERE>
- Total Tests: <TOTAL_COUNT>
- Passed: <PASSED_COUNT>
- Failed: <FAILED_COUNT>
- Skipped: <SKIPPED_COUNT>
</test_execution_evidence>

<target_device_matrix>
- Target iOS Versions: <e.g., iOS 17.5, iOS 18.0>
- Target Android API Levels: <e.g., API 31 (Android 12), API 36 (Android 16)>
- Form Factors Tested: <e.g., iPhone 15 Pro, Pixel 8, iPad Air>
</target_device_matrix>

<known_issues_and_skipped_tests>
<PASTE_KNOWN_ISSUES_OR_EXPLAIN_SKIPPED_TESTS_HERE>
</known_issues_and_skipped_tests>
</single_mount_variables>
``=
````

---

# 3. Complete Execution Example

### Input Context Provided to AI:

```text
App Version: 1.4.2 (142)
Target Environment: TestFlight / Play Internal
Total Tests: 120 | Passed: 118 | Failed: 1 | Skipped: 1

Failed Test: `e2e/checkout.spec.ts` -> "User completes Apple Pay checkout"
Error: `Element 'apple_pay_button' not visible within 10000ms`
Device Matrix Tested: iPhone 15 (iOS 17.5), Pixel 7 (API 33). Missing: iOS 18, Android API 36.
Skipped Test: `e2e/biometrics.spec.ts` (FaceID disabled in simulator).
```

### Expected AI Output:

````markdown
<thinking>
1. Analyzing test results: Total 120, Passed 118, Failed 1, Skipped 1.
2. Evaluating failed test: `e2e/checkout.spec.ts` failed on Apple Pay button rendering. This touches the Payment/Checkout critical flow -> P0 Blocker.
3. Evaluating device matrix: Tested iOS 17.5 and API 33. Missing Android API 36 (Android 16) and iOS 18 -> Device Matrix gap.
4. Verdict: NOT READY due to P0 failure in checkout flow.
</thinking>

# QA Readiness Verdict

- **Overall QA Verdict**: NOT READY
- **Total Test Pass Rate**: 98.3%
- **Critical Flow Status**: FAIL (Apple Pay Checkout flow failed)
- **Device Matrix Status**: INCOMPLETE (Missing Android API 36 and iOS 18 coverage)

# Test Suite Summary

| Test Suite | Total | Passed | Failed | Skipped | Status |
|---|---|---|---|---|---|
| Unit Tests (Jest) | 100 | 100 | 0 | 0 | PASS |
| E2E Flows (Maestro/Detox) | 18 | 17 | 1 | 0 | FAIL |
| Device Matrix Verification | 2 | 1 | 0 | 1 | INCOMPLETE |

# Detailed Findings & Blockers

### Finding 1: Apple Pay Checkout Flow Timeout
- **Severity**: P0 (Release Blocker)
- **Affected Flow**: Payment / Checkout
- **Evidence Snippet**:
> `Element 'apple_pay_button' not visible within 10000ms`
- **Diagnosis**: Critical flow failure during checkout initialization. Requires fix before release candidate promotion.
- **Required Action**: Fix PKPaymentAuthorizationViewController setup and re-verify test on physical device.

# Device Matrix Gap Analysis

- **Covered OS Versions**: iOS 17.5, Android API 33
- **Missing OS Coverage**: Android API 36 (Android 16), iOS 18
- **Risk Assessment**: Medium. Android 16 target enforcement requires verification.

# Actionable Recommendation

1. **HALT RELEASE**: Fix and re-verify `e2e/checkout.spec.ts` on iOS 17.5.
2. **Expand Matrix**: Run E2E automation against Android API 36 emulator or cloud device farm.
````

---

# 4. Operational Verification Checklist

- [ ] **100% Critical Flow Pass**: Authentication, payment, and core navigation tests passed.
- [ ] **Failing Tests Diagnosed**: Every failing test is classified with severity (`P0`–`P3`) and log evidence.
- [ ] **Skipped Tests Justified**: Skipped tests have documented reasons and manual verification paths.
- [ ] **Device Matrix Checked**: Minimum target iOS versions and Android API levels are verified.

---

# 5. Related Documentation

- [QA Agent Definition](../agents/qa-agent.md) - Specialized QA agent.
- [AI QA Workflow](../workflows/ai-qa.md) - End-to-end QA workflow.
- [Device Support Matrix](../../foundations/device-support.md) - Playbook device requirements.

---

# 6. Official Sources

- Maestro Mobile Testing: https://maestro.mobile.dev/
- Detox Testing Framework: https://wix.github.io/Detox/

---

**Last verified:** August 13, 2026

