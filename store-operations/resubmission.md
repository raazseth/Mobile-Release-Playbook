# Binary Resubmission & Patch Workflows Handbook

This handbook details the build versioning rules, binary patch uploads, metadata update procedures, changelog documentation, and resubmission execution flows for **Store Resubmissions** in App Store Connect and Google Play Console.

Engineered in alignment with **2026 platform specifications**, it provides exhaustive technical and operational guidance for cleanly submitting replacement builds after fixing review defects or release blockers.

This guide is **not**:

- an authorization mechanism to reuse existing build numbers (store APIs strictly reject duplicate build numbers)
- a substitute for verifying that code patches resolve the original failure
- a guide to bypassing versioning conventions

---

# 1. Resubmission Architecture & Versioning Rules

When resubmitting an app following a rejection or critical bug fix, store platforms enforce strict build versioning and binary replacement rules.

```text
[ Defect Fixed in Local Working Tree ]
                   │
                   ▼
┌────────────────────────────────────────────────────────┐
│             INCREMENT BUILD NUMBER MANDATE             │
│  - Marketing Version (`version`): Keep same (e.g., 1.2.0)│
│  - Build Number (`buildNumber`): MUST increment        │
│    (e.g., 1.2.0.1  ──→  1.2.0.2  or  142  ──→  143)    │
└──────────────────────────┬─────────────────────────────┘
                   │
                   ▼
┌────────────────────────────────────────────────────────┐
│            COMPILE & UPLOAD REPLACEMENT BINARY         │
│  - iOS: Upload new IPA to TestFlight via EAS/Fastlane  │
│  - Android: Upload new AAB to Play Console track       │
└──────────────────────────┬─────────────────────────────┘
                   │
                   ▼
[ Select New Build in App Store Connect / Play Console → Submit for Review ]
```

---

# 2. Resubmission Workflow Step-by-Step

### Step 1: Increment Build Number
Ensure `app.json` or native build files increment `buildNumber` (iOS) and `versionCode` (Android):

```json
{
  "expo": {
    "version": "1.2.0",
    "ios": {
      "buildNumber": "1.2.0.2"
    },
    "android": {
      "versionCode": 143
    }
  }
}
```

### Step 2: Compile & Upload Replacement Build
Run build tools to generate and upload the replacement binary:

```bash
# Compile and upload replacement iOS build to App Store Connect
eas build --platform ios --profile production --auto-submit
```

### Step 3: Select Replacement Build in Store Console
In App Store Connect under the rejected version:
1. Hover over the rejected build in the **Build** section and click **Remove**.
2. Click **+ Add Build** and select the newly uploaded build number (`1.2.0.2`).
3. Update Reviewer Notes explaining the fix.
4. Click **Submit for Review**.

---

# 3. Operational Verification Checklist

- [ ] **Build Number Incremented**: `buildNumber` / `versionCode` incremented cleanly.
- [ ] **Defect Verified Fixed**: Fixed behavior verified locally and on TestFlight / internal testing tracks.
- [ ] **Old Build Removed**: Rejected build removed from store submission draft.
- [ ] **New Build Attached**: Newly uploaded build selected in store submission section.
- [ ] **Changelog Updated**: Reviewer notes updated with specific details on the applied fix.

---

# 4. Related Documentation

- [Rejection Handling Handbook](rejection-handling.md) - Rejection triage.
- [App Review Handbook](app-review.md) - Review submission.
- [Versioning Handbook](../foundations/versioning.md) - Versioning rules.

---

# 5. Official Sources

- Apple Submit App for Review Help: https://developer.apple.com/help/app-store-connect/#/dev8b49e0c52
- Google Play Manage App Releases: https://support.google.com/googleplay/android-developer/answer/9859751

---

**Last verified:** August 14, 2026

