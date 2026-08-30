# Cross-Platform Metadata Synchronization & Schemas

This document covers reconciling iOS and Android character limits into one schema, and syncing it into Fastlane's metadata directory structure, for **Cross-Platform Store Metadata** in Expo and React Native applications — how to maintain one source of truth for store listing text instead of copy-pasting between App Store Connect and Google Play Console.

This guide is **not**:

- an authorization mechanism to ignore platform-specific metadata policies
- a substitute for managing localized store metadata
- a keyword-stuffing generator

---

# 1. Metadata Reconciliation & Limit Comparison

Apple App Store Connect and Google Play Console enforce different character limits and field rules. A unified metadata strategy reconciles these differences into a master schema.

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

| Metadata Field | iOS Limit | Android Limit | Unified Master Strategy |
|---|---|---|---|
| **App Title** | 30 chars | 30 chars | Use **identical 30-character title** across both platforms. |
| **Subtitle / Short Desc** | 30 chars | 80 chars | Write a concise 30-char summary for iOS; expand to 80 chars for Android. |
| **Long Description** | 4,000 chars | 4,000 chars | Share core description body text; include 2-3% keyword density for Android. |
| **Keywords** | 100 chars | N/A | Dedicated comma-separated keywords file for iOS (`keywords.txt`). |

---

# 2. Unified Master Metadata JSON Schema (`store-metadata.json`)

Maintain a single JSON source of truth for app store metadata in repository root:

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

---

# 3. Fastlane Synchronization Script (`sync-metadata.js`)

Automate splitting the master `store-metadata.json` into Fastlane Deliver (`ios/`) and Supply (`android/`) plain text files:

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

console.log('✅ Fastlane metadata directories successfully synchronized from master schema!');
```

---

# 4. Operational Verification Checklist

- [ ] **Master Schema Validated**: `store-metadata.json` Title fits within 30 characters for both platforms.
- [ ] **Fastlane Directories Synced**: Script `sync-metadata.js` generates plain text files for `ios` and `android`.
- [ ] **No Policy Terms in Title**: Words like "Free", "Sale", or "Top #1" absent from master title and subtitle.
- [ ] **Keywords Clean**: iOS `keywords.txt` is comma-separated without spaces or duplicate words.
- [ ] **Fastlane Deliver/Supply Tested**: Fastlane metadata sync command executes cleanly without store API errors.

---

# Related documentation

### Publishing (cross-platform)

- `publishing/cross-platform/README.md`
- `publishing/cross-platform/assets.md`
- `publishing/cross-platform/release-notes.md`
- `publishing/cross-platform/submission.md`

### Publishing (iOS)

- `publishing/ios/README.md`

### Publishing (Android)

- `publishing/android/README.md`

### Checklists

- `checklists/cross-platform.md`

### Store operations

- `store-operations/README.md`

---

# Official sources

- Fastlane Deliver Metadata Docs: https://docs.fastlane.tools/actions/deliver/
- Fastlane Supply Metadata Docs: https://docs.fastlane.tools/actions/supply/

---

**Last verified:** August 14, 2026

