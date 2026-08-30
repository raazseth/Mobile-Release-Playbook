# App Store Connect Text Metadata & Character Limits

This document covers character limits, title/subtitle rules, keyword field formatting, and promotional text for **iOS Store Metadata** in App Store Connect — how to fill in App Store Connect's text metadata fields for an Expo or React Native app at submission time. For the full metadata policy and localization guidance, see `store-operations/metadata.md`.

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
Correct   (comma-separated, no spaces):  fitness,tracker,workout,gym,log,run,calorie,diet
Incorrect (wastes characters on spaces): fitness, tracker, workout app, gym logger
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

# Related documentation

### Publishing (iOS)

- `publishing/ios/README.md`
- `publishing/ios/app-review.md`
- `publishing/ios/app-store-connect.md`
- `publishing/ios/build-upload.md`
- `publishing/ios/production-release.md`
- `publishing/ios/screenshots.md`
- `publishing/ios/testflight.md`

### Store accounts

- `store-accounts/app-store-connect.md`

### iOS signing

- `signing/ios/README.md`
- `signing/ios/distribution.md`

### Store operations

- `store-operations/app-review.md`
- `store-operations/rejection-handling.md`

### Checklists

- `checklists/ios.md`

### Publishing (cross-platform)

- `publishing/cross-platform/README.md`

---

# Official sources

- Apple App Store Product Page Guidance: https://developer.apple.com/app-store/product-page/

---

**Last verified:** August 14, 2026

