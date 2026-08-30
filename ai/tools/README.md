# AI Tool Integration Subsystem Handbook

This directory contains production handbook guides, Model Context Protocol (MCP) tool integrations, CLI tool bindings, and IDE extension setups for **AI Tools** in the Mobile Release Playbook.

Engineered in alignment with **2026 platform specifications**, it details how AI agents invoke tools (file viewing, code search, command execution, web verification).

This guide is **not**:

- an authorization mechanism to run un-sanitized CLI shell commands
- a substitute for verifying tool argument schemas
- a guide to bypassing tool approval boundaries

---

# 1. AI Tool Calling Architecture

AI agents invoke structured tools via JSON-schema tool declarations to interact with local filesystems, shell commands, and remote APIs.

```text
┌────────────────────────────────────────────────────────┐
│             MODEL CONTEXT PROTOCOL (MCP) TOOL FLOW     │
│                                                        │
│  [ Agent Model Call ] ──→ JSON Tool Request            │
│                             │                          │
│                             ▼                          │
│  [ MCP Host Executor ] ──→ System Tool Execution       │
│                             │                          │
│                             ▼                          │
│  [ Tool Result ] ───────→ Return Result to Model       │
└────────────────────────────────────────────────────────┘
```

---

# 2. Key Tool Integration Handbooks

- [claude.md](claude.md) - Claude Code & Anthropic tool integration rules.

---

# 3. Official Sources

- Model Context Protocol (MCP) Specification: https://modelcontextprotocol.io/

---

**Last verified:** August 14, 2026

---

# Related documentation

### Tools

- `ai/tools/chatgpt.md`
- `ai/tools/claude-code.md`
- `ai/tools/claude.md`
- `ai/tools/codex.md`
- `ai/tools/github-agents.md`
- `ai/tools/github-copilot.md`

### Getting started

- `ai/getting-started/when-to-use-ai.md`
- `ai/getting-started/when-not-to-use-ai.md`

### Agents

- `ai/agents/README.md`

### AI security

- `ai/security/README.md`
