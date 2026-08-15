# Playbook Governance & Research Standards Subsystem

This directory contains production handbook guides, source verification protocols, documentation style standards, contribution workflows, semantic versioning rules, and maintenance policies for **Playbook Governance** in the Mobile Release Playbook repository.

Engineered in alignment with **2026 platform specifications**, it details the research standards, editorial guidelines, and verification rules governing all documentation across the playbook.

This guide is **not**:

- an authorization mechanism to write un-verified or speculative documentation
- a substitute for verifying facts against official Apple Developer and Google Play Console portals
- a guide to making ad-hoc structural changes to playbook files

---

# 1. Architecture of Playbook Governance

Playbook Governance maintains documentation integrity, factual accuracy, and technical consistency across all 15+ playbook subsystems.

```text
┌────────────────────────────────────────────────────────┐
│             PLAYBOOK GOVERNANCE ARCHITECTURE           │
│                                                        │
│  - [source-verification.md](source-verification.md)    │
│    (Verify policy facts against live 2026 developer portals)│
│                                                        │
│  - [documentation-style.md](documentation-style.md)    │
│    (Enforce CLAUDE.md structure, ASCII diagrams, format)│
│                                                        │
│  - [contribution-guide.md](contribution-guide.md)      │
│    (Define PR review requirements & quality gates)     │
│                                                        │
│  - [versioning.md](versioning.md) & [maintenance.md](maintenance.md)│
│    (SemVer 2.0.0 release tracking & quarterly audits)  │
└────────────────────────────────────────────────────────┘
```

---

# 2. Subsystem Directory Taxonomy

| Governance Handbook | Primary Governance Domain & Scope | Key Rules & Standards |
|---|---|---|
| **[README.md](README.md)** | Subsystem index, governance architecture, and editorial rules. | High-level governance architecture and rules. |
| **[source-verification.md](source-verification.md)** | Source verification protocols, research standards, policy citations. | Mandatory live web search verification against Apple/Google. |
| **[documentation-style.md](documentation-style.md)** | Editorial style guide, H1-H4 hierarchy, ASCII flowcharts, format. | Pure Markdown, ASCII `↓` `→` flowcharts, `Last verified`. |
| **[contribution-guide.md](contribution-guide.md)** | Contribution workflows, PR quality gates, peer review checklist. | Verification proof requirement, PR review checklist. |
| **[versioning.md](versioning.md)** | Playbook SemVer 2.0.0 versioning, CHANGELOG.md management. | Semantic versioning rules for documentation releases. |
| **[maintenance.md](maintenance.md)** | Quarterly audit schedules, deprecation policy, policy updates. | 90-day verification audit cycle, deprecation tracking. |

---

# 3. Universal Governance Rules

All documentation work in this repository MUST adhere to five mandatory governance rules:

```text
1. Empirical Source Verification Requirement
   NEVER write policy rules, character limits, API requirements, or SDK submission gates based on memory.
   Every platform specification MUST be verified against active 2026 Apple Developer or Google Play documentation.

2. Strict Adherence to CLAUDE.md Standards
   All markdown files MUST follow the structure mandated in CLAUDE.md: H1 Title, framing description,
   what this guide is NOT, horizontal rules (`---`), ASCII flow diagrams, and `Last verified: August 14, 2026`.

3. Pure Markdown Immutability
   The playbook is 100% pure GitHub Flavored Markdown. HTML tags, inline styles, custom scripts, or external
   framework rendering engines are strictly forbidden.

4. Mandatory CHANGELOG.md Documentation
   Every pull request or update expanding playbook content MUST record its additions and changes under the
   `[Unreleased]` section of `CHANGELOG.md`.

5. Unanimous Governance Review for Policy Changes
   Any change modifying repository governance, safety guardrails, or verification rules requires explicit
   review and approval from Playbook Maintainers.
```

---

# 4. Related Repository Documentation

- [Repository Rules & Governance](../.claude/CLAUDE.md) - Repository instructions.
- [Official Sources Registry](../sources/README.md) - Primary documentation links.
- [Repository Changelog](../CHANGELOG.md) - Repository version history.

---

# 5. Official Sources

- Apple Developer Documentation: https://developer.apple.com/documentation/
- Google Play Console Help: https://support.google.com/googleplay/android-developer/

---

**Last verified:** August 14, 2026
