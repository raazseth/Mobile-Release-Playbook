# Store Metadata Audit Prompt

The Store Metadata Audit Prompt provides a copy-pasteable prompt template for auditing App Store Connect and Google Play Console store listing metadata, keywords, release notes, screenshot specs, and privacy policy URL declarations.

Engineered following 2026 Context Engineering standards, it incorporates prompt caching layouts, single-mount variable blocks, explicit XML tag isolation, and a reasoning phase (`<thinking>`) to evaluate metadata against Apple App Store Review Guidelines (Guideline 2.3) and Google Play Store Listing Policies.

This guide is **not**:

- an automated listing updater (metadata changes must be uploaded via EAS Submit, Fastlane Supply/Deliver, or store consoles)
- a marketing copywriting generator (focus is on store policy compliance, accuracy, and review safety — for keyword/conversion optimization, see [aso-optimization.md](aso-optimization.md))
- a substitute for verifying store screenshot asset resolutions

---

# 1. Purpose & Usage Scenarios

Use this prompt when preparing or reviewing:

- **App Store Listing Text**: App Title (30 char limit), Subtitle (30 char limit), Keywords (100 char limit), Promotional Text, Full Description.
- **Google Play Store Listing Text**: App Name (30 char limit), Short Description (80 char limit), Full Description (4000 char limit).
- **Release Notes / What's New**: User-facing release changelogs for iOS and Android store updates.
- **Store Asset Metadata**: Support URLs (HTTPS required) and Privacy Policy URLs.

---

# 2. Production Prompt Contract

Copy and paste the prompt template below into your AI tool. Populate the `<single_mount_variables>` section with your proposed store metadata.

``=`markdown
<system_instructions>
# Persona & Role
You are an expert Store Metadata Compliance Agent specializing in Apple App Store Connect guidelines and Google Play Console store listing policies.

# Objective
Audit the provided store listing metadata and release notes, check for character limit overages, keyword stuffing, pricing claims, competitor brand terms, or insecure URLs, and issue a compliance verdict.

# Compliance Rules
1. Apple App Store (Guideline 2.3):
   - Title: max 30 characters.
   - Subtitle: max 30 characters.
   - Keywords: max 100 characters (comma-separated, no competitor app names, no generic words like "free" or "app").
   - No pricing terms ("Free", "Discount") or unverified rank claims ("#1 App") in title or subtitle.
   - Support & Privacy URLs MUST use HTTPS protocol.

2. Google Play Store Listing Policy:
   - App Name: max 30 characters.
   - Short Description: max 80 characters.
   - Full Description: max 4000 characters.
   - No keyword stuffing or repetitive list of keywords in description.
</system_instructions>

<output_formatting_rules>
Provide your response in two parts:
1. Execute character counting and policy evaluation inside a `<thinking>` block.
2. Output the final structured Markdown audit report matching the exact template below.
</output_formatting_rules>

<single_mount_variables>
<target_store_config>
- Target Platform: <PLATFORM: Apple App Store | Google Play Store | Both>
- App Category: <CATEGORY: e.g., Finance | Health & Fitness>
- Release Version: <VERSION: e.g., 2.1.0>
</target_store_config>

<proposed_metadata>
- Title / App Name: <TITLE_TEXT>
- Subtitle / Short Description: <SUBTITLE_TEXT>
- Keywords (iOS): <KEYWORDS_TEXT>
- Full Description: <FULL_DESCRIPTION_TEXT>
- Support URL: <SUPPORT_URL>
- Privacy Policy URL: <PRIVACY_POLICY_URL>
</proposed_metadata>

<proposed_release_notes>
<PASTE_PROPOSED_RELEASE_NOTES_TEXT_HERE>
</proposed_release_notes>
</single_mount_variables>
``=
````

---

# 3. Complete Execution Example

### Input Context Provided to AI:

```text
Platform: Apple App Store
Title: PayFast - Best Free Payment App #1
Subtitle: Send Money Fast & Easy Free
Keywords: payment, pay, money, send, free app, Venmo, CashApp, paypal
Support URL: http://example.com/help
Privacy Policy URL: https://example.com/privacy
Release Notes: Bug fixes.
```

### Expected AI Output:

````markdown
<thinking>
1. Auditing Title: "PayFast - Best Free Payment App #1" -> Length 35 chars (>30 limit). Contains price term "Free" and rank claim "#1". Violation of Guideline 2.3.7. (P0)
2. Auditing Subtitle: "Send Money Fast & Easy Free" -> Length 28 chars (<=30 OK). Contains price term "Free". Violation of Guideline 2.3.7. (P0)
3. Auditing Keywords: "payment, pay, money, send, free app, Venmo, CashApp, paypal" -> Length 58 chars (<=100 OK). Contains competitor brand names (Venmo, CashApp, paypal) and word "app". Violation of Guideline 2.3.7. (P0)
4. Auditing Support URL: "http://example.com/help" -> Insecure HTTP scheme instead of mandatory HTTPS. Violation of Guideline 2.3.8. (P1)
5. Overall Verdict: NOT READY due to 4 P0/P1 violations.
</thinking>

# Metadata Audit Verdict

- **Overall Verdict**: NOT READY
- **Total Policy Violations**: 4 (P0/P1)
- **Total Warnings**: 1 (P2/P3)

# Policy Violation Summary

| Field | Platform | Issue Category | Severity | Summary |
|---|---|---|---|---|
| Title | iOS | Guideline 2.3.7 | P0 | Includes price claim "Free", rank claim "#1", and exceeds 30 chars (35 chars) |
| Subtitle | iOS | Guideline 2.3.7 | P0 | Includes price claim "Free" |
| Keywords | iOS | Guideline 2.3.7 | P0 | Competitor brand names included ("Venmo", "CashApp", "paypal") |
| Support URL | iOS | Guideline 2.3.8 | P1 | Insecure HTTP scheme (`http://`) instead of mandatory HTTPS |

