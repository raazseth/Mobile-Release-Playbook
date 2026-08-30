# Secret Protection

Secret Protection defines the security architecture, credential masking protocols, environment isolation, and leak prevention mechanisms designed to keep high-value mobile release secrets completely isolated from AI agent prompts, context windows, and execution logs.

Engineered in alignment with the **OWASP GenAI Security Project (2026)**—specifically addressing **LLM02: Sensitive Information Disclosure**—this document details how to protect Apple Distribution Certificates, Provisioning Profiles, Google Play Service Account JSONs, Fastlane Match passwords, API tokens, and CI/CD secrets from accidental exposure.

This guide is **not**:

- a guide to signing credential generation or certificate rotation mechanics (see [signing/](../../signing/README.md))
- a soft request for the AI to "forget" secrets pasted into prompts
- a substitute for managed secret storage (AWS Secrets Manager, HashiCorp Vault, GitHub Actions Secrets)

---

# 1. The Core Secret Protection Rule

Mobile release engineering requires handling high-value credentials that grant full administrative control over store listings, application binaries, and signing identities.

```text
┌────────────────────────────────────────────────────────┐
│                   THE CARDINAL RULE                    │
│                                                        │
│   Production secrets, signing keys, and service        │
│   account JSON credentials MUST NEVER be exposed       │
│   to AI agent prompts or LLM context windows.          │
└────────────────────────────────────────────────────────┘
```

The division of responsibilities in this repository is strictly defined:

- **`signing/`**: Documents the technical mechanics of certificate generation, Fastlane Match setup, Google Play App Signing, and credential storage (see [signing/](../../signing/README.md)).
- **`ai/security/`**: Defines rules and tooling gates to keep *AI agents* completely disconnected from raw secret values.

---

# 2. Secret Exposure Threat Vectors (OWASP LLM02)

AI agent workflows create unique vectors for accidental secret exposure:

```text
                               AGENT WORKFLOW THREAT VECTORS
                                             │
      ┌──────────────────────────────┬───────┴──────────────────────┐
      ▼                              ▼                              ▼
[ Tool Arguments Leak ]    [ Log & Console Leak ]       [ Prompt Context Leak ]
Passing raw tokens in      Reading `.env` files into   Pasting secrets into
CLI flags (eas login)      tool call responses         chat or issue prompts
```

1. **CLI Tool Arguments**: Passing raw secret strings directly as command-line flags (e.g., `eas login --token MY_RAW_TOKEN`). Command arguments are recorded in process trees and tool execution logs.
2. **File Reading & Search**: An agent invoking file search tools (`view_file`, `grep_search`) across repository paths and accidentally loading un-gitignored `.env` files or service account JSON files into prompt context windows.
3. **Build & Tool Output**: Build tools emitting raw API keys, private keys, or bearer tokens into standard output during execution.
4. **Prompt History Retention**: Pasting real credentials into interactive LLM chat sessions, exposing secrets to model training pipelines, session logs, or third-party API providers.

---

# 3. Opaque Token & Proxy Architecture

To allow AI agents to coordinate release tasks without accessing raw secrets, workflows must use an **Opaque Token Proxy Pattern**.

```text
┌────────────────────────────────────────────────────────┐
│                       AI AGENT                         │
│  Operates using Opaque Secret References ONLY.          │
│  "EXPO_TOKEN: [CONFIGURED IN CI]"                       │
└──────────────────────────┬─────────────────────────────┘
                           │
                 [ Opaque Reference Payload ]
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│               SECURE TOOL RUNNER / MCP                 │
│                                                        │
│  - Fetches Raw Secret from Managed Secret Vault        │
│  - Injects Secret into Isolated Execution Subprocess   │
│  - Redacts Secret Patterns from Process Output        │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│                 EXTERNAL STORE API / CI                │
│            Receives Authenticated Request              │
└──────────────────────────┬─────────────────────────────┘
```

## 3.1 Opaque Reference Schema

When an agent context requires verifying secret configuration status, the orchestrator supplies opaque boolean flags or placeholder references:

```yaml
# SAFE Context Representation for AI Agents (OWASP LLM02 Compliant)
secret_configuration_status:
  apple_distribution_certificate:
    configured: true
    exposed_to_agent: false
    expiration_date: "2027-06-30"
    vault_reference: "arn:aws:secretsmanager:us-east-1:123456789:secret:app-cert"

  google_play_service_account:
    configured: true
    exposed_to_agent: false
    service_account_email: "play-deployer@project.iam.gserviceaccount.com"
    vault_reference: "vault://ci/google-play-key"

  eas_build_token:
    configured: true
    exposed_to_agent: false
    token_mask: "sb_tok_...48a2"
```

---

