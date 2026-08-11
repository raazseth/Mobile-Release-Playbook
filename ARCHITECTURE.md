# Architecture

## 1. Purpose

The Mobile Release Playbook is an open-source knowledge and execution system for taking a mobile application from "built" to "safely released, published, monitored, and updated."

It is primarily focused on **Expo and React Native** in the initial implementation because those are the project's strongest first-party areas of knowledge.

The architecture is intentionally **framework-extensible**. The project should make it easy for contributors to add Flutter, native iOS, native Android, or other mobile stacks later without changing the core structure.

This is a **playbook, not a SaaS release-management platform**.

It does not require a backend, database, API gateway, microservices, Kubernetes cluster, or centralized release service.

The repository is the product.

---

## 2. Core Model

The playbook is organized around the mobile release lifecycle:

```text
App Built
   ↓
Release Readiness
   ↓
Configuration & Signing
   ↓
Testing
   ↓
Store Preparation
   ↓
Production Build
   ↓
TestFlight / Play Testing
   ↓
Submission
   ↓
Review / Rejection
   ↓
Production Release
   ↓
Monitoring
   ↓
Update / Hotfix
```

AI operates across this lifecycle as an execution and reasoning layer:

```text
                    AI
                     │
       ┌─────────────┼─────────────┐
       ↓             ↓             ↓
     Audit         Build          QA
       ↓             ↓             ↓
    Metadata      Debug         Release
       ↓             ↓             ↓
   Rejection     Automate       Monitor
```

The goal is not to automate everything.

The goal is to help developers use AI tools and agents to remove repetitive work while keeping sensitive, destructive, or irreversible actions under appropriate human control.

---

## 3. Architectural Principles

### 3.1 Documentation First

The primary artifact is practical, maintained documentation.

Guides should help a developer answer:

> "What do I do next?"

Every important workflow should include:

- prerequisites
- exact steps
- commands where useful
- expected results
- common failures
- verification
- official sources

### 3.2 Framework-Agnostic Core

The `core` knowledge should not assume Expo, React Native, Flutter, or native tooling.

Examples:

- release readiness
- store submission concepts
- versioning
- testing strategy
- privacy
- store metadata
- rejection handling
- post-release operations

Framework-specific implementation belongs under `frameworks/`.

### 3.3 Expo + React Native First

The initial implementation focuses on:

```text
frameworks/
├── expo/
└── react-native/
```

These are first-class, maintained paths.

Other frameworks are community-extensible rather than blocked by the architecture.

### 3.4 Community Extensibility

Contributors should be able to add:

```text
frameworks/
├── flutter/
├── ios/
├── android/
└── other-framework/
```

without rewriting the core playbook.

Framework additions should reuse existing core concepts wherever possible.

### 3.5 AI as an Accelerator

AI is treated as a practical engineering tool, not as a separate product.

The playbook documents:

- ChatGPT
- Claude
- Claude Code
- Codex
- GitHub Copilot
- GitHub Agents
- MCP
- agent workflows
- reusable prompts
- evaluation
- safe automation

AI-generated output must be verified before being trusted.

### 3.6 Automation Must Be Bounded

Automation should progress from:

```text
Explain
  ↓
Generate
  ↓
Inspect
  ↓
Execute
  ↓
Automate
  ↓
Human Approval
  ↓
Production
```

The more irreversible the action, the stronger the approval boundary should be.

Never encourage developers to expose signing keys, store credentials, production secrets, or unrestricted production access to an AI agent.

### 3.7 Official Sources Are the Authority

Apple and Google policies, APIs, console behavior, and submission requirements change.

The playbook should explain the workflow, but official platform documentation remains authoritative.

Volatile guidance should include a source reference and verification information.

### 3.8 Smallest Useful System

The repository should not become a framework, orchestration platform, or enterprise release-management product unless a real contributor use case requires it.

Prefer:

- Markdown
- scripts
- checklists
- reusable prompts
- CI workflows
- small validation utilities

