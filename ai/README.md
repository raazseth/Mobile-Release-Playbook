# AI-Assisted Mobile Release Engineering

This directory contains the architecture, agent role definitions, orchestration patterns, task-specific prompts, context schemas, evaluation benchmarks, security controls, and tool integration handbooks for **AI-Assisted Mobile Release Engineering**.

Engineered in alignment with **2026 Context Engineering standards** and the **OWASP GenAI Security Project (2026 Standard)**, it documents how developers and release teams can leverage AI tools (Claude, ChatGPT, GitHub Copilot, Claude Code) and specialized multi-agent workflows to evaluate release candidates, troubleshoot build failures, audit privacy manifests, recover from store rejections, and draft store metadata for Expo and React Native applications.

This directory is **not**:

- an application service, SaaS product, or AI agent implementation platform
- an un-monitored autonomous deployment engine
- a replacement for deterministic CI/CD build tools, unit test suites, or physical device validation
- a mechanism to bypass explicit human authorization on production releases

---

# 1. Architecture of the `ai/` Subsystem

The `ai/` subsystem is structured into nine distinct, interconnected modules. Each directory serves a specific document type and operational boundary within AI-assisted release engineering.

```text
                                 ┌────────────────────────┐
                                 │       ai/README.md     │
                                 │   Subsystem Architecture│
                                 └───────────┬────────────┘
                                             │
      ┌──────────────────────────────────────┼──────────────────────────────────────┐
      ▼                                      ▼                                      ▼
┌────────────────────────┐        ┌────────────────────────┐        ┌────────────────────────┐
│   ai/getting-started/  │        │       ai/tools/        │        │      ai/agents/        │
│ When to use / not use  │        │ Tool guidance (Claude, │        │ Reusable agent role    │
│ AI in release pipelines│        │ ChatGPT, Copilot, CLI) │        │ definitions (9 Agents) │
└────────────────────────┘        └────────────────────────┘        └────────────────────────┘
      │                                      │                                      │
      ▼                                      ▼                                      ▼
┌────────────────────────┐        ┌────────────────────────┐        ┌────────────────────────┐
│     ai/workflows/      │        │   ai/orchestration/    │        │      ai/prompts/       │
│ End-to-end multi-agent │        │ Topologies, Subagents, │        │ Copy-paste task-specific│
│ release procedures     │        │ Human Approval, MCP    │        │ structured prompts     │
└────────────────────────┘        └────────────────────────┘        └────────────────────────┘
      │                                      │                                      │
      ▼                                      ▼                                      ▼
┌────────────────────────┐        ┌────────────────────────┐        ┌────────────────────────┐
│      ai/context/       │        │       ai/evals/        │        │      ai/security/      │
│ Structured schemas for │        │ Benchmarks for agent   │        │ Prompt injection,      │
│ repo & release state   │        │ evals & hallucinations │        │ secrets & sandboxing   │
└────────────────────────┘        └────────────────────────┘        └────────────────────────┘
```

---

# 2. Subsystem Directory Taxonomy

