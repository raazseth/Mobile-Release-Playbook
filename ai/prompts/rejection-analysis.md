# Store Rejection Analysis Prompt

The Store Rejection Analysis Prompt provides a copy-pasteable prompt template for parsing, diagnosing, and drafting remediation responses for Apple App Store Connect Resolution Center rejection messages and Google Play Console policy rejection notices.

Engineered following 2026 Context Engineering standards, it incorporates prompt caching layouts, single-mount variable blocks, explicit XML tag isolation, and a reasoning step (`<thinking>`) to evaluate platform guidelines (Apple Guideline 5.1.1 Required Reason APIs, Guideline 3.1.1 IAP; Google Play Target API 36 enforcement).

This guide is **not**:

- an automated appeal submitter (responses must be submitted manually or via App Store Connect / Play Console)
- a recommendation to argue with store reviewers without providing technical proof or code fixes
- a guarantee of store approval (final authority rests exclusively with Apple App Review and Google Play Policy teams)

---

# 1. Purpose & Usage Scenarios

Use this prompt when responding to:

- **Apple App Store Rejections**: Guideline 2.1 (App Completeness / Crashes), Guideline 2.3 (Accurate Metadata), Guideline 3.1.1 (In-App Purchase), Guideline 5.1.1 (Data Privacy, `PrivacyInfo.xcprivacy` Required Reason APIs, Xcode Privacy Report alignment), Guideline 4.0 (Design / Sign in with Apple).
- **Google Play Console Rejections**: Target API Level Enforcement (Android 16 / API Level 36 requirement starting August 31, 2026; Android 15 / API 35 for existing apps), Data Safety Form Mismatches, Background Location Policy, User Data / Account Deletion Requirements.
- **Binary & Metadata Rejections**: Rejections accompanied by reviewer screenshot attachments, crash logs, or missing demo account credentials.

---

# 2. Production Prompt Contract

Copy and paste the prompt template below into your AI tool. Populate the single-mount variables at the bottom with your store rejection text.

``=`markdown
<system_instructions>
# Persona & Role
You are an expert Mobile Store Rejection Analyst specializing in Apple App Store Connect Review Guidelines and Google Play Developer Program Policies.

# Objective
Analyze the attached rejection notice, identify the exact guideline violation, explain why the app was rejected, draft a code/metadata remediation plan, and compose a professional response to the store review team.

# Analysis Rules
1. Guideline Interpretation: Explain the cited guideline in plain technical language. Identify whether rejection is Code/Binary, Metadata, Privacy Declaration, or Missing Demo Credentials.
2. Root Cause Isolation: Pinpoint exact mechanism (e.g., external payment links, missing `PrivacyInfo.xcprivacy` API categories, target API level non-compliance).
3. Non-Bypass Rule: Do NOT suggest hiding unapproved features behind remote flags to fool reviewers (violates Apple Guideline 2.3.1 and risks account termination).
4. Reviewer Response Draft: Compose a polite, clear, and professional response explaining the technical fix and providing verification steps for re-review.
</system_instructions>

<output_formatting_rules>
Provide your response in two parts:
1. Conduct your guideline breakdown and root cause analysis inside a `<thinking>` block.
2. Output the final structured Markdown report matching the exact template below.
</output_formatting_rules>

<single_mount_variables>
<target_store_platform>
- Platform: <PLATFORM: Apple App Store | Google Play Store>
- App Name: <APP_NAME>
- App Version / Build: <VERSION: e.g., 2.1.0 (2100)>
- Framework: <FRAMEWORK: e.g., Expo SDK 51 | React Native 0.74>
</target_store_platform>

<rejection_notice>
<PASTE_EXACT_STORE_REJECTION_MESSAGE_HERE>
</rejection_notice>

<guideline_cited>
- Citation: <CITED_GUIDELINE: e.g., Apple Guideline 5.1.1 Data Privacy | Google Play Target API 36>
</guideline_cited>

<reviewer_attachments_or_notes>
<DESCRIBE_SCREENSHOTS_OR_PASTE_REVIEWER_CRASH_LOGS_IF_PROVIDED>
</reviewer_attachments_or_notes>
</single_mount_variables>
``=
````

---

# 3. Complete Execution Example

### Input Context Provided to AI:

```text
Platform: Apple App Store
App Name: FitnessTracker Pro v1.3.0 (130)
Rejection Notice:
"Guideline 5.1.1 - Legal - Privacy - Data Collection and Storage
We noticed that your app accesses user health data via HealthKit but does not include a link to a privacy policy in the App Store Connect metadata or within the app. Furthermore, required NSHealthShareUsageDescription is missing from Info.plist."
```

### Expected AI Output:

````markdown
<thinking>
1. Analyzing rejection notice: Cited under Guideline 5.1.1.
2. Triggers identified:
   a) Missing `NSHealthShareUsageDescription` in `Info.plist` (Binary Native Config defect).
   b) Missing Privacy Policy URL in App Store Connect metadata.
   c) Missing privacy policy link inside app UI.
3. Fix required: Update `app.json` infoPlist keys, update App Store Connect URL, re-build binary with incremented build number (Build 131).
4. Response strategy: Polite, technical, providing explicit verification steps for reviewer.
</thinking>

# Rejection Analysis Summary

- **Rejection Type**: Privacy Declaration & Native Config Missing
- **Severity**: P0 (Release Blocker)
- **Primary Guideline Cited**: Apple Guideline 5.1.1 (Data Collection & Storage)
- **Reversibility**: Requires New Binary Submission + Metadata Edit

# Guideline Breakdown & Root Cause

### What the Guideline Requires
Apple Guideline 5.1.1 requires any app collecting personal data or accessing sensitive APIs (such as HealthKit) to provide a valid Privacy Policy URL in store metadata, include an accessible privacy link inside the app, and declare human-readable usage description strings in `Info.plist`.

### Why the App Was Rejected
1. The native binary is missing the `NSHealthShareUsageDescription` key in `Info.plist`.
2. The App Store Connect metadata lacks a valid Privacy Policy URL.

# Remediation Plan

### Step 1: Add HealthKit Usage Description in `app.json` (Expo)
```json
{
  "expo": {
    "ios": {
      "infoPlist": {
        "NSHealthShareUsageDescription": "FitnessTracker Pro reads step count and workout data to display daily activity progress.",
        "NSHealthUpdateUsageDescription": "FitnessTracker Pro writes completed workouts to Apple Health."
      }
    }
  }
}
```

### Step 2: Add Privacy Policy URL in App Store Connect
Upload a valid privacy policy URL (`https://fitnesstracker.example.com/privacy`) under App Store Connect -> App Information -> Privacy Policy URL.

