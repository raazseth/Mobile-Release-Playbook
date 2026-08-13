# End-to-End Store Rejection Recovery Workflow

This document details the AI-assisted Store Rejection Recovery Workflow—the procedure for triaging, diagnosing, resolving, and responding to Apple App Store Connect Resolution Center rejection messages and Google Play Console policy rejection notices.

Engineered in alignment with **2026 Context Engineering standards** and the **OWASP GenAI Security Project (2026)**—specifically addressing **LLM05: Improper Output Handling**—it specifies how the [Rejection Analyzer](../agents/rejection-analyzer.md) parses rejection notices, coordinates specialized subagents ([Metadata Agent](../agents/metadata-agent.md), [Debugging Agent](../agents/debugging-agent.md), [Privacy Auditor](../agents/privacy-auditor.md)), drafts code or metadata fixes, and composes reviewer communications.

This guide is **not**:

- an automated appeal submission bot
- a recommendation to argue with store review teams without technical proof or code fixes
- a guarantee of store approval (final decision authority rests exclusively with Apple App Review and Google Play Policy teams)

---

# 1. Workflow Architecture & Rejection Recovery Cycle

When a store rejection notification is received, the workflow analyzes the notice, categorizes the risk, isolates the root cause, and guides the team to resolution.

```text
[ Store Rejection Event Received ]
               │
               ▼
[ Single-Mount Rejection Data Injection ]
               │
               ▼
┌────────────────────────────────────────────────────────┐
│          REJECTION ANALYZER REASONING ENGINE           │
│  - Break Down Cited Guideline inside `<thinking>`      │
│  - Map Guideline 5.1.1 (Privacy) / Target API 36 (Play)│
│  - Determine Binary Fix vs Metadata Fix vs Demo Credentials│
└──────────────────────────┬─────────────────────────────┘
               │
   ┌───────────┴───────────┐
   ▼                       ▼
[ Metadata Fix ]       [ Code / Config Fix ]
   │                       │
   └───────────┬───────────┘
               │
               ▼
┌────────────────────────────────────────────────────────┐
│            RESOLUTION CENTER RESPONSE DRAFT            │
│  - Write Technical Response & Reviewer Test Steps      │
│  - Pause at Human Approval Gate for Resubmission       │
└────────────────────────────────────────────────────────┘
```

---

# 2. Step-by-Step Workflow Execution

## Step 1: Parsing Rejection Notices & Context Injection

The [Rejection Analyzer](../agents/rejection-analyzer.md) ingests the store notification and injects context into single-mount XML tags:

```markdown
<system_instructions>
# Persona & Mandate
You are an expert Mobile Store Rejection Analyst diagnosing store rejection notices.

# Analysis Protocol
1. Map cited guideline to technical root cause (Apple Guideline 5.1.1 Required Reason APIs; Google Play Target API Level 36 requirement).
2. Execute step-by-step guideline breakdown inside a <thinking> block before drafting response.
3. Classify whether resubmission requires a new compiled binary or metadata edit.
4. Non-Bypass Rule: Never suggest hiding unapproved code behind remote flags.
</system_instructions>

<single_mount_variables>
<rejection_notice_data>
<PASTE_EXACT_STORE_REJECTION_MESSAGE_HERE>
</rejection_notice_data>
</single_mount_variables>
```

## Step 2: Root Cause Classification

The agent categorizes the rejection into one of four primary types:

1. **Metadata & Listing Rejection**: Misleading claims, title length, keyword stuffing, HTTP URLs, or competitor references.
2. **Binary Crash & Functionality Rejection**: App crash on reviewer test hardware (e.g., iPad or specific Android device), broken buttons, IPv6 network failure.
3. **In-App Purchase & Licensing Rejection**: Digital content sold via external payment links rather than store IAP APIs.
4. **Privacy & Permission Rejection**: Missing `PrivacyInfo.xcprivacy` API categories (Apple Guideline 5.1.1), missing `Info.plist` usage descriptions, or Google Data Safety form mismatches.

## Step 3: Drafting Technical Remediation

The workflow coordinates specialized agents to generate a fix:

- **Metadata Fixes**: Drafts compliant titles, subtitles, and keywords.
- **Privacy Fixes**: Updates `app.json` or `PrivacyInfo.xcprivacy` with missing API reason keys.
- **Code Fixes**: Provides minimal code patches for crash logs or missing IPv6 network support.

## Step 4: Resolution Center Communication & Resubmission

The workflow drafts a clear, professional message for the store review team:

- Explains the specific code or metadata fix implemented.
- Provides test verification steps for reviewers.
- Supplies active demo account credentials (if a login wall exists).
- Pauses at the [Human Approval Gate](../orchestration/human-approval.md) before resubmission.

---

# 3. Machine-Readable Rejection Recovery Schema

```yaml
rejection_workflow_report:
  workflow_run_id: "rej-20260813-v1.3.0"
  status: "REMEDIATED"

  rejection_details:
    platform: "ios"
    cited_guideline: "Guideline 5.1.1 - Legal - Privacy"
    severity: "P0"
    rejection_category: "PRIVACY_MANIFEST_MISSING"

  remedial_action:
    requires_new_binary: true
    requires_metadata_edit: true
    fix_summary: "Added NSHealthShareUsageDescription to Info.plist and updated App Store Connect Privacy Policy URL."

  reviewer_communication:
    draft_response_ready: true
    demo_credentials_provided: true

  human_approval_required: true
```

---

# 4. Operational Verification Checklist

- [ ] **Context Engineering Structured**: System instructions use static caching layouts and XML tag shielding.
- [ ] **Root Cause Mapped**: Rejection trigger mapped directly to official Apple or Google guideline text.
- [ ] **Technical Fix Verified**: Code or metadata fix tested locally or on TestFlight/Play Internal.
- [ ] **Demo Credentials Active**: Working demo account credentials provided for login walls.
- [ ] **Human Sign-Off**: Resolution response and resubmission approved by a human maintainer.

---

# 5. Related Documentation

- [Rejection Analyzer Agent](../agents/rejection-analyzer.md) - Rejection analysis agent definition.
- [Rejection Analysis Prompt](../prompts/rejection-analysis.md) - Copy-paste rejection analysis prompt.
- [Rejection Handling](../../store-operations/rejections/README.md) - Playbook store rejection procedures.

---

# 6. Official Sources

- OWASP GenAI Security Project (2026 Standard): https://genai.owasp.org/
- Apple App Store Review Guidelines: https://developer.apple.com/app-store/review/guidelines/
- Google Play Policy Center & Appeals: https://support.google.com/googleplay/android-developer/answer/6334373

---

**Last verified:** August 13, 2026
