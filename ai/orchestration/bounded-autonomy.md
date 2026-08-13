# Bounded Autonomy

Bounded Autonomy defines the operational framework, permission constraints, tool execution boundaries, and safety limits governing AI agents operating within mobile release engineering workflows.

This document establishes how to grant AI agents sufficient operational freedom to perform complex tasks (such as dependency audits, build failure diagnosis, and release candidate evaluations) while deterministically preventing unauthorized, destructive, or non-reversible actions.

This guide is **not**:

- an all-or-nothing permission toggle (e.g., granting full shell access vs zero access)
- a prompt-only security policy (boundaries must be enforced at the tool/runner layer)
- a replacement for explicit [Human Approval](human-approval.md) on production-impacting actions
- an endorsement of fully autonomous, unmonitored production deployments

---

# 1. Core Principles of Bounded Autonomy

AI agents possess reasoning capabilities but lack intrinsic operational risk awareness. Left unconstrained, an agent executing a build troubleshooting task might attempt destructive actions—such as force-pushing git branches, deleting cached certificates, upgrading native SDKs across major versions, or executing unverified shell commands.

```text
┌────────────────────────────────────────────────────────┐
│                   TASK INVOCATION                      │
│     Target Goal: Audit Release Candidate & Build       │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│               BOUNDED AUTONOMY RUNNER                  │
│                                                        │
│  - Enforces Autonomy Level (Level 0 / 1 / 2)           │
│  - Restricts Tools to Allowed Whitelist                │
│  - Enforces File System Sandboxing                     │
│  - Limits Execution Time & Token Budgets               │
│  - Intercepts High-Impact Action Triggers              │
└──────────────────────────┬─────────────────────────────┘
                           │
         ┌─────────────────┴─────────────────┐
         ▼                                   ▼
 [ Safe Tool Execution ]           [ Approval Required ]
 (Read files, run linters,         (Submit store build,
  analyze crash stack traces)       rotate signing keys)
         │                                   │
         ▼                                   ▼
 [ Return Results ]                [ Human Approval Gate ]
```

Bounded Autonomy relies on five core principles:

1. **Default-Deny Security Posture**: Agents begin with zero execution capabilities. Tools, shell commands, file path access, and network domains must be explicitly granted per task.
2. **Deterministic Enforcement Outside Prompt Space**: Boundaries are enforced by the underlying tool runner or CI/CD container, not by asking the AI model in system prompts to "please be careful."
3. **Layered Autonomy Levels**: Operational privileges are categorized into discrete levels based on action reversibility and blast radius.
4. **Hard Resource Limits**: Timeouts, recursion depth caps, and token budgets prevent runaway loops or infinite retry chains.
5. **Circuit Breakers and Mandatory Gates**: Any attempt to perform a non-reversible operation (e.g., store publication, credential mutation) automatically halts execution and triggers a human approval gate.

---

# 2. Autonomy Level Taxonomy

Bounded Autonomy classifies agent operations into four discrete levels based on potential risk and reversibility.

| Autonomy Level | Name | Description | Allowed Actions | Human Approval Required? |
|---|---|---|---|---|
| **Level 0** | **Read-Only / Inspection** | Passive analysis and evaluation. Agent cannot alter repository state, file system, or infrastructure. | File viewing, git diff reading, log parsing, dependency scanning, drafting reports. | No |
| **Level 1** | **Local Workspace Drafting** | Active modification of local working tree artifacts. Changes remain local and uncommitted. | Editing code files, updating local config files, generating release notes, formatting store metadata drafts. | No (Local git diff review before commit) |
| **Level 2** | **Controlled CI / Internal Execution** | Invoking automated build pipelines, test suites, or internal preview deployments. | Triggering `eas build --profile preview`, running E2E test suites, deploying to TestFlight Internal / Play Internal Testing. | Conditional (Automated based on CI branch protection) |
| **Level 3** | **Production / Irreversible Execution** | Operations affecting production end-users, store listings, live credentials, or billing. | Store submission (`eas submit --profile production`), credential rotation, privacy manifest updates, OTA production updates. | **ALWAYS REQUIRED** (Unattended execution strictly forbidden) |

> **Important:** Agents must never be assigned Level 3 autonomy. Any action classified under Level 3 automatically triggers a mandatory [Human Approval Gate](human-approval.md).

---

# 3. Tool & Command Execution Boundaries

Bounded Autonomy requires strict control over tool capabilities and shell execution environments.

