# Google Play Store Metadata

This covers the store listing text fields Google Play Console asks for at submission time, their character limits, and the wording that gets a listing flagged. For the broader metadata policy and localization guidance, see `store-operations/metadata.md`.

This guide is **not**:

- an authorization mechanism to put call-to-action badges in your app title
- a keyword-stuffing script — Google Play's algorithms penalize unnatural keyword repetition
- a substitute for localizing your metadata for the markets you target

---

## 1. Character limits

| Field | Limit | How much it affects search | Watch out for |
|---|---|---|---|
| App Title | 30 characters | Highest | No pricing words ("Free", "Sale"); no calls to action ("Download Now"); no emoji spam |
| Short Description | 80 characters | High | A concise value summary shown on search cards — no misleading claims |
| Long Description | 4,000 characters | Medium (2–3% keyword density) | Plain text or simple formatting; no invalid HTML tags |
| Release Notes (Changelog) | 500 characters | Not indexed | A clear summary of what changed in this release |

## 2. Wording Google Play rejects

Google Play prohibits manipulative text in store listings:

```text
Prohibited in title/short description:
  - price or promotion terms: "Free", "Sale", "50% Off"
  - ranking claims: "Top #1 App", "Best of 2026"
  - calls to action: "Download Now", "Install Today"
  - emoji spam or ALL-CAPS spam
```

> **Important:** A title or short description containing "Top Free App" or "Download Now!" will get your listing rejected during review.

## 3. Keyword density in the long description

Google Play indexes the **Long Description** field for search:

- Work target keywords naturally into the text so they land around **2–3%** of total word count.
- Don't stuff a keyword 20 times in a block list at the bottom of the description — Google Play's spam filter catches this and it drops your app's search ranking instead of helping it.

## 4. Before you submit

- [ ] App Title is 30 characters or fewer.
- [ ] Short Description is 80 characters or fewer.
- [ ] Neither field contains "Free," "Top #1," or "Download Now."
- [ ] The long description's keyword density is natural, not stuffed.
- [ ] The plain-text files in `fastlane/metadata/android/` are version-controlled and in sync with what's actually in Play Console.

---

## Official sources

- Google Play store listing assets policy: https://support.google.com/googleplay/android-developer/answer/9866151

**Last verified:** August 14, 2026

---

## Related documentation

### Publishing (Android)

- `publishing/android/README.md`
- `publishing/android/app-bundle.md`
- `publishing/android/app-review.md`
- `publishing/android/closed-testing.md`
- `publishing/android/internal-testing.md`
- `publishing/android/play-console.md`
- `publishing/android/production-release.md`
- `publishing/android/screenshots.md`

### Store accounts

- `store-accounts/google-play-console.md`

### Android signing

- `signing/android/README.md`
- `signing/android/play-app-signing.md`

### Store operations

- `store-operations/app-review.md`
- `store-operations/rejection-handling.md`
- `store-operations/metadata.md`

### Troubleshooting

- `troubleshooting/metadata-rejected.md`

### Checklists

- `checklists/android.md`

### Publishing (cross-platform)

- `publishing/cross-platform/README.md`
