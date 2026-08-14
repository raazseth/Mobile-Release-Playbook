# Developer Repository CHANGELOG Template

Use this template format for maintaining `CHANGELOG.md` in repository root adhering to **Keep a Changelog** standards and **Semantic Versioning 2.0.0**.

---

# Master CHANGELOG Template Format

```markdown
# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added

- `[feat]` Added dynamic currency formatting support using `Intl.NumberFormat` API.
- `[feat]` Integrated StoreKit 2 Signed JWS transaction verification.

### Changed

- `[refactor]` Target Android SDK version bumped to API Level 36 (Android 16).
- `[refactor]` Updated Google Play Billing Library to 8.0.0.

### Fixed

- `[fix]` Resolved crash in background push notification handler on iOS 18.
- `[fix]` Fixed layout overflow on Right-to-Left (RTL) viewports.

---

## [1.2.0] - 2026-08-14

### Added

- Added interactive workout charts on Progress screen.
- Added custom dark mode theme options in Settings.

### Changed

- Reduced app cold start launch time by 25%.
- Enhanced offline sync reliability.

### Fixed

- Fixed timer reset bug during phone calls.
- Resolved CSV export crash.

---

## [1.1.0] - 2026-07-01

### Added

- Initial release of In-App Purchases via StoreKit 2.
- Added user profile avatar upload support.
```

---

# Operational Verification Checklist

- [ ] **Semantic Versioning Followed**: Version numbers follow `MAJOR.MINOR.PATCH`.
- [ ] **Dates Formatted ISO 8601**: Release dates formatted as `YYYY-MM-DD`.
- [ ] **Conventional Commit Categories**: Changes grouped under `Added`, `Changed`, `Deprecated`, `Removed`, `Fixed`, `Security`.

---

# Official Sources

- Keep a Changelog Standard: https://keepachangelog.com/
- Semantic Versioning 2.0.0: https://semver.org/

---

**Last verified:** August 14, 2026
