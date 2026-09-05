# Changelog

All notable changes to the Mobile Release Playbook are documented here.

The changelog tracks meaningful changes to the playbook itself, including:

- new release workflows
- new framework support
- new AI workflows and agents
- new automation
- important documentation changes
- corrections to inaccurate guidance
- changes to repository structure
- changes that affect how contributors should use or extend the project

This project follows a simple, human-readable changelog format inspired by [Keep a Changelog](https://keepachangelog.com/).

## Categories

Changes should use the following categories where applicable:

- **Added** — new guides, workflows, tools, framework support, scripts, or capabilities
- **Changed** — changes to existing guidance, workflows, architecture, or behavior
- **Deprecated** — guidance or tooling that should no longer be used and is planned for removal
- **Removed** — previously supported guidance, tooling, or framework support that has been removed
- **Fixed** — corrections to inaccurate, broken, outdated, or misleading content
- **Security** — security-related changes, fixes, or important credential-handling guidance

---

## [Unreleased]

Changes currently being developed and not yet included in a published release.

### Added

- New guide `frameworks/react-native/local-setup.md`: local machine setup for producing a React Native *release* build (not a development tutorial) — Node/Watchman, JDK, Android SDK and environment variables, Xcode/CocoaPods/Ruby, Apple Silicon notes, verifying the environment with `react-native doctor`, and matching a local toolchain to CI.
- New guide `frameworks/expo/local-setup.md`: the Expo equivalent, framed around the local-build-vs-EAS-cloud-build distinction — when native tooling is actually required, CNG vs. committed native projects, `expo-doctor`, and `eas build --local`.
- Cross-linked both new guides into `frameworks/react-native/README.md`, `frameworks/expo/README.md`, `frameworks/expo/development-builds.md`, `frameworks/react-native/common-failures.md`, and `frameworks/expo/common-failures.md`.
- Initial Mobile Release Playbook structure.
- Core release lifecycle documentation.
- Store account guidance for Apple Developer, App Store Connect, and Google Play Console.
- iOS and Android signing guidance.
- Release-focused testing guidance.
- Pre-release readiness checks.
- App Store and Google Play publishing workflows.
- Store operations and rejection-handling guidance.
- Monetization and privacy/compliance guidance.
- Common mobile integration guidance.
- Release engineering guidance for EAS, Fastlane, GitHub Actions, and store APIs.
- Release strategy and post-release operational guidance.
- AI-assisted release workflows.
- AI agent, prompt, orchestration, evaluation, and security guidance.
- First-class Expo documentation.
- First-class React Native documentation.
- Community framework extension structure.
- Expanded `ai/README.md` into a comprehensive architecture index and navigation guide for the entire AI-assisted mobile release engineering subsystem.
- Expanded `ai/orchestration/` (`agent-workflows.md`, `human-approval.md`, `bounded-autonomy.md`, `subagents.md`, `tool-permissions.md`, `mcp.md`) into comprehensive handbook guides covering multi-agent topologies, human approval gates, autonomy constraints, subagent isolation, tool permission matrices, and Model Context Protocol integrations.
- Expanded `ai/prompts/` (`debugging.md`, `metadata.md`, `qa.md`, `rejection-analysis.md`, `release-audit.md`, `release-notes.md`) into production-ready prompt templates, input/output schemas, and execution examples.
- Expanded `ai/security/` (`agent-permissions.md`, `destructive-actions.md`, `prompt-injection.md`, `secret-protection.md`) into security handbook guides covering container sandboxing, non-reversible action gating, prompt injection shielding, and secret masking architecture.
- Expanded `ai/tools/` (`chatgpt.md`, `claude.md`, `claude-code.md`, `codex.md`, `github-copilot.md`, `github-agents.md`) into comprehensive tool guides detailing privacy baselines, prompt setups, execution limits, and CLI workflows.
- Expanded `ai/workflows/` (`release-preparation.md`, `release-audit.md`, `ai-qa.md`, `debugging.md`, `metadata-generation.md`, `rejection-analysis.md`, `release-notes.md`) into end-to-end multi-agent workflow specifications with step-by-step procedures and machine-readable schemas.
- Expanded `integrations/` (`README.md` and 13 domain guides: `analytics`, `app-links`, `attribution`, `background-processing`, `camera`, `crash-reporting`, `deep-links`, `location`, `maps`, `oauth`, `payments`, `push-notifications`, `universal-links`) into comprehensive integration handbooks detailing native permissions, privacy manifests (`PrivacyInfo.xcprivacy`), store review compliance (Guideline 3.1.1, Guideline 4.0, Guideline 5.1.1), Android 14+ Selected Photos, FCM HTTP v1, StoreKit 2, and AASA domain verification.
- Expanded `monetization/` (`README.md` and 9 domain guides: `in-app-purchases`, `subscriptions`, `consumables`, `non-consumables`, `subscription-lifecycle`, `offers`, `pricing`, `receipt-validation`, `entitlements`) into comprehensive app commerce handbooks detailing StoreKit 2, Google Play Billing 8.0/9.0+, Apple Guideline 3.1.1 compliance, App Store Server Notifications v2 JWS decoding, and Play RTDN webhooks.
- Expanded `store-accounts/` (`README.md` and 8 domain guides: `apple-developer`, `google-play-console`, `app-store-connect`, `organization-accounts`, `roles-and-permissions`, `banking-and-tax`, `agreements`, `account-recovery`) into comprehensive store administration handbooks detailing D-U-N-S business verification, mandatory 2FA, Google Play Organization requirements, 12-tester / 14-day closed testing gates, App Store Connect `.p8` API keys, and W-8BEN-E / W-9 tax forms.
- Expanded `store-operations/` (`README.md` and 11 domain guides: `metadata`, `app-listing`, `screenshots`, `app-previews`, `localization`, `app-review`, `rejection-handling`, `resubmission`, `data-safety`, `privacy-information`, `content-ratings`) into comprehensive store operations handbooks detailing iPhone 6.9" screenshot resolution matrices (1320x2868), alpha channel stripping, Guideline 2.3 metadata limits, demo credential rules, Google Play Data Safety, App Privacy Labels, and IARC content ratings.
- Expanded `publishing/android/` (`README.md` and 8 domain guides: `app-bundle`, `play-console`, `internal-testing`, `closed-testing`, `production-release`, `app-review`, `metadata`, `screenshots`) into comprehensive Android publishing handbooks detailing Target API Level 36 (Android 16), Android App Bundle (`.aab`) compilation, R8 obfuscation, Play App Signing, Fastlane Supply / EAS Submit automation, Personal account testing gates (12 testers for 14 days), and Staged Rollout lifecycles (1% → 100%).
- Expanded `publishing/cross-platform/` (`README.md` and 4 domain guides: `submission`, `assets`, `metadata`, `release-notes`) into comprehensive cross-platform publishing handbooks detailing EAS Submit (`eas.json`), Fastlane multi-platform automation, GitHub Actions CI/CD matrix pipelines, Master App Icon (1024x1024) & Android Adaptive Icon setup, dynamic `Intl` currency localization, master metadata JSON schemas, and Keep a Changelog standards.
- Expanded `publishing/ios/` (`README.md` and 7 domain guides: `build-upload`, `app-store-connect`, `testflight`, `production-release`, `app-review`, `metadata`, `screenshots`) into comprehensive iOS publishing handbooks detailing `.ipa` archiving, Transporter CLI, App Store Connect API keys (`.p8`), TestFlight Internal (100 testers) vs External (10,000 testers), 7-Day Phased Releases (Day 1: 1% → Day 7: 100%), `ITSAppUsesNonExemptEncryption`, and canonical 6.9" Super Retina XDR screenshots (1320x2868).
- Expanded `sources/` (`README.md` and 6 domain registries: `apple`, `google`, `expo`, `react-native`, `fastlane`, `tooling`) into comprehensive official source documentation registries detailing primary developer portals, platform policy citations, API deprecation tracking, and source verification governance rules.
- Expanded `templates/` (`README.md` and 9 operational templates: `release-plan`, `release-checklist`, `rejection-response`, `release-notes`, `changelog`, `app-store-description`, `play-store-description`, `privacy-questionnaire`, `incident-report`) into production-ready release engineering artifacts detailing master release schedules, risk matrices, pre-release checklists, Resolution Center rejection response forms, store metadata templates, and incident post-mortem frameworks.
- Expanded `testing/` (`README.md` and 17 QA guides: `unit`, `integration`, `e2e`, `ios`, `android`, `device-matrix`, `device-testing`, `accessibility`, `localization`, `deep-links`, `push-notifications`, `payments`, `network`, `offline`, `performance`, `release-smoke-tests`, `upgrade-migrations`) into comprehensive mobile quality assurance handbooks detailing Jest 29+, React Native Testing Library (RNTL), Maestro/Detox E2E automation, iOS Simulator `simctl` & Android `adb`, VoiceOver/TalkBack accessibility, StoreKit 2 & Play Billing 8/9+ sandbox testing, Proxyman network latency injection, Hermes memory profiling, and 10-minute Release Candidate Smoke Test suites.
- Expanded `troubleshooting/` (`README.md` and 13 diagnostic guides: `build-fails`, `signing-fails`, `archive-fails`, `ci-fails`, `upload-fails`, `metadata-rejected`, `review-rejected`, `privacy-rejected`, `crash-after-release`, `push-notifications-broken`, `deep-links-broken`, `payments-broken`, `app-not-visible`) into comprehensive diagnostic handbooks detailing Gradle & CocoaPods compilation failures, `ITMS-90161` code signing errors, R8 ProGuard class stripping, `ITMS-90032` alpha transparency, Resolution Center rejection triage, Sentry Hermes stack trace de-obfuscation, FCM HTTP v1 migration, and AASA Universal Link verification.
- Expanded `privacy-compliance/` (`README.md` and 11 regulatory guides: `privacy-policy`, `data-collection`, `data-deletion`, `data-retention`, `tracking`, `advertising`, `analytics`, `children`, `health-data`, `permissions`, `regional-requirements`) into comprehensive privacy & compliance handbooks detailing Apple `PrivacyInfo.xcprivacy` manifests & Required Reason APIs, Google Play Data Safety & mandatory web deletion URL, App Tracking Transparency (ATT) & IDFA rules, COPPA & Kids Category neutral age gates, HealthKit & Health Connect entitlements, Android 14+ Selected Photos, EU GDPR, CCPA/CPRA, and South Korea GRAC age rating RCN overrides.
- Expanded `pre-release/` (`README.md` and 11 audit guides: `release-readiness`, `final-release-checklist`, `dependency-audit`, `security-audit`, `privacy-audit`, `permissions-audit`, `configuration-audit`, `asset-audit`, `metadata-audit`, `performance-audit`, `accessibility-audit`) into comprehensive pre-release quality gate handbooks detailing GO/NO-GO readiness criteria, OWASP Mobile Top 10 security scans, dependency vulnerability checks (`npm audit`), Target SDK 36 configuration, 6.9" screenshot asset validation, Hermes heap performance benchmarks, and VoiceOver/TalkBack accessibility audits.
- Expanded `post-release/` (`README.md` and 11 operational guides: `rollout-monitoring`, `monitoring`, `crash-analysis`, `performance`, `analytics`, `reviews-and-ratings`, `incident-response`, `hotfix`, `rollback`, `version-recall`, `release-retrospective`) into comprehensive post-release operations handbooks detailing iOS 7-Day Phased Release & Android Staged Rollout monitoring, Sentry crash triage & symbolication, Android Vitals ANR tracking, in-app review prompts (`StoreReview`), emergency hotfixes & EAS Update OTA patches, production rollbacks, server-driven forced update UI gates, and blameless 5 Whys retrospectives.
- Expanded `governance/` (`README.md` and 5 governance guides: `source-verification`, `documentation-style`, `contribution-guide`, `versioning`, `maintenance`) into comprehensive playbook governance handbooks detailing empirical source verification protocols, primary authority hierarchies, CLAUDE.md structural markdown standards, pull request quality gates, Semantic Versioning 2.0.0 rules, and 90-day quarterly verification audit cycles.
- Expanded `signing/` (`README.md`, 3 sub-indices, and 13 domain guides across `ios/`, `android/`, and `security/`: `certificates`, `provisioning-profiles`, `distribution`, `api-keys`, `key-rotation`, `keystore`, `upload-key`, `play-app-signing`, `secret-storage`, `ci-secrets`, `access-control`, `recovery`) into comprehensive code signing & credential management handbooks detailing Apple Distribution Certificates, Fastlane Match (`fastlane match appstore`), App Store Connect API keys (`.p8`), PKCS12 Android Keystores (RSA 4096-bit), Google Play App Signing (Cloud HSM KMS), PEPK tool key exports, HashiCorp Vault / AWS Secrets Manager integration, and emergency key compromise containment protocols.
- Expanded `release-engineering/` (`README.md`, 3 sub-indices, and 14 domain guides across `eas/`, `fastlane/`, and `github-actions/`: `build-systems`, `release-configurations`, `environment-management`, `release-pipelines`, `app-store-connect-api`, `google-play-api`, `eas-build`, `eas-submit`, `eas-update`, `fastfile`, `appfile`, `matchfile`, `workflows`, `action-templates`) into comprehensive release engineering handbooks detailing Xcode `xcodebuild` CLI & Android Gradle `./gradlew bundleRelease`, Hermes bytecode compilation, `eas.json` profiles, EAS Submit store upload automation, EAS Update Over-The-Air (OTA) JS patching, Fastlane Match/Pilot/Supply lanes, and GitHub Actions `macos-14` matrix release pipelines (`release-pipeline.yml`).
- Expanded `release-strategy/` (`README.md` and 8 strategy guides: `release-trains`, `phased-release`, `staged-rollouts`, `beta-testing`, `feature-flags`, `kill-switches`, `hotfixes`, `emergency-release`) into comprehensive progressive release strategy handbooks detailing bi-weekly 14-day release train governance, iOS 7-Day Phased Release (Day 1: 1% → Day 7: 100%), Google Play Staged Rollouts (ANR < 0.47% halting threshold), TestFlight External (10,000 testers) & Play Closed Testing (12 testers / 14 days), LaunchDarkly / Firebase remote feature flags, dynamic remote kill switches, emergency `hotfix/v1.2.1` deployment, and 2-hour expedited store reviews.
- Expanded `scripts/` (`README.md`, 5 category indices, and 5 CLI automation guides across `version-bump/`, `build-validation/`, `metadata-validation/`, `store-validation/`, and `release-audit/`: `version-bump`, `validate-build`, `validate-metadata`, `validate-store`, `audit-release`) into comprehensive release automation CLI toolchains detailing cross-platform version bumping (`package.json`, `app.json`, `Info.plist`, `build.gradle`), binary artifact inspection (`.ipa`/`.aab` Hermes bytecode & icon 1024x1024 no alpha checks), store metadata Guideline 2.3.7 character limit assertions, App Store Connect / Play API connection handshakes, and OWASP / Target SDK 36 quality gate audits.
- Created root and subfolder `README.md` index files across all repository subsystems (`publishing/`, `checklists/`, `foundations/`, `frameworks/`, `ai/agents/`, `ai/context/`, `ai/evals/`, `ai/getting-started/`, `ai/orchestration/`, `ai/prompts/`, `ai/security/`, `ai/tools/`, `ai/workflows/`), establishing 100% README coverage across every directory in the Mobile Release Playbook.
- Troubleshooting guides for common release failures.
- Reusable release checklists and templates.
- Source verification and documentation governance structure.

### Changed

- Rewrote all 23 documents in `publishing/` (`README.md`, `ios/`, `android/`, `cross-platform/`) in plainer, more direct prose — replaced AI-generated "handbook/taxonomy/architecture" framing, ALL-CAPS callout labels, and repetitive bold-keyword sentences with the conversational, engineer-to-engineer voice used elsewhere in the repo (see `checklists/ios.md`). Content, commands, dates, tables, and figures are unchanged; added cross-links to `release-engineering/eas/eas-submit.md`, `frameworks/expo/submit.md`, and the relevant `troubleshooting/` guides (`upload-fails.md`, `review-rejected.md`, `metadata-rejected.md`, `ci-fails.md`, `crash-after-release.md`) that were previously missing.
- Rewrote all 9 documents in `store-accounts/` (`README.md`, `apple-developer.md`, `google-play-console.md`, `app-store-connect.md`, `organization-accounts.md`, `roles-and-permissions.md`, `banking-and-tax.md`, `agreements.md`, `account-recovery.md`) in the same plain, conversational voice, dropping the "engineered against 2026 platform specifications" framing and ALL-CAPS callouts, and trimmed `app-store-connect.md`'s duplicated TestFlight walkthrough to a pointer at `publishing/ios/testflight.md`. A follow-up verification pass against current Apple/Google documentation then corrected several numbers the previous unsourced content had wrong: payout minimum thresholds in `banking-and-tax.md` (was a flat "$150" — actually $0.02–$40 for Apple depending on currency, $1–$100 for Google depending on payout method), the App Store Connect role matrix in `roles-and-permissions.md` (was missing the Sales role and had Developer's API key access wrong), the Google Play Console permissions section (rewritten to reflect that Play Console has no fixed "Release Manager"/"Store Marketer" roles, only Account Owner/Admin plus composable permission checkboxes), the account holder transfer path in `account-recovery.md` (Membership details, not Users and Access), and an added note in `google-play-console.md` that the 12-tester/14-day closed-testing window resets if a tester drops out mid-window. `Last verified` dates bumped to September 5, 2026 on the files actually re-checked.
- Validated and updated all documentation across `ai/orchestration/`, `ai/prompts/`, `ai/security/`, `ai/tools/`, and `ai/workflows/` against official 2026 platform requirements, including Google Play Android 16 (API Level 36) target enforcement, Apple App Store Guideline 5.1.1 Privacy Manifest Required Reason APIs, and Model Context Protocol (MCP) stateless request/response header-routing specification.
- Upgraded `ai/README.md` to 2026 Context Engineering standards and the OWASP GenAI Security Project (2026 Standard), reflecting prompt caching layouts, single-mount variable blocks, XML tag shielding, and the complete 9-module subsystem taxonomy.
- Upgraded all prompt templates in `ai/prompts/` (`debugging.md`, `metadata.md`, `qa.md`, `rejection-analysis.md`, `release-audit.md`, `release-notes.md`) to 2026 Context Engineering standards, incorporating static prompt caching architecture, single-mount variable blocks, explicit XML tag isolation, and mandatory native reasoning (`<thinking>`) steps.
- Upgraded all security handbooks in `ai/security/` (`agent-permissions.md`, `destructive-actions.md`, `prompt-injection.md`, `secret-protection.md`) to align with the OWASP GenAI Security Project (2026 Standard), establishing explicit mitigations for LLM01 Prompt Injection, LLM02 Sensitive Information Disclosure, LLM05 Improper Output Handling, and LLM06 Excessive Agency.
- Upgraded all tool integration guides in `ai/tools/` (`chatgpt.md`, `claude.md`, `claude-code.md`, `codex.md`, `github-copilot.md`, `github-agents.md`) to 2026 Context Engineering standards, incorporating system prompt caching layouts, XML tag prompt shielding, and OWASP GenAI security guardrails.
- Upgraded all workflow specifications in `ai/workflows/` (`release-preparation.md`, `release-audit.md`, `ai-qa.md`, `debugging.md`, `metadata-generation.md`, `rejection-analysis.md`, `release-notes.md`) to 2026 Context Engineering standards, incorporating static system prompt caching, single-mount XML tags, step-by-step reasoning steps, and machine-readable YAML reports.
- Validated and updated all commerce handbooks in `monetization/` (`README.md`, `in-app-purchases.md`, `subscriptions.md`, `consumables.md`, `non-consumables.md`, `subscription-lifecycle.md`, `offers.md`, `pricing.md`, `receipt-validation.md`, `entitlements.md`) against verified 2026 platform specifications, including Google Play Billing Library 8.0/9.0+ submission gates, Billing Choice Program APIs, StoreKit 2 Signed JWS transactions, and Apple Guideline 3.1.2 paywall disclosure rules.
- Validated and updated all store administration handbooks in `store-accounts/` (`README.md`, `apple-developer.md`, `google-play-console.md`, `app-store-connect.md`, `organization-accounts.md`, `roles-and-permissions.md`, `banking-and-tax.md`, `agreements.md`, `account-recovery.md`) against verified 2026 platform specifications, including mandatory D-U-N-S business identity matching, Apple 2FA requirements, Google Play Personal account closed testing gates (12 testers for 14 days), W-8BEN-E / W-9 tax withholding defense, and App Store Connect `.p8` API key security.
- Validated and updated all store operations handbooks in `store-operations/` (`README.md`, `metadata.md`, `app-listing.md`, `screenshots.md`, `app-previews.md`, `localization.md`, `app-review.md`, `rejection-handling.md`, `resubmission.md`, `data-safety.md`, `privacy-information.md`, `content-ratings.md`) against verified 2026 platform specifications, including iPhone 6.9" display class screenshot specs (1320x2868), alpha channel stripping, Guideline 2.3 metadata limits, demo credential rules, Google Play Data Safety, App Privacy Labels, and IARC content ratings.
- Validated and updated all Android publishing handbooks in `publishing/android/` (`README.md`, `app-bundle.md`, `play-console.md`, `internal-testing.md`, `closed-testing.md`, `production-release.md`, `app-review.md`, `metadata.md`, `screenshots.md`) against verified 2026 platform specifications, including Target API Level 36 (Android 16), Android App Bundle (`.aab`) compilation, R8 obfuscation, Play App Signing, Fastlane Supply / EAS Submit automation, Personal account testing gates (12 testers for 14 days), and Staged Rollout lifecycles (1% → 100%).
- Validated and updated all cross-platform publishing handbooks in `publishing/cross-platform/` (`README.md`, `submission.md`, `assets.md`, `metadata.md`, `release-notes.md`) against verified 2026 platform specifications, including EAS Submit (`eas.json`), Fastlane multi-platform automation, GitHub Actions CI/CD matrix pipelines, Master App Icon (1024x1024) & Android Adaptive Icon setup, dynamic `Intl` currency localization, master metadata JSON schemas, and Keep a Changelog standards.
- Validated and updated all iOS publishing handbooks in `publishing/ios/` (`README.md`, `build-upload.md`, `app-store-connect.md`, `testflight.md`, `production-release.md`, `app-review.md`, `metadata.md`, `screenshots.md`) against verified 2026 platform specifications, including `.ipa` archiving, Transporter CLI, App Store Connect API keys (`.p8`), TestFlight Internal (100 testers) vs External (10,000 testers), 7-Day Phased Releases (Day 1: 1% → Day 7: 100%), `ITSAppUsesNonExemptEncryption`, and canonical 6.9" Super Retina XDR screenshots (1320x2868).
- Validated and updated all official source documentation registries in `sources/` (`README.md`, `apple.md`, `google.md`, `expo.md`, `react-native.md`, `fastlane.md`, `tooling.md`) against verified 2026 platform specifications, detailing primary developer portals, platform policy citations, API deprecation tracking, and source verification governance rules.
- Validated and updated all operational release templates in `templates/` (`README.md`, `release-plan.md`, `release-checklist.md`, `rejection-response.md`, `release-notes.md`, `changelog.md`, `app-store-description.md`, `play-store-description.md`, `privacy-questionnaire.md`, `incident-report.md`) against verified 2026 platform specifications, detailing master release schedules, risk matrices, pre-release checklists, Resolution Center rejection response forms, store metadata templates, and incident post-mortem frameworks.
- Validated and updated all mobile QA handbooks in `testing/` (`README.md`, `unit.md`, `integration.md`, `e2e.md`, `ios.md`, `android.md`, `device-matrix.md`, `device-testing.md`, `accessibility.md`, `localization.md`, `deep-links.md`, `push-notifications.md`, `payments.md`, `network.md`, `offline.md`, `performance.md`, `release-smoke-tests.md`, `upgrade-migrations.md`) against verified 2026 platform specifications, detailing Jest 29+, React Native Testing Library (RNTL), Maestro/Detox E2E automation, iOS Simulator `simctl` & Android `adb`, VoiceOver/TalkBack accessibility, StoreKit 2 & Play Billing 8/9+ sandbox testing, Proxyman network latency injection, Hermes memory profiling, and 10-minute Release Candidate Smoke Test suites.
- Validated and updated all diagnostic triage handbooks in `troubleshooting/` (`README.md`, `build-fails.md`, `signing-fails.md`, `archive-fails.md`, `ci-fails.md`, `upload-fails.md`, `metadata-rejected.md`, `review-rejected.md`, `privacy-rejected.md`, `crash-after-release.md`, `push-notifications-broken.md`, `deep-links-broken.md`, `payments-broken.md`, `app-not-visible.md`) against verified 2026 platform specifications, detailing Gradle & CocoaPods compilation failures, `ITMS-90161` code signing errors, R8 ProGuard class stripping, `ITMS-90032` alpha transparency, Resolution Center rejection triage, Sentry Hermes stack trace de-obfuscation, FCM HTTP v1 migration, and AASA Universal Link verification.
- Validated and updated all privacy compliance handbooks in `privacy-compliance/` (`README.md`, `privacy-policy.md`, `data-collection.md`, `data-deletion.md`, `data-retention.md`, `tracking.md`, `advertising.md`, `analytics.md`, `children.md`, `health-data.md`, `permissions.md`, `regional-requirements.md`) against verified 2026 platform specifications, detailing Apple `PrivacyInfo.xcprivacy` manifests & Required Reason APIs, Google Play Data Safety & mandatory web deletion URL, App Tracking Transparency (ATT) & IDFA rules, COPPA & Kids Category neutral age gates, HealthKit & Health Connect entitlements, Android 14+ Selected Photos, EU GDPR, CCPA/CPRA, and South Korea GRAC age rating RCN overrides.
- Validated and updated all pre-release quality gate handbooks in `pre-release/` (`README.md`, `release-readiness.md`, `final-release-checklist.md`, `dependency-audit.md`, `security-audit.md`, `privacy-audit.md`, `permissions-audit.md`, `configuration-audit.md`, `asset-audit.md`, `metadata-audit.md`, `performance-audit.md`, `accessibility-audit.md`) against verified 2026 platform specifications, detailing GO/NO-GO readiness criteria, OWASP Mobile Top 10 security scans, dependency vulnerability checks (`npm audit`), Target SDK 36 configuration, 6.9" screenshot asset validation, Hermes heap performance benchmarks, and VoiceOver/TalkBack accessibility audits.
- Validated and updated all post-release operations handbooks in `post-release/` (`README.md`, `rollout-monitoring.md`, `monitoring.md`, `crash-analysis.md`, `performance.md`, `analytics.md`, `reviews-and-ratings.md`, `incident-response.md`, `hotfix.md`, `rollback.md`, `version-recall.md`, `release-retrospective.md`) against verified 2026 platform specifications, detailing iOS 7-Day Phased Release & Android Staged Rollout monitoring, Sentry crash triage & symbolication, Android Vitals ANR tracking, in-app review prompts (`StoreReview`), emergency hotfixes & EAS Update OTA patches, production rollbacks, server-driven forced update UI gates, and blameless 5 Whys retrospectives.
- Validated and updated all playbook governance handbooks in `governance/` (`README.md`, `source-verification.md`, `documentation-style.md`, `contribution-guide.md`, `versioning.md`, `maintenance.md`) against verified 2026 platform specifications, detailing empirical source verification protocols, primary authority hierarchies, CLAUDE.md structural markdown standards, pull request quality gates, Semantic Versioning 2.0.0 rules, and 90-day quarterly verification audit cycles.
- Validated and updated all code signing & credential management handbooks in `signing/` (`README.md`, 3 sub-indices, and 13 domain guides across `ios/`, `android/`, and `security/`: `certificates`, `provisioning-profiles`, `distribution`, `api-keys`, `key-rotation`, `keystore`, `upload-key`, `play-app-signing`, `secret-storage`, `ci-secrets`, `access-control`, `recovery`) against verified 2026 platform specifications, detailing Apple Distribution Certificates, Fastlane Match (`fastlane match appstore`), App Store Connect API keys (`.p8`), PKCS12 Android Keystores (RSA 4096-bit), Google Play App Signing (Cloud HSM KMS), PEPK tool key exports, HashiCorp Vault / AWS Secrets Manager integration, and emergency key compromise containment protocols.
- Validated and updated all release engineering handbooks in `release-engineering/` (`README.md`, 3 sub-indices, and 14 domain guides across `eas/`, `fastlane/`, and `github-actions/`: `build-systems`, `release-configurations`, `environment-management`, `release-pipelines`, `app-store-connect-api`, `google-play-api`, `eas-build`, `eas-submit`, `eas-update`, `fastfile`, `appfile`, `matchfile`, `workflows`, `action-templates`) against verified 2026 platform specifications, detailing Xcode `xcodebuild` CLI & Android Gradle `./gradlew bundleRelease`, Hermes bytecode compilation, `eas.json` profiles, EAS Submit store upload automation, EAS Update Over-The-Air (OTA) JS patching, Fastlane Match/Pilot/Supply lanes, and GitHub Actions `macos-14` matrix release pipelines (`release-pipeline.yml`).
- Validated and updated all progressive release strategy handbooks in `release-strategy/` (`README.md` and 8 strategy guides: `release-trains`, `phased-release`, `staged-rollouts`, `beta-testing`, `feature-flags`, `kill-switches`, `hotfixes`, `emergency-release`) against verified 2026 platform specifications, detailing bi-weekly 14-day release train governance, iOS 7-Day Phased Release (Day 1: 1% → Day 7: 100%), Google Play Staged Rollouts (ANR < 0.47% halting threshold), TestFlight External (10,000 testers) & Play Closed Testing (12 testers / 14 days), LaunchDarkly / Firebase remote feature flags, dynamic remote kill switches, emergency `hotfix/v1.2.1` deployment, and 2-hour expedited store reviews.
- Validated and updated all release automation script handbooks in `scripts/` (`README.md`, 5 category indices, and 5 CLI automation guides across `version-bump/`, `build-validation/`, `metadata-validation/`, `store-validation/`, and `release-audit/`: `version-bump`, `validate-build`, `validate-metadata`, `validate-store`, `audit-release`) against verified 2026 platform specifications, detailing cross-platform version bumping (`package.json`, `app.json`, `Info.plist`, `build.gradle`), binary artifact inspection (`.ipa`/`.aab` Hermes bytecode & icon 1024x1024 no alpha checks), store metadata Guideline 2.3.7 character limit assertions, App Store Connect / Play API connection handshakes, and OWASP / Target SDK 36 quality gate audits.

