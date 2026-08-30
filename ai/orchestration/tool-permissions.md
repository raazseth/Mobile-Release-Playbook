# Tool Permissions

Tool Permissions define the classification, scoping, authorization boundaries, and enforcement mechanisms governing the tools provided to AI agents operating within mobile release engineering workflows.

This document details how tools (such as file viewers, code search tools, file editors, command runners, and external API connectors) are restricted according to agent role, task scope, and execution environment to maintain system safety and prevent unauthorized operations.

This guide is **not**:

- a set of soft prompt guidelines (permissions must be enforced at the runtime/tool-call boundary)
- a static all-or-nothing permission grant (permissions are dynamic, scoped, and environment-aware)
- a replacement for explicit [Human Approval](human-approval.md) on production-impacting operations

---

# 1. Core Principles of Tool Permission Scoping

AI tools act as the bridge between model reasoning and repository infrastructure. If an agent receives unrestricted tool access (such as un-sandboxed shell execution), a prompt injection attack, model hallucination, or logic error can compromise signing keys, damage working trees, or trigger unintended store publications.

```text
┌────────────────────────────────────────────────────────┐
│                      AGENT TASK                        │
│         Target: Audit Release Candidate Diff           │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│             TOOL PERMISSION GATEKEEPER                 │
│                                                        │
│  - Verifies Agent Role & Assigned Tool Tier            │
│  - Validates Argument Safety (No raw secrets/del)      │
│  - Enforces Path & Command Whitelists                  │
│  - Blocks Tier 3 High-Impact Tool Invocation           │
└──────────────────────────┬─────────────────────────────┘
                           │
         ┌─────────────────┴─────────────────┐
         ▼                                   ▼
 [ Tier 0 / Tier 1 Allowed ]       [ Tier 3 Blocked ]
 (Execute view_file, grep)         (Requires Human Approval
                                    Record for EAS Submit)
```

Tool permission management adheres to four core principles:

1. **Principle of Least Privilege (POLP)**: Agents receive only the absolute minimum tools required for their assigned role. Analysis agents receive read-only tools; editing tools are granted only to drafting roles.
2. **Deterministic Runtime Enforcement**: Permissions are validated by the tool runner or Model Context Protocol (MCP) server before tool execution. The agent cannot grant itself tool access through system prompts.
3. **Environment-Aware Scoping**: Tool permissions automatically tighten as the execution environment progresses toward production (Local Dev → CI Runner → Production Gate).
4. **Auditability of Tool Execution**: Every tool call, passed parameter, execution result, and permission denial must be recorded in an immutable audit log.

---

# 2. Tool Classification Matrix

Tools are categorized into four distinct **Tool Tiers** based on operational risk, reversibility, and blast radius.

| Tool Tier | Tier Name | Risk Level | Description & Examples | Enforcement Policy |
|---|---|---|---|---|
| **Tier 0** | **Read-Only Analysis** | Low | Inspecting code, parsing logs, reading configs, viewing git diffs.<br>Examples: `view_file`, `grep_search`, `list_dir`, `read_url_content`. | Always permitted for analysis and auditor agents. |
| **Tier 1** | **Local File Modification** | Medium | Creating or updating local workspace files.<br>Examples: `write_to_file`, `replace_file_content`, `multi_replace_file_content`. | Permitted for drafting roles in designated workspace directories. Uncommitted diffs. |
| **Tier 2** | **Safe Command Execution** | Medium-High | Invoking deterministic build tools, linters, and unit test suites.<br>Examples: `run_command` (whitelisted: `npm test`, `npx expo-doctor`). | Permitted under strict command whitelisting and resource limits. |
| **Tier 3** | **Sensitive / High-Impact** | Critical | Actions touching store APIs, signing keys, production branches, or live infrastructure.<br>Examples: `eas submit`, `fastlane match`, key rotation, store metadata push. | **STRICTLY BLOCKED** unless accompanied by a verified [Human Approval Record](human-approval.md). |

---

# 3. Permission Scoping by Agent Role

