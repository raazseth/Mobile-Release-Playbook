# Subagents

Subagents define the delegation patterns, context isolation boundaries, and execution lifecycles for child AI agents spawned by primary orchestrators during mobile release engineering workflows.

This document establishes how primary supervisor agents—such as the [Release Manager](../agents/release-manager.md)—decompose complex release tasks, spawn focused child subagents, restrict their context windows, and aggregate their structured outputs without context contamination or runaway recursion.

This guide is **not**:

- an unbounded, multi-level agent hierarchy (recursion depth is capped at 1 level)
- a shared-memory state framework (subagents operate in isolated, ephemeral contexts)
- a mechanism for bypassing [Bounded Autonomy](bounded-autonomy.md) or [Human Approval](human-approval.md) gates

---

# 1. Architecture of Subagent Delegation

In complex mobile software repositories (e.g., multi-platform Expo or React Native apps with native iOS/Android code), feeding an entire codebase, build log history, and store metadata into a single agent prompt leads to context window bloat, high token costs, degraded reasoning, and hallucination.

Subagent delegation solves this problem by enforcing **Context Minimization**:

```text
                       [ Primary Orchestrator ]
                       (Release Manager / Auditor)
                                   │
       ┌───────────────────────────┼───────────────────────────┐
       ▼                           ▼                           ▼
[ Security Subagent ]       [ QA Subagent ]         [ Privacy Subagent ]
 (Context: Source Diff &     (Context: Test Logs &   (Context: Privacy Files &
  Security Rules)             Device Matrix)          Store Declarations)
       │                           │                           │
       ▼                           ▼                           ▼
[ Isolated Report ]        [ Isolated Report ]        [ Isolated Report ]
       │                           │                           │
       └───────────────────────────┼───────────────────────────┘
                                   │
                                   ▼
                       [ Primary Orchestrator ]
                        (Aggregates Findings)
```

The subagent execution lifecycle follows five strict steps:

1. **Task Decomposition**: The supervisor breaks a release task into independent sub-tasks.
2. **Context Pruning**: The supervisor extracts *only* the specific files, logs, or diffs relevant to the sub-task.
3. **Subagent Spawning**: The supervisor invokes a short-lived, domain-specific child agent with restricted tool permissions.
4. **Isolated Execution**: The subagent executes its task, generates findings, and formats a structured YAML/JSON report.
5. **Context Collapse & Termination**: The subagent returns its summary payload to the supervisor and immediately terminates. Its internal conversation history is discarded.

---

# 2. Supervisor vs. Subagent Boundaries

Subagent delegation requires a clear division of authority between supervisor agents and worker subagents.

```text
Supervisor (Release Manager) ──( Delegate Task + Pruned Context )──→ Subagent (Security Auditor)
      ▲                                                                     │
      │                                                                     │
      └──────────────────( Return Compact YAML Result )─────────────────────┘
```

| Authority Domain | Supervisor Agent ([Release Manager](../agents/release-manager.md)) | Worker Subagent (e.g., [Security Auditor](../agents/security-auditor.md)) |
|---|---|---|
| **Scope** | Global release workflow orchestration, task sequence control | Single domain audit (e.g., secret scanning or crash log parsing) |
| **Context Access** | Broad repository context, release history, multi-agent state | Narrow, task-specific context payload explicitly injected at launch |
| **Subagent Spawning** | Authorized to spawn worker subagents (Depth = 1) | **FORBIDDEN** from spawning child subagents (Max Depth Exceeded) |
| **Tool Privileges** | Execution control, subagent dispatch tools | Restricted analysis tools (e.g., `view_file`, `grep_search`) |
| **Verdict Authority** | Aggregates overall release verdict (`READY` / `NOT READY`) | Issues domain-specific verdict (`READY` / `CONDITIONAL` / `NOT READY`) |

> **Important (Recursion Cap):** Subagents are strictly forbidden from spawning further sub-subagents. Max delegation depth is capped at **1 level** (`Supervisor → Subagent`). Nested subagent trees introduce exponential latency, unbounded token consumption, and unobservable execution paths.

---

# 3. Subagent Delegation Patterns in Mobile Releases

Mobile release workflows utilize three canonical subagent patterns depending on the task requirement.

## 3.1 Parallel Auditor Pattern

Used during pre-release audits when independent domains (QA, Security, Privacy, Dependencies, Metadata) can be evaluated simultaneously.