Avoid unnecessary infrastructure.

---

# 4. Repository Architecture

```text
mobile-release-playbook/
│
├── README.md
├── LICENSE
├── CONTRIBUTING.md
├── CHANGELOG.md
│
├── foundations/
│   ├── mobile-release-lifecycle.md
│   ├── release-environments.md
│   ├── project-configuration.md
│   ├── identifiers.md
│   ├── versioning.md
│   ├── device-support.md
│   └── dependency-management.md
│
├── store-accounts/
│   ├── apple-developer.md
│   ├── app-store-connect.md
│   ├── google-play-console.md
│   ├── organization-accounts.md
│   ├── roles-and-permissions.md
│   ├── agreements.md
│   ├── banking-and-tax.md
│   └── account-recovery.md
│
├── signing/
│   ├── ios/
│   │   ├── certificates.md
│   │   ├── provisioning-profiles.md
│   │   ├── distribution.md
│   │   ├── api-keys.md
│   │   └── key-rotation.md
│   ├── android/
│   │   ├── keystore.md
│   │   ├── upload-key.md
│   │   ├── play-app-signing.md
│   │   └── key-rotation.md
│   └── security/
│       ├── secret-storage.md
│       ├── ci-secrets.md
│       ├── access-control.md
│       └── recovery.md
│
├── testing/
│   ├── unit.md
│   ├── integration.md
│   ├── e2e.md
│   ├── device-testing.md
│   ├── device-matrix.md
│   ├── ios.md
│   ├── android.md
│   ├── accessibility.md
│   ├── localization.md
│   ├── performance.md
│   ├── network.md
│   ├── offline.md
│   ├── deep-links.md
│   ├── push-notifications.md
│   ├── payments.md
│   ├── upgrade-migrations.md
│   └── release-smoke-tests.md
│
├── pre-release/
│   ├── release-readiness.md
│   ├── dependency-audit.md
│   ├── security-audit.md
│   ├── privacy-audit.md
│   ├── permissions-audit.md
│   ├── performance-audit.md
│   ├── accessibility-audit.md
│   ├── metadata-audit.md
│   ├── asset-audit.md
│   ├── configuration-audit.md
│   └── final-release-checklist.md
│
├── publishing/
│   ├── ios/
│   │   ├── app-store-connect.md
│   │   ├── build-upload.md
│   │   ├── testflight.md
│   │   ├── metadata.md
│   │   ├── screenshots.md
│   │   ├── app-review.md
│   │   └── production-release.md
│   ├── android/
│   │   ├── play-console.md
│   │   ├── app-bundle.md
│   │   ├── internal-testing.md
│   │   ├── closed-testing.md
│   │   ├── metadata.md
│   │   ├── screenshots.md
│   │   ├── app-review.md
│   │   └── production-release.md
│   └── cross-platform/
│       ├── metadata.md
│       ├── assets.md
│       ├── release-notes.md
│       └── submission.md
│
├── store-operations/
│   ├── app-listing.md
│   ├── metadata.md
│   ├── screenshots.md
│   ├── app-previews.md
│   ├── localization.md
│   ├── privacy-information.md
│   ├── content-ratings.md
│   ├── data-safety.md
│   ├── app-review.md
│   ├── rejection-handling.md
│   └── resubmission.md
│
├── monetization/
│   ├── subscriptions.md
│   ├── in-app-purchases.md
│   ├── consumables.md
│   ├── non-consumables.md
│   ├── pricing.md
│   ├── offers.md
│   ├── entitlements.md
│   ├── receipt-validation.md
│   └── subscription-lifecycle.md
│
├── privacy-compliance/
│   ├── privacy-policy.md
│   ├── data-collection.md
│   ├── permissions.md
│   ├── tracking.md
│   ├── analytics.md
│   ├── advertising.md
│   ├── data-retention.md
│   ├── data-deletion.md
│   ├── children.md
│   ├── health-data.md
│   └── regional-requirements.md
│
├── integrations/
│   ├── push-notifications/
│   ├── deep-links/
│   ├── universal-links/
│   ├── app-links/
│   ├── oauth/
│   ├── maps/
│   ├── payments/
│   ├── analytics/
│   ├── crash-reporting/
│   ├── attribution/
│   ├── camera/
│   ├── location/
│   └── background-processing/
│
├── release-engineering/
│   ├── build-systems.md
│   ├── release-configurations.md
│   ├── fastlane/
│   ├── eas/
│   ├── github-actions/
│   ├── app-store-connect-api.md
│   ├── google-play-api.md
│   ├── environment-management.md
│   └── release-pipelines.md
│
├── release-strategy/
│   ├── release-trains.md
│   ├── beta-testing.md
│   ├── staged-rollouts.md
│   ├── phased-release.md
│   ├── feature-flags.md
│   ├── kill-switches.md
│   ├── hotfixes.md
│   └── emergency-release.md
│
├── post-release/
│   ├── monitoring.md
│   ├── crash-analysis.md
│   ├── performance.md
│   ├── analytics.md
│   ├── reviews-and-ratings.md
│   ├── rollout-monitoring.md
│   ├── incident-response.md
│   ├── rollback.md
│   ├── hotfix.md
│   ├── version-recall.md
│   └── release-retrospective.md
│
├── ai/
│   ├── README.md
│   ├── getting-started/
│   │   ├── when-to-use-ai.md
│   │   └── when-not-to-use-ai.md
│   ├── tools/
│   │   ├── chatgpt.md
│   │   ├── claude.md
│   │   ├── claude-code.md
│   │   ├── codex.md
│   │   ├── github-copilot.md
│   │   └── github-agents.md
│   ├── agents/
│   │   ├── release-auditor.md
│   │   ├── qa-agent.md
│   │   ├── debugging-agent.md
│   │   ├── metadata-agent.md
│   │   ├── privacy-auditor.md
│   │   ├── security-auditor.md
│   │   ├── dependency-auditor.md
│   │   ├── rejection-analyzer.md
│   │   └── release-manager.md
│   ├── workflows/
│   │   ├── release-audit.md
│   │   ├── ai-qa.md
│   │   ├── metadata-generation.md
│   │   ├── release-notes.md
│   │   ├── rejection-analysis.md
│   │   ├── debugging.md
│   │   └── release-preparation.md
│   ├── orchestration/
│   │   ├── agent-workflows.md
│   │   ├── subagents.md
│   │   ├── mcp.md
│   │   ├── tool-permissions.md
│   │   ├── human-approval.md
│   │   └── bounded-autonomy.md
│   ├── prompts/
│   │   ├── release-audit.md
│   │   ├── qa.md
│   │   ├── debugging.md
│   │   ├── metadata.md
│   │   ├── rejection-analysis.md
│   │   └── release-notes.md
│   ├── context/
│   │   ├── repository-context.md
│   │   ├── release-context.md
│   │   └── store-context.md
│   ├── evals/
│   │   ├── agent-evaluation.md
│   │   ├── regression-tests.md
│   │   ├── hallucination-tests.md
│   │   └── workflow-evaluation.md
│   └── security/
│       ├── prompt-injection.md
│       ├── secret-protection.md
│       ├── agent-permissions.md
│       └── destructive-actions.md
│
├── frameworks/
│   ├── react-native/
│   │   ├── README.md
│   │   ├── ios-release.md
│   │   ├── android-release.md
│   │   ├── signing.md
│   │   ├── fastlane.md
│   │   ├── eas.md
│   │   └── common-failures.md
│   ├── expo/
│   │   ├── README.md
│   │   ├── development-builds.md
│   │   ├── build.md
│   │   ├── submit.md
│   │   ├── eas.md
│   │   ├── app-config.md
│   │   ├── credentials.md
│   │   ├── updates.md
│   │   ├── ci-cd.md
│   │   └── common-failures.md
│   └── community/
│       ├── README.md
│       └── adding-a-framework.md
│
├── troubleshooting/
│   ├── build-fails.md
│   ├── signing-fails.md
│   ├── archive-fails.md
│   ├── upload-fails.md
│   ├── ci-fails.md
│   ├── review-rejected.md
│   ├── metadata-rejected.md
│   ├── privacy-rejected.md
│   ├── crash-after-release.md
│   ├── push-notifications-broken.md
│   ├── deep-links-broken.md
│   ├── payments-broken.md
│   └── app-not-visible.md
│
├── checklists/
│   ├── first-release.md
│   ├── ios.md
│   ├── android.md
│   ├── cross-platform.md
│   ├── pre-release.md
│   ├── production.md
│   └── post-release.md
│
├── templates/
│   ├── release-checklist.md
│   ├── release-plan.md
│   ├── release-notes.md
│   ├── app-store-description.md
│   ├── play-store-description.md
│   ├── privacy-questionnaire.md
│   ├── rejection-response.md
│   ├── incident-report.md
│   └── changelog.md
│
├── scripts/
│   ├── release-audit/
│   ├── version-bump/
│   ├── build-validation/
│   ├── metadata-validation/
│   └── store-validation/
│
├── sources/
│   ├── apple.md
│   ├── google.md
│   ├── react-native.md
│   ├── expo.md
│   ├── fastlane.md
│   └── tooling.md
│
├── governance/
│   ├── documentation-style.md
│   ├── source-verification.md
│   ├── versioning.md
│   ├── maintenance.md
│   └── contribution-guide.md
│
└── .github/
    ├── workflows/
    ├── ISSUE_TEMPLATE/
    ├── pull_request_template.md
    └── dependabot.yml
```