# 4. Automated Log Masking & Sanitization

All tool call outputs, build logs, and console streams returned to AI agents must pass through automated secret sanitization filters:

```text
Raw Tool Output: "Error authenticating with key -----BEGIN PRIVATE KEY-----\nMIIEvgIBADANBgkqhkiG9w0BAQEFAASCBKgwggSkAgEAAoIBAQC3...\n-----END PRIVATE KEY-----"
                                             │
                                             ▼
                             [ Regex Redaction Filter ]
                                             │
                                             ▼
Sanitized Tool Output: "Error authenticating with key [REDACTED PRIVATE KEY]"
```

## 4.1 Redaction Patterns

Tool runners must execute regex pattern matching against all tool response strings, automatically masking matching content before returning context to the agent:

```regex
# High-Value Secret Redaction Patterns
Private Keys:     -----BEGIN (RSA|EC|OPENSSH|PRIVATE) KEY-----[\s\S]*?-----END \1 KEY-----
JWT / Bearer:     eyJ[A-Za-z0-9-_=]+\.[A-Za-z0-9-_=]+\.?[A-Za-z0-9-_.+/=]*
Expo Tokens:      eas-[a-zA-Z0-9_-]{32,}
AWS Keys:         (AKIA|ASIA)[0-9A-Z]{16}
Generic Keys:     (?i)(api_key|secret|password|token)\s*[:=]\s*['"][^'"]{8,}['"]
```

---

# 5. Emergency Secret Leak Incident Response

If a raw secret is accidentally exposed to an AI agent prompt or recorded in an agent execution log:

```text
                     [ Secret Leak Detected ]
                                │
                                ▼
            ┌───────────────────────────────────────┐
            │   IMMEDIATE INCIDENT RESPONSE PIPELINE│
            └───────────────────┬───────────────────┘
                                │
       ┌────────────────────────┼────────────────────────┐
       ▼                        ▼                        ▼
[ Revoke Credential ]   [ Purge Agent Logs ]     [ Issue Fresh Key ]
Immediately invalidate  Delete conversation &   Generate new key &
exposed key at source.  tool logs containing    update managed vault.
                        secret string.
```

### Action Steps

1. **Immediate Revocation**: Instantly invalidate the leaked key at the provider source (Apple Developer Console, Google Cloud Console, Expo Dashboard, GitHub Secrets). Do not wait to confirm if the key was misused.
2. **Purge Agent Trajectory Logs**: Delete agent conversation transcripts, tool response logs, and CI artifacts containing the raw secret string.
3. **Rotate & Re-Issue**: Generate a new credential, store it exclusively in managed vault storage, and update opaque references.
4. **Audit Scope**: Inspect git history and server logs to verify the secret was not committed to source control.

---

# 6. Operational Verification Checklist

- [ ] **OWASP LLM02 Aligned**: Sensitive information disclosure prevention active across all tool outputs.
- [ ] **Zero Secrets in Prompts**: No raw API keys, private keys, certificates, or passwords appear in system prompts, context files, or example code.
- [ ] **Placeholder Convention**: All documentation and examples strictly use standard placeholders (`YOUR_APP_STORE_CONNECT_KEY`, `YOUR_GOOGLE_PLAY_SERVICE_ACCOUNT`).
- [ ] **Opaque Reference Schema**: Agent context schemas represent secret presence as `configured: true / exposed_to_agent: false`.
- [ ] **Automated Redaction Active**: Tool execution runners filter stdout/stderr through regex sanitizers before passing context to LLM models.
- [ ] **Environment Isolation**: CI agent runners strip unneeded environment variables before executing subprocesses.

---

# 7. Related Documentation

### Security & Architecture

- [Agent Permissions](agent-permissions.md) - Security sandboxing and permission gating.
- [Destructive Actions](destructive-actions.md) - Protecting against unauthorized destructive operations.
- [Prompt Injection Protection](prompt-injection.md) - Shielding agents against malicious prompt injection.
- [Tool Permissions](../orchestration/tool-permissions.md) - Scoping read-only vs execution tool privileges.

---

# 8. Official Sources

### Credential & Secret Management

- OWASP GenAI Security Project (2026 Standard - LLM02 Sensitive Information Disclosure): https://genai.owasp.org/
- OWASP Secrets Management Cheat Sheet: https://cheatsheetseries.owasp.org/cheatsheets/Secrets_Management_Cheat_Sheet.html
- Apple App Store Connect API Key Security: https://developer.apple.com/documentation/appstoreconnectapi/creating_api_keys_for_app_store_connect_api
- Google Cloud Service Account Key Best Practices: https://cloud.google.com/iam/docs/best-practices-for-managing-service-account-keys

---

**Last verified:** August 13, 2026

