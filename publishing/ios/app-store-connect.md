# App Store Connect Build Management & Versioning

This document covers build version mapping (`CFBundleShortVersionString` vs `CFBundleVersion`), export compliance declarations (`ITSAppUsesNonExemptEncryption`), and how to attach an uploaded build to a release version for **App Store Connect Build Management** in Expo and React Native applications.

This guide is **not**:

- an authorization mechanism to reuse duplicate build numbers (App Store Connect rejects duplicate build numbers)
- a substitute for testing builds on TestFlight before attaching them to store releases
- a guide to manual submission when automated submissions are configured

---

# 1. Version Mapping Taxonomy (`CFBundleShortVersionString` vs `CFBundleVersion`)

iOS versioning requires two distinct version keys in `Info.plist`:

```text
Marketing version (CFBundleShortVersionString) → the publicly visible version string (e.g. "1.2.0")
Build number (CFBundleVersion)                 → internal iteration counter (e.g. "1.2.0.4"), must
                                                   increase with every upload
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

# Related documentation

### Publishing (iOS)

- `publishing/ios/README.md`
- `publishing/ios/app-review.md`
- `publishing/ios/build-upload.md`
- `publishing/ios/metadata.md`
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

---

# Official sources

- Apple App Store Connect Build Management: https://developer.apple.com/help/app-store-connect/#/dev8b49e0c52

---

**Last verified:** August 14, 2026

