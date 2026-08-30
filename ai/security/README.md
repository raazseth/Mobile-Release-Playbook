# AI Security & Guardrails Subsystem Handbook

This directory contains production handbook guides, OWASP GenAI Top 10 mitigations, prompt injection defenses, secret protection rules, and permission boundaries for **AI Security** in the Mobile Release Playbook.

Engineered in alignment with the **OWASP GenAI Security Project (2026 Standard)**, it details how to secure AI agents against security vulnerabilities.

This guide is **not**:

- an authorization mechanism to expose API secrets to AI models
- a substitute for implementing input validation prompt shields
- a guide to granting unrestricted execution permissions to AI agents

---

# 1. OWASP GenAI Security Architecture

Security guardrails protect AI agents from LLM01 Prompt Injection, LLM02 Sensitive Information Disclosure, LLM05 Improper Output Handling, and LLM06 Excessive Agency.

```text
┌────────────────────────────────────────────────────────┐
│             OWASP GENAI SECURITY GUARDRAILS            │
│                                                        │
│  - Prompt Shielding: Isolated XML tag variable bounds  │
│  - Secret Masking: Redact API keys before LLM call     │
│  - Human-in-the-Loop: Approval required for store ops  │
└────────────────────────────────────────────────────────┘
```

---

# 2. Key Security Handbooks

- [prompt-injection.md](prompt-injection.md) - Prompt injection defense rules.

---

# 3. Official Sources

- OWASP GenAI Security Project: https://owasp.org/www-project-top-10-for-large-language-model-applications/

---

**Last verified:** August 14, 2026

---

# Related documentation

### AI security

- `ai/security/agent-permissions.md`
- `ai/security/destructive-actions.md`
- `ai/security/prompt-injection.md`
- `ai/security/secret-protection.md`

### Signing security

- `signing/security/secret-storage.md`
- `signing/security/access-control.md`

### Orchestration

- `ai/orchestration/human-approval.md`
- `ai/orchestration/tool-permissions.md`
- `ai/orchestration/bounded-autonomy.md`

### Agents

- `ai/agents/README.md`

### Governance

- `governance/documentation-style.md`
