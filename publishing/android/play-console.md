# Google Play Console: Service Accounts and Automated Deployment

This covers setting up a Google Cloud Service Account and connecting it to Play Console, so CI tools like EAS Submit and Fastlane Supply can upload `.aab` builds without a human clicking through the console every time.

This guide is **not**:

- an authorization mechanism to commit a Service Account JSON key to a git repository
- a substitute for setting up developer account permissions in the first place (see [store-accounts/google-play-console.md](../../store-accounts/google-play-console.md))
- a manual-upload-only guide — automated API deployment is the standard path here

---

## 1. How the pieces connect

The Google Play Developer API is what lets a CI pipeline push builds, manage tracks, and sync store listing text without a person in the loop.

```text
Google Cloud Console: enable the Google Play Android Developer API, create a service
account, export its key (pc-api.json)
        │
        ↓ (grant access in Play Console)
Play Console: link the service account as a user, grant "Release to testing tracks"
        │
        ↓
CI/CD tool (EAS / Fastlane): authenticate with pc-api.json, deploy .aab to
Internal / Closed / Production
```

## 2. Generating a service account key

1. In **Google Cloud Console**, go to API & Services → Create Service Account (name it something recognizable, like `fastlane-play-deploy`).
2. Give it the **Service Account User** role.
3. Create a key in **JSON** format and download it (`pc-api.json`).
4. In **Google Play Console → Users & Permissions**, invite the service account's email (something like `fastlane-play-deploy@project-id.iam.gserviceaccount.com`) as a user.
5. Grant it *Release to testing tracks*, *Manage testing tracks*, and *Edit store listings* — nothing broader than it needs.

> **Important:** Never store `pc-api.json` in your repository's source code. Save it as an encrypted secret in EAS Secrets (`eas secret:create`) or GitHub Actions Secrets.

## 3. Wiring it into EAS Submit or Fastlane Supply

**Expo `eas.json`:**

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

**Fastlane `Appfile` and `Fastfile`:**

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

For the full `eas submit` command reference and submit-profile options, see [release-engineering/eas/eas-submit.md](../../release-engineering/eas/eas-submit.md) and [frameworks/expo/submit.md](../../frameworks/expo/submit.md).

## 4. Before you automate

- [ ] The Google Play Developer API is enabled on the Google Cloud project.
- [ ] The service account's permissions are scoped to testing tracks and release management — not full admin.
- [ ] `pc-api.json` lives only in an encrypted CI secret, never in git.
- [ ] An `eas submit` or Fastlane Supply run completes without auth errors.
- [ ] The first automated deploy targets the `internal` track, not production.

---

## Official sources

- Google Play Developer API setup: https://developers.google.com/android-publisher/getting_started
- Fastlane Supply documentation: https://docs.fastlane.tools/actions/supply/

**Last verified:** August 14, 2026

---

## Related documentation

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

### Release engineering

- `release-engineering/eas/eas-submit.md`

### Checklists

- `checklists/android.md`

### Publishing (cross-platform)

- `publishing/cross-platform/README.md`
