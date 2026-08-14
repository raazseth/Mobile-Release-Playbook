# Store Operations & Review Administration

This directory contains production handbook guides, metadata specifications, screenshot resolution matrices, App Review triage protocols, rejection appeal workflows, and compliance questionnaires for **Apple App Store Connect & Google Play Console Store Operations**.

Engineered in alignment with **2026 platform specifications**, it specifies how to prepare, localize, submit, defend, and maintain public store listings for Expo and React Native applications while avoiding submission rejections or store takedowns.

This directory is **not**:

- a marketing growth strategy guide for paid user acquisition
- an authorization mechanism to submit un-tested binaries to store review
- a substitute for providing valid reviewer demo credentials during submission

---

# 1. Architecture of Store Operations

Store Operations governs the lifecycle of public-facing store listings, visual assets, metadata compliance, app review submissions, and post-submission rejection triage.

```text
┌────────────────────────────────────────────────────────┐
│             STORE LISTING & METADATA PREPARATION       │
│  (Title 30c, Subtitle 30c, Short Desc 80c, Keywords)   │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│            VISUAL ASSETS & MEDIA PRODUCTION            │
│  - iPhone 6.9" (1320x2868) / iPad 13" / Android        │
│  - App Previews (30fps H.264) & Feature Graphics      │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│            PRIVACY & CONTENT RATING QUESTIONNAIRES      │
│  - Apple App Privacy Labels & PrivacyInfo Manifests    │
│  - Google Play Data Safety & IARC Content Ratings      │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│            STORE REVIEW SUBMISSION & TRIAGE            │
│  - Demo Credentials + Reviewer Notes                   │
│  - Resolution Center / Play Console Appeal Triage      │
└────────────────────────────────────────────────────────┘
```

---

# 2. Subsystem Directory Taxonomy

| Handbook File | Core Purpose & Scope | Key Platform & Store Rules |
|---|---|---|
| **[README.md](README.md)** | Subsystem index, store operations architecture, and universal listing rules. | High-level operations workflow, 2026 platform rules. |
| **[metadata.md](metadata.md)** | Character limits, title rules, keywords, and promotional text guidelines. | Title 30c, Subtitle 30c, Keywords 100c, Short Desc 80c, Guideline 2.3. |
| **[app-listing.md](app-listing.md)** | Category selection, developer contact details, and store listing management. | Category taxonomy, public developer contact info, privacy URL. |
| **[screenshots.md](screenshots.md)** | Dimension matrices, alpha channel stripping, and visual design safety zones. | iPhone 6.9" (1320x2868 / 1290x2796), iPad 13", no alpha channels. |
| **[app-previews.md](app-previews.md)** | Video preview specifications, poster frames, and Guideline 2.3.4 compliance. | 30fps H.264, 15-30s duration, no device frames or ads. |
| **[localization.md](localization.md)** | Multi-region store listing localization and cultural adaptation rules. | Primary language fallback, localized metadata, right-to-left layout. |
| **[app-review.md](app-review.md)** | Submission preparation, reviewer notes, demo accounts, and export compliance. | Demo credentials mandate, TestFlight export compliance, reviewer notes. |
| **[rejection-handling.md](rejection-handling.md)** | Resolution Center triage, rejection code taxonomy, and appeal protocols. | Guideline 2.1, 2.3, 3.1.1, 5.1.1 triage; Board of Appeals. |
| **[resubmission.md](resubmission.md)** | Patching build issues, updating metadata, and binary resubmission flows. | Increment build numbers, update changelog, submit replacement build. |
| **[data-safety.md](data-safety.md)** | Google Play Data Safety form questionnaire and SDK data disclosures. | Data collection types, encryption status, third-party SDK alignment. |
| **[privacy-information.md](privacy-information.md)** | Apple App Privacy Labels and `PrivacyInfo.xcprivacy` manifest alignment. | Data Linked / Not Linked / Tracking, Required Reason API declarations. |
| **[content-ratings.md](content-ratings.md)** | IARC rating questionnaire, PEGI/ESRB ratings, and regional age rules. | IARC questionnaire, South Korea GRAC rules, age rating accuracy. |

---

# 3. Universal Store Operations Rules

All store operations in this playbook must adhere to five mandatory engineering rules:

```text
1. Absolute Metadata Accuracy (Apple Guideline 2.3)
   Store metadata, titles, subtitles, keywords, and screenshots MUST accurately reflect the app's
   actual functionality in the binary. Including competitor names or misleading features will trigger rejection.

2. Mandatory Demo Credentials & Reviewer Steps
   Submissions requiring login MUST provide fully functional demo account credentials (username/password)
   and step-by-step reviewer instructions in App Store Connect / Play Console Review Notes.

3. Strict Visual Asset Dimension Compliance
   Screenshots MUST strictly conform to store specifications (e.g., iPhone 6.9" 1320x2868, no alpha
   channels). Submitting images with alpha transparency (alpha channel) causes immediate upload failure.

4. 100% Privacy Declaration Alignment
   Apple App Privacy Labels and Google Play Data Safety forms MUST match the app's actual data collection
   and the declared `PrivacyInfo.xcprivacy` manifests of all embedded third-party SDKs.

5. Structured Rejection Response Protocol
   Never respond to a store rejection with hostility or immediate re-upload of the identical binary.
   Always diagnose the specific rejection code, fix the underlying defect, and submit a clear response.
```

---

# 4. Related Repository Documentation

- [Store Accounts Subsystem](../store-accounts/README.md) - Account administration & permissions.
- [Mobile Release Lifecycle](../foundations/mobile-release-lifecycle.md) - Release lifecycle.
- [Rejection Analysis Prompt](../ai/prompts/rejection-analysis.md) - AI rejection triage.

---

# 5. Official Sources

- Apple App Store Screenshot Specifications: https://developer.apple.com/help/app-store-connect/#/devd274dd925
- Google Play Graphic Assets Specifications: https://support.google.com/googleplay/android-developer/answer/9866151
- Apple App Review Guidelines: https://developer.apple.com/app-store/review/guidelines/

---

**Last verified:** August 14, 2026
