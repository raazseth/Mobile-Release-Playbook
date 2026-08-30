# End-to-End Build & Crash Debugging Workflow

This document details the AI-assisted Debugging Workflow—the structured procedure for triaging mobile build compilation failures, Xcode/Gradle native errors, Hermes JS runtime exceptions, and production crash logs (Sentry, Crashlytics).

Engineered in alignment with **2026 Context Engineering standards** and the **OWASP GenAI Security Project (2026)**—specifically addressing **LLM05: Improper Output Handling**—it specifies how the [Debugging Agent](../agents/debugging-agent.md) isolates root causes, drafts minimal code fixes, verifies fixes locally, and prevents symptom-masking anti-patterns.

This guide is **not**:

- an automated patch merger (code modifications must be verified and committed by a human developer)
- a recommendation to wrap crashing functions in silent `try/catch` suppressions
- a substitute for reading actual un-truncated build logs

---

# 1. Workflow Architecture & Diagnostic Cycle

The Debugging Workflow enforces a strict evidence-based diagnostic loop:

```text
[ Build Failure / Crash Event ]
               │
               ▼
[ Single-Mount Log & Trace Injection ]
               │
               ▼
┌────────────────────────────────────────────────────────┐
│            DEBUGGING AGENT REASONING ENGINE            │
│  - Execute Root Cause Analysis inside `<thinking>` Block│
│  - Separate Primary Failure Line from Cascading Noise  │
│  - Formulate Minimal Non-Symptom Patch Plan            │
└──────────────────────────┬─────────────────────────────┘
               │
               ▼
┌────────────────────────────────────────────────────────┐
│             LOCAL VERIFICATION & PREBUILD              │
│  - Clean Prebuild Check (`npx expo prebuild --clean`)  │
│  - Unit Test Execution                                 │
└──────────────────────────┬─────────────────────────────┘
               │
         ┌─────┴─────┐
         ▼           ▼
      ( Pass )    ( Fail )
         │           │
         ▼           ▼
[ Submit Patch ]  [ Re-diagnose ]
```

---

# 2. Step-by-Step Workflow Execution

## Step 1: Log Ingestion & Single-Mount Context Injection

The workflow collects raw log evidence and injects context into single-mount XML tags:

```markdown
<system_instructions>
# Persona & Mandate
You are an expert Mobile Debugging Agent diagnosing build compilation errors and native crashes.

# Reasoning & Safety Rules
1. Ground diagnosis strictly in evidence provided inside <single_mount_variables>.
2. Execute step-by-step root cause analysis inside a <thinking> block before generating fix.
3. Anti-Symptom Rule: Never suggest wrapping crashing calls in empty try/catch blocks or deleting lockfiles without explanation.
4. Provide real CLI verification commands (npx expo prebuild --clean, npx pod-install).
</system_instructions>

<single_mount_variables>
<raw_failure_log>
<PASTE_RAW_BUILD_LOG_OR_CRASH_TRACE_HERE>
</raw_failure_log>
</single_mount_variables>
```

## Step 2: Root Cause Isolation

The [Debugging Agent](../agents/debugging-agent.md) processes the log to separate primary root causes from secondary cascading errors:

- **Native Linker Errors**: Missing CocoaPods frameworks, autolinking failures, duplicate symbols.
- **Compilation Errors**: Swift syntax changes, Kotlin type mismatches, missing C++ headers.
- **JS Runtime Exceptions**: Null pointer dereferences, unhandled promise rejections, Hermes bytecode mismatches.

## Step 3: Minimal Remediation Drafting

The agent formulates a precise, minimal patch plan:

- Fixes root causes in configuration (`app.json`, `build.gradle`, `Podfile`) or code files.
- **Anti-Symptom Rule**: The agent strictly avoids wrapping crashing code in empty `try/catch` blocks or deleting lockfiles without explanation.

## Step 4: Local Verification & Clean Prebuild

The fix is verified against local build tools:

1. Clean prebuild check (`npx expo prebuild --clean` or `pod install`).
2. Local build compilation check (`npx expo run:ios` / `npx expo run:android`).
3. Execution of unit tests to verify zero regressions.

---

# 3. Machine-Readable Debugging Schema

```yaml
debugging_workflow_report:
  workflow_run_id: "dbg-20260813-001"
  status: "DIAGNOSED" # Options: DIAGNOSED, UNRESOLVED, INSUFFICIENT_LOGS

  diagnosis:
    error_category: "LINKER_SYMBOL_MISSING"
    primary_symbol: "_OBJC_CLASS_$_RNCPushNotificationIOS"
    severity: "P0"
    confidence: "FACT"

  root_cause_summary: "CocoaPods dependency missing from Xcode workspace target due to autolinking skip."

  remediation_steps:
    - action: "run_command"
      command: "npx pod-install"
    - action: "update_config"
      file: "app.json"
      plugin: "@react-native-community/push-notification-ios"

  verification_command: "npx expo prebuild --clean"
```

---

# 4. Operational Verification Checklist

- [ ] **Context Engineering Structured**: System instructions use static caching layouts and XML tag shielding.
- [ ] **Raw Log Inspected**: Diagnosis is based on exact log evidence, not guesses.
- [ ] **No Symptom Masking**: Solution addresses the root cause without empty exception blocks.
- [ ] **Clean Build Verified**: Local prebuild and compilation pass without native linker errors.
- [ ] **Tests Passing**: Regression tests executed after code patch.

---

# 5. Related Documentation

- [Debugging Agent Definition](../agents/debugging-agent.md) - Specialized Debugging Agent.
- [Debugging Prompt](../prompts/debugging.md) - Copy-paste debugging prompt.
- [Expo Common Failures](../../frameworks/expo/common-failures.md) - Common build failures.

---

# 6. Official Sources

- OWASP GenAI Security Project (2026 Standard): https://genai.owasp.org/
- Expo EAS Build Troubleshooting: https://docs.expo.dev/build/troubleshooting/
- React Native Troubleshooting: https://reactnative.dev/docs/troubleshooting

---

**Last verified:** August 13, 2026

