# Production Templates & Checklists Subsystem

This directory contains production-ready markdown templates, release checklists, store rejection response forms, metadata schemas, incident post-mortem forms, and release planning frameworks for **Mobile Release Engineering** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it provides copy-paste ready, standardized operational artifacts for release engineering teams, QA leads, and mobile developers.

This directory is **not**:

- a set of abstract, non-editable prose documents
- an authorization mechanism to skip release validation gates
- a substitute for performing actual binary QA testing

---

# 1. Architecture of Release Operational Artifacts

Operational templates standardize release planning, risk assessment, QA execution, store submission, rejection resolution, and production incident post-mortems across mobile teams.

```text
┌────────────────────────────────────────────────────────┐
│             RELEASE PLANNING PHASE                     │
│  - [release-plan.md](release-plan.md)                  │
│  - [release-checklist.md](release-checklist.md)        │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│             STORE LISTING & CONTENT PREPARATION        │
│  - [app-store-description.md](app-store-description.md) │
│  - [play-store-description.md](play-store-description.md)│
│  - [release-notes.md](release-notes.md)                │
│  - [privacy-questionnaire.md](privacy-questionnaire.md)│
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│             STORE TRIAGE & INCIDENT RESPONSE           │
│  - [rejection-response.md](rejection-response.md)      │
│  - [incident-report.md](incident-report.md)            │
│  - [changelog.md](changelog.md)                        │
└────────────────────────────────────────────────────────┘
```

---

# 2. Subsystem Directory Taxonomy

| Template File | Core Operational Purpose | Key Deliverables & Sections |
|---|---|---|
| **[README.md](README.md)** | Subsystem index, template architecture, and operational rules. | High-level template workflow and governance rules. |
| **[release-plan.md](release-plan.md)** | Master Release Plan for scheduling and risk management. | Release milestones, risk matrix, owner assignments, rollback triggers. |
| **[release-checklist.md](release-checklist.md)** | Pre-release and release execution checklist. | Code freeze audit, TestFlight/Internal QA, store review, phased rollout. |
| **[rejection-response.md](rejection-response.md)** | Resolution Center & Policy Rejection response templates. | Professional response templates for Apple Guidelines 2.1, 2.3, 3.1.1, 5.1.1. |
| **[release-notes.md](release-notes.md)** | User-facing release notes templates. | Keep a Changelog format for iOS (4000c) and Android (500c). |
| **[changelog.md](changelog.md)** | Developer repository CHANGELOG template. | Semantic Versioning 2.0.0, conventional commit categories. |
| **[app-store-description.md](app-store-description.md)** | Apple App Store metadata template. | App Name (30c), Subtitle (30c), Keywords (100c), Description (4000c). |
| **[play-store-description.md](play-store-description.md)** | Google Play Store listing metadata template. | App Title (30c), Short Desc (80c), Long Desc (4000c, 2-3% keyword density). |
| **[privacy-questionnaire.md](privacy-questionnaire.md)** | Store privacy questionnaire audit form. | Apple App Privacy Labels & Google Play Data Safety mapping. |
| **[incident-report.md](incident-report.md)** | Release incident and outage post-mortem template. | Incident timeline, crash rate triage, root cause analysis, action items. |

---

# 3. Universal Operational Template Rules

All operational templates in this playbook must adhere to five mandatory rules:

```text
1. Standard Placeholder Notation
   All customizable template fields MUST use explicit bracket placeholders (e.g., `[App Name]`,
   `[Version 1.2.0]`, `[YYYY-MM-DD]`) for clear identification and automated text replacement.

2. Human Approval Sign-Off Boundaries
   Templates involving release execution (Release Plan, Release Checklist, Incident Post-Mortem)
   MUST include explicit `Human Approval Sign-Off` sections requiring lead engineer signatures.

3. Platform Character Limit Enforcement
   Metadata and store description templates MUST include character count indicators and strict
   validation boundaries (Title ≤ 30c, Subtitle ≤ 30c, Keywords ≤ 100c, Short Desc ≤ 80c).

4. Production Risk Rating Taxonomy
   Risk matrices MUST classify release risks into standardized severity tiers (P0 - Critical Blocker,
   P1 - High Priority, P2 - Medium Priority, P3 - Low / Cosmetic).

5. Clean Version Control Syntax
   All templates MUST be formatted in clean GitHub Flavored Markdown (GFM) suitable for direct
   commit into repository root or `.github/` release tracking folders.
```

---

Every template in this directory now includes an **AI-Assisted Draft Generation** section — a prompt (or a pointer to the matching prompt in `ai/prompts/` where one already exists) that turns raw release facts into a first draft of that template. None of them replace the human sign-off sections already built into these templates; they just remove the blank-page problem.

---

# 4. Related Repository Documentation

- [Release Preparation Workflow](../ai/workflows/release-preparation.md) - Automated release prep.
- [Release Engineering Subsystem](../release-engineering/README.md) - Release pipelines.
- [AI Prompts Index](../ai/prompts/README.md) - Standalone prompts several templates draft from.
- [CHANGELOG.md](../CHANGELOG.md) - Repository changelog.

---

# 5. Official Sources

- Keep a Changelog Standard: https://keepachangelog.com/
- Semantic Versioning 2.0.0: https://semver.org/

---

**Last verified:** September 5, 2026