| Directory | Core Purpose | Fleshed-Out Guides & Artifacts |
|---|---|---|
| **[getting-started/](getting-started/when-to-use-ai.md)** | Operational guidelines on where AI adds leverage vs where deterministic automation is required. | [when-to-use-ai.md](getting-started/when-to-use-ai.md), [when-not-to-use-ai.md](getting-started/when-not-to-use-ai.md) |
| **[tools/](tools/chatgpt.md)** | Platform-specific guidance for configuring AI tools (ChatGPT, Claude, Copilot, Claude Code) for mobile engineering. | [chatgpt.md](tools/chatgpt.md), [claude.md](tools/claude.md), [claude-code.md](tools/claude-code.md), [codex.md](tools/codex.md), [github-copilot.md](tools/github-copilot.md), [github-agents.md](tools/github-agents.md) |
| **[agents/](agents/release-manager.md)** | Formal role definitions specifying agent purpose, scope, inputs, rules, and outputs (9 Agents). | [release-manager.md](agents/release-manager.md), [release-auditor.md](agents/release-auditor.md), [qa-agent.md](agents/qa-agent.md), [security-auditor.md](agents/security-auditor.md), [privacy-auditor.md](agents/privacy-auditor.md), [dependency-auditor.md](agents/dependency-auditor.md), [metadata-agent.md](agents/metadata-agent.md), [debugging-agent.md](agents/debugging-agent.md), [rejection-analyzer.md](agents/rejection-analyzer.md) |
| **[workflows/](workflows/release-preparation.md)** | End-to-end multi-agent procedure specifications with machine-readable YAML reports. | [release-preparation.md](workflows/release-preparation.md), [release-audit.md](workflows/release-audit.md), [ai-qa.md](workflows/ai-qa.md), [debugging.md](workflows/debugging.md), [metadata-generation.md](workflows/metadata-generation.md), [rejection-analysis.md](workflows/rejection-analysis.md), [release-notes.md](workflows/release-notes.md) |
| **[orchestration/](orchestration/agent-workflows.md)** | Coordination protocols, topologies, permission limits, MCP stateless spec, and human gates. | [agent-workflows.md](orchestration/agent-workflows.md), [human-approval.md](orchestration/human-approval.md), [bounded-autonomy.md](orchestration/bounded-autonomy.md), [subagents.md](orchestration/subagents.md), [tool-permissions.md](orchestration/tool-permissions.md), [mcp.md](orchestration/mcp.md) |
| **[prompts/](prompts/debugging.md)** | Task-specific prompt templates featuring prompt caching, XML tag shielding, and `<thinking>` blocks. | [debugging.md](prompts/debugging.md), [metadata.md](prompts/metadata.md), [qa.md](prompts/qa.md), [rejection-analysis.md](prompts/rejection-analysis.md), [release-audit.md](prompts/release-audit.md), [release-notes.md](prompts/release-notes.md) |
| **[context/](context/repository-context.md)** | Machine-readable schemas for injecting repository and release state into prompts. | [repository-context.md](context/repository-context.md), [release-context.md](context/release-context.md), [store-context.md](context/store-context.md) |
| **[evals/](evals/agent-evaluation.md)** | Evaluation frameworks for testing agent outputs against hallucinations and regressions. | [agent-evaluation.md](evals/agent-evaluation.md), [hallucination-tests.md](evals/hallucination-tests.md), [regression-tests.md](evals/regression-tests.md), [workflow-evaluation.md](evals/workflow-evaluation.md) |
| **[security/](security/agent-permissions.md)** | OWASP GenAI 2026 security handbooks covering container sandboxing, action gating, and secret isolation. | [agent-permissions.md](security/agent-permissions.md), [destructive-actions.md](security/destructive-actions.md), [prompt-injection.md](security/prompt-injection.md), [secret-protection.md](security/secret-protection.md) |

---

# 3. Core Philosophy & Design Rules

AI integration within this playbook adheres strictly to five foundational design rules:

```text
1. 2026 Context Engineering Standards
   Prompts use static system prompt caching layouts, single-mount variable blocks
   (<single_mount_variables>), explicit XML tag shielding, and native reasoning blocks (<thinking>).

2. OWASP GenAI Security Alignment (2026 Standard)
   Mitigates LLM01 (Prompt Injection), LLM02 (Sensitive Information Disclosure),
   LLM05 (Improper Output Handling), LLM06 (Excessive Agency), and LLM07 (System Prompt Leakage).

3. The Non-Bypassable Human Gate
   AI assists + Human verifies + Human approves. Production releases, store
   submissions, signing key changes, and privacy updates strictly require human sign-off.

4. Opaque Reference Secret Isolation
   Secrets (p12 certs, keystores, service account JSONs, API keys) are NEVER
   exposed to AI prompt context windows. Agents operate via opaque token proxies.

5. Bounded Tool Permissions & Sandboxing
   Tool execution privileges are strictly bounded using least-privilege tiers (Tier 0–3)
   and enforced outside prompt space via MCP servers and container sandboxes.
```

