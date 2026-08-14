# Store Metadata Specifications & Character Limits Handbook

This handbook details the character limits, title optimization rules, subtitle strategies, keyword research frameworks, short and long description structures, promotional text updates, and store review compliance rules for **Store Metadata** in Apple App Store Connect and Google Play Console.

Engineered in alignment with **2026 platform specifications**—specifically Apple Guideline 2.3 (Accurate Metadata), Guideline 2.3.7 (Competitor Trademarks & Keyword Stuffing), and Google Play Store Listing Policies—it provides exhaustive technical and operational guidance for structuring app store metadata for Expo and React Native applications.

This guide is **not**:

- an authorization mechanism to include competitor trademark terms in keyword lists
- a spammy keyword-stuffing generator (store search indexing algorithms reject spammy metadata)
- a substitute for maintaining localized metadata across target storefronts

---

# 1. Architecture of Store Metadata & Search Indexing

Store search algorithms index metadata fields differently across iOS and Android. Understanding how search engines parse title, subtitle, keywords, and description fields determines store discoverability and review compliance.

```text
┌────────────────────────────────────────────────────────┐
│             APPLE APP STORE INDEXING ENGINE            │
│  - App Title (30c)     ─────→ HIGHEST Index Weight     │
│  - Subtitle (30c)      ─────→ HIGH Index Weight        │
│  - Keywords (100c)     ─────→ MEDIUM Index Weight      │
│  - Long Description    ─────→ NOT Indexed for Search   │
└────────────────────────────────────────────────────────┘
                           vs
┌────────────────────────────────────────────────────────┐
│             GOOGLE PLAY STORE INDEXING ENGINE          │
│  - App Title (30c)     ─────→ HIGHEST Index Weight     │
│  - Short Description (80c) ─→ HIGH Index Weight        │
│  - Long Description (4000c) → Indexed (Density 2-3%)   │
└────────────────────────────────────────────────────────┘
```

---

# 2. Comprehensive Store Metadata Character Limit Matrix

Store frontends strictly truncate or reject metadata fields exceeding character limits:

| Metadata Field | Apple App Store Connect | Google Play Console | Search Indexing Impact | Key Compliance Rules |
|---|---|---|---|---|
| **App Title / Name** | **30 characters max** | **30 characters max** | **Highest Search Weight** | Must be unique; no price references (e.g., "Free", "Sale", "Discount"); no competitor names. |
| **Subtitle** | **30 characters max** | N/A | **High Search Weight** | Summarize core value proposition; no competitor trademarks or call-to-action badges. |
| **Short Description** | N/A | **80 characters max** | **High Search Weight** | Appears on Google Play search cards; concise value summary; no emoji spam. |
| **Long Description** | **4,000 characters max** | **4,000 characters max** | **Indexed on Play Only** | Plain text / formatted copy; no invalid HTML tags; keyword density 2-3% on Google Play. |
| **Keywords Field** | **100 characters max** | N/A | **Medium Search Weight** | Comma-separated without spaces; no duplicate words; no special characters or brand names. |
| **Promotional Text** | **170 characters max** | N/A | Not Indexed for Search | Appears above description in App Store; editable anytime without build submission. |
| **What's New (Changelog)** | **4,000 characters max** | **500 characters max** | Not Indexed | Clear release highlights; no generic "Bug fixes and improvements" only. |

---

# 3. Apple App Store Keyword Field Optimization (Guideline 2.3.7)

Apple's 100-character keyword field requires strict formatting optimization to maximize search indexing without wasting character space.

```text
┌────────────────────────────────────────────────────────┐
│             APPLE KEYWORD FIELD FORMATTING             │
│                                                        │
│  - CORRECT Format: Comma-separated without spaces      │
│    `fitness,tracker,workout,gym,log,run,calorie,diet`  │
│                                                        │
│  - INCORRECT Format (Wastes Characters):               │
│    `fitness, tracker, workout app, gym logger`         │
└────────────────────────────────────────────────────────┘
```

### What to EXCLUDE from the Apple Keyword Field

1. **Words already in your Title or Subtitle**: App Store Connect automatically combines words from Title, Subtitle, and Keyword fields. Re-using title words in keywords wastes space.
2. **Category Names**: Do not include your primary category name (e.g., "Health", "Fitness") as Apple indexes categories automatically.
3. **The word "App"**: Apple automatically removes "app" from search indexing queries.
4. **Competitor Brand Names (Guideline 2.3.7)**: Including competitor app names (e.g., "Strava", "MyFitnessPal") will trigger an immediate rejection under Guideline 2.3.7.

---

# 4. Google Play Store Listing Text Formatting & Policy

Google Play enforces strict guidelines regarding text formatting in titles, short descriptions, and long descriptions:

- **No Call-To-Action Badges**: Titles and short descriptions MUST NOT contain phrases like "Top #1 App", "Best of 2026", "Download Now", "Free", or "Sale".
- **No Emoji Spam**: Excessive capital letters, repeated punctuation (e.g., "!!!"), or emoji spam in titles will trigger Google Play Console submission rejection.
- **Keyword Density Boundary**: Long descriptions on Google Play should maintain a natural keyword density between 2% and 3%. Repeating keywords dozens of times triggers Google Play's keyword stuffing penalty.

---

# 5. Fastlane Automated Metadata File Layout

Store metadata should be version-controlled in git using plain text files managed by **Fastlane Deliver** (iOS) and **Fastlane Supply** (Android):

```text
fastlane/
├── metadata/
│   ├── android/
│   │   └── en-US/
│   │       ├── title.txt
│   │       ├── short_description.txt
│   │       ├── full_description.txt
│   │       └── changelogs/
│   │           └── 143.txt
│   └── ios/
│       └── en-US/
│           ├── name.txt
│           ├── subtitle.txt
│           ├── keywords.txt
│           ├── description.txt
│           ├── promotional_text.txt
│           └── release_notes.txt
```

---

# 6. Operational Verification Checklist

- [ ] **Character Limits Enforced**: Title ≤ 30c; Subtitle ≤ 30c; Short Description ≤ 80c; Keywords ≤ 100c.
- [ ] **No Competitor Trademarks**: Verified zero competitor brand names in keywords or subtitles (Guideline 2.3.7).
- [ ] **No Pricing Terms in Title**: Words like "Free", "Sale", or "Discount" absent from title and subtitle.
- [ ] **Keyword Formatting Clean**: Keywords separated by commas without spaces or duplicate words.
- [ ] **Google Play Keyword Density 2-3%**: Long description maintains natural reading flow without keyword stuffing.
- [ ] **Changelog Descriptive**: "What's New" field details tangible user features or specific bug fixes.

---

# 7. Related Documentation

- [App Listing Handbook](app-listing.md) - Listing setup.
- [Localization Handbook](localization.md) - Localized metadata.
- [Metadata Generation Workflow](../../ai/workflows/metadata-generation.md) - AI metadata generation.

---

# 8. Official Sources

- Apple App Store Product Page Guidance: https://developer.apple.com/app-store/product-page/
- Apple Guideline 2.3 (Accurate Metadata): https://developer.apple.com/app-store/review/guidelines/#accurate-metadata
- Google Play Store Listing Assets Policy: https://support.google.com/googleplay/android-developer/answer/9866151

---

**Last verified:** August 14, 2026
