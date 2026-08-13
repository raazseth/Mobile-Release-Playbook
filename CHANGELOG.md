# Changelog

All notable changes to the Mobile Release Playbook are documented here.

The changelog tracks meaningful changes to the playbook itself, including:

- new release workflows
- new framework support
- new AI workflows and agents
- new automation
- important documentation changes
- corrections to inaccurate guidance
- changes to repository structure
- changes that affect how contributors should use or extend the project

This project follows a simple, human-readable changelog format inspired by [Keep a Changelog](https://keepachangelog.com/).

## Categories

Changes should use the following categories where applicable:

- **Added** — new guides, workflows, tools, framework support, scripts, or capabilities
- **Changed** — changes to existing guidance, workflows, architecture, or behavior
- **Deprecated** — guidance or tooling that should no longer be used and is planned for removal
- **Removed** — previously supported guidance, tooling, or framework support that has been removed
- **Fixed** — corrections to inaccurate, broken, outdated, or misleading content
- **Security** — security-related changes, fixes, or important credential-handling guidance

---

## [Unreleased]

Changes currently being developed and not yet included in a published release.

### Added

- Initial Mobile Release Playbook structure.
- Core release lifecycle documentation.
- Store account guidance for Apple Developer, App Store Connect, and Google Play Console.
- iOS and Android signing guidance.
- Release-focused testing guidance.
- Pre-release readiness checks.
- App Store and Google Play publishing workflows.
- Store operations and rejection-handling guidance.
- Monetization and privacy/compliance guidance.
- Common mobile integration guidance.
- Release engineering guidance for EAS, Fastlane, GitHub Actions, and store APIs.
- Release strategy and post-release operational guidance.
- AI-assisted release workflows.
- AI agent, prompt, orchestration, evaluation, and security guidance.
- First-class Expo documentation.
- First-class React Native documentation.
- Community framework extension structure.
- Expanded `ai/README.md` into a comprehensive architecture index and navigation guide for the entire AI-assisted mobile release engineering subsystem.
- Expanded `ai/orchestration/` (`agent-workflows.md`, `human-approval.md`, `bounded-autonomy.md`, `subagents.md`, `tool-permissions.md`, `mcp.md`) into comprehensive handbook guides covering multi-agent topologies, human approval gates, autonomy constraints, subagent isolation, tool permission matrices, and Model Context Protocol integrations.
- Expanded `ai/prompts/` (`debugging.md`, `metadata.md`, `qa.md`, `rejection-analysis.md`, `release-audit.md`, `release-notes.md`) into production-ready prompt templates, input/output schemas, and execution examples.
- Expanded `ai/security/` (`agent-permissions.md`, `destructive-actions.md`, `prompt-injection.md`, `secret-protection.md`) into security handbook guides covering container sandboxing, non-reversible action gating, prompt injection shielding, and secret masking architecture.
- Expanded `ai/tools/` (`chatgpt.md`, `claude.md`, `claude-code.md`, `codex.md`, `github-copilot.md`, `github-agents.md`) into comprehensive tool guides detailing privacy baselines, prompt setups, execution limits, and CLI workflows.
- Expanded `ai/workflows/` (`release-preparation.md`, `release-audit.md`, `ai-qa.md`, `debugging.md`, `metadata-generation.md`, `rejection-analysis.md`, `release-notes.md`) into end-to-end multi-agent workflow specifications with step-by-step procedures and machine-readable schemas.
- Expanded `integrations/` (`README.md` and 13 domain guides: `analytics`, `app-links`, `attribution`, `background-processing`, `camera`, `crash-reporting`, `deep-links`, `location`, `maps`, `oauth`, `payments`, `push-notifications`, `universal-links`) into comprehensive integration handbooks detailing native permissions, privacy manifests (`PrivacyInfo.xcprivacy`), store review compliance (Guideline 3.1.1, Guideline 4.0, Guideline 5.1.1), Android 14+ Selected Photos, FCM HTTP v1, StoreKit 2, and AASA domain verification.
- Troubleshooting guides for common release failures.
- Reusable release checklists and templates.
- Source verification and documentation governance structure.

### Changed

- Validated and updated all documentation across `ai/orchestration/`, `ai/prompts/`, `ai/security/`, `ai/tools/`, and `ai/workflows/` against official 2026 platform requirements, including Google Play Android 16 (API Level 36) target enforcement, Apple App Store Guideline 5.1.1 Privacy Manifest Required Reason APIs, and Model Context Protocol (MCP) stateless request/response header-routing specification.
- Upgraded `ai/README.md` to 2026 Context Engineering standards and the OWASP GenAI Security Project (2026 Standard), reflecting prompt caching layouts, single-mount variable blocks, XML tag shielding, and the complete 9-module subsystem taxonomy.
- Upgraded all prompt templates in `ai/prompts/` (`debugging.md`, `metadata.md`, `qa.md`, `rejection-analysis.md`, `release-audit.md`, `release-notes.md`) to 2026 Context Engineering standards, incorporating static prompt caching architecture, single-mount variable blocks, explicit XML tag isolation, and mandatory native reasoning (`<thinking>`) steps.
- Upgraded all security handbooks in `ai/security/` (`agent-permissions.md`, `destructive-actions.md`, `prompt-injection.md`, `secret-protection.md`) to align with the OWASP GenAI Security Project (2026 Standard), establishing explicit mitigations for LLM01 Prompt Injection, LLM02 Sensitive Information Disclosure, LLM05 Improper Output Handling, and LLM06 Excessive Agency.
- Upgraded all tool integration guides in `ai/tools/` (`chatgpt.md`, `claude.md`, `claude-code.md`, `codex.md`, `github-copilot.md`, `github-agents.md`) to 2026 Context Engineering standards, incorporating system prompt caching layouts, XML tag prompt shielding, and OWASP GenAI security guardrails.
- Upgraded all workflow specifications in `ai/workflows/` (`release-preparation.md`, `release-audit.md`, `ai-qa.md`, `debugging.md`, `metadata-generation.md`, `rejection-analysis.md`, `release-notes.md`) to 2026 Context Engineering standards, incorporating static system prompt caching, single-mount XML tags, step-by-step reasoning steps, and machine-readable YAML reports.

### Deprecated

- None yet.

### Removed

- None yet.

### Fixed

- None yet.

### Security

- Established guidance for protecting signing credentials, API keys, CI secrets, and AI agent access.

---

## Versioning

The project uses release versions to communicate meaningful changes to the playbook.

A version should be created when a group of changes is coherent enough to be useful as a reference point.

Documentation-only corrections do not need to be treated as major releases.

### Version guidelines

**Major**

Use when the project introduces a significant structural or scope change that may affect how contributors or users navigate the playbook.

Examples:

- major repository restructuring
- major change in project scope
- removal of a previously supported framework
- breaking changes to contribution conventions

**Minor**

Use when meaningful new capabilities or coverage are added without breaking the existing structure.

Examples:

- new framework support
- new release workflow
- new AI agent workflow
- new automation
- new platform-specific guidance

**Patch**

Use for small corrections and maintenance changes.

Examples:

- correcting commands
- fixing broken links
- correcting inaccurate documentation
- updating examples
- clarifying existing instructions

---

## What belongs in the changelog

Record changes that materially affect how developers use the playbook.

Good changelog entries:

```text
Added Expo EAS production submission workflow.

Added troubleshooting guide for Android release signing failures.

Fixed incorrect App Store Connect credential setup instructions.

Added Claude Code release-audit workflow.

Added community framework contribution guide.
```

Avoid entries such as:

```text
Updated wording.

Changed a heading.

Fixed a typo.
```

unless the change has meaningful impact on users or contributors.

---

## Keeping the changelog useful

The changelog should answer three questions quickly:

1. **What changed?**
2. **Why does it matter?**
3. **Who is affected?**

Prefer concise entries over implementation detail.

For significant changes, link to the relevant documentation, pull request, issue, or release where useful.

---

## Maintenance

When making a meaningful change:

1. Update the relevant documentation.
2. Add the change to `Unreleased`.
3. Use the appropriate category.
4. Keep the entry concise and user-focused.
5. Remove the entry from `Unreleased` when it becomes part of a release.
6. Create the new version section with the release date.

Do not rewrite historical changelog entries simply because the current implementation has changed.

Historical entries should describe what was true at the time of that release.

---

## Release format

Published releases should follow this structure:

```markdown
## [0.1.0] - YYYY-MM-DD

### Added

- Added ...

### Changed

- Changed ...

### Fixed

- Fixed ...

### Security

- Improved ...
```

Only include categories that contain actual changes.

---

## Release philosophy

The changelog is not a commit log.

It exists to communicate meaningful changes to developers and contributors.

Prefer:

> Added an Expo EAS production submission workflow covering build, credentials, submission, and verification.

over:

> Added 14 files under `frameworks/expo/`.

The first explains the value.

The second describes implementation noise.

---

## Current project direction

The Mobile Release Playbook currently focuses on **Expo and React Native** as first-class implementations.

The architecture remains open to community contributions for additional frameworks and mobile stacks.

Future changelog entries should make framework additions, AI workflow improvements, automation changes, and important release-process changes easy to discover.
