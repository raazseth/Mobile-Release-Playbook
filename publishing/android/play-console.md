# Google Play Console Administration & Service Account Integration

This document details track administration, Google Cloud Service Account authentication, automated deployment tools (Fastlane Supply, EAS Submit), and API access scoping for **Google Play Console** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it specifies how to connect automated CI/CD build pipelines to Google Play Console for seamless `.aab` uploads and release management.

This guide is **not**:

- an authorization mechanism to embed Service Account JSON keys in git repositories
- a substitute for configuring developer account permissions (see [store-accounts/](../../store-accounts/google-play-console.md))
- a manual-only upload guide (automated API deployments are recommended)

---

# 1. Google Play Developer API Architecture

The Google Play Developer API allows CI/CD systems to automate build deployment, track updates, store listing text synchronization, and rollout management.

```text
┌────────────────────────────────────────────────────────┐
│             GOOGLE CLOUD PLATFORM CONSOLE              │
│  - Enable Google Play Android Developer API            │
│  - Create Service Account & Export Key (`pc-api.json`)  │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼ (Grant Access in Play Console)
┌────────────────────────────────────────────────────────┐
│             GOOGLE PLAY CONSOLE API ACCESS             │
│  - Link Service Account User to Play Console          │
│  - Grant "Release to testing tracks" Permission        │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│             CI/CD DEPLOYMENT TOOL (EAS / Fastlane)     │
│  - Authenticate via `pc-api.json` Secret Key           │
│  - Deploy `.aab` to Internal / Closed / Production     │
└──────────────────────────┘
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

# 5. Related Documentation

- [Internal Testing Handbook](internal-testing.md) - Internal track setup.
- [App Bundle Handbook](app-bundle.md) - `.aab` compilation.
- [Google Play Store Accounts](../../store-accounts/google-play-console.md) - Account administration.

---

# 6. Official Sources

- Google Play Developer API Setup: https://developers.google.com/android-publisher/getting_started
- Fastlane Supply Documentation: https://docs.fastlane.tools/actions/supply/

---

**Last verified:** August 14, 2026