---

# 5. Layer Responsibilities

## `foundations/`

Framework-independent concepts that every mobile release needs.

This layer explains the lifecycle, environments, identifiers, versions, supported devices, and dependency decisions.

It should answer:

> "What needs to be true before I start publishing?"

---

## `store-accounts/`

Account-level setup required before publishing.

It covers:

- Apple Developer
- App Store Connect
- Google Play Console
- organization ownership
- team access
- roles
- agreements
- financial setup
- account recovery

This is deliberately separate from app-level publishing.

---

## `signing/`

Everything related to proving that a release is authorized to represent the application.

It covers:

- iOS certificates
- provisioning
- Android keystores
- Play App Signing
- API keys
- CI secrets
- rotation
- recovery

Signing credentials are production assets and must be handled with least privilege and secure storage.

---

## `testing/`

Release validation before store submission.

Testing should cover more than functional correctness:

- device compatibility
- network conditions
- offline behavior
- permissions
- deep links
- push notifications
- payments
- accessibility
- localization
- performance
- upgrade behavior

The objective is to catch failures that only appear in a production-like environment.

---

## `pre-release/`

The final release gate.

It combines technical and store readiness:

```text
Code
+ Configuration
+ Security
+ Privacy
+ Permissions
+ Performance
+ Metadata
+ Assets
        ↓
Release Ready
```

