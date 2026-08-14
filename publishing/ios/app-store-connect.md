# App Store Connect Build Management & Versioning

This document details the build version mapping (`CFBundleShortVersionString` vs `CFBundleVersion`), export compliance declarations (`ITSAppUsesNonExemptEncryption`), build attachment procedures, and submission status tracking for **App Store Connect Build Management** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it specifies how to manage uploaded `.ipa` builds inside App Store Connect.

This guide is **not**:

- an authorization mechanism to reuse duplicate build numbers (App Store Connect rejects duplicate build numbers)
- a substitute for testing builds on TestFlight before attaching them to store releases
- a guide to manual submission when automated submissions are configured

---

# 1. Version Mapping Taxonomy (`CFBundleShortVersionString` vs `CFBundleVersion`)

iOS versioning requires two distinct version keys in `Info.plist`:

```text
┌────────────────────────────────────────────────────────┐
│             iOS VERSIONING KEY TAXONOMY                │
│                                                        │
│  - Marketing Version (`CFBundleShortVersionString`):   │
│    Publicly visible version string (e.g., "1.2.0").    │
│                                                        │
│  - Build Number (`CFBundleVersion`):                   │
│    Internal build iteration counter (e.g., "1.2.0.4"). │
│    MUST increase monotonically with every upload.      │
└────────────────────────────────────────────────────────┘
```

### Expo `app.json` Version Configuration

```json
{
  "expo": {
    "version": "1.2.0",
    "ios": {
      "buildNumber": "1.2.0.4"
    }
  }
}
```

---

# 2. Export Compliance Declaration (`ITSAppUsesNonExemptEncryption`)

When a new build finishes processing in App Store Connect, Apple requires an **Export Compliance Declaration** before the build can be distributed on TestFlight or attached to a release.

To automate this declaration and skip manual prompt dialogs in App Store Connect, include `ITSAppUsesNonExemptEncryption: false` in `Info.plist`:

```xml
<key>ITSAppUsesNonExemptEncryption</key>
<false/>
```

---

# 3. Attaching Builds to an App Store Release Version

To attach a processed build to a store release draft:

1. Log in to **App Store Connect** -> Select your app -> Select the target version draft (e.g., `1.2.0`).
2. Scroll to the **Build** section.
3. Click **+ Add Build** (or hover over an existing build and click **Remove** to replace).
4. Select the desired build number from the list of processed TestFlight builds.
5. Save changes.

---

# 4. Operational Verification Checklist

- [ ] **Marketing Version Matched**: `CFBundleShortVersionString` matches intended release tag.
- [ ] **Build Number Monotonic**: `CFBundleVersion` incremented higher than all previous uploads.
- [ ] **Export Compliance Automated**: `ITSAppUsesNonExemptEncryption` declared in `Info.plist`.
- [ ] **Build Attached to Draft**: Desired build number attached in App Store Connect version draft.
- [ ] **Build Status Ready**: Build status marked as *Ready to Submit* without missing asset warnings.

---

# 5. Related Documentation

- [Build Upload Handbook](build-upload.md) - `.ipa` uploading.
- [TestFlight Handbook](testflight.md) - Beta testing tracks.
- [Versioning Handbook](../../foundations/versioning.md) - Versioning rules.

---

# 6. Official Sources

- Apple App Store Connect Build Management: https://developer.apple.com/help/app-store-connect/#/dev8b49e0c52

---

**Last verified:** August 14, 2026
