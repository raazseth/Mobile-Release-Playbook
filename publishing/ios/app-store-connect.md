# App Store Connect: Build Management and Versioning

This covers how App Store Connect maps version numbers, how export compliance declarations work, and how to attach an uploaded build to a release version.

This guide is **not**:

- an authorization mechanism to reuse a duplicate build number — App Store Connect rejects duplicates
- a substitute for testing a build on TestFlight before attaching it to a store release
- a guide to manual submission when you already have automated submission set up

---

## 1. Two version numbers, not one

iOS versioning uses two separate keys in `Info.plist`, and it's easy to mix them up:

```text
Marketing version (CFBundleShortVersionString) → the version users see (e.g. "1.2.0")
Build number (CFBundleVersion)                 → an internal counter (e.g. "1.2.0.4"), must
                                                   go up with every single upload
```

In Expo's `app.json`:

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

## 2. Export compliance

When a new build finishes processing in App Store Connect, Apple asks for an **Export Compliance Declaration** before the build can go to TestFlight or be attached to a release.

To skip the manual prompt every time, declare it up front in `Info.plist`:

```xml
<key>ITSAppUsesNonExemptEncryption</key>
<false/>
```

Set this to `false` if your app only uses standard HTTPS/TLS. If you're using custom encryption beyond that, don't set it to `false` — check Apple's export compliance documentation for what applies to your app.

## 3. Attaching a build to a release

Once a build has finished processing:

1. Log in to **App Store Connect**, select your app, and open the version draft you're releasing (e.g., `1.2.0`).
2. Scroll to the **Build** section.
3. Click **+ Add Build** (or, to replace an existing one, hover over it and click **Remove** first).
4. Pick the build you want from the list of processed TestFlight builds.
5. Save.

## 4. Before you submit

- [ ] `CFBundleShortVersionString` matches the version you intend to release.
- [ ] `CFBundleVersion` is higher than every previous upload.
- [ ] `ITSAppUsesNonExemptEncryption` is declared in `Info.plist`.
- [ ] The right build is attached to the version draft.
- [ ] The build shows *Ready to Submit* with no missing-asset warnings.

---

## Official sources

- App Store Connect build management: https://developer.apple.com/help/app-store-connect/#/dev8b49e0c52

**Last verified:** August 14, 2026

---

## Related documentation

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
