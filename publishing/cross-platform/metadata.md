# Cross-Platform Metadata

Apple and Google enforce different character limits and field rules for store listing text, which makes it tempting to just copy-paste between App Store Connect and Play Console and hope nothing gets cut off. This covers reconciling both into one schema you maintain once.

This guide is **not**:

- an authorization mechanism to ignore either platform's metadata policy
- a substitute for managing localized store metadata
- a keyword-stuffing generator

---

## 1. Reconciling two different limit sets

```text
Cross-platform master metadata
  - app title: fits in 30 characters on both stores
  - iOS subtitle (30c) vs Android short description (80c)
  - long description: shared 4,000-character text
  - keywords: iOS-only 100c field; Android relies on the description instead
        │
        ├─→ Fastlane Deliver → metadata/ios/
        └─→ Fastlane Supply  → metadata/android/
```

| Field | iOS limit | Android limit | Unified strategy |
|---|---|---|---|
| App Title | 30 chars | 30 chars | One identical 30-character title, used on both platforms |
| Subtitle / Short Description | 30 chars | 80 chars | Write a tight 30-char version for iOS, then expand it to 80 chars for Android |
| Long Description | 4,000 chars | 4,000 chars | Share the core description body; keep 2–3% keyword density for Android's benefit |
| Keywords | 100 chars | N/A | A dedicated comma-separated `keywords.txt`, iOS-only |

## 2. A single source-of-truth schema

Keep one JSON file at the repo root instead of maintaining separate copies per store:

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "appName": "FitnessTracker Pro",
  "locales": {
    "en-US": {
      "title": "FitnessTracker Pro",
      "subtitle": "Workout & Gym Log Tracker",
      "shortDescription": "Track your gym workouts, log exercises, and monitor fitness progress effortlessly.",
      "keywords": "fitness,tracker,workout,gym,log,exercise,calorie,weight",
      "description": "FitnessTracker Pro is the ultimate companion for your gym workouts and fitness journey..."
    }
  }
}
```

## 3. Splitting it into Fastlane's directory structure

A small script keeps `fastlane/metadata/ios/` and `fastlane/metadata/android/` in sync with the master file, so you're never editing two places by hand:

```javascript
// scripts/sync-metadata.js
const fs = require('fs');
const path = require('path');

const masterData = JSON.parse(fs.readFileSync('./store-metadata.json', 'utf8'));

Object.entries(masterData.locales).forEach(([locale, data]) => {
  // Write iOS Fastlane files
  const iosDir = path.join('./fastlane/metadata/ios', locale);
  fs.mkdirSync(iosDir, { recursive: true });
  fs.writeFileSync(path.join(iosDir, 'name.txt'), data.title);
  fs.writeFileSync(path.join(iosDir, 'subtitle.txt'), data.subtitle);
  fs.writeFileSync(path.join(iosDir, 'keywords.txt'), data.keywords);
  fs.writeFileSync(path.join(iosDir, 'description.txt'), data.description);

  // Write Android Fastlane files
  const androidDir = path.join('./fastlane/metadata/android', locale);
  fs.mkdirSync(androidDir, { recursive: true });
  fs.writeFileSync(path.join(androidDir, 'title.txt'), data.title);
  fs.writeFileSync(path.join(androidDir, 'short_description.txt'), data.shortDescription);
  fs.writeFileSync(path.join(androidDir, 'full_description.txt'), data.description);
});

console.log('Fastlane metadata directories synchronized from the master schema.');
```

## 4. Before you sync

- [ ] The title in `store-metadata.json` fits within 30 characters on both platforms.
- [ ] `sync-metadata.js` runs and produces plain text files for both `ios` and `android`.
- [ ] Neither the title nor subtitle contains "Free," "Sale," or "Top #1."
- [ ] `keywords.txt` is comma-separated, no spaces, no duplicate words.
- [ ] A Fastlane Deliver/Supply dry run completes without a store API error.

---

## Official sources

- Fastlane Deliver metadata docs: https://docs.fastlane.tools/actions/deliver/
- Fastlane Supply metadata docs: https://docs.fastlane.tools/actions/supply/

**Last verified:** August 14, 2026

---

## Related documentation

### Publishing (cross-platform)

- `publishing/cross-platform/README.md`
- `publishing/cross-platform/assets.md`
- `publishing/cross-platform/release-notes.md`
- `publishing/cross-platform/submission.md`

### Publishing (iOS)

- `publishing/ios/metadata.md`

### Publishing (Android)

- `publishing/android/metadata.md`

### Store operations

- `store-operations/README.md`
- `store-operations/metadata.md`

### Troubleshooting

- `troubleshooting/metadata-rejected.md`

### Checklists

- `checklists/cross-platform.md`
