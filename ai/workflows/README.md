# AI Automated Workflows Subsystem Handbook

This directory contains production handbook guides, end-to-end release preparation workflows, AI QA suites, rejection triage automation, and release note generators for **AI Workflows** in the Mobile Release Playbook.

Engineered in alignment with **2026 Context Engineering standards**, it details how to automate complete release workflows using AI.

This guide is **not**:

- an authorization mechanism to automate production releases without human sign-off
- a substitute for executing manual QA smoke testing
- a guide to un-structured workflow execution

---

# 1. AI Automated Workflow Architecture

AI workflows orchestrate sequential sub-tasks to automate release preparation, pre-flight audits, and store rejection resolution.

```text
┌────────────────────────────────────────────────────────┐
│             AI AUTOMATED RELEASE WORKFLOW              │
│                                                        │
│  Step 1: Release Audit Workflow (`release-audit.md`)   │
│  Step 2: AI QA Automation (`ai-qa.md`)                 │
│  Step 3: Metadata Generation (`metadata-generation.md`)│
│  Step 4: Release Preparation (`release-preparation.md`)│
└────────────────────────────────────────────────────────┘
```

---

# 2. Key Workflow Specifications

- [release-preparation.md](release-preparation.md) - Release preparation workflow spec.

---

# 3. Official Sources

- AI Playbook Master Architecture: [../README.md](../README.md)

---

**Last verified:** August 14, 2026

---

# Related documentation

### Workflows

- `ai/workflows/ai-qa.md`
- `ai/workflows/debugging.md`
- `ai/workflows/metadata-generation.md`
- `ai/workflows/rejection-analysis.md`
- `ai/workflows/release-audit.md`
- `ai/workflows/release-notes.md`
- `ai/workflows/release-preparation.md`

### Agents

- `ai/agents/README.md`

### Prompts

- `ai/prompts/README.md`

### Orchestration

- `ai/orchestration/agent-workflows.md`

### Pre-release

- `pre-release/release-readiness.md`

### Post-release

- `post-release/incident-response.md`
