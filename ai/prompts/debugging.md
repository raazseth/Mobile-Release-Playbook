# Debugging Prompt

The Debugging Prompt provides a production-grade, copy-pasteable prompt template for analyzing mobile build failures, Xcode/Gradle native compilation errors, JavaScript/Hermes stack traces, and crash reporting logs (Sentry, Crashlytics).

This prompt is engineered following 2026 Context Engineering standards—featuring static instruction caching, single-mount input variable blocks, explicit XML tag isolation, and a mandatory native reasoning step (`<thinking>`) before generating diagnostic fixes.

This guide is **not**:

- an automated patch merger (edits must be verified by a developer)
- a substitute for reading actual un-truncated build logs
- a recommendation to wrap crashing calls in silent `try/catch` suppressions

---

# 1. Purpose & Usage Scenarios

Use this prompt when encountering:

- **EAS Build or Fastlane Failures**: Native compilation errors in Xcode (`xcodebuild`), CocoaPods dependency mismatches, autolinking skips, or Gradle task failures.
- **Native Crash Logs**: iOS crash reports (`.crash`, `.ips`), Android logcat stack traces, or signal crash logs (`SIGSEGV`, `SIGBUS`).
- **JavaScript Engine Crashes**: React Native Hermes/JSC runtime exceptions, unhandled promise rejections, or bundle loading errors.
- **Dependency Resolution Errors**: Version conflicts in `Podfile.lock`, `build.gradle`, `package.json`, or native C++ header mismatches.

---

# 2. Context Engineering Architecture

To optimize token efficiency and prevent instruction drift, the prompt follows a **Prompt Caching Structure**:

```text
┌────────────────────────────────────────────────────────┐
│             STATIC SYSTEM PROMPT (CACHED)              │
│  - System Persona & Expert Identity                    │
│  - Diagnostic Logic Rules & Safety Constraints         │
│  - Output Format Specification & Reasoning Protocol    │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│            DYNAMIC INPUT VARIABLES (SINGLE-MOUNT)      │
│  - `<environment_context>` (Platform, SDK, Build Tool) │
│  - `<raw_failure_log>` (Un-truncated Build Log/Trace)   │
│  - `<manifest_config>` (`package.json`, `app.json`)    │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│                  REASONING & EXECUTION                 │
│  1. `<thinking>` Step (Root Cause Isolation)           │
│  2. Structured Markdown Diagnostic Output               │
└────────────────────────────────────────────────────────┘
```

---

# 3. The Production Debugging Prompt

Copy and paste the prompt template below into your AI tool. Replace the single-mount variables at the bottom of the prompt with your actual log data.

