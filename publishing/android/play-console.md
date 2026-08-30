# Google Play Console Administration & Service Account Integration

This document covers track administration, Google Cloud Service Account authentication, and automated deployment tools (Fastlane Supply, EAS Submit) for **Google Play Console** in Expo and React Native applications — how to connect a CI/CD pipeline to Google Play Console for automated `.aab` uploads and release management.

This guide is **not**:

- an authorization mechanism to embed Service Account JSON keys in git repositories
- a substitute for configuring developer account permissions (see [store-accounts/](../../store-accounts/google-play-console.md))
- a manual-only upload guide (automated API deployments are recommended)

---

# 1. Google Play Developer API Architecture

The Google Play Developer API allows CI/CD systems to automate build deployment, track updates, store listing text synchronization, and rollout management.

```text
Google Cloud Console: enable Google Play Android Developer API, create a service
account, export its key (pc-api.json)
        │
        ↓ (grant access in Play Console)
Play Console: link the service account as a user, grant "Release to testing tracks"
        │
        ↓
CI/CD tool (EAS / Fastlane): authenticate with pc-api.json, deploy .aab to
Internal / Closed / Production
```

---

# 2. Service Account Key Generation Step-by-Step

To set up automated deployment credentials:

1. **Google Cloud Console**: Navigate to API & Services -> Create Service Account (name: `fastlane-play-deploy`).
2. **Assign Role**: Select **Service Account User**.
3. **Export Key**: Create key in **JSON** format. Download the JSON key file (`pc-api.json`).
4. **Google Play Console Users & Permissions**: Invite the Service Account email (e.g., `fastlane-play-deploy@project-id.iam.gserviceaccount.com`) as a user.
5. **Set Permissions**: Grant *Release to testing tracks*, *Manage testing tracks*, and *Edit store listings*.

> **SECURITY MANDATE**: Never store `pc-api.json` inside repository source code. Save the JSON string as an encrypted secret in EAS Secrets (`eas secret:create`) or GitHub Actions Secrets.

---

# 3. Expo EAS Submit & Fastlane Supply Setup

### Expo `eas.json` Configuration

```json
{
  "submit": {
    "production": {
      "android": {
        "serviceAccountKeyPath": "./pc-api.json",
        "track": "internal"
      }
    }
  }
}
```

### Fastlane `Appfile` & `Fastfile` Execution

```ruby
# fastlane/Appfile
json_key_file("fastlane/pc-api.json")
package_name("com.example.mobileapp")

# fastlane/Fastfile
lane :upload_to_internal do
  upload_to_play_store(
    track: 'internal',
    aab: 'android/app/build/outputs/bundle/release/app-release.aab',
    skip_upload_metadata: true,
    skip_upload_images: true
  )
end
```

---

# 4. Operational Verification Checklist

- [ ] **Google Play Developer API Enabled**: API enabled in Google Cloud Console project.
- [ ] **Service Account Privileges Scoped**: Permissions restricted to testing tracks and release management.
- [ ] **JSON Key Vaulted**: `pc-api.json` stored exclusively in encrypted CI secret storage.
- [ ] **EAS Submit / Fastlane Verified**: Automated upload command executes successfully without auth errors.
- [ ] **Track Configured**: Initial deployment targeted at `internal` testing track before production.

---

# Related documentation

### Publishing (Android)

- `publishing/android/README.md`
- `publishing/android/app-bundle.md`
- `publishing/android/app-review.md`
- `publishing/android/closed-testing.md`
- `publishing/android/internal-testing.md`
- `publishing/android/metadata.md`
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

### Checklists

- `checklists/android.md`

### Publishing (cross-platform)

- `publishing/cross-platform/README.md`

---

# Official sources

- Google Play Developer API Setup: https://developers.google.com/android-publisher/getting_started
- Fastlane Supply Documentation: https://docs.fastlane.tools/actions/supply/

---

**Last verified:** August 14, 2026