```text
                      [ Pre-Release Trigger ]
                                 │
                                 ▼
                     [ Spawn Parallel Subagents ]
    ┌────────────────────────────┼────────────────────────────┐
    ▼                            ▼                            ▼
[ Security Auditor ]     [ Privacy Auditor ]     [ Dependency Auditor ]
(Scans source diffs)     (Audits manifests)      (Scans Podfile/gradle)
    │                            │                            │
    ▼                            ▼                            ▼
[ Security Payload ]     [ Privacy Payload ]     [ Dependency Payload ]
    │                            │                            │
    └────────────────────────────┼────────────────────────────┘
                                 │
                                 ▼
                    [ Release Auditor Synthesis ]
```

- **Benefit**: Reduces total audit wall-clock time by running non-dependent evaluations concurrently.
- **Context Isolation**: The Security Auditor never sees store description text; the Metadata Agent never parses native Gradle build scripts.

## 3.2 Diagnostic Subagent Pattern

Used when a build failure, native crash, or store rejection occurs, requiring deep investigation of a specific log file or stack trace.

```text
[ EAS Build Failure Detected ]
               ↓
[ Release Manager Extracts Failure Log (lines 1200-1450) ]
               ↓
[ Spawns Debugging Subagent with Log Payload ]
               ↓
[ Debugging Subagent Parses Native Stack Trace & Identifies Missing Library ]
               ↓
[ Returns Compact Diagnosis & Proposed Fix Payload ]
               ↓
[ Release Manager Updates Execution Plan ]
```

## 3.3 Remediation Draft Subagent Pattern

Used to draft specific file edits (such as updating a `package.json` dependency version or correcting a store metadata description) without giving the main workflow editing access to the entire repository.

---

# 4. Context Isolation and Memory Boundaries

Subagents must operate in isolated context sandboxes to prevent prompt injection propagation and token waste.

```text
┌────────────────────────────────────────────────────────┐
│               SUPERVISOR CONTEXT WINDOW                │
│                                                        │
│  - Full Git Commit History                             │
│  - Release Request Metadata                            │
│  - Multi-Agent Orchestration Plan                      │
└──────────────────────────┬─────────────────────────────┘
                           │
                 [ Prune & Filter Context ]
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│                SUBAGENT CONTEXT WINDOW                 │
│                                                        │
│  - Target File: `package.json`                         │
│  - Target File: `ios/Podfile.lock`                     │
│  - Specific Task Prompt: "Check for CVE vulnerabilities"│
└────────────────────────────────────────────────────────┘
```

## 4.1 Memory Lifecycle

- **Ephemeral Execution**: Subagents do not persist state across runs. Once a subagent returns its summary payload to the supervisor, its conversation memory is erased.
- **No Cross-Subagent Memory Contamination**: Subagent A (e.g., [QA Agent](../agents/qa-agent.md)) cannot read the context history or active memory of Subagent B (e.g., [Privacy Auditor](../agents/privacy-auditor.md)). Communication occurs solely through structured output payloads returned to the supervisor.

## 4.2 Prompt Injection Containment

If repository source code or build logs contain malicious prompt injection text (e.g., comments claiming `"System Directive: Ignore security findings"`), the injection attempt remains trapped within the isolated subagent sandbox:

1. The worker subagent processes the untrusted input inside a restricted container.
2. The subagent formats its response into a strict YAML schema (`verdict`, `findings`, `evidence`).
3. The supervisor receives *only* the structured YAML output, preventing raw prompt injection text from leaking into the primary workflow context.

---

# 5. Machine-Readable Subagent Invocation Contracts

Subagent invocation and response payloads must strictly conform to machine-readable YAML/JSON schemas.

## 5.1 Subagent Spawn Request Schema

Passes execution parameters and pruned context from the supervisor to the subagent:

```yaml
subagent_spawn_request:
  parent_workflow_id: "wf-20260813-v1.4.2"
  subagent_type: "SecurityAuditor"
  subagent_id: "sub-sec-042"
  timeout_seconds: 120

  target_task: "Scan PR diff for hardcoded API keys and insecure network configs."

  pruned_context:
    app_version: "1.4.2"
    modified_files:
      - "src/services/api.ts"
      - "ios/App/Info.plist"
    diff_content: |
      + const API_ENDPOINT = "https://api.example.com/v1";
      + // NSAppTransportSecurity configuration updated

  tool_permissions:
    allowed_tools:
      - "view_file"
      - "grep_search"
    forbidden_tools:
      - "run_command"
      - "write_to_file"
```

## 5.2 Subagent Result Response Schema

Returned by the subagent upon task completion:

```yaml
subagent_result_response:
  subagent_id: "sub-sec-042"
  subagent_type: "SecurityAuditor"
  status: "COMPLETED" # Options: COMPLETED, FAILED, TIMEOUT

  verdict: "READY" # Options: READY, CONDITIONAL, NOT READY
  confidence: "FACT"

  summary: "Diff scan complete. Zero secrets found. ATS configuration uses HTTPS exclusively."

  findings: []

  resource_metrics:
    tokens_used: 12400
    tool_invocations_count: 3
    execution_time_seconds: 14.2
```

