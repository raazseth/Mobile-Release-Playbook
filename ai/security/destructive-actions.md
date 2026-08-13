# Destructive Actions

Destructive Actions define the taxonomy, interception mechanisms, protection rules, and safety boundaries designed to prevent AI agents from executing non-reversible or high-impact operations within mobile software repositories and distribution channels.

Engineered in alignment with the **OWASP GenAI Security Project (2026)**—specifically addressing **LLM05: Improper Output Handling** and **LLM06: Excessive Agency**—this document details how to classify destructive operations (such as branch deletions, force pushes, certificate revocations, production OTA updates, store binary submissions, and database wipes) and enforce non-bypassable safety gates.

This guide is **not**:

- a recommendation to disable developer administration tools
- a prompt-only warning policy (destructive action protection must be enforced at the runtime/tool-gateway layer)
- a temporary policy that can be bypassed during emergency hotfix operations

---

# 1. Defining Destructive & Irreversible Actions

In mobile release engineering, certain actions carry permanent or widespread operational consequences. Unlike local software edits that can be reverted via `git checkout`, destructive actions alter remote production state, terminate platform developer assets, or publish unverified code directly to end-user hardware.

```text
┌────────────────────────────────────────────────────────┐
│                   AGENT TOOL REQUEST                   │
│       Target: Execute `git push origin main --force`   │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│             DESTRUCTIVE ACTION INTERCEPTOR             │
│                                                        │
│  - Evaluates Tool Command against Destructive Matrix   │
│  - Detects Category: CATASTROPHIC DESTRUCTIVE ACTION   │
│  - Intercepts Execution & Blocks Subprocess            │
│  - Requires Verified Human Authorization Record       │
└──────────────────────────┬─────────────────────────────┘
                           │
         ┌─────────────────┴─────────────────┐
         ▼                                   ▼
 [ Approval Missing: BLOCKED ]     [ Verified Approval Record ]
 Execution HALTED.                  Execute Scoped Operation
 Security Log Created.               with Mandatory Audit Trail.
```

Destructive actions are categorized into three risk levels:

| Destructive Level | Impact Category | Examples | Reversibility | Protection Required |
|---|---|---|---|---|
| **Level 1: Severe** | **Workspace / Local Destruction** | Deleting uncommitted local source files, wiping local build caches (`rm -rf node_modules`), force-cleaning git working tree (`git clean -fdx`). | Moderate (Local work lost; recoverable if committed) | File path sandboxing & soft-delete backups. |
| **Level 2: Major** | **Repository / CI Destruction** | Force-pushing remote branches (`git push --force`), deleting remote release tags, deleting CI pipeline secrets, modifying branch protection rules. | High (Requires git history repair or secret re-issuance) | Tool command blacklisting & repository protection rules. |
| **Level 3: Catastrophic** | **Production & Platform Destruction** | Submitting production store binaries, publishing production OTA updates, revoking signing certificates, deleting store listings, wiping database tables. | Critical / Irreversible (Immediate impact on end-users or developer account status) | **STRICTLY BLOCKED**. Requires non-bypassable [Human Approval](human-approval.md). |

---

# 2. Destruction Prevention Architecture

To guarantee safety, destructive action protections are implemented across three deterministic layers:

## 2.1 Layer 1: Repository & Platform Controls

Standard git branch protection rules and cloud infrastructure permissions act as the primary line of defense:

- **Branch Protection**: Block `--force` pushes and un-reviewed commits to `main` and `release/*` branches.
- **Role-Based Access Control (RBAC)**: Service accounts and CI tokens used by agents lack administrative rights to delete certificates or modify store account settings.

## 2.2 Layer 2: Tool Runner Interception Gateway (OWASP LLM05 Mitigation)

The tool execution layer inspects every command and API call before execution:

- **Pattern Matching**: Matches command strings against a strict blacklist of destructive patterns (`*push --force*`, `*credential:delete*`, `*match nuke*`).
- **Path Verification**: Verifies file deletion targets are within designated ephemeral scratch directories (`/tmp/scratch/`).

## 2.3 Layer 3: Mandatory Human Authorization Gate (OWASP LLM06 Mitigation)

Any operation classified as Level 3 (Catastrophic) pauses workflow execution and generates a human review request.

```text
AI Agent Request ──→ [ Destructive Gate Interceptor ] ──→ [ Human Review Gateway ]
                                                                 │
                                                      ┌──────────┴──────────┐
                                                      ▼                     ▼
                                                 [ Approved ]          [ Rejected ]
                                                      │                     │
                                                      ▼                     ▼
                                             [ Execute Command ]    [ Cancel Action ]
```