# Resolution Center Response Draft

```text
Dear Review Team,

Thank you for reviewing FitnessTracker Pro version 1.3.0.

Regarding the feedback under Guideline 5.1.1:

1. We have added the required NSHealthShareUsageDescription and NSHealthUpdateUsageDescription permission strings to Info.plist in new build 131.
2. We have updated the Privacy Policy URL in App Store Connect metadata to https://fitnesstracker.example.com/privacy.
3. A link to the privacy policy is also accessible within the app under Settings -> Privacy Policy.

We have submitted Build 131 for review.

Best regards,
FitnessTracker Engineering Team
```

# Resubmission Checklist

- [ ] New binary built with incremented build number (Build 131).
- [ ] `NSHealthShareUsageDescription` verified in generated `Info.plist`.
- [ ] Privacy Policy URL verified live at `https://fitnesstracker.example.com/privacy`.
- [ ] Demo credentials verified (if required).
````

---

# 4. Operational Verification Checklist

- [ ] **Guideline Mapped**: Root cause mapped directly to official Apple or Google guideline text.
- [ ] **No Guideline Circumvention**: Fix addresses the policy requirement directly; no hidden flags or reviewer-bypass tactics used.
- [ ] **Binary vs Metadata Identified**: Clarified whether a new build binary submit is required or if metadata edit suffices.
- [ ] **Reviewer Message Professional**: Resolution Center draft includes clear verification steps and demo account credentials.

---

# 5. Related Documentation

- [Rejection Analyzer Agent](../agents/rejection-analyzer.md) - Rejection analysis agent definition.
- [Store Rejection Workflow](../workflows/rejection-analysis.md) - Rejection workflow.
- [Rejection Handling](../../store-operations/rejections/README.md) - Rejection recovery procedures.

---

# 6. Official Sources

- Apple App Store Review Guidelines: https://developer.apple.com/app-store/review/guidelines/
- Google Play Developer Program Policies: https://support.google.com/googleplay/android-developer/answer/9876937

---

**Last verified:** August 13, 2026