---

# 4. Standard Agent Vocabularies

All agents, prompts, and orchestration schemas in `ai/` implement standard repository vocabularies:

### Release Verdict Vocabulary

- **`READY`**: All checks passed. Zero P0/P1 blockers. Build and metadata verified.
- **`CONDITIONAL`**: Non-blocking warnings (P2/P3) exist. Requires explicit human sign-off to proceed.
- **`NOT READY`**: One or more P0 or P1 blockers exist. Release candidate execution MUST HALT.

### Finding Severity Vocabulary

- **`P0` (Release Blocker)**: Critical flaw. Immediate app crash, secret exposure, privacy non-compliance, or store rejection.
- **`P1` (High Risk)**: Major functionality flaw or unverified device matrix gap. Requires explicit human sign-off waiver.
- **`P2` (Medium Warning)**: Non-critical warning or minor debt.
- **`P3` (Low Warning)**: Non-functional cleanup or style suggestion.

### Finding Confidence Vocabulary

- **`FACT`**: Directly verified from repository files, build logs, or official platform documentation.
- **`INFERENCE`**: Deduced logically from evidence, but unconfirmed by empirical build/test execution.
- **`UNKNOWN`**: Cannot be verified due to missing context or truncated logs. Never guess missing data.

---

# 5. Operational Flow: How a Mobile Release Runs with AI

```text
[ Git Release Candidate Tagged ]
               │
               ▼
[ Load Repository & Release Context ] ──→ (See context/)
               │
               ▼
[ Run Deterministic CI Pipeline ]     ──→ (Linters, Unit Tests, EAS Build)
               │
               ▼
[ Dispatch Sub-Auditors ]             ──→ (See agents/ & orchestration/subagents.md)
  - QA Agent (Test matrix & logs)
  - Security Auditor (Secret scan)
  - Privacy Auditor (PrivacyInfo.xcprivacy)
  - Dependency Auditor (SDK locks)
  - Metadata Agent (Store text & notes)
               │
               ▼
[ Aggregate Reports & Issue Verdict ]  ──→ (See agents/release-auditor.md)
               │
               ▼
┌────────────────────────────────────────────────────────┐
│               HUMAN APPROVAL GATEWAY                   │
│  (Review Evidence Package → Decide Store Submission)   │
└──────────────────────────┬─────────────────────────────┘
                           │
                 ┌─────────┴─────────┐
                 ▼                   ▼
           [ Approved ]        [ Rejected ]
                 │                   │
                 ▼                   ▼
       [ Bounded Execution ]  [ Return to Development ]
       (EAS Submit / Push)    (Run Debugging Agent)
```

---

# 6. Related Repository Documentation

- [Repository Architecture](../ARCHITECTURE.md) - Full rationale for playbook structure.
- [Mobile Release Lifecycle](../foundations/mobile-release-lifecycle.md) - Core release lifecycle stages.
- [Governance & Contribution](../CONTRIBUTING.md) - Playbook governance rules.
- [Repository CLAUDE.md](../.claude/CLAUDE.md) - System rules for AI pair-programming in this repo.

---

# 7. Official Sources

- OWASP GenAI Security Project (2026 Standard): https://genai.owasp.org/
- Apple Developer Documentation: https://developer.apple.com/documentation/
- Google Play Console Help: https://support.google.com/googleplay/android-developer/
- Expo EAS Documentation: https://docs.expo.dev/eas/
- React Native Documentation: https://reactnative.dev/
- Model Context Protocol (MCP) Specification: https://modelcontextprotocol.io/

---

**Last verified:** August 13, 2026

