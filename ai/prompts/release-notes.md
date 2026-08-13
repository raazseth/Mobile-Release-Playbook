# Release Notes Prompt

The Release Notes Prompt provides a copy-pasteable prompt template for generating user-facing app store release notes ("What's New" text for App Store Connect and Google Play Console) and developer-facing changelogs (`CHANGELOG.md`) from git commit histories, pull request titles, and release candidate scope documents.

Engineered following 2026 Context Engineering standards, it incorporates prompt caching layouts, single-mount variable blocks, explicit XML tag isolation, and a translation step (`<thinking>`) to convert technical commit logs into user-focused value propositions while respecting store character limits.

This guide is **not**:

- a marketing hype generator (release notes must reflect actual changes included in the build)
- a place to dump raw commit messages or internal refactoring noise
- a substitute for human review of store listing copy before submission

---

# 1. Purpose & Usage Scenarios

Use this prompt when generating:

- **App Store "What's New" Text**: Concise, engaging user-facing updates for iOS App Store release notes.
- **Google Play Store Release Notes**: Release notes tailored for Android Play Store tracks (Production, Beta, Internal Testing).
- **Internal / Developer Changelogs**: Structured `CHANGELOG.md` updates formatted according to [Keep a Changelog](https://keepachangelog.com/) standards.
- **OTA Update Descriptions**: Release descriptions for Over-The-Air JavaScript updates (Expo Updates, CodePush).

---

# 2. Production Prompt Contract

Copy and paste the prompt template below into your AI tool. Populate the single-mount variables at the bottom with your commit history or PR summaries.

``=`markdown
<system_instructions>
# Persona & Role
You are an expert Mobile Technical Writer specializing in user-facing App Store & Google Play release notes and developer changelogs.

# Objective
Analyze the attached git commit messages and pull request titles, extract meaningful user-facing features, bug fixes, and performance improvements, and format them into policy-compliant release notes.

# Writing & Formatting Rules
1. User Value Focus: Translate internal code changes into user-facing benefits (e.g., "Faster screen loading times" instead of "Refactored Redux saga async middleware").
2. Exclude Noise: Exclude internal CI, build script, or dependency update commits (`chore: upgrade webpack`) from store notes unless they directly impact performance or security.
3. Tone: Direct, practical, professional. Avoid buzzwords or AI clichés ("We are thrilled", "leverage", "unlock", "game-changing").
4. Character Limits: App Store / Google Play release notes should be concise (200–500 characters recommended; 4000 max).
</system_instructions>

<output_formatting_rules>
Provide your response in two parts:
1. Perform commit filtering and user value translation inside a `<thinking>` block.
2. Output the final structured Markdown report matching the exact template below.
</output_formatting_rules>

<single_mount_variables>
<release_metadata>
- App Name: <APP_NAME>
- Version: <VERSION: e.g., 2.1.0>
- Target Audience: <TARGET_AUDIENCE: End Users (Store Listing) | Beta Testers | Internal Developers>
- Target Platform: <PLATFORM: iOS App Store | Google Play Store | CHANGELOG.md>
</release_metadata>

<commit_history_and_prs>
<PASTE_GIT_COMMITS_OR_PR_TITLES_HERE>
Examples:
- feat(checkout): add Apple Pay checkout support (#142)
- fix(ui): resolve layout glitch on iOS 18 devices (#145)
- perf(feed): optimize image caching and initial load time (#148)
- chore(deps): upgrade react-native-reanimated to 3.10 (#150)
</commit_history_and_prs>
</single_mount_variables>
``=
````

---

# 3. Complete Execution Example

### Input Context Provided to AI:

```text
App: PayFast v2.1.0
Audience: End Users (Store Listing) & Developer Changelog
Commits:
- feat(checkout): integrate Apple Pay one-click checkout (#112)
- fix(ios18): resolve status bar overlapping header on iOS 18 (#115)
- perf(db): optimize SQLite query index for transaction history (#118)
- chore(ci): update GitHub Actions workflow runners (#120)
- fix(auth): resolve intermittent session logout on app resume (#122)
```

### Expected AI Output:

````markdown
<thinking>
1. Categorizing commits:
   - #112 (Apple Pay): User Feature -> Include in Store & Changelog Added.
   - #115 (iOS 18 layout): User Bug Fix -> Include in Store & Changelog Fixed.
   - #118 (SQLite query): User Performance -> Include in Store & Changelog Performance.
   - #120 (GH Actions): Internal CI -> Exclude from Store, Include in Changelog Internal.
   - #122 (Auth logout): User Bug Fix -> Include in Store & Changelog Fixed.
2. Character count for Store Notes: 412 characters (Well under 4000 limit, ideal for mobile screens).
</thinking>

# Generated Release Notes

### App Store / Play Store Release Notes (User-Facing)

```text
What's New in Version 2.1.0:

• Apple Pay Support: You can now check out instantly using Apple Pay for faster, secure payments.
• Improved Reliability: Resolved an issue that occasionally logged users out when resuming the app.
• iOS 18 Compatibility: Fixed a header layout glitch on devices running iOS 18.
• Faster Transaction History: Optimized transaction search and history loading speeds.
```

- **Character Count**: 412 characters (Limit: 4000)
- **Tone Verification**: Pass (Direct, clear, no marketing fluff)

---

### Internal Developer Changelog (`CHANGELOG.md`)

```markdown
## [2.1.0] - 2026-08-13

### Added
- Integrated Apple Pay one-click checkout support (`#112`).

### Fixed
- Resolved status bar layout overlap on iOS 18 devices (`#115`).
- Fixed intermittent session invalidation when resuming application from background (`#122`).

### Performance
- Optimized SQLite query indexing for transaction history screen (`#118`).

### Internal / Maintenance
- Updated GitHub Actions CI workflow runner environment (`#120`).
```

---

# Transformation Mapping

| Raw Commit / PR Title | User-Facing Release Note Translation | Included in Store Notes? |
|---|---|---|
| `feat(checkout): integrate Apple Pay (#112)` | Check out instantly using Apple Pay | YES |
| `fix(ios18): status bar overlap (#115)` | Fixed header layout glitch on iOS 18 | YES |
| `perf(db): SQLite query index (#118)` | Optimized transaction history loading speeds | YES |
| `fix(auth): session logout on resume (#122)` | Resolved issue causing unexpected logouts | YES |
| `chore(ci): update GH Actions (#120)` | N/A (Internal CI tooling) | NO (Internal Only) |
````

---

# 4. Operational Verification Checklist

- [ ] **Static Instructions First**: Cached system instructions precede single-mount variable blocks.
- [ ] **User Value Focused**: Technical commit logs are translated into clear user benefits; internal refactoring noise is excluded from store listings.
- [ ] **Reasoning Block Active**: Includes a `<thinking>` commit classification phase.
- [ ] **Character Limits Checked**: Text length is well within store limits (under 500 chars recommended for mobile readability).

---

# 5. Related Documentation

- [Metadata Agent](../agents/metadata-agent.md) - Store metadata agent.
- [Release Notes Workflow](../workflows/release-notes.md) - Release notes workflow.
- [Repository Changelog](../../CHANGELOG.md) - Playbook changelog guidelines.

---

# 6. Official Sources

- Apple App Store Connect Release Notes Guidance: https://developer.apple.com/help/app-store-connect/
- Google Play Store Listing Updates: https://support.google.com/googleplay/android-developer/answer/9859348
- Keep a Changelog Specification: https://keepachangelog.com/

---

**Last verified:** August 13, 2026
