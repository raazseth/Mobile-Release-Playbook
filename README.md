# Mobile Release Playbook

> A practical, open-source playbook for taking an Expo or React Native app from "built" to safely released, published, monitored, and updated.

## What is this?

The Mobile Release Playbook is a practical guide for developers who have built a mobile app and now need to get it into production.

It brings the release process into one place:

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

It focuses on the parts of mobile development that often become painful at release time: certificates, signing, builds, testing, store metadata, review, rejection handling, CI/CD, production issues, and updates.

It also shows how modern AI tools and agents can help developers do this work faster.

---

## Why this exists

Building the app is only part of shipping it.

A developer can have a working Expo or React Native application and still get stuck on questions like:

- How do I create a production build?
- What certificates or keys do I need?
- How should I configure App Store Connect?
- How do I submit to Google Play?
- What should I test before submitting?
- Why did Apple or Google reject the app?
- How do I safely handle signing credentials?
- How do I automate releases?
- What can Claude Code, Codex, ChatGPT, or GitHub agents actually do for me?
- What should an AI agent be allowed to change?
- What do I monitor after the app goes live?
- What do I do when a production release is broken?

This project exists to make those questions easier to answer.

---

## Who is it for?

### Developers publishing their first app

If you have an Expo or React Native app and have never published it before, the playbook gives you a path from the finished application to production.

### Experienced developers

If you already know mobile development, use the playbook as a release checklist, reference, troubleshooting guide, or automation reference.

### AI-assisted developers

If you use Claude Code, Codex, ChatGPT, GitHub Copilot, or other agentic tools, the AI section provides practical workflows for using them during release work.

### Teams

Teams can use the playbook as a shared release process so that important steps are not dependent on one developer remembering them.

### Contributors

The initial implementation focuses on Expo and React Native, but the repository is intentionally designed so the community can add other frameworks and platforms.

---

# What it covers

## Foundations

Core concepts that apply regardless of framework:

- release lifecycle
- environments
- project configuration
- identifiers
- versioning
- device support
- dependencies

See [`foundations/`](./foundations/).

## Store accounts

Everything that needs to exist before an app can be published:

- Apple Developer
- App Store Connect
- Google Play Console
- organization accounts
- roles and permissions
- agreements
- banking and tax
- account recovery

See [`store-accounts/`](./store-accounts/).

## Signing

Production signing and credential management for iOS and Android:

- certificates
- provisioning profiles
- distribution
- API keys
- Android keystores
- upload keys
- Play App Signing
- CI secrets
- key rotation
- recovery

See [`signing/`](./signing/).

## Testing

Release-focused testing beyond normal development testing:

- unit and integration testing
- E2E testing
- device testing
- device matrices
- accessibility
- localization
- performance
- network conditions
- offline behavior
- deep links
- push notifications
- payments
- upgrade migrations
- release smoke tests

See [`testing/`](./testing/).

## Pre-release

A final release-readiness check across:

- dependencies
- security
- privacy
- permissions
- performance
- accessibility
- metadata
- assets
- configuration

See [`pre-release/`](./pre-release/).

## Publishing

Platform-specific publishing workflows for:

### iOS

- App Store Connect
- production build upload
- TestFlight
- metadata
- screenshots
- App Review
- production release

### Android

- Play Console
- Android App Bundles
- internal testing
- closed testing
- metadata
- screenshots
- app review
- production release

See [`publishing/`](./publishing/).

## Store operations

The work does not end after submission.

This section covers:

- listings
- metadata
- screenshots
- previews
- localization
- privacy information
- content ratings
- data safety
- review
- rejection handling
- resubmission

See [`store-operations/`](./store-operations/).

## Monetization

For apps using platform commerce:

- subscriptions
- in-app purchases
- consumables
- non-consumables
- pricing
- offers
- entitlements
- receipt validation
- subscription lifecycle

See [`monetization/`](./monetization/).

## Privacy and compliance

Release-related privacy concerns including:

- privacy policies
- data collection
- permissions
- tracking
- analytics
- advertising
- retention
- deletion
- children
- health data
- regional requirements

See [`privacy-compliance/`](./privacy-compliance/).

## Integrations

Common integrations that can create production-specific problems:

- push notifications
- deep links
- Universal Links
- Android App Links
- OAuth
- maps
- payments
- analytics
- crash reporting
- attribution
- camera
- location
- background processing

See [`integrations/`](./integrations/).

## Release engineering

Automation around:

- build systems
- release configuration
- Fastlane
- EAS
- GitHub Actions
- App Store Connect API
- Google Play API
- environment management
- release pipelines

See [`release-engineering/`](./release-engineering/).

## Release strategy

How to release changes safely:

- release trains
- beta testing
- staged rollouts
- phased releases
- feature flags
- kill switches
- hotfixes
- emergency releases

See [`release-strategy/`](./release-strategy/).

## Post-release

Once the app is live:

- monitoring
- crash analysis
- performance
- analytics
- reviews and ratings
- rollout monitoring
- incident response
- rollback
- hotfixes
- version recall
- release retrospectives

See [`post-release/`](./post-release/).

---

# AI-native release workflows

