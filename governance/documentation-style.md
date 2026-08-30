# Documentation Style Guide & Structural Standards Handbook

This handbook details structural markdown rules, H1-H4 heading hierarchy conventions, ASCII flowchart formatting standards, standard vocabularies, and `Last verified` timestamp rules for **Documentation Style** in the Mobile Release Playbook.

Engineered in alignment with **2026 platform specifications**, it specifies how to write uniform, handbook-grade documentation.

This guide is **not**:

- an authorization mechanism to use raw HTML elements in playbook markdown files
- a substitute for following [CLAUDE.md](../.claude/CLAUDE.md) structural standards
- a guide to omitting framing descriptions or "This guide is not" sections

---

# 1. Structural Section Architecture

Every domain handbook in this repository MUST follow a standardized 5-part section structure:

```text
┌────────────────────────────────────────────────────────┐
│             STANDARDIZED FILE ARCHITECTURE             │
│                                                        │
│  Part 1: H1 Title (`# Title`)                          │
│  Part 2: Framing Description Paragraph                 │
│  Part 3: Negative Scope Clarification ("This guide is not")│
│  Part 4: Rule Separator (`---`)                        │
│  Part 5: Main Content (Numbered Sections + ASCII Flow) │
│  Part 6: Operational Verification Checklist             │
│  Part 7: Official Sources Links                        │
│  Part 8: Footer (`**Last verified:** August 14, 2026`) │
└────────────────────────────────────────────────────────┘
```

---

# 2. Key Editorial Rules

1. **Pure GitHub Flavored Markdown**: Use pure Markdown syntax. No HTML tags (`<br>`, `<div>`).
2. **ASCII Flowcharts**: Use ASCII art boxes with `│`, `─`, `┌`, `┐`, `└`, `┘`, `▲`, `▼`, `◄`, `►` characters inside ` ```text ` blocks to illustrate workflows.
3. **Standard Vocabularies**:
   - Statuses: `READY`, `CONDITIONAL`, `NOT READY`, `IN PROGRESS`, `DEPRECATED`.
   - Severities: `P0 - Blocker`, `P1 - High`, `P2 - Medium`, `P3 - Low`.
4. **Code Blocks**: Always specify language identifier (`bash`, `tsx`, `typescript`, `json`, `yaml`, `xml`, `sql`, `text`).

---

# 3. Operational Verification Checklist

- [ ] **H1 Heading Formatted**: Single `# Title` at top of file.
- [ ] **Negative Scope Present**: "This guide is **not**:" section includes 3 clear bullet points.
- [ ] **ASCII Diagram Validated**: Flowchart renders cleanly in standard monospace font.
- [ ] **`Last verified` Present**: File ends with `**Last verified:** August 14, 2026`.

---

# 4. Official Sources

- Repository Instructions: [../../.claude/CLAUDE.md](../.claude/CLAUDE.md)

---

**Last verified:** August 14, 2026

---

# Related documentation

### Governance

- `governance/README.md`
- `governance/contribution-guide.md`
- `governance/maintenance.md`
- `governance/source-verification.md`
- `governance/versioning.md`

### Sources

- `sources/README.md`
