# Playbook Contribution Workflow & Peer Review Handbook

This handbook details contribution workflows, Pull Request quality gates, content expansion rules, verification proof requirements, and peer review checklists for **Contributions** to the Mobile Release Playbook repository.

Engineered in alignment with **2026 platform specifications**, it details how contributors propose updates or additions to the playbook.

This guide is **not**:

- an authorization mechanism to merge un-reviewed pull requests
- a substitute for updating `CHANGELOG.md` when proposing documentation edits
- a guide to creating stub files without full handbook depth

---

# 1. Contribution Workflow Pipeline

All contributions MUST pass through a structured 4-stage contribution pipeline:

```text
┌────────────────────────────────────────────────────────┐
│             CONTRIBUTION WORKFLOW PIPELINE             │
│                                                        │
│  Stage 1: Issue & Research Verification                │
│           Verify 2026 specs via `search_web`           │
│                                                        │
│  Stage 2: Content Drafting                             │
│           Expand file to full handbook depth (> 10KB)  │
│                                                        │
│  Stage 3: Local Verification & CHANGELOG.md Update     │
│           Record additions in `CHANGELOG.md`           │
│                                                        │
│  Stage 4: Peer Review & Governance Approval            │
│           Unanimous review pass prior to merge         │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ Pull Request Merged into Main Repository Branch ]
```

---

# 2. Pull Request Peer Review Quality Gate

Every Pull Request submitted to this repository MUST satisfy four mandatory quality gates:

1. **Handbook-Grade Depth**: New or updated domain files must provide exhaustive technical depth (~10–25KB per file) with complete code snippets and schemas. No 4-line stub files permitted.
2. **CLAUDE.md Style Compliance**: Markdown formatting MUST comply 100% with [CLAUDE.md](../.claude/CLAUDE.md) structural standards.
3. **Verified Sources**: All policy assertions MUST cite primary official URLs under `# Official Sources`.
4. **Changelog Entry**: `CHANGELOG.md` MUST be updated under `[Unreleased] -> Added` or `Changed`.

---

# 3. Operational Verification Checklist

- [ ] **Full Depth Achieved**: File expanded to full handbook depth with code/schema samples.
- [ ] **Style Audit Passed**: Formatting matches CLAUDE.md style guidelines 100%.
- [ ] **Official Sources Cited**: Primary Apple or Google portals cited in document.
- [ ] **CHANGELOG Updated**: Additions recorded under `[Unreleased]` in `CHANGELOG.md`.

---

# 4. Official Sources

- Repository Instructions: [../../.claude/CLAUDE.md](../.claude/CLAUDE.md)
- Repository Changelog: [../../CHANGELOG.md](../CHANGELOG.md)

---

**Last verified:** August 14, 2026

---

# Related documentation

### Governance

- `governance/README.md`
- `governance/documentation-style.md`
- `governance/maintenance.md`
- `governance/source-verification.md`
- `governance/versioning.md`

### Sources

- `sources/README.md`
