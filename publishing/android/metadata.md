# Google Play Store Metadata & Listing Text

This document details the character limits, title optimization, short description guidelines, long description density rules, and policy compliance for **Google Play Metadata** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it specifies how to construct store listing text that maximizes Play Store discoverability while avoiding policy rejections.

This guide is **not**:

- an authorization mechanism to include call-to-action badges in app titles
- a keyword stuffing script (Google Play algorithms penalize unnatural keyword repetition)
- a substitute for localizing metadata across target global markets

---

# 1. Google Play Metadata Character Limit Matrix

Google Play Console strictly enforces character boundaries on store listing text fields:

| Metadata Field | Character Limit | Search Indexing Weight | Key Compliance Rules |
|---|---|---|---|
| **App Title** | **30 characters max** | **Highest** | No pricing words ("Free", "Sale"); no call-to-action ("Download Now"); no emoji spam. |
| **Short Description** | **80 characters max** | **High** | Concise value summary displayed on search cards; no misleading claims. |
| **Long Description** | **4,000 characters max** | **Medium (Density 2-3%)** | Plain text / simple formatting; no invalid HTML tags; keyword density 2-3%. |
| **Release Notes (Changelog)** | **500 characters max** | Not Indexed | Clear summary of new features or bug fixes for the current release. |

---

# 2. Google Play Title & Description Formatting Policy

Google Play strictly prohibits manipulative text elements in store listings:

```text
┌────────────────────────────────────────────────────────┐
│             PROHIBITED TEXT ELEMENTS ON GOOGLE PLAY    │
│                                                        │
│  - No Price/Promotion Terms: "Free", "Sale", "50% Off" │
│  - No Ranking Claims: "Top #1 App", "Best of 2026"     │
│  - No Call-to-Action: "Download Now", "Install Today"  │
│  - No Emoji Spam: Repeated emojis or CAPITAL CAPS SPAM │
└────────────────────────────────────────────────────────┘
```

> **POLICY REJECTION RISK**: Including phrases like "Top Free App" or "Download Now!" in your Google Play Title or Short Description will trigger an immediate store listing rejection during review.

---

# 3. Keyword Density Optimization (2-3% Rule)

Google Play indexes the **Long Description** field for search queries:

- **Optimal Density**: Include target keywords naturally throughout the long description so they account for **2% to 3%** of total word count.
- **Avoid Keyword Stuffing**: Repeating a keyword 20 times in a block list at the bottom of the description will trigger Google Play's spam filter, dropping your app's search ranking.

---

# 4. Operational Verification Checklist

- [ ] **Title Length Verified**: App Title ≤ 30 characters.
- [ ] **Short Description Verified**: Short Description ≤ 80 characters.
- [ ] **No Promotional Claims**: Title and short description free of "Free", "Top #1", or "Download Now".
- [ ] **Keyword Density Balanced**: Long description maintains 2-3% natural keyword density.
- [ ] **Fastlane Directory Synced**: Plain text files version-controlled in `fastlane/metadata/android/`.

---

# 5. Related Documentation

- [Screenshots Handbook](screenshots.md) - Asset specifications.
- [Metadata Handbook](../../store-operations/metadata.md) - Store operations metadata.
- [Play Console Handbook](play-console.md) - Console setup.

---

# 6. Official Sources

- Google Play Store Listing Assets Policy: https://support.google.com/googleplay/android-developer/answer/9866151

---

**Last verified:** August 14, 2026
