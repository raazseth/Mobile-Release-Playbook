# Automated Cross-Platform Submission

This covers building a submission pipeline that pushes both `.ipa` and `.aab` builds to their respective stores through EAS Submit, Fastlane, and GitHub Actions — so a tagged release goes out without anyone manually clicking through App Store Connect or Play Console.

This guide is **not**:

- an authorization mechanism to embed plain-text private keys in CI configuration
- a substitute for verifying that the native build actually compiles cleanly before you try to submit it
- a manual-only submission guide — automated API submission is the standard here

---

## 1. How a build gets from CI to a store

Automated submission tools take a compiled build artifact and upload it to a store backend through an authenticated API.

```text
Compiled build artifacts
  - iOS:     build/output.ipa (signed with Distribution Certificate)
  - Android: build/output.aab (signed with the upload key)
        │
        ├─→ EAS Submit (iOS)     → App Store Connect API Key (.p8) → App Store Connect TestFlight
        └─→ EAS Submit (Android) → Play Developer API service account JSON → Google Play internal track
```

## 2. `eas.json` for both platforms

```json
{
  "cli": {
    "version": ">= 10.0.0"
  },
  "build": {
    "production": {
      "channel": "production",
      "autoIncrement": true
    }
  },
  "submit": {
    "production": {
      "ios": {
        "appleId": "developer@company.com",
        "ascApiKeyPath": "./credentials/asc-api-key.p8",
        "ascApiKeyId": "2X9R49336D",
        "ascApiKeyIssuerId": "69a6de71-7034-47e3-e053-5b8c7c11a4d1"
      },
      "android": {
        "serviceAccountKeyPath": "./credentials/pc-api.json",
        "track": "internal"
      }
    }
  }
}
```

```bash
# Submit both compiled builds in parallel
eas submit --platform all --profile production --non-interactive
```

For the full submit-profile schema and every available flag, see [release-engineering/eas/eas-submit.md](../../release-engineering/eas/eas-submit.md).

## 3. A GitHub Actions matrix build

```yaml
name: Production Release Pipeline

on:
  push:
    tags:
      - 'v*'

jobs:
  build-and-submit:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        platform: [ios, android]
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: 'npm'

      - name: Setup Expo CLI & EAS
        uses: expo/expo-github-action@v8
        with:
          eas-version: latest
          token: ${{ secrets.EXPO_TOKEN }}

      - name: Install dependencies
        run: npm ci

      - name: Build and submit
        run: |
          eas build --platform ${{ matrix.platform }} --profile production --non-interactive --auto-submit
        env:
          EXPO_TOKEN: ${{ secrets.EXPO_TOKEN }}
```

## 4. Before you wire this into CI

- [ ] `eas.json` validates for both `ios` and `android` submit profiles.
- [ ] The App Store Connect `.p8` key lives in an encrypted CI secret (e.g. `ASC_API_KEY`), not in the repo.
- [ ] The Play service account JSON lives in an encrypted CI secret (e.g. `PLAY_SERVICE_ACCOUNT`), not in the repo.
- [ ] `autoIncrement: true` is set so build numbers don't collide.
- [ ] The GitHub Actions matrix builds iOS and Android in parallel, not one blocking the other.

---

## Official sources

- Expo EAS Submit guide: https://docs.expo.dev/submit/introduction/
- Fastlane multi-platform automation: https://docs.fastlane.tools/

**Last verified:** August 14, 2026

---

## Related documentation

### Publishing (cross-platform)

- `publishing/cross-platform/README.md`
- `publishing/cross-platform/assets.md`
- `publishing/cross-platform/metadata.md`
- `publishing/cross-platform/release-notes.md`

### Publishing (iOS)

- `publishing/ios/README.md`

### Publishing (Android)

- `publishing/android/README.md`

### Release engineering

- `release-engineering/eas/eas-submit.md`

### Troubleshooting

- `troubleshooting/upload-fails.md`
- `troubleshooting/ci-fails.md`

### Checklists

- `checklists/cross-platform.md`

### Store operations

- `store-operations/README.md`
