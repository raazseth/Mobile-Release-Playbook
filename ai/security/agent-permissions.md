# Agent Permissions

Agent Permissions define the infrastructure security posture, sandboxing boundaries, and runtime execution constraints governing AI agents operating within mobile release engineering environments.

Engineered in alignment with the **OWASP GenAI Security Project (2026)**—specifically targeting **LLM06: Excessive Agency** and **LLM05: Improper Output Handling**—this document details how to configure host container sandboxes, process isolation, network egress filters, and system access levels to prevent AI agents from exceeding their operational scope.

This guide is **not**:

- a guide to signing credentials or certificate mechanics (see [signing/](../../signing/README.md))
- a soft prompt instruction list (permissions must be enforced deterministically by the runtime container)
- a substitute for [Tool Permissions](../orchestration/tool-permissions.md) or [Human Approval](../orchestration/human-approval.md)

---

# 1. Core Principles of Agent Security Permissions

AI agents executing in mobile development workflows require access to local files, build tools, and package managers. However, executing un-sandboxed agent processes on developer workstations or CI runners creates significant security risks—including accidental host file modification, secret exfiltration, and unauthorized network calls.

```text
┌────────────────────────────────────────────────────────┐
│                      AGENT TASK                        │
│          Target: Run Dependency Vulnerability Scan     │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│               ISOLATED CONTAINER SANDBOX               │
│                                                        │
│  - Non-Root Execution User (`uid: 1000`)               │
│  - Read-Only Mount of Source Directory                 │
│  - Environment Secret Stripping                        │
│  - Whitelisted Egress Proxy (API endpoints only)       │
│  - Seccomp Process Restriction (OWASP LLM06 Protection)│
└──────────────────────────┬─────────────────────────────┘
                           │
         ┌─────────────────┴─────────────────┐
         ▼                                   ▼
 [ Safe Tool Execution ]           [ System Access Blocked ]
 (Read `package.json`,             (Attempt to access `~/.ssh`
  scan dependency AST)              or write system files)
         │                                   │
         ▼                                   ▼
 [ Return Analysis Payload ]       [ Security Violation Alert ]
```

Security permissions adhere to four core principles:

1. **Least Privilege & Zero-Trust Sandbox (OWASP LLM06 Mitigation)**: Agents run in unprivileged, isolated containers (e.g., Docker, gVisor, or OS-level sandboxes) with no root access or administrative host privileges.
2. **Deterministic Infrastructure Enforcement**: Security limits are enforced by container runtime flags, seccomp profiles, and network firewalls—not by model system prompts.
3. **Environment Separation**: Permission tiers dynamically adjust based on the execution environment (Local Workstation vs CI Pipeline vs Production Approval Gateway).
4. **Environment Secret Masking (OWASP LLM02 Mitigation)**: Unused host environment variables and cloud provider credentials are completely stripped from the agent runtime environment.

---

# 2. Security Permission Tiers & Sandbox Boundaries

Agent execution environments are divided into three distinct sandbox security tiers:

| Sandbox Tier | Execution Context | File System Access | Network Egress | Process Capabilities |
|---|---|---|---|---|
| **Tier 0: Analysis Sandbox** | Local CLI / CI Auditor | Read-Only mount of project directory. Write restricted to ephemeral `/tmp` scratch. | Blocked (Offline) or Whitelisted Package Registry mirror only. | Unprivileged (`uid: 1000`). No subprocess execution except static linters. |
| **Tier 1: Workspace Sandbox** | Local Dev / CI Builder | Read-Write mount restricted to project directory. Host paths (`~/.ssh`, `~/.aws`) blocked. | Egress restricted to verified build APIs (`api.expo.dev`, `github.com`). | Unprivileged. Can execute whitelisted build CLI commands (`eas`, `fastlane`). |
| **Tier 2: Production Gate** | Isolated Approval Runner | Read-Only project mount. Sealed secret store access *only* upon verified human approval. | Egress restricted to store production APIs (`appstoreconnect.apple.com`). | Dedicated runner. Single-task execution, immediate container teardown post-execution. |

---

# 3. Network & System Scoping

Unconstrained network egress allows compromised dependencies or malicious prompt injections to exfiltrate repository data or secrets to external servers.

## 3.1 Egress Network Whitelisting

Agent execution sandboxes must enforce outbound network proxy rules, permitting connections exclusively to verified domain endpoints:

```text
Whitelisted Egress Endpoints:
├── api.expo.dev (EAS Build & Update status APIs)
├── github.com / api.github.com (Repository source & CI status)
├── registry.npmjs.org (Package metadata checking)
├── developer.apple.com / appstoreconnect.apple.com (Store APIs)
└── googleapis.com (Play Developer APIs)

Default Policy: REJECT ALL OTHER OUTBOUND HTTP/HTTPS AND RAW SOCKET CONNECTIONS
```

## 3.2 Host Environment Variable Isolation

Before spawning an agent process or subagent container, the runtime environment must sanitize environment variables:

- **Strip Host Credentials**: Unset `AWS_ACCESS_KEY_ID`, `SSH_AUTH_SOCK`, `GITHUB_TOKEN` (administrative tokens), and developer personal access tokens.
- **Provide Opaque Environment Tokens**: Supply only scoped, short-lived, read-only tokens specifically required for the task.

---

# 4. Machine-Readable Agent Security Policy Schema

Security container parameters must be defined in machine-readable YAML policy files consumed by CI/CD execution engines or container orchestrators.

```yaml
agent_security_policy:
  policy_id: "sec-pol-analysis-container-v1"
  owasp_compliance: "GenAI-2026-LLM06-LLM05"
  sandbox_tier: "Tier 0"
  container:
    image: "release-playbook-runner:latest"
    user: "1000:1000" # Non-root user
    read_only_root_filesystem: true
    capabilities_drop:
      - "ALL"

  mounts:
    - host_path: "./"
      container_path: "/workspace"
      read_only: true
    - host_path: "/tmp/agent-scratch"
      container_path: "/tmp"
      read_only: false

  network:
    egress_policy: "RESTRICTED"
    allowed_domains:
      - "api.expo.dev"
      - "github.com"
      - "registry.npmjs.org"

  environment_isolation:
    strip_environment_patterns:
      - "*KEY*"
      - "*SECRET*"
      - "*TOKEN*"
      - "*PASSWORD*"
    inject_scoped_variables:
      NODE_ENV: "production"
      AGENT_EXECUTION_MODE: "read_only"
```

---

# 5. Security Violations & Automated Isolation

If an agent process attempts to violate sandbox boundaries (e.g., executing a forbidden syscall, writing to a read-only mount, or contacting an unapproved domain):

1. **Immediate Process Termination**: The container engine issues an immediate `SIGKILL` to the agent subprocess.
2. **Container Teardown**: The execution container is immediately destroyed to prevent persistence.
3. **Security Alert Escalation**: An immutable security log entry is recorded, and the Release Manager is notified.
4. **Workflow Degradation**: The release workflow halts, marking the candidate status as `NOT READY`.

---

# 6. Operational Verification Checklist

- [ ] **OWASP LLM06 Aligned**: Agent process has zero root access and excessive execution agency is constrained.
- [ ] **Non-Root Execution**: Container runs under an unprivileged user ID (`uid: 1000`).
- [ ] **Read-Only Project Mount**: Source files are mounted as read-only for audit and analysis agents.
- [ ] **Host Paths Blocked**: Host credential directories (`~/.ssh`, `~/.aws`, `~/.fastlane`) are inaccessible inside the sandbox.
- [ ] **Egress Whitelisting**: Outbound network traffic is proxy-filtered to explicit official domain lists.
- [ ] **Environment Sanitized**: Administrative host keys and secrets are stripped from the container environment before execution.

---

# 7. Related Documentation

### Security & Architecture

- [Secret Protection](secret-protection.md) - Masking and isolating release credentials.
- [Destructive Actions](destructive-actions.md) - Gating non-reversible operations.
- [Prompt Injection Protection](prompt-injection.md) - Defensive prompt shielding patterns.
- [Tool Permissions](../orchestration/tool-permissions.md) - Tool classification tiers and role mappings.

### Signing & Credentials

- [Signing Overview](../../signing/README.md) - Certificate and keystore mechanics.
- [CI Secret Management](../../signing/security/ci-secrets.md) - Secure handling of secrets in CI/CD pipelines.

---

# 8. Official Sources

### Container & Infrastructure Security

- OWASP GenAI Security Project (2026 Standard): https://genai.owasp.org/
- Docker Security Best Practices: https://docs.docker.com/engine/security/
- CIS Benchmarks for Container Security: https://www.cisecurity.org/benchmark/docker

---

**Last verified:** August 13, 2026
