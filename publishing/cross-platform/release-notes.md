# Cross-Platform Release Notes

Release notes are what users actually read before deciding whether to update. This covers writing them well, keeping them within each store's character limit, and generating a first draft from your git history instead of starting from a blank page every time.

This guide is **not**:

- an authorization mechanism to ship "Bug fixes and performance improvements" release after release
- a substitute for documenting a genuinely breaking change or a real feature addition
- a place for a raw commit dump — internal refactors and commit hashes don't belong in front of users

---

## 1. From commits to store text

```text
Git commit log (Conventional Commits: feat:, fix:)
        ↓
Changelog parsing: filter out internal refactors/CI/test commits, group user-facing
changes into New Features / Improvements / Bug Fixes, format per Keep a Changelog
        │
        ├─→ App Store "What's New"    (max 4,000 characters)
        └─→ Google Play release notes (max 500 characters)
```

## 2. Character limits by store

| Platform | Limit | Formatting | Practical advice |
|---|---|---|---|
| Apple App Store | 4,000 characters | Plain text, line breaks, bullets | Lead with the feature users will actually notice, not the fix that only mattered internally |
| Google Play | 500 characters | Plain text, line breaks | You have room for maybe 3–4 short bullets — pick the ones that matter most |

## 3. A format worth following

Keep a Changelog's structure works well for release notes too — it groups changes so a reader can scan them in seconds:

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

## 4. Generating a first draft from git

This won't replace someone editing for tone, but it saves you from starting from nothing:

```javascript
// scripts/generate-release-notes.js
const { execSync } = require('child_process');
const fs = require('fs');

// Fetch git commits since the last release tag
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
// Truncate to 500 chars for Android's release notes limit
fs.writeFileSync('./fastlane/metadata/android/en-US/changelogs/143.txt', releaseNotes.slice(0, 490));

console.log('Generated cross-platform release notes.');
```

Treat this as a starting draft, not a final copy — a script can't tell which fix actually mattered to users and which was invisible to everyone but the team.

## 5. Before you publish

- [ ] The notes describe something specific, not just "bug fixes."
- [ ] The Google Play text is strictly under 500 characters — it gets truncated silently otherwise.
- [ ] Refactoring, CI, and internal test commits are filtered out.
- [ ] Bullets and headers make the notes scannable, not a wall of text.
- [ ] `release_notes.txt` and the versioned `changelogs/<code>.txt` file are both updated.

---

## Official sources

- Keep a Changelog standard: https://keepachangelog.com/
- Apple release notes guidelines: https://developer.apple.com/help/app-store-connect/#/dev8b49e0c52

**Last verified:** August 14, 2026

---

## Related documentation

### Publishing (cross-platform)

- `publishing/cross-platform/README.md`
- `publishing/cross-platform/assets.md`
- `publishing/cross-platform/metadata.md`
- `publishing/cross-platform/submission.md`

### Publishing (iOS)

- `publishing/ios/README.md`

### Publishing (Android)

- `publishing/android/README.md`

### Templates

- `templates/release-notes.md`

### Checklists

- `checklists/cross-platform.md`

### Store operations

- `store-operations/README.md`