---

# 3. Non-Bypassable Destructive Operations

The following operations are classified as strictly destructive and must **never** be executed autonomously by an AI agent:

1. Executing force pushes (`git push --force` or `git push -f`) to any remote git branch.
2. Revoking or deleting Apple Distribution Certificates, Provisioning Profiles, or Android Keystores.
3. Executing credential destruction utilities (e.g., `fastlane match nuke`).
4. Publishing production Over-The-Air (OTA) JavaScript or asset updates (e.g., `eas update --branch production`).
5. Submitting application binaries to Apple App Store Connect or Google Play Console production tracks.
6. Modifying or deleting production database tables, storage buckets, or production backend configurations.
7. Deleting store listings, app versions, or store metadata entries.
8. Modifying organization team roles, developer account ownership, or financial/banking configurations.

---

# 4. Command & Tool Blacklisting Schema

Destructive action rules must be configured in machine-readable YAML policy files consumed by command execution gateways and Model Context Protocol (MCP) servers.

```yaml
destructive_action_policy:
  policy_id: "sec-pol-destructive-gating-v1"
  owasp_compliance: "GenAI-2026-LLM05-LLM06"
  enforcement_mode: "STRICT_BLOCK"

  blacklisted_command_patterns:
    - regex: "git\\s+push.*(--force|-f)"
      description: "Force pushing remote git branches is strictly prohibited."
      severity: "CATASTROPHIC"

    - regex: "fastlane\\s+match\\s+nuke"
      description: "Destructive certificate nuke utility is strictly prohibited."
      severity: "CATASTROPHIC"

    - regex: "eas\\s+credentials?:(delete|revoke)"
      description: "Revoking or deleting signing credentials is strictly prohibited."
      severity: "CATASTROPHIC"

    - regex: "rm\\s+(-[a-zA-Z]*r[a-zA-Z]*f|-[a-zA-Z]*f[a-zA-Z]*r)\\s+(/|~|\\./)"
      description: "Recursive forced deletion of root or project directory is strictly prohibited."
      severity: "SEVERE"

  file_system_protection:
    soft_delete_only: true
    trash_directory: "./scratch/.trash/"
    protected_paths:
      - "./ios/Pods/*"
      - "./android/.gradle/*"
      - "./.git/*"
      - "./*certificate*"
      - "./*keystore*"
```

---

# 5. Emergency Incident Response & Recovery

If an unauthorized or accidental destructive command attempt occurs:

1. **Immediate Execution Interception**: The tool gateway blocks command execution and revokes active agent session tokens.
2. **State Preservation**: The current git tree status and execution logs are captured for audit analysis.
3. **Security Alert Trigger**: An automated alert is dispatched to the Lead Security Engineer and Release Manager.
4. **Audit Investigation**: Security teams review the agent trajectory log to determine if the attempt was caused by model hallucination or indirect prompt injection.

---

# 6. Operational Verification Checklist

- [ ] **OWASP LLM05 Compliant**: Downstream tool runner output handling intercepts destructive shell command execution.
- [ ] **Branch Protection Active**: Remote git branches (`main`, `release/*`) enforce branch protection blocking force pushes.
- [ ] **Command Blacklist Configured**: Interceptor gateways enforce regex blacklists for `push --force`, `match nuke`, and `rm -rf`.
- [ ] **Soft-Delete Enforced**: Local workspace file tools use soft-delete mechanisms moving files to scratch trash rather than permanent deletion.
- [ ] **Level 3 Gate Active**: Store submissions, OTA updates, and credential deletions require verified [Human Approval Records](../orchestration/human-approval.md).

---

# 7. Related Documentation

### Security & Governance

- [Agent Permissions](agent-permissions.md) - Security sandboxing and container isolation.
- [Secret Protection](secret-protection.md) - Isolating signing keys and API secrets.
- [Prompt Injection Protection](prompt-injection.md) - Protecting agents against malicious instruction overrides.
- [Bounded Autonomy](../orchestration/bounded-autonomy.md) - Operational rules for constraining agent freedom.

---

# 8. Official Sources

### Security & Infrastructure Safety

- OWASP GenAI Security Project (2026 Standard): https://genai.owasp.org/
- GitHub Branch Protection Rules: https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/about-protected-branches

---

**Last verified:** August 13, 2026
