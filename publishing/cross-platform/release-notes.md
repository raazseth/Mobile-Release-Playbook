# Cross-Platform Release Notes & Changelogs

This document covers Keep a Changelog formatting, generating release notes from git commits, and the character limits for both stores' "what's new" fields, for **Cross-Platform Release Notes** in Expo and React Native applications — how to write clear, user-facing release notes for App Store Connect and Google Play Console.

This guide is **not**:

- an authorization mechanism to submit generic "Bug fixes and performance improvements" notes repeatedly
- a substitute for documenting major breaking changes or user-facing feature additions
- a developer commit dump (git commit hashes and internal refactors must be filtered out for public store releases)

---

# 1. Release Notes Architecture & Parsing Pipeline

Public release notes inform end users about new features, UI improvements, and bug fixes included in the current release.

```text
Git commit log (Conventional Commits: feat:, fix:)
        ↓
Changelog parsing: filter out internal refactors/CI/test commits, group user-facing
changes into New Features / Improvements / Bug Fixes, format per Keep a Changelog
        │
        ├─→ App Store "What's New"    (max 4,000 characters)
        └─→ Google Play release notes (max 500 characters)
```

---

# 2. Store Changelog Character Limits & Guidelines

| Platform | Character Limit | Formatting Rules | Best Practice |
|---|---|---|---|
| **Apple App Store** | **4,000 characters max** | Plain text, line breaks, bullet points | Clear category headers (`- New:`, `- Fixed:`); highlight key features first. |
| **Google Play Store** | **500 characters max** | Plain text, line breaks | Concise bulleted summary fitting within 500 characters. |

---

# 3. Keep a Changelog Standard Template

Follow the Keep a Changelog standard format for user-facing release notes:

```text
# Version 1.2.0 Release Notes

### Added
- Interactive workout analytics charts on the Progress screen.
- Dark mode custom theme options in Settings.

### Improved
- Reduced app cold start launch time by 25%.
- Enhanced offline sync reliability when logging workouts without network access.

### Fixed
- Fixed an issue where workout duration timer reset unexpectedly during phone calls.
- Resolved crash on Android when exporting CSV workout logs.
```

---

# 4. Automated Release Notes Generation Script

Use Node.js scripts to generate formatted release notes from git commits:

```javascript
// scripts/generate-release-notes.js
const { execSync } = require('child_process');
const fs = require('fs');

// Fetch git commits since last release tag
const rawCommits = execSync('git log $(git describe --tags --abbrev=0)..HEAD --oneline').toString();
const lines = rawCommits.split('\n').filter(Boolean);

const features = [];
const fixes = [];

lines.forEach(line => {
  if (line.includes('feat:')) features.push(line.replace(/^[a-f0-9]+\s+feat:\s*/, '• '));
  if (line.includes('fix:')) fixes.push(line.replace(/^[a-f0-9]+\s+fix:\s*/, '• '));
});

const releaseNotes = `What's New in Version 1.2.0:

Features:
${features.join('\n') || '• General performance enhancements.'}

Fixes:
${fixes.join('\n') || '• Stability improvements.'}
`;

// Write to iOS and Android release notes plain text files
fs.writeFileSync('./fastlane/metadata/ios/en-US/release_notes.txt', releaseNotes);
// Truncate to 500 chars for Android release notes limit
fs.writeFileSync('./fastlane/metadata/android/en-US/changelogs/143.txt', releaseNotes.slice(0, 490));

console.log('✅ Generated cross-platform release notes!');
```

---

# 5. Operational Verification Checklist

- [ ] **No Generic Text Only**: Release notes detail specific features/fixes beyond generic "Bug fixes".
- [ ] **Android 500-Char Limit Checked**: Play Store changelog text is strictly ≤ 500 characters.
- [ ] **Internal Commits Filtered**: Refactoring, CI pipeline, and internal test commits excluded.
- [ ] **Clear Formatting**: Release notes use bullet points and clean category headings.
- [ ] **Fastlane Directories Updated**: Release notes written to `release_notes.txt` and `changelogs/143.txt`.

---

# Related documentation

### Publishing (cross-platform)

- `publishing/cross-platform/README.md`
- `publishing/cross-platform/assets.md`
- `publishing/cross-platform/metadata.md`
- `publishing/cross-platform/submission.md`

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

- Keep a Changelog Standard: https://keepachangelog.com/
- Apple Release Notes Guidelines: https://developer.apple.com/help/app-store-connect/#/dev8b49e0c52

---

**Last verified:** August 14, 2026

