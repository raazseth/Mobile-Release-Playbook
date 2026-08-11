mobile-release-playbook/
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