### Deprecated

- None yet.

### Removed

- None yet.

### Fixed

- None yet.

### Security

- Established guidance for protecting signing credentials, API keys, CI secrets, and AI agent access.

---

## Versioning

The project uses release versions to communicate meaningful changes to the playbook.

A version should be created when a group of changes is coherent enough to be useful as a reference point.

Documentation-only corrections do not need to be treated as major releases.

### Version guidelines

**Major**

Use when the project introduces a significant structural or scope change that may affect how contributors or users navigate the playbook.

Examples:

- major repository restructuring
- major change in project scope
- removal of a previously supported framework
- breaking changes to contribution conventions

**Minor**

Use when meaningful new capabilities or coverage are added without breaking the existing structure.

Examples:

- new framework support
- new release workflow
- new AI agent workflow
- new automation
- new platform-specific guidance

**Patch**

Use for small corrections and maintenance changes.

Examples:

- correcting commands
- fixing broken links
- correcting inaccurate documentation
- updating examples
- clarifying existing instructions

---

## What belongs in the changelog

Record changes that materially affect how developers use the playbook.

Good changelog entries:

```text
Added Expo EAS production submission workflow.

Added troubleshooting guide for Android release signing failures.

Fixed incorrect App Store Connect credential setup instructions.

Added Claude Code release-audit workflow.

Added community framework contribution guide.
```

