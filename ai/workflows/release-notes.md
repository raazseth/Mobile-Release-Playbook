# End-to-End Release Notes & Changelog Workflow

This document details the AI-assisted Release Notes Workflow—the procedure for generating, formatting, auditing, and publishing user-facing store release notes ("What's New" text for App Store Connect and Google Play Console) and developer-facing changelogs (`CHANGELOG.md`).

Engineered in alignment with **2026 Context Engineering standards** and the **OWASP GenAI Security Project (2026)**—specifically addressing **LLM05: Improper Output Handling**—it specifies how orchestrators process raw git commit logs, pull request titles, and release scope documents into clear, user-focused release notes while adhering to store character limits and repository changelog standards.

This guide is **not**:

- a marketing hype copywriting engine
- a dump for raw commit messages or internal refactoring noise
- a substitute for human review of store listing copy before submission

---

# 1. Workflow Architecture & Changelog Lifecycle

The Release Notes Workflow ingests git history and release candidate scope, filters internal technical noise, translates technical changes into user benefits, and outputs dual release documentation.

```text
[ Git Commits & Merged Pull Requests ]
               │
               ▼
[ Single-Mount Commit Data Injection ]
               │
               ▼
┌────────────────────────────────────────────────────────┐
│             RELEASE NOTES REASONING ENGINE             │
│  - Categorize Commits inside `<thinking>` Block       │
│  - Filter Internal CI & Refactoring Noise              │
│  - Translate Code Diffs into User Value Propositions   │
│  - Audit Character Counts (Store Text <=500 chars)     │
└──────────────────────────┬─────────────────────────────┘
               │
   ┌───────────┴───────────┐
   ▼                       ▼
[ Store "What's New" ]  [ `CHANGELOG.md` Entry ]
(User-facing bullets)   (Keep a Changelog standard)
   │                       │
   └───────────┬───────────┘
               │
               ▼
┌────────────────────────────────────────────────────────┐
│               HUMAN REVIEW & PUBLICATION               │
│  Maintainer Approves Copy → Commit & Upload via Store │
└────────────────────────────────────────────────────────┘
```

---

# 2. Step-by-Step Workflow Execution

## Step 1: Ingesting Commits & Context Injection

The workflow parses git commit logs and pull request titles since the last release tag and injects context into single-mount XML tags:

```markdown
<system_instructions>
# Persona & Mandate
You are an expert Mobile Technical Writer formatting release notes.

# Formatting Rules
1. User Value Focus: Translate code diffs into user-facing benefits.
2. Filter Noise: Exclude internal CI, build script, or unit test commits from store notes.
3. Perform commit categorization inside a <thinking> block before emitting output.
4. Output both Store "What's New" text and Keep-a-Changelog CHANGELOG.md markdown entry.
</system_instructions>

<single_mount_variables>
<commit_history_input>
<PASTE_GIT_COMMITS_OR_PR_TITLES_HERE>
</commit_history_input>
</single_mount_variables>
```

## Step 2: Noise Filtering & Classification

Commits are categorized into user-facing vs internal-only changes:

- **User-Facing**: New features, UI/UX bug fixes, performance speedups, new platform support.
- **Internal Only (Excluded from Store Notes)**: CI workflow edits, unit test additions, build script updates, refactoring.

## Step 3: Dual Output Generation

1. **Store "What's New" Release Notes**: Concise, user-focused bullet points optimized for mobile screen readability (200–500 characters recommended).
2. **Developer `CHANGELOG.md` Entry**: Structured markdown entry conforming to [Keep a Changelog](https://keepachangelog.com/) standards (`Added`, `Changed`, `Fixed`, `Security`).

## Step 4: Character Count & Policy Audit

The output is audited against store limits:

- App Store / Google Play character limit: 4000 characters.
- Verification of no price terms ("Free"), rank claims ("#1 App"), or competitor brand references.

---

# 3. Machine-Readable Release Notes Schema

```yaml
release_notes_workflow_report:
  workflow_run_id: "rn-20260813-v1.4.0"
  status: "COMPLETED"

  source_commits_analyzed: 28
  user_facing_items_extracted: 4
  internal_items_filtered: 24

  store_release_notes:
    character_count: 385
    policy_audit_status: "PASS"
    text: |
      What's New in Version 1.4.0:
      • Instant Checkout: Pay faster with new Apple Pay integration.
      • iOS 18 Support: Fixed header layout glitches on iOS 18.
      • Performance Boost: Screen loading times are now 30% faster.
      • Stability: Resolved an issue causing unexpected session logouts.

  changelog_entry_generated: true
  human_approval_required: true
```

---

# 4. Operational Verification Checklist

- [ ] **Context Engineering Structured**: System instructions use static caching layouts and XML tag shielding.
- [ ] **User Value Focused**: Technical commit logs are translated into clear user-facing benefits.
- [ ] **No Implementation Noise**: Refactoring, CI build edits, and unit test commits are excluded from store release notes.
- [ ] **Store Limits Verified**: Character count is well within the 4000 character store limit (200–500 chars recommended).
- [ ] **Changelog Formatted**: `CHANGELOG.md` entry adheres to Keep a Changelog markdown standards.
- [ ] **Human Review**: Release notes copy reviewed and approved by a human maintainer before submission.

---

# 5. Related Documentation

- [Release Notes Prompt](../prompts/release-notes.md) - Copy-paste release notes prompt template.
- [Metadata Agent](../agents/metadata-agent.md) - Store metadata agent.
- [Repository Changelog](../../CHANGELOG.md) - Mobile Release Playbook changelog guidelines.

---

# 6. Official Sources

- OWASP GenAI Security Project (2026 Standard): https://genai.owasp.org/
- Apple App Store Connect Release Notes Guidelines: https://developer.apple.com/help/app-store-connect/
- Google Play Store Listing Updates: https://support.google.com/googleplay/android-developer/answer/9859348
- Keep a Changelog Specification: https://keepachangelog.com/

---

**Last verified:** August 13, 2026