This is also the primary input for AI release-audit workflows.

---

## `publishing/`

The actual store submission process.

This is platform-specific:

```text
publishing/ios/
publishing/android/
```

The content should be procedural and actionable.

A developer should be able to follow the guide from a completed production build to a submitted release.

---

## `store-operations/`

Ongoing management of the store listing and review lifecycle.

It covers:

- listing
- metadata
- screenshots
- previews
- localization
- ratings
- privacy declarations
- data safety
- review
- rejection
- resubmission

This is intentionally separate from `publishing/` because submission is an event; store operations continue for the lifetime of the app.

---

## `monetization/`

Store-based commercial functionality.

The documentation should distinguish:

- client-side purchase UX
- platform purchase systems
- backend entitlement state
- receipt or transaction validation
- subscription lifecycle

The client must not be treated as the authoritative source for valuable entitlements.

---

## `privacy-compliance/`

Data and platform privacy requirements.

This section should explain what data is collected, why it is collected, how permissions and tracking work, and what platform declarations are required.

It should link to current official requirements rather than trying to permanently mirror changing policies.

---

## `integrations/`

Common mobile capabilities that frequently create release-specific failures.

Examples:

- push notifications
- deep links
- universal links
- Android App Links
- OAuth
- payments
- analytics
- crash reporting
- attribution
- camera
- location
- background processing

