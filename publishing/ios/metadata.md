# App Store Connect Text Metadata & Character Limits

This document details the character limits, title optimization, subtitle rules, keyword formatting, promotional text updates, and store compliance rules for **iOS Store Metadata** in App Store Connect.

Engineered in alignment with **2026 platform specifications**, it specifies how to construct App Store Connect text metadata for Expo and React Native applications.

This guide is **not**:

- an authorization mechanism to include competitor brand names in keywords
- a keyword stuffing strategy
- a substitute for localizing metadata across target global markets

---

# 1. App Store Connect Metadata Character Limit Matrix

App Store Connect strictly enforces character boundaries on text fields:

| Metadata Field | Character Limit | Search Indexing Weight | Key Compliance Rules |
|---|---|---|---|
| **App Name (Title)** | **30 characters max** | **Highest** | Must be unique; no price references ("Free", "Sale"); no competitor names. |
| **Subtitle** | **30 characters max** | **High** | Summarize core value proposition; no competitor names or CTA badges. |
| **Keywords** | **100 characters max** | **Medium** | Comma-separated without spaces; no duplicate words; no special characters. |
| **Description** | **4,000 characters max** | Not Indexed | Plain text / formatted copy; describe app features accurately. |
| **Promotional Text** | **170 characters max** | Not Indexed | Appears above description; editable anytime without build submission. |
| **What's New (Changelog)** | **4,000 characters max** | Not Indexed | Clear summary of new features or bug fixes for the current release. |

---

# 2. Keyword Field Optimization (Guideline 2.3.7)

Apple's 100-character keyword field requires strict formatting optimization:

```text
┌────────────────────────────────────────────────────────┐
│             APPLE KEYWORD FIELD OPTIMIZATION           │
│                                                        │
│  - CORRECT Format: Comma-separated without spaces      │
│    `fitness,tracker,workout,gym,log,run,calorie,diet`  │
│                                                        │
│  - INCORRECT Format (Wastes Characters):               │
│    `fitness, tracker, workout app, gym logger`         │
└────────────────────────────────────────────────────────┘
```

### What to EXCLUDE from Keywords

1. Words already present in your **App Name** or **Subtitle**.
2. Category names (e.g., "Health", "Fitness").
3. The word "app".
4. Competitor brand names (Guideline 2.3.7 rejection trigger).

---

# 3. Operational Verification Checklist

- [ ] **App Name Verified**: App Name ≤ 30 characters.
- [ ] **Subtitle Verified**: Subtitle ≤ 30 characters.
- [ ] **Keywords Formatted**: Keywords ≤ 100 characters, comma-separated without spaces.
- [ ] **No Competitor Trademarks**: Verified zero competitor brand names in keywords or subtitle.
- [ ] **Promotional Text Ready**: Promotional text configured for marketing announcements.

---

# 4. Related Documentation

- [Screenshots Handbook](screenshots.md) - iOS screenshots.
- [Metadata Handbook](../../store-operations/metadata.md) - Metadata rules.
- [App Store Connect Handbook](app-store-connect.md) - Console setup.

---

# 5. Official Sources

- Apple App Store Product Page Guidance: https://developer.apple.com/app-store/product-page/

---

**Last verified:** August 14, 2026