``=`markdown
<system_instructions>
# Persona & Role
You are an expert Mobile Debugging Agent specializing in iOS (Swift, Objective-C, Xcode CLI, CocoaPods), Android (Kotlin, Java, Gradle, NDK), and React Native / Expo runtimes (Hermes, Metro, EAS Build).

# Objective
Analyze the provided build log, stack trace, or configuration files, isolate the true root cause from secondary cascading warnings, evaluate failure severity, and provide a verified, step-by-step remediation plan.

# Operational Guardrails & Rules
1. Evidence-Based Reasoning: Ground every diagnosis strictly in evidence present within the `<raw_failure_log>` tags. If logs are truncated or missing key symbols, explicitly flag missing information as `UNKNOWN`.
2. Anti-Symptom Rule: Never suggest wrapping crashing calls in empty `try/catch` blocks, suppressing linter warnings, or deleting lockfiles without explanation.
3. Command Verification: Provide exact, real build CLI commands (`npx expo prebuild --clean`, `npx pod-install`, `eas build`). Never invent CLI flags.
4. Output Schema: Adhere strictly to the requested response format.
</system_instructions>

<output_formatting_rules>
You must execute your analysis using the following steps:
1. First, provide your step-by-step diagnostic reasoning inside a `<thinking>` block.
2. Second, output the final structured Markdown report matching the exact template below.
</output_formatting_rules>

<single_mount_variables>
<environment_context>
- Platform: <PLATFORM: iOS | Android | Expo | React Native>
- SDK / Framework Version: <FRAMEWORK_VERSION: e.g., Expo SDK 51 | React Native 0.74.2>
- Build Tool: <BUILD_TOOL: e.g., EAS Build | Fastlane | Xcode CLI | Gradle>
- Environment: <ENVIRONMENT: e.g., Preview | Production>
</environment_context>

<raw_failure_log>
<PASTE_RAW_BUILD_LOG_OR_STACK_TRACE_HERE>
</raw_failure_log>

<manifest_config>
<PASTE_PACKAGE_JSON_APP_JSON_PODFILE_OR_BUILD_GRADLE_HERE>
</manifest_config>
</single_mount_variables>
``=
````

---

# 4. Complete Execution Example

### Input Context Provided to AI:

```text
Platform: iOS (Expo SDK 51, EAS Build)
Failure Log Excerpt:
[xcodebuild] Undefined symbols for architecture arm64:
[xcodebuild]   "_OBJC_CLASS_$_RNCPushNotificationIOS", referenced from:
[xcodebuild]       objc-class-ref in AppDelegate.o
[xcodebuild] ld: symbol(s) not found for architecture arm64
[xcodebuild] clang: error: linker command failed with exit code 1 (use -v to see invocation)
```

### Expected AI Output:

````markdown
<thinking>
1. Analyzing the raw log: The linker failed with exit code 1 on `xcodebuild`.
2. The specific undefined symbol is `_OBJC_CLASS_$_RNCPushNotificationIOS` in `AppDelegate.o`.
3. This symbol belongs to `@react-native-community/push-notification-ios`.
4. Root cause: The JavaScript dependency is referenced in AppDelegate, but the native pod binary was not compiled/linked into the target project during CocoaPods installation or autolinking.
5. Fix: Run `npx pod-install` to regenerate Pods, verify `app.json` config plugin, and run clean prebuild.
</thinking>

# Diagnosis Summary

- **Primary Issue**: Missing native linker symbol `RNCPushNotificationIOS` during iOS compilation.
- **Error Category**: Linker / Symbol Error
- **Severity**: P0 (Release Blocker)
- **Confidence**: FACT (Confirmed from log evidence)

# Log Evidence

> `Undefined symbols for architecture arm64: "_OBJC_CLASS_$_RNCPushNotificationIOS", referenced from: objc-class-ref in AppDelegate.o`

# Root Cause Analysis

`AppDelegate.mm` references native header symbols for `@react-native-community/push-notification-ios`, but the corresponding native static library or pod binary was not linked into the CocoaPods build target. This occurs when the pod is added to `package.json` but `pod install` was skipped, or when the Expo autolinking plugin was missing from `app.json`.

# Recommended Fix

### Step 1: Re-install CocoaPods dependencies
```bash
npx pod-install ios
```

### Step 2: Verify `app.json` config plugin presence
Ensure the Expo config plugin for push notifications is included in `app.json`:
```json
{
  "expo": {
    "plugins": ["@react-native-community/push-notification-ios"]
  }
}
```

# Verification Plan

- [ ] Execute `npx expo prebuild --clean` to regenerate clean native project files.
- [ ] Run `eas build --platform ios --profile preview` to verify successful linking.

# Prevention

Ensure all native module dependencies added to `package.json` include their corresponding Expo Config Plugins in `app.json` so autolinking executes automatically during EAS Build.
````

---

# 5. Operational Verification Checklist

- [ ] **Static Instructions First**: System prompt caching rules place static instructions before input variables.
- [ ] **Single-Mount Variables Used**: Log data and configs are passed inside single-mount XML tags (`<raw_failure_log>`).
- [ ] **Reasoning Tag Active**: Output includes a mandatory `<thinking>` block showing step-by-step diagnostic reasoning.
- [ ] **Verification Command Provided**: Response includes a real CLI verification command (`npx expo prebuild --clean`).

---

# 6. Related Documentation

### Agents & Orchestration

- [Debugging Agent](../agents/debugging-agent.md) - Specialized debugging agent definition.
- [Rejection Analyzer](../agents/rejection-analyzer.md) - Store rejection log diagnosis agent.
- [Agent Workflows](../orchestration/agent-workflows.md) - Multi-agent debugging workflow topologies.

### Troubleshooting & Frameworks

- [Expo Common Failures](../../frameworks/expo/common-failures.md) - Common EAS build failures.
- React Native Release Guidelines: [../../frameworks/react-native/README.md](../../frameworks/react-native/README.md) - Native compilation and linking concepts.

---

# 7. Official Sources

### Platform & Build Tooling

- Expo EAS Build Troubleshooting: https://docs.expo.dev/build/troubleshooting/
- React Native Troubleshooting: https://reactnative.dev/docs/troubleshooting
- Apple Xcode Build Errors: https://developer.apple.com/documentation/xcode/diagnosing-build-failures
- Android Gradle Build Failures: https://developer.android.com/studio/build/troubleshooting

---

**Last verified:** August 13, 2026