Each specialized agent defined in `ai/agents/` is mapped to an authorized set of Tool Tiers.

```text
┌─────────────────────────┬─────────────────────────────────────────────────┐
│ Agent Role              │ Authorized Tool Tiers                           │
├─────────────────────────┼─────────────────────────────────────────────────┤
│ Release Auditor         │ Tier 0 (Read-Only)                              │
│ Security Auditor        │ Tier 0 (Read-Only)                              │
│ Privacy Auditor         │ Tier 0 (Read-Only)                              │
│ Dependency Auditor      │ Tier 0 (Read-Only) + Tier 1 (Manifest Draft)    │
│ Metadata Agent          │ Tier 0 (Read-Only) + Tier 1 (Metadata Draft)    │
│ Debugging Agent         │ Tier 0 (Read-Only) + Tier 2 (Whitelisted Test)  │
│ Release Manager         │ Tier 0 (Read-Only) + Subagent Dispatch Tools    │
└─────────────────────────┴─────────────────────────────────────────────────┘
```

> **Note:** Auditor agents ([Release Auditor](../agents/release-auditor.md), [Security Auditor](../agents/security-auditor.md), [Privacy Auditor](../agents/privacy-auditor.md)) are strictly restricted to **Tier 0 (Read-Only)**. They do not possess tools to modify source code, alter working trees, or execute shell commands.

---

# 4. Dynamic Tool Scoping & Session Elevation

Certain workflows require temporary tool elevation (e.g., the [Debugging Agent](../agents/debugging-agent.md) starting in Tier 0 analysis and requesting Tier 2 tool invocation to execute a targeted unit test).

```text
[ Debugging Agent (Tier 0) ] ──( Identifies Crash Cause )
                                      │
                                      ▼
                        [ Request Tier 2 Tool Elevation ]
                        (Target: `npm test -- -t "auth"`)
                                      │
                                      ▼
                      ┌───────────────────────────────┐
                      │  Tool Permission Gatekeeper   │
                      └───────────────┬───────────────┘
                                      │
               ┌──────────────────────┴──────────────────────┐
               ▼                                             ▼
  [ Whitelisted Command: PASS ]               [ Blacklisted Command: FAIL ]
   Grant Ephemeral Lease (60s)                 Block Elevation & Alert
               │                                             │
               ▼                                             ▼
   [ Execute Command & Return ]               [ Return Permission Error ]
```

## 4.1 Ephemeral Permission Leases

1. **Explicit Scope**: Tool elevation is granted for a single tool call or a time-bounded lease (e.g., 60 seconds).
2. **Automatic Revocation**: Once the whitelisted command completes, the session immediately reverts to the agent's baseline Tier 0/1 permissions.
3. **No Ambient Privileges**: Elevation to Tier 2 never implicitly grants access to Tier 3 tools or unwhitelisted commands.

---

# 5. Machine-Readable Tool Permission Schema

Tool permissions must be defined in machine-readable YAML policy files consumed by Model Context Protocol (MCP) servers or tool execution runners.

```yaml
tool_permissions_policy:
  policy_id: "pol-debug-agent-v1"
  agent_name: "DebuggingAgent"
  environment: "ci_runner"

  tiers:
    tier_0_read_only:
      enabled: true
      allowed_tools:
        - "view_file"
        - "grep_search"
        - "list_dir"
      path_restrictions:
        allowed_directories: ["./"]
        forbidden_directories: ["~/.ssh", "~/.fastlane", "./.env*"]

    tier_1_file_modification:
      enabled: true
      allowed_tools:
        - "replace_file_content"
      path_restrictions:
        allowed_directories: ["./src/*", "./scratch/*"]
        forbidden_directories: ["./ios/*", "./android/*", "./.github/*"]

    tier_2_command_execution:
      enabled: true
      allowed_tools:
        - "run_command"
      command_whitelist:
        - "npm test"
        - "npx jest --testPathPattern=*"
        - "npx expo-doctor"
      max_execution_time_seconds: 120

    tier_3_high_impact:
      enabled: false # Strictly blocked
      require_human_approval_token: true

  security_rules:
    block_raw_secrets_in_args: true
    sanitize_stdout_stderr: true
    audit_logging_enabled: true
```

