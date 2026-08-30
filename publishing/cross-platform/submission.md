# Cross-Platform Automated Submissions & CI/CD Pipelines

This document covers automated submission workflows via EAS Submit, Fastlane, and GitHub Actions, plus secret management, for **Cross-Platform App Submissions** in Expo and React Native applications — how to build a reproducible submission pipeline for both App Store Connect and Google Play Console.

This guide is **not**:

- an authorization mechanism to embed plain-text private keys in CI configuration scripts
- a substitute for verifying that native builds compile cleanly before submitting
- a manual-only submission guide (automated API submissions are standard practice)

---

# 1. Automated Submission Pipeline Architecture

Automated submission tools accept compiled build artifacts (`.ipa` for iOS, `.aab` for Android) and upload them to store backends via authenticated store APIs.

```text
Compiled build artifacts
  - iOS:     build/output.ipa (signed with Distribution Certificate)
  - Android: build/output.aab (signed with the upload key)
        │
        ├─→ EAS Submit (iOS)     → App Store Connect API Key (.p8) → App Store Connect TestFlight
        └─→ EAS Submit (Android) → Play Developer API service account JSON → Google Play internal track
```

---

# 2. Expo EAS Submit Configuration (`eas.json`)

Expo's `eas.json` provides a unified declarative configuration schema for automated submission across iOS and Android:

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

### EAS Command Execution

```bash
# Submit compiled iOS and Android builds in parallel
eas submit --platform all --profile production --non-interactive
```

---

# 3. GitHub Actions Unified Matrix Workflow

Automate cross-platform build and submission using GitHub Actions CI/CD pipelines:

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
      - name: Checkout Repository
        uses: actions/checkout@v4

      - name: Setup Node.js & Bun
        uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: 'npm'

      - name: Setup Expo CLI & EAS
        uses: expo/expo-github-action@v8
        with:
          eas-version: latest
          token: ${{ secrets.EXPO_TOKEN }}

      - name: Install Dependencies
        run: npm ci

      - name: Compile & Submit Build to Store
        run: |
          eas build --platform ${{ matrix.platform }} --profile production --non-interactive --auto-submit
        env:
          EXPO_TOKEN: ${{ secrets.EXPO_TOKEN }}
```

---

# 4. Operational Verification Checklist

- [ ] **EAS Config Validated**: `eas.json` schema validated for both `ios` and `android` platforms.
- [ ] **App Store Connect Key Secret**: `.p8` API key stored in encrypted CI secrets (`ASC_API_KEY`).
- [ ] **Play Service Account Secret**: Service account JSON stored in encrypted CI secrets (`PLAY_SERVICE_ACCOUNT`).
- [ ] **Build Numbers Incremented**: Automated build number incrementing (`autoIncrement: true`) active.
- [ ] **Parallel CI Execution**: GitHub Actions matrix compiles iOS and Android in parallel without blocking.

---

# Related documentation

### Publishing (cross-platform)

- `publishing/cross-platform/README.md`
- `publishing/cross-platform/assets.md`
- `publishing/cross-platform/metadata.md`
- `publishing/cross-platform/release-notes.md`

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

- Expo EAS Submit Guide: https://docs.expo.dev/submit/introduction/
- Fastlane Multi-Platform Automation: https://docs.fastlane.tools/

---

**Last verified:** August 14, 2026

