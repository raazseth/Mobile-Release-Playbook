# End-to-End Store Metadata Workflow

This document details the AI-assisted Store Metadata Workflow—the procedure for auditing, generating, formatting, and verifying App Store Connect and Google Play Console store listing metadata, keywords, release notes, and asset declarations.

Engineered in alignment with **2026 Context Engineering standards** and the **OWASP GenAI Security Project (2026)**—specifically addressing **LLM05: Improper Output Handling**—it specifies how the [Metadata Agent](../agents/metadata-agent.md) ensures listing text conforms to Apple App Store Review Guidelines (Guideline 2.3) and Google Play Store Listing Policies.

This guide is **not**:

- an automated store metadata pusher (listing updates must be uploaded via Fastlane, EAS Submit, or store consoles)
- a marketing hype copywriting engine
- a substitute for verifying store screenshot asset resolutions and URLs

---

# 1. Workflow Architecture & Metadata Lifecycle

The Store Metadata Workflow converts product change summaries and app configurations into store-compliant listing text and changelogs.

```text
[ Product Changes & Scope Input ]
               │
               ▼
[ Single-Mount Data Injection ]
               │
               ▼
┌────────────────────────────────────────────────────────┐
│             METADATA AGENT REASONING ENGINE            │
│  - Execute Policy Checks inside `<thinking>` Block     │
│  - Validate Character Limits (Title <=30, Sub <=30)    │
│  - Strip Competitor Names, Pricing Terms, & Rank Claims│
│  - Verify HTTPS Protocol on Support & Privacy URLs     │
└──────────────────────────┬─────────────────────────────┘
               │
               ▼
┌────────────────────────────────────────────────────────┐
│               HUMAN REVIEW & FASTLANE DELIVERY         │
│  Developer Approves Text → Upload via Deliver/Supply  │
└────────────────────────────────────────────────────────┘
```

---

# 2. Step-by-Step Workflow Execution

## Step 1: Ingesting Scope & Context Injection

The workflow collects product change inputs and injects context into single-mount XML tags:

```markdown
<system_instructions>
# Persona & Mandate
You are an expert Store Metadata Compliance Agent auditing store listing text.

# Compliance Rules
1. Apple Guideline 2.3: Title max 30 chars, Subtitle max 30 chars, Keywords max 100 chars. No pricing terms ("Free") or rank claims ("#1 App").
2. Google Play Policy: App Name max 30 chars, Short Desc max 80 chars, Full Desc max 4000 chars.
3. HTTPS Protocol: Support and Privacy URLs MUST use HTTPS.
4. Execute policy checks inside a <thinking> block before generating output.
</system_instructions>

<single_mount_variables>
<proposed_metadata_text>
<PASTE_PROPOSED_METADATA_HERE>
</proposed_metadata_text>
</single_mount_variables>
```

## Step 2: Generating & Formatting Listing Text

The [Metadata Agent](../agents/metadata-agent.md) formats text according to store rules:

- **Apple App Store**: Title (<= 30 chars), Subtitle (<= 30 chars), Keywords (<= 100 chars, comma-separated), Promotional Text, Full Description.
- **Google Play Store**: App Name (<= 30 chars), Short Description (<= 80 chars), Full Description (<= 4000 chars).

## Step 3: Policy Compliance Audit

The agent audits generated metadata against store guidelines:

- **No Price Terms**: Removes "Free", "Discount", or "Sale" from titles/subtitles (Apple Guideline 2.3.7).
- **No Rank Claims**: Removes unverified claims ("#1 App", "Best App") without official award documentation.
- **No Competitor Keywords**: Strips trademarked competitor brand names from keyword fields.
- **HTTPS Enforcement**: Verifies Support URL and Privacy Policy URL use secure HTTPS protocols.

## Step 4: Human Review & Fastlane Delivery

The generated metadata report is presented to the Human Release Manager. Upon approval, metadata files are saved to `fastlane/metadata/` or uploaded via store management toolchains.

---

# 3. Machine-Readable Metadata Schema

```yaml
metadata_workflow_report:
  workflow_run_id: "meta-20260813-v1.4.0"
  status: "COMPLETED"
  compliance_verdict: "READY" # Options: READY, CONDITIONAL, NOT READY

  character_counts:
    ios_title: 29 # Limit 30
    ios_subtitle: 28 # Limit 30
    android_short_description: 74 # Limit 80

  url_validation:
    support_url_https: true
    privacy_url_https: true

  policy_check:
    competitor_brand_terms_found: 0
    price_terms_in_title: 0
    rank_claims_found: 0

  human_approval_required: true
```

---

# 4. Operational Verification Checklist

- [ ] **Context Engineering Structured**: System instructions use static caching layouts and XML tag shielding.
- [ ] **Character Limits Enforced**: Title (<=30), Subtitle (<=30), Short Description (<=80), Keywords (<=100).
- [ ] **Policy Compliant**: Free of competitor brand names, pricing claims, and unverified rank claims.
- [ ] **HTTPS URLs**: Support and Privacy Policy URLs use active HTTPS endpoints.
- [ ] **Human Review**: Listing text and changelogs approved by a human maintainer before submission.

---

# 5. Related Documentation

- [Metadata Agent Definition](../agents/metadata-agent.md) - Metadata agent.
- [Store Metadata Prompt](../prompts/metadata.md) - Copy-paste metadata audit prompt.
- [Store Operations Overview](../../store-operations/README.md) - Store operations.

---

# 6. Official Sources

- OWASP GenAI Security Project (2026 Standard): https://genai.owasp.org/
- Apple App Store Review Guidelines (Guideline 2.3): https://developer.apple.com/app-store/review/guidelines/#accurate-metadata
- Google Play Metadata Policy: https://support.google.com/googleplay/android-developer/answer/9869137

---

**Last verified:** August 13, 2026
