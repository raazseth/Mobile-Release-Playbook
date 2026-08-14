# Systematic Diagnostic Triage & Troubleshooting Handbook

This directory contains production handbook guides, diagnostic flowcharts, error log extraction protocols, step-by-step resolution steps, and prevention strategies for **Mobile Release Troubleshooting** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it provides systematic triage procedures for build compilation errors, code signing failures, store rejections, upload crashes, and post-release production outages.

This guide is **not**:

- a collection of superficial symptom patches or trial-and-error code hacks
- an authorization mechanism to comment out broken unit tests or swallow runtime exceptions
- a substitute for reading full, un-truncated diagnostic error logs

---

# 1. Architecture of Systematic Diagnostic Triage

Every troubleshooting handbook in this directory follows a strict four-stage diagnostic workflow: **Symptom Identification** -> **Empirical Root Cause Log Inspection** -> **Step-by-Step Resolution** -> **Verification & Prevention**.

```text
┌────────────────────────────────────────────────────────┐
│             STAGE 1: SYMPTOM IDENTIFICATION            │
│  - Categorize error (Build, Signing, Metadata, Crash)  │
│  - Classify severity (P0 - Blocker, P1 - High)         │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│             STAGE 2: EMPIRICAL LOG INSPECTION          │
│  - Inspect full un-truncated logs (Xcode / Gradle)     │
│  - Extract exact error code (e.g., `ITMS-90032`, 5.1.1)│
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│             STAGE 3: STEP-BY-STEP RESOLUTION           │
│  - Apply root cause fix to source code or metadata     │
│  - Recompile & verify locally before uploading         │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│             STAGE 4: VERIFICATION & PREVENTION         │
│  - Execute verification command                        │
│  - Update CI checks to prevent regression              │
└────────────────────────────────────────────────────────┘
```

---

# 2. Subsystem Directory Taxonomy

| Troubleshooting Handbook | Primary Problem Scope & Failure Domain | Key Error Codes & Symptoms Covered |
|---|---|---|
| **[README.md](README.md)** | Subsystem index, diagnostic triage architecture, and governance rules. | Universal diagnostic workflow and triage rules. |
| **[build-fails.md](build-fails.md)** | Gradle compile errors, CocoaPods dependency failures, Metro bundler. | Target API 36 mismatch, Podfile lock conflicts, Hermes JS. |
| **[signing-fails.md](signing-fails.md)** | Provisioning Profile expiration, certificate mismatch, keystore errors. | `ITMS-90161`, revoked distribution cert, invalid keystore. |
| **[archive-fails.md](archive-fails.md)** | Xcode archive failures, R8/ProGuard obfuscation crashes, `.aab` errors. | `ClassNotFoundException`, ProGuard stripping, Bitcode. |
| **[ci-fails.md](ci-fails.md)** | CI/CD pipeline, EAS Build, and GitHub Actions workflow failures. | Missing EAS secrets, Xcode runner mismatch, environment. |
| **[upload-fails.md](upload-fails.md)** | Transporter, App Store Connect, and Google Play upload failures. | `ITMS-90032` (alpha), `ITMS-90034` (duplicate build number). |
| **[metadata-rejected.md](metadata-rejected.md)** | Store metadata rejection triage for App Store and Google Play. | Guideline 2.3 accurate metadata, Guideline 2.3.7 keywords. |
| **[review-rejected.md](review-rejected.md)** | App Store Review and Google Play Policy rejection resolution. | Guideline 2.1 completeness, Guideline 3.1.1 IAP missing. |
| **[privacy-rejected.md](privacy-rejected.md)** | Privacy manifest, Required Reason API, and Data Safety rejections. | Guideline 5.1.1, `PrivacyInfo.xcprivacy`, Data Safety mismatch. |
| **[crash-after-release.md](crash-after-release.md)** | Post-release production crashes, symbolication, Hermes stack traces. | Production crash spikes, `.dSYM` missing, Sentry triage. |
| **[push-notifications-broken.md](push-notifications-broken.md)** | Push notification delivery failures across APNs and FCM HTTP v1. | APNs `.p8` auth error, FCM v1 token invalid, background handler. |
| **[deep-links-broken.md](deep-links-broken.md)** | Universal Links, Android App Links, and deep link navigation breakdowns.| AASA 404 error, `assetlinks.json` SHA-256 fingerprint mismatch. |
| **[payments-broken.md](payments-broken.md)** | StoreKit 2 and Google Play Billing 8.0/9.0+ transaction failures. | JWS signature error, `ITEM_ALREADY_OWNED`, paywall EULA. |
| **[app-not-visible.md](app-not-visible.md)** | Store visibility issues, search indexing delays, regional restrictions.| App Store index propagation, Play country availability. |

---

# 3. Universal Diagnostic Governance Rules

All troubleshooting implementations in this playbook must adhere to five mandatory diagnostic rules:

```text
1. Empirical Evidence Before Diagnostic Hypothesis
   NEVER guess the cause of a build failure or crash. Inspect un-truncated terminal logs, Xcode build
   logs, Gradle stack traces, or Sentry reports BEFORE modifying code.

2. No Symptom Patches or Swallowed Exceptions
   NEVER fix an error by wrapping calls in empty try/catch blocks, commenting out failing assertions,
   or returning dummy fallback values without resolving the underlying contract failure.

3. Step-by-Step Root Cause Elimination
   Isolate failures systematically: verify configuration syntax -> verify local build -> verify CI environment
   -> verify store backend API status.

4. Empirical Runtime Verification Required
   NEVER declare a troubleshooting issue fixed until running the build or test command and verifying a
   clean success exit code (code 0).

5. Prevention & CI Automation Update
   Every resolved troubleshooting incident MUST result in a regression check added to CI/CD pipelines
   or pre-release checklists to prevent recurrence.
```

---

# 4. Related Repository Documentation

- [Release Engineering Subsystem](../../release-engineering/README.md) - Build pipelines.
- [AI Debugging Workflow](../../ai/workflows/debugging.md) - AI debugging procedures.
- [Incident Report Template](../../templates/incident-report.md) - Post-mortem forms.

---

# 5. Official Sources

- Apple Developer Troubleshooting: https://developer.apple.com/documentation/
- Android Build Troubleshooting: https://developer.android.com/studio/build/troubleshooting

---

**Last verified:** August 14, 2026
