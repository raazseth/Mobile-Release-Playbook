# User-Facing Release Notes & What's New Templates

Use these templates to format public user-facing release notes for **Apple App Store Connect** ("What's New") and **Google Play Console** ("Release Notes").

---

# 1. Apple App Store Release Notes Template (Max 4,000 Characters)

### Plain Text File (`fastlane/metadata/ios/en-US/release_notes.txt`)

```text
What's New in Version [1.2.0]:

NEW FEATURES:
• Interactive Workout Charts: Track your monthly progress with interactive visual charts on the Analytics tab.
• Custom Dark Mode Themes: Choose between Midnight, AMOLED Black, and Slate theme options in App Settings.

IMPROVEMENTS:
• Faster Startup: Optimized app cold launch time by 25%.
• Enhanced Offline Sync: Log workouts offline with automatic background sync when internet connection restores.

BUG FIXES:
• Fixed an issue where the workout timer reset unexpectedly when receiving phone calls.
• Resolved CSV export crash on iPad devices.

Thank you for using [App Name]! If you enjoy the app, please consider leaving a review on the App Store.
```

---

# 2. Google Play Store Release Notes Template (Max 500 Characters)

### Plain Text File (`fastlane/metadata/android/en-US/changelogs/143.txt`)

```text
What's New in v[1.2.0]:

• NEW: Interactive workout analytics charts on the Progress tab.
• NEW: Custom Dark Mode theme options in Settings.
• IMPROVED: 25% faster cold start performance & reliable offline sync.
• FIXED: Workout timer reset bug & CSV export crash.

Enjoying [App Name]? Leave us a review on Google Play!
```

---

# 3. Emergency Patch Release Notes Template

```text
Version [1.2.1] Maintenance Release:

• Fixed a critical issue preventing payment checkout on certain device configurations.
• Overall stability and performance improvements.
```

---

# 4. Operational Verification Checklist

- [ ] **iOS Limit Checked**: App Store release notes ≤ 4,000 characters.
- [ ] **Android Limit Checked**: Google Play release notes strictly ≤ 500 characters.
- [ ] **User-Facing Focus**: Technical refactors and CI commits filtered out.
- [ ] **Bullet Points Clean**: Bullet points use standard unicode bullet symbols (`•` or `-`).

---

# 5. Official Sources

- Keep a Changelog Standard: https://keepachangelog.com/

---

**Last verified:** August 14, 2026

---

# Related documentation

### Templates

- `templates/README.md`
- `templates/app-store-description.md`
- `templates/changelog.md`
- `templates/incident-report.md`
- `templates/play-store-description.md`
- `templates/privacy-questionnaire.md`
- `templates/rejection-response.md`
- `templates/release-checklist.md`
- `templates/release-plan.md`

### Store operations

- `store-operations/README.md`

### Checklists

- `checklists/README.md`

### Post-release

- `post-release/incident-response.md`

### Release strategy

- `release-strategy/release-trains.md`
