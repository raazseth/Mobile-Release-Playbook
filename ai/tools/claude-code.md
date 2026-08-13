# Using Claude Code for Mobile Release Engineering

This guide details how to use **Claude Code** (Anthropic's terminal-based agentic coding assistant) to execute mobile release engineering tasks—including repository auditing, static code inspection, configuration refactoring, build error troubleshooting, and changelog generation.

Engineered in alignment with **2026 Context Engineering standards** and the **OWASP GenAI Security Project (2026)**—specifically mitigating **LLM06: Excessive Agency** and **LLM05: Improper Output Handling**—it specifies how to configure Claude Code's terminal execution permissions, file sandboxing rules, and custom subagent sub-commands while enforcing [CLAUDE.md](../../.claude/CLAUDE.md) governance rules.

This guide is **not**:

- authorization for Claude Code to execute `git push --force` or autonomous store submissions
- a bypass around [Human Approval](../orchestration/human-approval.md) gates
- an un-monitored autonomous agent framework

---

# 1. Purpose & Architecture of Claude Code in this Repository

Claude Code operates directly inside the terminal, reading repository files, running shell commands, editing code, and creating git commits. In the Mobile Release Playbook, Claude Code acts as a primary pair-programming release engineer.

```text
┌────────────────────────────────────────────────────────┐
│                   CLAUDE CODE CLI                      │
│      (Terminal Agent Pair-Programming with Developer)  │
└──────────────────────────┬─────────────────────────────┘
                           │
             [ Consumes `.claude/CLAUDE.md` Rules ]
                           │
    ┌──────────────────────┼──────────────────────┐
    ▼                      ▼                      ▼
┌───────────────┐  ┌───────────────┐  ┌───────────────┐
│ File Viewing  │  │ Local Edits   │  │ Shell Runner  │
│ & Code Search │  │ & Formatting  │  │ (Whitelisted) │
└───────┬───────┘  └───────┬───────┘  └───────┬───────┘
        │                  │                  │
        ▼                  ▼                  ▼
 [ Read Repo ]      [ Update Docs/ ]   [ Run linters /]
 (Read diffs,       (Draft release     [  expo-doctor ]
  configs, logs)     notes, specs)
```

The core operating rule for Claude Code in this repository is defined in `CLAUDE.md`:

> **You are the gate. Validate placement, check duplication, match conventions, verify commands, source volatile claims, protect secrets, and enforce human approval boundaries on all release actions.**

## 1.1 Installation & CLI Command Features

Claude Code is installed natively on developer workstations or CI environments via the official installer:

```bash
# Official Native Installer Syntax
curl -fsSL https://claude.ai/install.sh | bash
```

Key CLI features include:

- **Directory Governance Integration**: Automatically loads `.claude/CLAUDE.md` guidelines at launch.
- **Built-in Slash Commands**: `/code-review` triggers automated PR review subagents; `/fork` creates isolated background sub-sessions for parallel task execution.
- **Permission Modes**: Configured via `auto` mode or explicit interactive permission prompts for shell command execution.

# 2. Tool Permissions & Execution Boundaries (OWASP LLM06 Mitigation)

Claude Code must be constrained using the [Bounded Autonomy](../orchestration/bounded-autonomy.md) framework:

| Action Category | Allowed Tools / Commands | Execution Policy |
|---|---|---|
| **Read-Only Inspection** | `view_file`, `grep_search`, `list_dir`, `git status`, `git diff` | Always permitted. |
| **Local Workspace Edits** | `replace_file_content`, `write_to_file` | Permitted for local drafting. Uncommitted edits. |
| **Whitelisted Shell Commands** | `npx expo-doctor`, `npm test`, `git log`, `npx pod-install` | Permitted in local working directory. |
| **Blacklisted Commands** | `git push --force`, `eas submit --profile production`, `rm -rf` | **STRICTLY BLOCKED**. Requires human approval. |

---

# 3. Context Engineering Layout in Terminal Workflows

When invoking Claude Code for release engineering tasks, structure commands to take advantage of prompt caching and XML tag isolation:

```bash
# Example: Running a Pre-Release Audit with Claude Code
claude "Read .claude/CLAUDE.md rules. Run a pre-release audit on current git diff. Follow the XML context format defined in ai/prompts/release-audit.md."
```

Claude Code evaluates task instructions through a step-by-step reasoning cycle before executing file edits or running terminal commands.

---

# 4. Working with `.claude/CLAUDE.md`

Claude Code automatically reads [CLAUDE.md](../../.claude/CLAUDE.md) at startup. When prompting Claude Code:

1. **Reference Repository Governance**: Instruct Claude Code to verify changes against `CLAUDE.md` rules.
2. **Preserve Documentation Conventions**: Ensure all created or edited markdown documents feature proper H1 titles, framing paragraphs, ASCII flow diagrams, horizontal rules, and `Last verified` source footers.
3. **Update Changelog**: When modifying playbook content or release scripts, ensure Claude Code updates `CHANGELOG.md` under `[Unreleased]`.

---

# 5. Operational Verification Checklist

- [ ] **OWASP LLM06 Aligned**: Terminal execution privileges are bounded; non-root user ID and blacklisted commands (`push --force`) enforced.
- [ ] **CLAUDE.md Loaded**: Claude Code recognizes and enforces repository governance guidelines.
- [ ] **Whitelisted Execution**: Shell command calls are restricted to approved test and diagnostic commands.
- [ ] **No Force Pushes**: Branch protection and command blacklists prevent `git push --force`.
- [ ] **Secrets Masked**: Credentials in `.env` files are not read into prompt transcripts or committed to git.
- [ ] **Human Sign-Off**: Production releases and credential modifications require explicit human approval.

---

# 6. Related Documentation

- [Claude Tool Guide](claude.md) - Web & API Claude workflows.
- [ChatGPT Tool Guide](chatgpt.md) - ChatGPT workflows.
- [Agent Workflows](../orchestration/agent-workflows.md) - Multi-agent topologies.
- [Bounded Autonomy](../orchestration/bounded-autonomy.md) - Autonomous execution boundaries.

---

# 7. Official Sources

- OWASP GenAI Security Project (2026 Standard): https://genai.owasp.org/
- Claude Code Documentation: https://docs.anthropic.com/
- Mobile Release Playbook Governance: [CLAUDE.md](../../.claude/CLAUDE.md)

---

**Last verified:** August 13, 2026