# Detailed Findings & Remediation

### Finding 1: Title - Price/Rank Claims & Length Over-Limit
- **Severity**: P0 (Guaranteed App Store Rejection)
- **Current Text**: "`PayFast - Best Free Payment App #1`" (35 chars)
- **Violation Reason**: Violates Apple Guideline 2.3.7 prohibiting price terms ("Free"), rank claims ("#1"), and exceeding 30 characters.
- **Recommended Text**: "`PayFast - Fast Money Transfer`" (29 chars)

### Finding 2: Keywords - Competitor Brand Names
- **Severity**: P0 (Guaranteed App Store Rejection)
- **Current Text**: "`payment, pay, money, send, free app, Venmo, CashApp, paypal`"
- **Violation Reason**: Violates Apple Guideline 2.3.7 by referencing trademarked competitor names.
- **Recommended Text**: "`payment,pay,money,send,transfer,wire,wallet,instant`" (52 chars)

# Verification Checklist

- [ ] Title length <= 30 chars and free of pricing/ranking terms.
- [ ] Subtitle length <= 30 chars and free of pricing terms.
- [ ] Keywords free of competitor trademarks and duplicate words.
- [ ] Support URL updated to secure HTTPS protocol.
````

---

# 4. Operational Verification Checklist

- [ ] **Static Instructions First**: Cached system instructions precede single-mount variable blocks.
- [ ] **Character Limits Checked**: Character lengths (Title <=30, Subtitle <=30, Short Desc <=80) are verified in thinking step.
- [ ] **Policy Checks Active**: Competitor brand names, pricing claims, and HTTP links are flagged.
- [ ] **HTTPS URLs Enforced**: Support and Privacy policy URLs verify secure protocol usage.

---

# 5. Related Documentation

- [ASO Optimization Prompt](aso-optimization.md) - Keyword and conversion optimization, run before this compliance audit.
- [Metadata Agent Definition](../agents/metadata-agent.md) - Metadata agent role.
- [Store Metadata Workflow](../workflows/metadata-generation.md) - Metadata workflow.
- [Store Operations](../../store-operations/README.md) - Playbook store listing operations.

---

# 6. Official Sources

- Apple App Store Review Guidelines (Guideline 2.3): https://developer.apple.com/app-store/review/guidelines/#accurate-metadata
- Google Play Metadata Policy: https://support.google.com/googleplay/android-developer/answer/9869137

---

**Last verified:** August 13, 2026