Avoid entries such as:

```text
Updated wording.

Changed a heading.

Fixed a typo.
```

unless the change has meaningful impact on users or contributors.

---

## Keeping the changelog useful

The changelog should answer three questions quickly:

1. **What changed?**
2. **Why does it matter?**
3. **Who is affected?**

Prefer concise entries over implementation detail.

For significant changes, link to the relevant documentation, pull request, issue, or release where useful.

---

## Maintenance

When making a meaningful change:

1. Update the relevant documentation.
2. Add the change to `Unreleased`.
3. Use the appropriate category.
4. Keep the entry concise and user-focused.
5. Remove the entry from `Unreleased` when it becomes part of a release.
6. Create the new version section with the release date.

Do not rewrite historical changelog entries simply because the current implementation has changed.

Historical entries should describe what was true at the time of that release.

---

## Release format

Published releases should follow this structure:

```markdown
## [0.1.0] - YYYY-MM-DD

### Added

- Added ...

### Changed

- Changed ...

### Fixed

- Fixed ...

### Security

- Improved ...
```

Only include categories that contain actual changes.

---

## Release philosophy

The changelog is not a commit log.

It exists to communicate meaningful changes to developers and contributors.

Prefer:

> Added an Expo EAS production submission workflow covering build, credentials, submission, and verification.

over:

> Added 14 files under `frameworks/expo/`.

The first explains the value.

The second describes implementation noise.

---

## Current project direction

The Mobile Release Playbook currently focuses on **Expo and React Native** as first-class implementations.

The architecture remains open to community contributions for additional frameworks and mobile stacks.

Future changelog entries should make framework additions, AI workflow improvements, automation changes, and important release-process changes easy to discover.