```text
                        Agent Tool Request
                                │
                                ▼
                   ┌──────────────────────────┐
                   │  Tool Permission Router  │
                   └────────────┬─────────────┘
                                │
       ┌────────────────────────┼────────────────────────┐
       ▼                        ▼                        ▼
[ Read-Only Tools ]    [ Whitelisted Shell ]     [ Forbidden Tools ]
(view_file, grep)     (eas build preview)     (git push --force)
       │                        │                        │
       ▼                        ▼                        ▼
 [ ALLOW & EXECUTE ]     [ RUN IN SANDBOX ]      [ BLOCK & ESCALATE ]
```

## 3.1 Command Whitelisting and Blacklisting

When an agent is granted shell execution privileges (e.g., via command runner tools), commands must be validated against strict execution rules:

### Explicitly Whitelisted Commands (Examples)

- `eas build --platform ios --profile preview --non-interactive`
- `eas build --platform android --profile preview --non-interactive`
- `npx expo-doctor`
- `npm test` / `yarn test`
- `git diff HEAD`
- `fastlane run validate_play_store_credentials`

### Strictly Blacklisted Commands (Always Blocked)

- `git push --force` or `git push origin main --force`
- `eas submit --platform all --profile production` (unattended)
- `rm -rf` or destructive file deletions outside scratch directories
- `eval` or executing dynamically fetched remote scripts (`curl ... | sh`)
- Commands containing raw API keys, secrets, or certificate passwords in CLI arguments
- Modifying production environment secret files (`.env.production`)

## 3.2 File System Sandboxing

Agents executing workspace modifications must be constrained by path sandboxing rules:

- **Writable Scope**: Restricted exclusively to the active repository directory or dedicated temporary scratch directories (`<appDataDir>/scratch/` or `.tmp/`).
- **Read-Only Scope**: Repository source files, documentation, and configuration manifests.
- **Forbidden Paths**: System directories (`/etc`, `C:\Windows`), user home credentials (`~/.ssh`, `~/.aws`, `~/.fastlane/readme`), and root-level environment secret storage outside the workspace.

---

# 4. Resource Limits & Execution Constraints

To prevent runaway agent execution, infinite retry loops, or excessive token usage, every bounded agent task must operate under strict quantitative limits.

```text
┌────────────────────────────────────────────────────────┐
│                   RESOURCE CONSTRAINTS                 │
├────────────────────────────────────────────────────────┤
│ Max Execution Timeout: 600 seconds                     │
│ Max Token Budget: 150,000 tokens per task              │
│ Max Tool Invocation Count: 30 calls per agent turn     │
│ Max Subagent Recursion Depth: 1 level                  │
│ Duplicate Tool Invocation Threshold: 3 identical calls │
└────────────────────────────────────────────────────────┘
```

## 4.1 Loop and Stagnation Detection

Agents encountering build or test failures may attempt the same failing command repeatedly without altering underlying context. Bounded Autonomy runners enforce loop limits:

1. **Duplicate Command Limit**: If an agent executes the exact same command line string three consecutive times with identical failure results, the runner terminates the loop and forces an evaluation error.
2. **No-Progress Termination**: If an agent completes 10 tool calls without generating new file edits, test execution results, or context outputs, execution is halted.
3. **Recursion Depth Cap**: Primary orchestrator agents ([Release Manager](../agents/release-manager.md)) can spawn specialized subagents, but subagents are forbidden from spawning deeper subagents (Max Depth = 1). Refer to [Subagents](subagents.md) for full context rules.

---

# 5. Machine-Readable Autonomy Policy Schema

Bounded Autonomy specifications must be defined in machine-readable YAML policy files passed directly to the agent runtime framework.

```yaml
autonomy_policy_id: "pol-release-auditor-v1"
version: "1.0"
agent_name: "ReleaseAuditor"
max_autonomy_level: 1 # Level 0 (Read-Only) + Level 1 (Local Workspace Edit)

sandbox_constraints:
  allowed_working_directory: "./"
  writable_paths:
    - "./docs/audits/*"
    - "./scratch/*"
  forbidden_paths:
    - "./.env*"
    - "~/.ssh/*"
    - "~/.fastlane/*"

tool_rules:
  allowed_tools:
    - "view_file"
    - "grep_search"
    - "list_dir"
    - "run_command"
  denied_tools:
    - "publish_to_store"
    - "modify_signing_credentials"

command_execution:
  enforce_whitelist: true
  allowed_commands:
    - "npm test"
    - "npx expo-doctor"
    - "git status"
    - "git diff"
  blocked_patterns:
    - "*push --force*"
    - "*rm -rf *"
    - "*eas submit*"

execution_limits:
  max_runtime_seconds: 300
  max_token_budget: 100000
  max_tool_invocations: 25
  max_subagent_depth: 1

human_approval_triggers:
  - trigger_on_p0_blocker: true
  - trigger_on_credential_access: true
  - trigger_on_file_delete: true
```

---

