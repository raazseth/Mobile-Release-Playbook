# AI Prompt Engineering & Templates Subsystem Handbook

This directory contains production handbook guides, static prompt caching templates, single-mount XML variable blocks, and native reasoning steps for **AI Prompts** in the Mobile Release Playbook.

Engineered in alignment with **2026 Context Engineering standards**, it provides production-ready system prompts for release tasks.

This guide is **not**:

- an authorization mechanism to use un-shielded user inputs in prompt templates
- a substitute for using static prompt caching architecture
- a guide to omitting `<thinking>` native reasoning steps

---

# 1. AI Prompt Architecture

Prompts use static system prompt caching, single-mount XML tag variable injection, and native reasoning blocks to maximize task execution accuracy.

```text
┌────────────────────────────────────────────────────────┐
│             STATIC PROMPT TEMPLATE LAYOUT              │
│                                                        │
│  - System Prompt (Cached)                             │
│  - Single-Mount Variable Block (`<release_context>`)   │
│  - Native Reasoning Step (`<thinking>`)                │
│  - Structured Output Format (`<release_report>`)       │
└────────────────────────────────────────────────────────┘
```

---

# 2. Key Prompt Templates

- [release-audit.md](release-audit.md) - Pre-release quality audit prompt template.
- [aso-optimization.md](aso-optimization.md) - App Store Optimization prompt for store listing keywords, copy, and A/B test variants.

---

# 3. Official Sources

- AI Playbook Master Architecture: [../README.md](../README.md)

---

**Last verified:** August 14, 2026

---

# Related documentation

### Prompts

- `ai/prompts/aso-optimization.md`
- `ai/prompts/debugging.md`
- `ai/prompts/metadata.md`
- `ai/prompts/qa.md`
- `ai/prompts/rejection-analysis.md`
- `ai/prompts/release-audit.md`
- `ai/prompts/release-notes.md`

### Agents

- `ai/agents/README.md`

### Workflows

- `ai/workflows/README.md`