Each integration should explain both setup and release-specific verification.

---

## `release-engineering/`

The automation layer.

It connects the application build process to release infrastructure:

```text
Source
  ↓
CI
  ↓
Build
  ↓
Validation
  ↓
Store Upload
  ↓
Submission
```

Expo users should find EAS workflows here.

React Native users should find Fastlane, native build, and CI workflows here.

---

## `release-strategy/`

How to safely release changes after the first successful submission.

Topics include:

- beta releases
- staged rollouts
- phased releases
- feature flags
- kill switches
- hotfixes
- emergency releases

The goal is to reduce blast radius when a release is wrong.

---

## `post-release/`

Publishing is not the end of the lifecycle.

This layer covers:

```text
Release
  ↓
Observe
  ↓
Detect
  ↓
Diagnose
  ↓
Mitigate
  ↓
Fix
  ↓
Release Again
```

Monitoring should focus on user impact:

- crashes
- failed flows
- performance
- rollout health
- reviews
- ratings
- incidents

---

# 6. AI Architecture

The AI section is not a collection of generic prompt examples.

It is organized around real release work.

## Tools

```text
ai/tools/
```

Documents how different AI coding tools can be used.

The guidance should remain tool-aware without making the project dependent on a single vendor.

## Agents

```text
ai/agents/
```

Reusable agent roles for concrete jobs.

Examples:

```text
Release Auditor
QA Agent
Debugging Agent
Metadata Agent
Privacy Auditor
Security Auditor
Dependency Auditor
Rejection Analyzer
Release Manager
```

An agent definition should specify:

- objective
- inputs
- allowed tools
- expected output
- validation
- failure modes
- approval boundary

## Workflows

```text
ai/workflows/
```

End-to-end AI-assisted procedures.

Example:

```text
Repository
   ↓
Release Audit Agent
   ↓
Find blockers
   ↓
Developer fixes blockers
   ↓
Build
   ↓
QA Agent
   ↓
Store Preparation
   ↓
Human Approval
```

## Prompts

```text
ai/prompts/
```

Reusable prompts for developers who want to use an AI tool manually.

Prompts should be short, contextual, and designed around actual release tasks.

## Evals

```text
ai/evals/
```

AI output must be evaluated.

The goal is to detect:

- hallucinated requirements
- incorrect commands
- missed release blockers
- unsafe recommendations
- regressions
- incorrect store-policy interpretation

AI output is not treated as authoritative.

---

# 7. Framework Architecture

## Expo

Expo is a first-class implementation path.

```text
frameworks/expo/
```

It covers:

- development builds
- EAS Build
- EAS Submit
- app configuration
- credentials
- updates
- CI/CD
- common failures

## React Native

React Native is also first-class.

```text
frameworks/react-native/
```

It covers:

- iOS release
- Android release
- native signing
- Xcode
- Gradle
- Fastlane
- EAS where relevant
- CI/CD
- common failures

The Expo and React Native sections should link back to shared core guidance rather than duplicating it.

---

# 8. Community Framework Architecture

```text
frameworks/community/
├── README.md
└── adding-a-framework.md
```

The project should explicitly invite contributors to add other stacks.

A framework contribution should generally follow:

```text
Identify shared concept
        ↓
Link to core guide
        ↓
Add framework-specific implementation
        ↓
Add verification
        ↓
Add troubleshooting
        ↓
Add official sources
```

Do not fork the entire playbook for each framework.

---

# 9. Troubleshooting Architecture

Troubleshooting is organized by developer problem rather than by technology.

