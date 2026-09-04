# App Store Connect Metadata

This covers the text fields App Store Connect asks for at submission time — title, subtitle, keywords, description — and their character limits. For the broader metadata policy and localization guidance, see `store-operations/metadata.md`.

This guide is **not**:

- an authorization mechanism to include competitor brand names in your keywords
- a keyword-stuffing strategy
- a substitute for localizing your metadata for the markets you actually target

---

## 1. Character limits

App Store Connect enforces hard limits on every text field:

| Field | Limit | How much it affects search | Watch out for |
|---|---|---|---|
| App Name (Title) | 30 characters | Highest | Must be unique; no "Free"/"Sale"; no competitor names |
| Subtitle | 30 characters | High | Summarize the core value; no competitor names or fake CTA badges |
| Keywords | 100 characters | Medium | Comma-separated, no spaces, no duplicate words, no special characters |
| Description | 4,000 characters | Not indexed | Describe what the app actually does |
| Promotional Text | 170 characters | Not indexed | Shown above the description; you can edit it anytime without a new build |
| What's New (Changelog) | 4,000 characters | Not indexed | Clear summary of what changed in this release |

## 2. Formatting keywords correctly

Apple's 100-character keyword field is easy to waste on formatting mistakes:

```text
Correct   (comma-separated, no spaces):  fitness,tracker,workout,gym,log,run,calorie,diet
Incorrect (wastes characters on spaces): fitness, tracker, workout app, gym logger
```

Skip these when writing keywords:

1. Words already in your App Name or Subtitle — they're indexed automatically.
2. Category names like "Health" or "Fitness."
3. The word "app."
4. Competitor brand names — this is a Guideline 2.3.7 rejection trigger.

## 3. Before you submit

- [ ] App Name is 30 characters or fewer.
- [ ] Subtitle is 30 characters or fewer.
- [ ] Keywords are 100 characters or fewer, comma-separated, no spaces.
- [ ] No competitor trademarks anywhere in keywords or subtitle.
- [ ] Promotional text is ready if you're planning a launch announcement.

---

## Official sources

- Apple App Store product page guidance: https://developer.apple.com/app-store/product-page/

**Last verified:** August 14, 2026

---

## Related documentation

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
