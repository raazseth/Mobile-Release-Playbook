# End-to-End Release Preparation Workflow

This document details the AI-assisted Release Preparation Workflow—the procedure executed prior to triggering a production build candidate.

Engineered in alignment with **2026 Context Engineering standards** and the **OWASP GenAI Security Project (2026)**—specifically targeting **LLM06: Excessive Agency** and **LLM02: Sensitive Information Disclosure**—it specifies how orchestrators coordinate pre-build checks (version alignment, native config verification, privacy manifest sync, lockfile audits, and signing key readiness) while enforcing non-bypassable human approval gates.

This guide is **not**:

- an automated build trigger (build commands must be authorized by a human maintainer)
- a substitute for running native compilation prebuilds (`npx expo prebuild --clean`)
- an authorization mechanism to bypass versioning rules or un-verified signing credentials

---

# 1. Workflow Architecture & Lifecycle

The Release Preparation Workflow validates all build inputs, configurations, dependencies, and credentials before code is compiled into native iOS `.ipa` or Android `.aab` binaries.

```text
[ Trigger: Release Candidate Branch / Tag Designated ]
                           │
                           ▼
[ Context Initialization & Single-Mount Data Injection ]
(Load App Config, Version Numbers, Lockfiles, Secrets Status)
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│             PARALLEL PREPARATION AUDITS                │
│  - Versioning Alignment Check (SemVer & Build Numbers) │
│  - App Config & Target API Audit (Android 16 / API 36) │
│  - Dependency & CVE Lock Audit                         │
│  - Privacy Manifest & Permission Audit (Guideline 5.1.1)│
│  - Opaque Signing Credential Readiness Verification    │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│              ORCHESTRATOR SYNTHESIS & REASONING        │
│  - Execute Step-by-Step Assessment inside `<thinking>` │
│  - Evaluate Overall Release Preparation Status         │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│                 HUMAN SIGN-OFF GATE                    │
│  Developer Reviews Evidence → Approves RC Build Launch │
└────────────────────────────────────────────────────────┘
```

---

# 2. Step-by-Step Workflow Specification

## Step 1: Context Initialization & Single-Mount Data Injection

The orchestrator loads repository state into single-mount XML tags:

- **Repository Context**: Git branch, commit hash, framework (Expo SDK 51+ / React Native 0.74+).
- **Release Context**: Target version string (`1.4.0`), target iOS build number (`140`), target Android version code (`1400`), target environment (`production`).

```markdown
<system_instructions>
# Persona & Mandate
You are an expert Release Preparation Orchestrator evaluating pre-build candidate readiness.

# Reasoning & Safety Rules
1. Ground every check strictly in evidence provided in <single_mount_variables>.
2. Execute step-by-step evaluation inside a <thinking> block before outputting report.
3. Classify findings as P0 (Blocker), P1 (High), P2 (Medium), P3 (Low).
4. Never expose raw credentials; verify status using opaque handles.
</system_instructions>

<single_mount_variables>
<release_prep_context>
  app_name: "MobileApp"
  target_version: "1.4.0"
  target_build_ios: "140"
  target_version_code_android: 1400
  git_branch: "release/v1.4.0"
  git_commit: "c7f8a9b0d1e2"
</release_prep_context>

<manifest_and_config>
<PASTE_APP_JSON_PACKAGE_JSON_PODFILE_BUILD_GRADLE_HERE>
</manifest_and_config>
</single_mount_variables>
```

## Step 2: Versioning & Identifier Alignment

The workflow verifies application versioning and build numbers (see [foundations/versioning.md](../../foundations/versioning.md)):

- [ ] **App Version**: User-facing version matches Semantic Versioning (`MAJOR.MINOR.PATCH`).
- [ ] **iOS Build Number**: Monotonically increases past the highest build number in App Store Connect (`CFBundleVersion`).
- [ ] **Android Version Code**: Monotonically increases past the highest version code in Google Play Console (`versionCode`).
- [ ] **Bundle Identifiers**: Package name (`com.example.app`) matches registered developer accounts.

