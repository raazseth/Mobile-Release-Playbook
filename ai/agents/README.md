# AI Autonomous Agents Subsystem Handbook

This directory contains production handbook guides, subagent delegation patterns, agent task specifications, and autonomous agent roles for **AI Agents** in the Mobile Release Playbook.

Engineered in alignment with **2026 platform specifications**, it details how autonomous AI agents execute release engineering workflows.

This guide is **not**:

- an authorization mechanism to delegate destructive git or store deployment tasks without human approval gates
- a substitute for verifying AI-generated code against 2026 platform rules
- a guide to running unbounded agent loops

---

# 1. AI Agents Subsystem Architecture

Autonomous AI agents leverage specialized prompt context and tool capabilities to execute multi-step release engineering tasks.

```text
┌────────────────────────────────────────────────────────┐
│             AI AUTONOMOUS AGENT TAXONOMY               │
│                                                        │
│  - Release Auditor Agent   ──→ Pre-flight quality gates │
│  - Rejection Analyst Agent ──→ Resolution Center triage│
│  - Metadata Specialist     ──→ Character limit checks  │
│  - QA Automation Agent     ──→ Test suite execution    │
└────────────────────────────────────────────────────────┘
```

---

# 2. Key Agent Handbooks

- [release-manager.md](release-manager.md) - Autonomous release manager agent role.
- [release-auditor.md](release-auditor.md) - Pre-flight release auditor agent role.

---

# 3. Official Sources

- AI Playbook Master Architecture: [../README.md](../README.md)

---

**Last verified:** August 14, 2026
