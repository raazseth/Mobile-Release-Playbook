# Contributing

Thank you for contributing to the Mobile Release Playbook.

This project is intended to become a practical, community-maintained resource for developers shipping mobile applications. The initial first-class focus is **Expo and React Native**, while the architecture is intentionally open to other frameworks and mobile stacks.

The best contributions are practical, verified, and useful to someone trying to get a real app released.

---

## What you can contribute

You can contribute:

- new release guides
- Expo workflows
- React Native workflows
- iOS or Android publishing guidance
- troubleshooting guides
- testing strategies
- signing and credential guidance
- CI/CD workflows
- store submission lessons
- AI-assisted workflows
- reusable prompts
- agent definitions
- automation scripts
- templates and checklists
- corrections to outdated or incorrect documentation
- official source updates
- support for additional frameworks

If you solved a release problem that took you hours to figure out, documenting that solution is a valuable contribution.

---

## Before contributing

Start by checking:

1. `README.md`
2. `architecture.md`
3. `governance/documentation-style.md`
4. `governance/source-verification.md`
5. the existing section where your contribution belongs

Avoid creating a new section when an existing one already covers the concept.

The repository is intentionally structured around clear responsibilities. Keep related information together and avoid duplicating the same workflow across multiple files.

---

# Contribution principles

## 1. Prefer practical guidance

Write for a developer who needs to complete a task.

Prefer:

```text
Do this
↓
Run this
↓
Verify this
↓
If it fails, check this
```

over long theoretical explanations.

A good guide should help someone move forward without requiring them to understand the entire mobile ecosystem first.

---

## 2. Document the real workflow

If you are documenting a process, explain the actual sequence.

For example:

```text
Configure
  ↓
Build
  ↓
Test
  ↓
Upload
  ↓
Submit
  ↓
Review
  ↓
Release
```

Do not describe an idealized workflow that you have not verified.

---

## 3. Verify before contributing

Before submitting technical guidance, verify it where practical.

For commands, check that they actually work.

For platform requirements, check the current official documentation.

For AI workflows, test the workflow against a realistic repository or task when possible.

If something is based on experience rather than official documentation, say so.

---

## 4. Use primary sources

For platform-specific requirements, prefer official sources:

- Apple
- Google
- Expo
- React Native
- Fastlane
- relevant tooling vendors

Do not present community assumptions as official platform requirements.

If a requirement can change over time, include the relevant official source.

---

# Where should my contribution go?

Use the existing repository structure.

### General mobile concepts

Use:

```text
foundations/
```

Examples:

- versioning
- environments
- device support
- dependency management

### Accounts and store access

Use:

```text
store-accounts/
```

### Certificates and credentials

Use:

```text
signing/
```

### Testing

Use:

```text
testing/
```

### Release readiness

Use:

```text
pre-release/
```

### App Store / Google Play publishing

Use:

```text
publishing/
```

### Store listings and review

Use:

```text
store-operations/
```

### Payments and subscriptions

Use:

```text
monetization/
```

### Privacy and platform declarations

Use:

```text
privacy-compliance/
```

### Mobile integrations

Use:

```text
integrations/
```

### Build and CI/CD automation

Use:

```text
release-engineering/
```

### Rollout and release strategy

Use:

```text
release-strategy/
```

### Production operations

Use:

```text
post-release/
```

### AI workflows

Use:

```text
ai/
```

### Framework-specific implementation

Use:

```text
frameworks/
```

Expo and React Native are first-class.

Other frameworks should use the community extension path where appropriate.

### Problems and fixes

Use:

```text
troubleshooting/
```

---

# Adding a new framework

The project currently focuses on:

```text
frameworks/
├── expo/
└── react-native/
```

This does not mean other frameworks are excluded.

If you want to add Flutter, native iOS, native Android, or another framework:

1. Check whether the relevant core guidance already exists.
2. Reuse the core concepts instead of copying them.
3. Add the framework-specific implementation under `frameworks/`.
4. Include setup and release prerequisites.
5. Include the actual build and publishing workflow.
6. Include common failures where useful.
7. Link to official framework and platform sources.
8. Keep the contribution focused on the framework-specific differences.

Do not duplicate the entire playbook for a new framework.

---

# Adding AI workflows

AI contributions are welcome, but they should solve a real release problem.

Good examples:

```text
AI Release Auditor
AI QA Workflow
Store Metadata Generator
Release Notes Generator
Build Debugging Agent
Store Rejection Analyzer
Privacy Auditor
Dependency Auditor
```

An AI workflow should document:

- objective
- required context
- inputs
- tools or permissions
- workflow
- expected output
- validation
- failure cases
- human approval boundary

Do not recommend unrestricted agent access to:

- signing credentials
- production secrets
- store credentials
- financial systems
- destructive production operations

AI output should always be treated as untrusted until verified.

---

# Adding prompts

Prompts should be reusable and task-specific.

A good prompt should provide enough context for an AI tool to perform the task without unnecessary instructions.

Prefer:

```text
Audit this Expo application for production release blockers.

Check:
- app configuration
- iOS signing configuration
- Android signing configuration
- production environment configuration
- permissions
- deep links
- push notifications
- versioning
- store metadata

Return:
1. blockers
2. warnings
3. passed checks
4. recommended fixes
```

over a vague prompt such as:

```text
Check my app before release.
```