Examples:

```text
Build fails
Signing fails
Archive fails
Upload fails
CI fails
Review rejected
Metadata rejected
Privacy rejected
Crash after release
Push notifications broken
Deep links broken
Payments broken
App not visible
```

Each troubleshooting guide should follow:

```text
Symptom
   ↓
Likely Causes
   ↓
How to Diagnose
   ↓
Fix
   ↓
Verify
   ↓
Prevention
```

---

# 10. Scripts

Scripts should automate deterministic work, not replace engineering judgment.

Examples:

```text
scripts/release-audit/
scripts/version-bump/
scripts/build-validation/
scripts/metadata-validation/
scripts/store-validation/
```

Scripts must be:

- small
- inspectable
- reproducible
- safe by default
- documented

Avoid scripts that silently perform destructive production actions.

---

# 11. Sources and Verification

The `sources/` directory records authoritative external references.

Primary sources should be preferred:

- Apple
- Google
- Expo
- React Native
- Fastlane
- relevant tool vendors

For volatile information, documentation should identify:

```text
Source
Last verified
Applies to
```

The playbook should not pretend that a static Markdown file can replace current store documentation.

---

# 12. Governance

The repository is public and therefore needs contribution discipline.

Governance should define:

- documentation style
- source verification
- versioning
- maintenance
- contribution rules

Contributors should be encouraged to submit:

- new framework guides
- troubleshooting cases
- AI workflows
- release lessons
- updated official sources
- automation scripts

Changes should remain practical and evidence-based.

---

# 13. What This Architecture Is Not

This repository is not:

- a mobile CI/CD platform
- a release-management SaaS
- a replacement for App Store Connect
- a replacement for Google Play Console
- a policy mirror
- a generic React Native tutorial
- an AI-agent platform
- a backend service
- a microservices system
- a Kubernetes deployment

The architecture deliberately avoids unnecessary application infrastructure.

---

# 14. Security Boundaries

The playbook must treat the following as sensitive:

- signing certificates
- provisioning profiles
- Android keystores
- upload keys
- App Store Connect API keys
- Google Play credentials
- CI secrets
- production environment secrets
- AI provider credentials

Never place real credentials in examples.

Never recommend committing secrets to Git.

AI workflows must use least privilege.

High-impact actions should have explicit human approval.

AI output must be treated as untrusted until verified.

These principles follow the project's security baseline: secure boundaries, least privilege, secret protection, validation of external input, and explicit controls for AI actions. fileciteturn2file0 fileciteturn2file3

---

# 15. Quality and Maintenance

Every guide should be checked for:

- technical correctness
- current platform behavior
- working commands
- working links
- clear prerequisites
- safe credential handling
- expected output
- failure handling

Do not claim a workflow works unless it has been verified.

For automation and AI workflows, validate the smallest relevant path first, then expand coverage.

This follows the broader engineering rule of making small, reversible changes and validating what the repository actually supports. fileciteturn2file11

---

# 16. Architecture Decision

The repository deliberately uses:

```text
Static Knowledge
        +
Executable Examples
        +
Small Scripts
        +
AI Workflows
        +
Community Contributions
```

rather than:

```text
Gateway
   ↓
Backend
   ↓
Microservices
   ↓
Database
   ↓
Queue
   ↓
Orchestrator
   ↓
Platform Services
```

The latter architecture would be appropriate for a product that provides automated release management as a service.

It is unnecessary for this open-source playbook.

---

# 17. Final Architecture Principle

The Mobile Release Playbook should be:

```text
Simple enough to understand
        +
Practical enough to use
        +
Specific enough to execute
        +
Open enough to extend
        +
Safe enough for production
        +
Current enough to trust
```

The core architecture stays framework-agnostic.

The initial maintained implementations are **Expo and React Native**.

The community extends the framework layer.

AI accelerates the workflow.

Official platform documentation remains the authority.

The repository itself remains the source of reusable engineering knowledge.