# 6. Emergency Interventions & Circuit Breakers

When an agent violates boundary conditions or exhibits anomalous behavior, safety systems must immediately isolate the workflow.

```text
                   [ Tool Execution Event ]
                              │
                              ▼
            ┌───────────────────────────────────┐
            │  Circuit Breaker Monitor Checked  │
            └─────────────────┬─────────────────┘
                              │
         ┌────────────────────┼────────────────────┐
         ▼                    ▼                    ▼
[ Policy Violation ]   [ Resource Exceeded ] [ Unexpected Error ]
         │                    │                    │
         ▼                    ▼                    ▼
[ Immediate Revoke ]   [ Terminate Task ]    [ Quarantine State ]
         │                    │                    │
         └────────────────────┼────────────────────┘
                              │
                              ▼
                 [ Log Security Incident ]
                              │
                              ▼
                 [ Notify Release Manager ]
```

## 6.1 Automated Circuit Breakers

Automated circuit breakers trigger an immediate kill signal under any of the following conditions:

- **Policy Violation**: The agent attempts to call a forbidden tool or execute a blacklisted command.
- **Resource Exhaustion**: The agent exceeds token, memory, or runtime thresholds.
- **Data Exfiltration Attempt**: The agent attempts an HTTP network request to an unapproved external IP or domain.
- **Secret Scanning Trigger**: The output of an agent tool call contains unmasked API keys, private keys, or passwords.

## 6.2 Manual Emergency Kill Switches

Human release operators must possess single-command capability to revoke agent execution tokens and kill all active subagent processes across local and CI environments.

---

# 7. Operational Verification Checklist

Before deploying an AI agent with bounded autonomy rules, verify the implementation against this checklist:

- [ ] **Policy Enforcement**: Autonomy levels (Level 0–2) are enforced by the execution runner, not by prompt text alone.
- [ ] **Level 3 Protection**: Production store submission, credential mutation, and privacy changes strictly require [Human Approval](human-approval.md).
- [ ] **Command Filtering**: Shell execution tools enforce strict command whitelisting; blacklisted patterns (`push --force`, `rm -rf`, raw secrets) are blocked.
- [ ] **Path Sandboxing**: File writing is restricted to specific repository paths; system and secret directories are inaccessible.
- [ ] **Resource Limits**: Max runtime, token budgets, tool call count limits, and recursion depth caps (Max Depth = 1) are active.
- [ ] **Loop Prevention**: Duplicate command detection automatically terminates infinite retry loops after 3 consecutive failures.
- [ ] **Circuit Breakers**: Secret exposure and policy violations trigger immediate task termination and audit logging.
- [ ] **Machine-Readable Policy**: The agent runtime consumes a validated YAML policy schema defining exact tool boundaries.

---

# 8. Related Documentation

### Architecture & Orchestration

- [Human Approval](human-approval.md) - Mandatory human review boundaries for production actions.
- [Agent Workflows](agent-workflows.md) - Multi-agent orchestration patterns and topology.
- [Tool Permissions](tool-permissions.md) - Detailed breakdown of read-only vs execution tool privileges.
- [Subagents](subagents.md) - Context boundaries and subagent delegation limits.
- [MCP Architecture](mcp.md) - Model Context Protocol tool integration standards.

### Agent Definitions & Security

- [Release Manager](../agents/release-manager.md) - Primary orchestrator role and subagent supervision.
- [Release Auditor](../agents/release-auditor.md) - Release candidate risk assessment agent.
- [Agent Permissions](../security/agent-permissions.md) - Security sandboxing and permission gating.
- [Prompt Injection Protection](../security/prompt-injection.md) - Isolating untrusted repository data.

### Core Release Lifecycle

- [Mobile Release Lifecycle](../../foundations/mobile-release-lifecycle.md) - Lifecycle phases from development to production monitoring.

---

# 9. Official Sources

For security guidelines, tool sandboxing standards, and platform safety standards, consult current official documentation:

### OWASP & AI Security

- OWASP Top 10 for LLM Applications: https://owasp.org/www-project-top-10-for-large-language-model-applications/
- NIST AI Risk Management Framework: https://www.nist.gov/itl/ai-risk-management-framework

### Platform & Tooling Guidance

- Apple Developer Program License Agreement & Security: https://developer.apple.com/support/terms/
- Google Play Developer Policy Center: https://support.google.com/googleplay/android-developer/answer/9876937
- Expo EAS Security & Permissions: https://docs.expo.dev/eas/security/

---

**Last verified:** August 13, 2026

Platform security guidelines, AI safety frameworks, CLI tool flags, and sandbox capabilities evolve. Re-verify current official documentation before adjusting agent autonomy boundaries or security policy rules.