## Step 3: App Configuration & Target API Level Audit

Native configuration manifests (`app.json`, `Info.plist`, `build.gradle`) are validated:

- **Expo Prebuild Check**: Verifies that Expo Config Plugins generate valid native projects without crashing (`npx expo prebuild --clean`).
- **Target API Level Check**: Verifies Android build configuration meets Google Play requirements (Android 16 / API Level 36 requirement for new app submissions and updates).
- **Native Permissions**: Ensures all native permission keys (`NSCameraUsageDescription`, `NSLocationWhenInUseUsageDescription`) feature human-readable usage descriptions.

## Step 4: Privacy Manifest & Permission Audit

The [Privacy Auditor](../agents/privacy-auditor.md) checks native privacy configurations:

- Verifies `ios/PrivacyInfo.xcprivacy` includes declared "Required Reason" API categories for disk stat, user defaults, timestamp, and boot time APIs (Apple Guideline 5.1.1).
- Cross-references data collection in code against declared Google Play Data Safety forms.

## Step 5: Dependency Lock & Security Audit

The [Dependency Auditor](../agents/dependency-auditor.md) verifies dependency locks:

- Verifies `package-lock.json`, `yarn.lock`, or `bun.lockb` is present and consistent.
- Verifies iOS `Podfile.lock` is synchronized with `package.json`.
- Audits for known high-severity CVE vulnerabilities in third-party dependencies.

## Step 6: Opaque Signing Credential Check (OWASP LLM02 Mitigation)

Verifies that production signing credentials are functional using opaque handles (`configured: true / exposed_to_agent: false`):

- **iOS**: Verifies Apple Distribution Certificate and Production Provisioning Profile validity via Fastlane Match or EAS Credentials.
- **Android**: Verifies Play App Signing keystore or upload key presence in CI secret storage.

---

# 3. Machine-Readable Preparation Schema

```yaml
release_prep_workflow_report:
  workflow_run_id: "prep-20260813-v1.4.0"
  status: "COMPLETED"
  readiness_verdict: "READY" # Options: READY, CONDITIONAL, NOT READY

  versioning_checks:
    app_version: "1.4.0"
    ios_build_number: 140
    android_version_code: 1400
    semver_compliant: true

  platform_target_checks:
    android_target_api_level: 36 # Android 16 requirement verified
    expo_prebuild_clean: true

  privacy_manifest_checks:
    apple_guideline_5_1_1_compliant: true
    required_reason_apis_declared: true
    data_safety_matched: true

  signing_credentials_status:
    ios_distribution_cert_valid: true
    android_upload_key_valid: true
    raw_credentials_exposed: false # OWASP LLM02 Compliance

  human_approval_required: true
```

---

# 4. Operational Verification Checklist

- [ ] **Context Engineering Structured**: Prompt instructions use static caching layouts and single-mount XML tags.
- [ ] **Target API Level 36 Verified**: Android build targets Android 16 / API Level 36.
- [ ] **Apple Guideline 5.1.1 Verified**: `PrivacyInfo.xcprivacy` declares all Required Reason APIs.
- [ ] **Opaque Credentials Used**: Signing keys are verified via opaque handles without exposing raw secrets to the agent.
- [ ] **Human Sign-Off**: Pre-release evidence package is reviewed and approved by a human maintainer before launching compilation.

---

# 5. Related Documentation

- [Release Audit Workflow](release-audit.md) - Pre-submission release candidate audit workflow.
- [Privacy Auditor Agent](../agents/privacy-auditor.md) - Privacy Auditor agent definition.
- [Human Approval](../orchestration/human-approval.md) - Mandatory human review boundaries.

---

# 6. Official Sources

- OWASP GenAI Security Project (2026 Standard): https://genai.owasp.org/
- Expo EAS Build Preparation: https://docs.expo.dev/build/setup/
- Google Play Target API Policy: https://support.google.com/googleplay/android-developer/answer/9859152
- Apple Privacy Manifest Guidelines: https://developer.apple.com/documentation/bundleresources/privacy_manifest_files

---

**Last verified:** August 13, 2026