AI is a first-class part of this playbook.

The goal is not to tell developers to "use AI."

The goal is to show exactly where AI can remove repetitive release work.

Examples include:

```text
Repository
   ↓
AI Release Auditor
   ↓
Find blockers
   ↓
Developer fixes blockers
   ↓
Build
   ↓
AI QA
   ↓
Store preparation
   ↓
Human approval
   ↓
Release
```

The playbook covers:

- ChatGPT
- Claude
- Claude Code
- Codex
- GitHub Copilot
- GitHub Agents
- MCP
- reusable prompts
- agent workflows
- agent evaluation
- safe automation

See [`ai/`](./ai/).

### AI should help, not become the source of truth

AI-generated commands, recommendations, code, metadata, and policy interpretations must be verified.

Agents should have only the access they need.

Sensitive and irreversible actions should have explicit human approval.

The goal is:

```text
AI handles repetitive work
        +
Developer keeps control
```

---

# Framework support

## First-class

The initial maintained framework implementations are:

- Expo
- React Native

See:

- [`frameworks/expo/`](./frameworks/expo/)
- [`frameworks/react-native/`](./frameworks/react-native/)

The framework layer contains implementation-specific instructions while shared concepts remain in the core sections.

## Community extensibility

The project is intentionally not limited to Expo and React Native.

Contributors can add other frameworks through [`frameworks/community/`](./frameworks/community/).

Potential future additions include:

- Flutter
- native iOS
- native Android
- other cross-platform frameworks

You do not need to wait for the core maintainers to support your framework.

If the core concept already exists, add the framework-specific implementation and link back to the shared guidance.

See [`frameworks/community/adding-a-framework.md`](./frameworks/community/adding-a-framework.md).

---

# How to use the playbook

## First release

If this is your first mobile release, start here:

```text
1. foundations/
2. store-accounts/
3. signing/
4. testing/
5. pre-release/
6. publishing/
7. store-operations/
8. post-release/
```

For Expo, use the Expo-specific guides alongside the shared workflow.

For React Native, use the React Native-specific guides alongside the shared workflow.

## Existing app

If your app is already in production, jump directly to the area you need:

```text
Build problem       → troubleshooting/
Signing problem     → signing/ + troubleshooting/
Store rejection     → store-operations/ + troubleshooting/
CI/CD               → release-engineering/
AI workflow         → ai/
Production issue    → post-release/
New framework       → frameworks/community/
```

## Using AI

Start with [`ai/README.md`](./ai/README.md), then choose the workflow that matches the task.

Do not give an AI agent more access than the task requires.

---

# Repository structure

```text
mobile-release-playbook/
├── foundations/
├── store-accounts/
├── signing/
├── testing/
├── pre-release/
├── publishing/
├── store-operations/
├── monetization/
├── privacy-compliance/
├── integrations/
├── release-engineering/
├── release-strategy/
├── post-release/
├── ai/
├── frameworks/
│   ├── expo/
│   ├── react-native/
│   └── community/
├── troubleshooting/
├── checklists/
├── templates/
├── scripts/
├── sources/
├── governance/
└── .github/
```

Each directory has a specific responsibility. Avoid duplicating the same guidance across multiple sections.

---

# Documentation principles

The playbook aims to be:

### Practical

Prefer working steps and examples over theory.

### Current

Platform requirements and tooling change. Volatile information should reference authoritative sources and be periodically verified.

### Framework-aware

Shared concepts belong in the core. Framework-specific implementation belongs under `frameworks/`.

### AI-friendly

Guides should be usable by both humans and coding agents.

### Safe

Never expose real credentials or recommend unsafe access patterns.

### Community-driven

If something is missing, add it rather than waiting for the project maintainers to cover every framework and edge case.

---

# Sources

This project does not attempt to replace official platform documentation.

Official documentation is the authority for changing requirements.

The project should prefer primary sources from:

- Apple
- Google
- Expo
- React Native
- Fastlane
- relevant tooling vendors

See [`sources/`](./sources/).

---

# Contributing

Contributions are welcome.

You can contribute:

- new framework guides
- new release workflows
- troubleshooting cases
- AI workflows
- prompts
- automation scripts
- updated official sources
- corrections to existing documentation

Before contributing, read [`CONTRIBUTING.md`](./CONTRIBUTING.md).

For framework additions, see [`frameworks/community/adding-a-framework.md`](./frameworks/community/adding-a-framework.md).

---

# Scope

This project focuses on the journey from:

> **"My mobile app is built"**

to:

> **"My mobile app is safely live and I can keep shipping it."**

It is not:

- a mobile development course
- a replacement for App Store Connect
- a replacement for Google Play Console
- an App Store or Play Store policy mirror
- an AI-agent platform
- a release-management SaaS
- a generic DevOps platform

The repository is primarily a **practical release engineering knowledge base with executable workflows and AI-assisted tooling**.

---

# Status

The project is being built incrementally.

Expo and React Native are the first-class implementations.

Other frameworks and workflows can be added by contributors.

If you find a release problem that took you hours to solve, consider documenting it here so the next developer does not have to solve it from scratch.

---

## License

See [`LICENSE`](./LICENSE).