---

# 6. Anti-Patterns & Security Violations

Failing to enforce strict tool permissions exposes mobile release pipelines to severe vulnerabilities:

| Anti-Pattern | Operational Risk | Corrective Policy |
|---|---|---|
| **Ambient Unrestricted Shell** | Agent receives open bash access (`run_command "*"`). Prompt injection can run `git push --force` or exfiltrate environment secrets. | Strict command whitelisting; block shell piping, redirection, and unapproved binaries. |
| **Prompt-Only Tool Rules** | Informing the agent in system prompts "Do not touch production keys." Models can be instructed by untrusted input to ignore instructions. | Enforce tool blocking deterministically at the MCP server or runner layer. |
| **Secret Arguments Leakage** | Passing API keys or passwords directly as command CLI flags (`eas login --token SECRET`). | Inject secrets as environment variables inside isolated build containers; never pass in tool call arguments. |
| **Over-Privileged Auditors** | Granting file editing or shell execution tools to audit agents. | Restrict auditors strictly to Tier 0 read-only tools. |

---

# 7. Operational Verification Checklist

Before deploying tool permission policies to release workflows, verify against this checklist:

- [ ] **Deterministic Enforcement**: Tool permissions are enforced by the MCP server or execution runner, not by system prompt text.
- [ ] **Auditor Restrictions**: All audit agents ([Release Auditor](../agents/release-auditor.md), [Security Auditor](../agents/security-auditor.md), [Privacy Auditor](../agents/privacy-auditor.md)) are restricted strictly to Tier 0 read-only tools.
- [ ] **Command Whitelisting**: Tier 2 command tools enforce exact command whitelists; unapproved shell commands are rejected.
- [ ] **Tier 3 Gate Active**: Production publishing and credential management tools require a validated [Human Approval Record](human-approval.md).
- [ ] **Path Sandboxing**: File tools cannot read or write outside designated repository directories or access host environment secret files.
- [ ] **Secret Sanitization**: Tool call logs automatically sanitize output strings matching API keys, certificates, or token patterns.
- [ ] **Machine-Readable Schema**: Tool permission configurations are stored as validated YAML files (`tool_permissions_policy.yaml`).

---

# 8. Related Documentation

### Architecture & Orchestration

- [Agent Workflows](agent-workflows.md) - Multi-agent orchestration patterns and topology.
- [Human Approval](human-approval.md) - Mandatory human review boundaries for production actions.
- [Bounded Autonomy](bounded-autonomy.md) - Operational rules for constraining agent freedom.
- [Subagents](subagents.md) - Subagent delegation boundaries and tool isolation.
- [MCP Architecture](mcp.md) - Model Context Protocol tool integration standards.

### Agent Definitions & Security

- [Release Manager](../agents/release-manager.md) - Primary orchestrator role.
- [Security Auditor](../agents/security-auditor.md) - Security scanning agent definition.
- [Agent Permissions](../security/agent-permissions.md) - Infrastructure security sandboxing.
- [Secret Protection](../security/secret-protection.md) - Credential and secret masking rules.

### Core Release Lifecycle

- [Mobile Release Lifecycle](../../foundations/mobile-release-lifecycle.md) - Core lifecycle phases from build to monitoring.

---

# 9. Official Sources

For tool permission standards, Model Context Protocol security, and AI safety guidelines, consult current official documentation:

### Model Context Protocol & AI Safety

- Model Context Protocol (MCP) Specification & Security Architecture: https://modelcontextprotocol.io/
- OWASP Top 10 for LLM Applications (Excessive Agency & Insecure Plugin Design): https://owasp.org/www-project-top-10-for-large-language-model-applications/

---

**Last verified:** August 13, 2026

MCP specifications, tool runner capabilities, and security sandboxing standards evolve. Re-verify current official documentation before altering tool permission matrices or elevation rules.

