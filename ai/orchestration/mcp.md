# Model Context Protocol (MCP)

Model Context Protocol (MCP) defines the open, standardized client-server architecture connecting AI agents to repository tools, mobile build systems, release engineering APIs, and static analysis utilities.

This document details how MCP servers provide safe, structured tool interfaces and context providers for mobile release engineering workflows—enabling seamless integration with Expo EAS, Fastlane, Git, App Store Connect APIs, Google Play Developer APIs, and local development environments while incorporating the latest stateless protocol updates.

This guide is **not**:

- an unmonitored proxy granting agents unrestricted access to external APIs
- a replacement for deterministic security sandboxing or [Tool Permissions](tool-permissions.md)
- an authorization mechanism that bypasses mandatory [Human Approval](human-approval.md) gates

---

# 1. Role of MCP in Mobile Release Automation

Mobile release engineering requires interacting with diverse, platform-specific ecosystems: local git repositories, Expo/React Native configuration files, Fastlane toolchains, Apple App Store Connect APIs, Google Play Console APIs, and crash reporting systems (Sentry, Crashlytics).

Without a standardized protocol, connecting AI agents to these tools results in custom, brittle tool wrappers that struggle with context formatting, security sandboxing, and error recovery.

```text
┌────────────────────────────────────────────────────────┐
│                   MCP HOST / CLIENT                    │
│     (AI Agent Orchestrator / Release Manager)          │
└──────────────────────────┬─────────────────────────────┘
                           │
    [ Stateless JSON-RPC Protocol over HTTP / Stdio ]
    [ Header Routing: Mcp-Method / Mcp-Name         ]
                           │
    ┌──────────────────────┼──────────────────────┐
    ▼                      ▼                      ▼
┌───────────────┐  ┌───────────────┐  ┌───────────────┐
│ File & Git    │  │ Expo / EAS    │  │ Store API     │
│ MCP Server    │  │ MCP Server    │  │ MCP Server    │
└───────┬───────┘  └───────┬───────┘  └───────┬───────┘
        │                  │                  │
        ▼                  ▼                  ▼
 [ Local Repo ]     [ EAS Build API ]  [ App Store Connect ]
 (Source Code,      (Artifact status,  (Metadata status,
  Diffs, Logs)       Build profiles)    Review status)
```

MCP solves this fragmentation by establishing three standardized primitives:

1. **Tools**: Executable functions exposed by MCP servers to MCP client agents (e.g., `inspect_build_log`, `validate_app_config`).
2. **Resources**: Read-only data streams provided to agents (e.g., git diff outputs, build manifest files, store metadata).
3. **Prompts**: Standardized, reusable prompt templates provided by servers for specific tasks (e.g., pre-release security audit prompts).

---

# 2. Stateless Protocol Architecture & Header-Based Routing

The Model Context Protocol specification enforces a **stateless, request/response-based core architecture**. This model removes persistent session handshakes, enabling MCP servers to run efficiently in serverless environments, isolated containers, and behind standard cloud load balancers.

```text
┌────────────────────────────────────────────────────────┐
│                      MCP CLIENT                        │
│  Generates self-describing HTTP request                │
│  Headers: Mcp-Method: "tools/call", Mcp-Name: "grep"   │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│               CLOUD GATEWAY / LOAD BALANCER            │
│  Routes request using `Mcp-Method` / `Mcp-Name`        │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│                  STATELESS MCP SERVER                  │
│  Executes tool logic & returns JSON Schema response    │
└────────────────────────────────────────────────────────┘
```

Key features of the stateless MCP protocol:

- **Header-Based Routing**: Clients pass method and tool identifiers via explicit HTTP headers (`Mcp-Method: tools/call`, `Mcp-Name: inspect_build_log`), enabling microservice routing without parsing body payloads at the gateway layer.
- **Multi Round-Trip Requests (MRTR)**: Supports multi-step interaction patterns for complex operations (such as multi-step authentication or artifact chunking).
- **TypeScript & JSON Schema Definition**: Authoritative protocol schemas are defined in TypeScript, generating JSON Schema definitions for automated client validation.
- **Stateless Serverless Execution**: Server tools process self-contained payloads without relying on sticky sessions or stateful local memory.

---

# 3. MCP Server Taxonomy for Mobile Playbook

Mobile release workflows utilize specialized MCP servers. Each server exposes a tightly scoped set of tools and resources corresponding to a specific release engineering domain.

| MCP Server Name | Primary Domain | Exposed Tools & Resources | Security Sandbox Boundary |
|---|---|---|---|
| **File & Git Server** | Local repository inspection and diff analysis | `view_file`, `grep_search`, `list_dir`, `get_git_diff`, `get_commit_history` | Restricted strictly to working tree root. System directories forbidden. |
| **Expo / EAS Server** | Managed build and update status verification | `get_eas_build_status`, `list_build_profiles`, `inspect_eas_json`, `verify_update_channel` | Read-only status tools permitted. Submission tools require [Human Approval](human-approval.md). |
| **Fastlane Server** | Native iOS/Android signing and certificate validation | `validate_match_credentials`, `check_provisioning_expiry`, `inspect_fastfile` | Certificate read status tools permitted. Key generation or modification blocked. |
| **Store Management Server** | Store metadata and review status verification | `get_app_store_review_status`, `get_play_console_track_status`, `verify_privacy_nutrition_label` | Read-only status verification permitted. Binary submission blocked. |
| **Crash & Monitoring Server** | Production crash stack trace analysis | `fetch_sentry_issue`, `get_crashlytics_stacktrace`, `summarize_crash_spike` | Read-only log access. No user PII exposed to agent context. |