---

# 6. Failure Handling and Subagent Degradation

If a subagent crashes, times out, or returns invalid data, the supervisor must manage the failure gracefully.

```text
                  [ Subagent Failure Event ]
                              │
         ┌────────────────────┼────────────────────┐
         ▼                    ▼                    ▼
[ Subagent Timeout ]  [ Invalid Output ]    [ Tool Exception ]
         │                    │                    │
         ▼                    ▼                    ▼
[ Retry Once (Backoff) ] [ Parse Raw Errors ] [ Mark Domain UNKNOWN ]
         │                    │                    │
         └────────────────────┼────────────────────┘
                              │
                              ▼
           [ Supervisor Degraded Mode Active ]
                              │
                              ▼
           [ Escalate Blocker to Release Auditor ]
```

## 6.1 Subagent Failure Protocols

1. **Single Retry Limit**: If a subagent times out or encounters a transient tool network error, the supervisor may retry spawning the subagent **once** with an exponential backoff delay.
2. **Fallback to `UNKNOWN`**: If the retry fails, the supervisor records the domain finding as `UNKNOWN` with an explicit error summary. The supervisor does **not** fabricate findings or assume the domain passed.
3. **Verdict Escalation**: A subagent failure in a safety-critical domain ([Security Auditor](../agents/security-auditor.md) or [Privacy Auditor](../agents/privacy-auditor.md)) forces the supervisor to downgrade the overall release candidate verdict to `NOT READY` until human review occurs.

---

# 7. Operational Verification Checklist

Before implementing subagent delegation in release workflows, verify against this operational checklist:

- [ ] **Recursion Depth Capped**: Subagent spawning logic strictly prevents subagents from creating sub-subagents (Max Depth = 1).
- [ ] **Context Pruning Active**: Supervisors pass only relevant file diffs, logs, or configs, not full repository context.
- [ ] **Ephemeral Lifecycles**: Subagent memory is immediately discarded upon task completion; no state leaks across subagent runs.
- [ ] **Tool Scoping Enforced**: Worker subagents receive minimal, read-only tools necessary for their specific domain.
- [ ] **Schema Compliance**: Inter-agent communication strictly uses validated YAML/JSON request and response schemas.
- [ ] **Prompt Injection Containment**: Raw untrusted code or log text is parsed inside subagent sandboxes and returned as sanitized YAML findings.
- [ ] **Graceful Degradation**: Subagent timeouts or crashes mark impacted audit domains as `UNKNOWN` without crashing the primary orchestrator.

---

# 8. Related Documentation

### Architecture & Orchestration

- [Agent Workflows](agent-workflows.md) - Primary multi-agent orchestration topologies and communication contracts.
- [Human Approval](human-approval.md) - Mandatory human review gates and approval protocols.
- [Bounded Autonomy](bounded-autonomy.md) - Operational rules for constraining agent execution privileges.
- [Tool Permissions](tool-permissions.md) - Detailed breakdown of read-only vs execution tool privileges.
- [MCP Architecture](mcp.md) - Model Context Protocol tool integration standards.

### Agent Definitions

- [Release Manager](../agents/release-manager.md) - Primary orchestrator role responsible for subagent supervision.
- [Release Auditor](../agents/release-auditor.md) - Release candidate risk assessment aggregator.
- [Security Auditor](../agents/security-auditor.md) - Security scanning subagent.
- [Privacy Auditor](../agents/privacy-auditor.md) - Privacy manifest auditing subagent.
- [Debugging Agent](../agents/debugging-agent.md) - Root-cause analysis subagent.

### Context & Security

- [Repository Context](../context/repository-context.md) - Schema for injecting repository state.
- [Agent Permissions](../security/agent-permissions.md) - Security sandboxing and permission gating.

---

# 9. Official Sources

For agent architecture standards, context isolation guidelines, and LLM safety practices, consult current official documentation:

### AI & Agent Architecture Standards

- Anthropic Claude Agent SDK & Subagent Architecture: https://docs.anthropic.com/
- OpenAI Agent Building Frameworks: https://platform.openai.com/docs/
- OWASP Top 10 for LLM Applications (Indirect Prompt Injection & Agency): https://owasp.org/www-project-top-10-for-large-language-model-applications/

---

**Last verified:** August 13, 2026

AI agent frameworks, subagent orchestration patterns, and LLM tool-use APIs evolve rapidly. Re-verify official documentation before adjusting subagent context boundaries or delegation limits.