If a prompt depends on a specific framework or tool, make that dependency clear.

---

# Adding scripts

Scripts should automate deterministic work.

Good examples:

```text
release audit
version validation
metadata validation
build validation
store validation
```

Scripts should be:

- small
- readable
- reproducible
- safe by default
- documented

Avoid scripts that silently:

- publish to production
- delete resources
- rotate credentials
- modify production infrastructure
- expose secrets

If a script can perform a high-impact action, make the action explicit and require deliberate invocation.

---

# Documentation style

Keep documentation:

- direct
- practical
- concise
- technically precise
- easy to scan

Prefer:

> Run `eas build --platform ios`.

over:

> At this stage, the developer may wish to consider initiating the process of generating an iOS build.

Use headings, tables, code blocks, checklists, and diagrams when they make the workflow easier to understand.

Avoid unnecessary abstraction and marketing language.

---

# Commands and examples

Commands should be copy-paste friendly where practical.

Always make important assumptions visible.

For example:

```bash
eas build --platform ios --profile production
```

should be accompanied by enough context to understand:

- what it does
- what must be configured first
- where credentials come from
- what successful completion looks like

Never include real credentials, tokens, private keys, or secrets in examples.

Use placeholders:

```text
YOUR_APP_STORE_CONNECT_KEY
YOUR_GOOGLE_PLAY_SERVICE_ACCOUNT
YOUR_API_KEY
```

---

# Sources and changing requirements

Mobile platform requirements change frequently.

When documenting a requirement that may change, include the relevant official source.

For example:

```text
Source: Apple Developer Documentation
Last verified: YYYY-MM-DD
```

Do not copy large sections of third-party documentation into the repository.

Summarize the requirement in your own words and link to the authoritative source.

The playbook is a practical layer over official platform documentation, not a replacement for it.

---

# Troubleshooting contributions

Troubleshooting guides are especially valuable.

Use this structure:

```markdown
# Problem

Describe the symptom.

## Likely Causes

- Cause 1
- Cause 2
- Cause 3

## How to Diagnose

1. Check ...
2. Run ...
3. Verify ...

## Fix

Explain the solution.

## Verify

Explain how to confirm that the problem is resolved.

## Prevention

Explain how to avoid the problem in future releases.
```

Avoid documenting a workaround without explaining its tradeoffs or risks.

---

# Pull requests

Keep pull requests focused.

A PR should ideally solve one clear problem.

Good:

```text
Add Expo EAS production submission guide
```

Good:

```text
Fix Android upload-key documentation
```

Good:

```text
Add AI workflow for release readiness audits
```

Avoid combining unrelated changes into one PR.

---

# Commit messages

Use clear commit messages.

Examples:

```text
docs: add Expo production build guide
docs: fix App Store Connect signing steps
feat: add release audit script
feat: add AI rejection workflow
fix: correct Android Play App Signing guidance
chore: update official source links
```

The exact convention is less important than making the intent obvious.

---

# Updating the changelog

For meaningful changes, update:

```text
CHANGELOG.md
```

Include changes such as:

- new framework support
- new workflows
- important corrections
- new automation
- significant AI workflows
- security changes
- structural changes

Do not turn the changelog into a commit history.

---

# Review expectations

A contribution should be reviewed for:

### Correctness

Does the guidance actually work?

### Relevance

Does it solve a real release problem?

### Maintainability

Will someone be able to update it later?

### Security

Does it handle credentials, secrets, and permissions safely?

### Source quality

Are platform requirements backed by authoritative sources?

### Scope

Does it belong in this repository?

### Duplication

Does it unnecessarily repeat existing guidance?

### AI safety

If AI is involved:

- Are permissions limited?
- Is output validated?
- Are destructive actions gated?
- Are secrets protected?
- Are failure modes documented?

---

# Breaking or sensitive changes

Some changes require extra care:

- changing repository architecture
- moving major sections
- removing framework support
- changing security guidance
- changing credential-handling recommendations
- changing agent permissions
- adding automated production actions

For these changes, explain:

1. what changed
2. why it changed
3. what existing users are affected
4. how to migrate, if necessary

---

# What not to contribute

Avoid:

- unverified commands
- copied documentation
- outdated platform requirements presented as current
- vendor marketing material
- generic AI hype
- unsafe credential practices
- unrestricted autonomous production workflows
- framework duplication
- unnecessary infrastructure
- undocumented breaking changes
- large unrelated refactors

The goal is useful engineering knowledge, not repository size.

---

# Contributor checklist

Before opening a PR:

```text
[ ] I placed the contribution in the correct section.
[ ] I checked for existing documentation before creating new content.
[ ] I verified the technical steps where practical.
[ ] I used official sources for platform requirements.
[ ] I did not include secrets or private credentials.
[ ] I documented important assumptions.
[ ] I included troubleshooting where useful.
[ ] I added AI safety boundaries if AI is involved.
[ ] I updated CHANGELOG.md if the change is meaningful.
[ ] I kept the PR focused.
[ ] I checked links and code examples.
```

---

# Community standard

The project should improve because developers contribute what they learn.

The standard is simple:

> **If you add something, make it useful enough that another developer can actually ship with it.**

You do not need to be a maintainer or an expert in the entire project to contribute.

If you know one framework, one platform workflow, one recurring failure, or one useful AI workflow particularly well, that knowledge can still make the playbook better.