---

# 4. Security, Sandboxing, and Secret Isolation in MCP

Connecting AI agents to external APIs via MCP requires strict protocol-level security enforcement to prevent credential exposure, prompt injection attacks, and unauthorized tool calls.

```text
┌────────────────────────────────────────────────────────┐
│               AGENT TOOL CALL REQUEST                  │
│   `call_tool: eas_submit (platform: ios, prod)`        │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│             MCP SERVER SECURITY GATEWAY                │
│                                                        │
│  - Parses Request Tool Name & Arguments                │
│  - Checks Tool Permission Matrix (Tier 0 vs Tier 3)    │
│  - Detects Tier 3 High-Impact Operation                │
│  - Verifies Human Approval Record Presence             │
└──────────────────────────┬─────────────────────────────┘
                           │
         ┌─────────────────┴─────────────────┐
         ▼                                   ▼
 [ Approval Record Valid ]          [ Approval Missing ]
 Execute EAS API Call via            Reject Tool Execution
 Server Credentials                  Return Protocol Error
```

## 4.1 Secret Isolation at the Server Boundary

MCP servers act as secure isolation proxies for administrative credentials:

- **Secrets Never Passed to Agent**: High-value credentials (App Store Connect API Keys, Google Play Service Account JSONs, Fastlane Match Passwords) reside strictly on the MCP server host environment.
- **Agent Interacts via Opaque Handles**: The AI agent sees tools such as `check_app_store_status()`. The agent *never* sees or handles the underlying JWT tokens or private keys used by the MCP server to authenticate with Apple or Google.

---

# 5. Machine-Readable MCP Configuration Schema

MCP clients consume standardized configuration files (e.g., `mcp-config.json`) defining available servers, tool permissions, and environment bounds.

```json
{
  "mcpServers": {
    "file-git-server": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "./"],
      "env": {
        "NO_COLOR": "1"
      },
      "permissions": {
        "read_only": true,
        "allowed_paths": ["./src", "./docs", "./package.json", "./app.json"]
      }
    },
    "eas-build-server": {
      "command": "node",
      "args": ["./scripts/mcp/eas-server.js"],
      "env": {
        "EXPO_TOKEN": "env:EXPO_READ_ONLY_TOKEN"
      },
      "permissions": {
        "allowed_tools": ["get_eas_build_status", "list_build_profiles"],
        "blocked_tools": ["trigger_production_build", "publish_ota_update"]
      }
    },
    "store-status-server": {
      "command": "node",
      "args": ["./scripts/mcp/store-status-server.js"],
      "env": {
        "APP_STORE_CONNECT_KEY_ID": "env:ASC_KEY_ID",
        "PLAY_CONSOLE_JSON_KEY": "env:GOOGLE_PLAY_KEY"
      },
      "permissions": {
        "read_only": true,
        "require_human_approval_for_write": true
      }
    }
  }
}
```

---

# 6. Operational Verification Checklist

- [ ] **Stateless Protocol Alignment**: MCP server integration uses the stateless request/response model with self-describing payloads and header routing (`Mcp-Method`, `Mcp-Name`).
- [ ] **Secret Isolation**: MCP servers consume API tokens locally from host environment variables; secrets are never passed in prompt contexts or tool arguments.
- [ ] **Tool Scoping**: MCP configuration files (`mcp-config.json`) explicitly restrict available tools and paths using least-privilege principles.
- [ ] **Tier 3 Block**: Production submission, key rotation, and OTA publishing tools exposed via MCP require a verified [Human Approval Record](human-approval.md).
- [ ] **Schema Compliance**: Tool parameters and resource schemas validate against strict JSON Schema definitions automatically generated from TypeScript sources.

---

# 7. Related Documentation

### Architecture & Orchestration

- [Agent Workflows](agent-workflows.md) - Multi-agent orchestration patterns and topology.
- [Human Approval](human-approval.md) - Mandatory human review boundaries for production actions.
- [Bounded Autonomy](bounded-autonomy.md) - Operational rules for constraining agent freedom.
- [Tool Permissions](tool-permissions.md) - Detailed breakdown of read-only vs execution tool privileges.
- [Subagents](subagents.md) - Subagent delegation boundaries and context isolation.

---

# 8. Official Sources

### Model Context Protocol (MCP)

- Model Context Protocol (MCP) Specification: https://modelcontextprotocol.io/
- MCP GitHub Organization & Reference Servers: https://github.com/modelcontextprotocol
- OWASP Top 10 for LLM Applications (Insecure Plugin Design - LLM07): https://owasp.org/www-project-top-10-for-large-language-model-applications/

---

**Last verified:** August 13, 2026

Model Context Protocol specifications, stateless server implementation SDKs, Expo EAS APIs, and App Store Connect API capabilities evolve. Re-verify current official documentation before updating MCP server configurations or transport schemas.

