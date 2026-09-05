# Mobile Release Playbook Folder Structure

This document outlines the complete directory structure and taxonomy of the **Mobile Release Playbook** repository.

---

# 1. Complete Directory Tree

```text
mobile-release-playbook/
├── README.md                          # Master Playbook Architecture & Subsystem Taxonomy
├── ARCHITECTURE.md                    # Core Release Architecture Blueprint
├── FOLDER_STRUCTURE.md                # Complete Repository Directory Tree
├── CONTRIBUTING.md                    # Contribution Guidelines & Verification Rules
├── CHANGELOG.md                       # Repository Version History (Keep a Changelog)
├── LICENSE                            # MIT Open-Source License
│
├── foundations/                       # Release Engineering Foundations Subsystem
│   ├── README.md                      # Subsystem Index & Architecture Overview
│   ├── mobile-release-lifecycle.md    # End-to-End Release Lifecycle Stage Gates
│   ├── release-environments.md        # Environment Isolation (Dev, Staging, Prod)
│   ├── project-configuration.md       # App Configuration (app.json, Xcode, Gradle)
│   ├── identifiers.md                 # Bundle IDs, Package Names, App IDs
│   ├── versioning.md                  # SemVer 2.0.0 & Monotonic Build Increments
│   ├── device-support.md              # OS Version Policies (Android 16, iOS 18)
│   └── dependency-management.md       # Lockfile Pinning & Security Audits
│
├── store-accounts/                    # Store Administration & Compliance Subsystem
│   ├── README.md                      # Subsystem Index & Architecture Overview
│   ├── apple-developer.md             # Apple Developer Program & D-U-N-S Matching
│   ├── app-store-connect.md           # App Store Connect Management & API Keys
│   ├── google-play-console.md         # Google Play Console & Personal Account Gates
│   ├── organization-accounts.md       # Enterprise Organization Identity Defense
│   ├── roles-and-permissions.md       # Least Privilege RBAC & 2FA Governance
│   ├── agreements.md                  # Paid Applications & Legal Agreements
│   ├── banking-and-tax.md             # W-8BEN-E Tax Withholding & Banking Setup
│   └── account-recovery.md            # Emergency Account Recovery Protocols
│
├── signing/                           # Code Signing & Credential Management Subsystem
│   ├── README.md                      # Master Signing Architecture & Governance
│   ├── ios/                           # iOS Code Signing Infrastructure
│   │   ├── README.md                  # iOS Signing Index
│   │   ├── certificates.md            # Apple Distribution & Development Certs (.p12)
│   │   ├── provisioning-profiles.md   # Mobile Provisioning Profiles & Entitlements
│   │   ├── distribution.md            # Fastlane Match Encrypted Cert Sync
│   │   ├── api-keys.md                # App Store Connect API Keys (.p8)
│   │   └── key-rotation.md            # 90-Day Dual-Certificate Rotation Protocol
│   ├── android/                       # Android Code Signing Infrastructure
│   │   ├── README.md                  # Android Signing Index
│   │   ├── keystore.md                # Java keytool PKCS12 Keystores (RSA 4096-bit)
│   │   ├── upload-key.md              # Play Console Upload Key Registration
│   │   ├── play-app-signing.md        # Google Play App Signing (Cloud HSM KMS)
│   │   └── key-rotation.md            # PEPK Tool Key Exports & Reset Protocols
│   └── security/                      # Cryptographic Secret Storage
│       ├── README.md                  # Secret Security Index
│       ├── secret-storage.md          # HashiCorp Vault & AWS Secrets Manager
│       ├── ci-secrets.md              # CI Secret Injection & Base64 Key Encoding
│       ├── access-control.md          # RBAC & YubiKey Hardware 2FA Enforcement
│       └── recovery.md                # Emergency Key Compromise Containment
│
├── testing/                           # Mobile Quality Assurance Subsystem
│   ├── README.md                      # Subsystem Index & QA Architecture Overview
│   ├── unit.md                        # Jest 29+ Unit Testing Standards
│   ├── integration.md                 # React Native Testing Library (RNTL)
│   ├── e2e.md                         # Maestro & Detox End-to-End Automation
│   ├── device-testing.md              # Physical Hardware Device Testing Protocols
│   ├── device-matrix.md               # iOS & Android Device Matrix Strategy
│   ├── ios.md                         # Xcode Simulator CLI simctl Testing
│   ├── android.md                     # Android Emulator CLI adb Testing
│   ├── accessibility.md               # VoiceOver & TalkBack WCAG 2.1 AA Audits
│   ├── localization.md                # RTL Layout & Internationalization Testing
│   ├── performance.md                 # Hermes Memory Heap Profiling & FPS Metrics
│   ├── network.md                     # Latency Injection & Proxyman Traffic Audits
│   ├── offline.md                     # Offline State & Data Sync Verification
│   ├── deep-links.md                  # Custom Scheme & Universal Link Audits
│   ├── push-notifications.md          # APNs & FCM Token Testing Protocols
│   ├── payments.md                    # StoreKit 2 & Play Billing Sandbox Testing
│   ├── upgrade-migrations.md          # Database Migration & Schema Compatibility
│   └── release-smoke-tests.md         # 10-Minute Release Candidate Smoke Tests
│
├── pre-release/                       # Pre-Release Quality Gates Subsystem
│   ├── README.md                      # Subsystem Index & Quality Gate Architecture
│   ├── release-readiness.md           # GO/NO-GO Readiness Criteria & Severity Matrix
│   ├── dependency-audit.md            # Vulnerability Audits (npm audit) & Licenses
│   ├── security-audit.md              # OWASP Mobile Top 10 Security Scans
│   ├── privacy-audit.md               # PrivacyInfo.xcprivacy & Data Safety Scans
│   ├── permissions-audit.md           # Info.plist & Manifest Permission Audits
│   ├── performance-audit.md           # Cold Launch Latency & Hermes Performance
│   ├── accessibility-audit.md         # VoiceOver/TalkBack & Contrast Audits
│   ├── metadata-audit.md              # Store Metadata Guideline 2.3.7 Audits
│   ├── asset-audit.md                 # 6.9" Screenshots & Icon Asset Validation
│   ├── configuration-audit.md         # Target SDK 36 & Encryption Configuration
│   └── final-release-checklist.md     # Pre-Flight Pre-Release Verification Checklist
│
├── publishing/                        # Binary Packaging & Distribution Subsystem
│   ├── README.md                      # Subsystem Index & Distribution Architecture
│   ├── ios/                           # iOS App Store Publishing
│   │   ├── README.md                  # iOS Publishing Index
│   │   ├── app-store-connect.md       # App Store Connect Setup & Version Creation
│   │   ├── build-upload.md            # Xcode Archive CLI & Transporter Uploads
│   │   ├── testflight.md              # TestFlight Internal & External Groups
│   │   ├── metadata.md                # App Store Description & Search Keywords
│   │   ├── screenshots.md             # 6.9" Super Retina Display Class Assets
│   │   ├── app-review.md              # App Review Submission & Guideline Compliance
│   │   └── production-release.md      # iOS 7-Day Phased Release Deployment
│   ├── android/                       # Android Google Play Publishing
│   │   ├── README.md                  # Android Publishing Index
│   │   ├── play-console.md            # Google Play Console Setup & Track Management
│   │   ├── app-bundle.md              # Android App Bundle (.aab) & R8 Minification
│   │   ├── internal-testing.md        # Play Internal Testing Track Setup
│   │   ├── closed-testing.md          # Play Closed Testing Gate (12 Testers / 14 Days)
│   │   ├── metadata.md                # Play Store Listing & Feature Graphic
│   │   ├── screenshots.md             # Android Phone & Tablet Screenshot Specs
│   │   ├── app-review.md              # Google Play App Review Submission
│   │   └── production-release.md      # Android Staged Rollout Deployment (1% -> 100%)
│   └── cross-platform/                # Multi-Platform Automated Submissions
│       ├── README.md                  # Cross-Platform Publishing Index
│       ├── metadata.md                # Master JSON Metadata Schema
│       ├── assets.md                  # Master Asset Generation (1024x1024 Icon)
│       ├── release-notes.md           # Multi-Language Release Notes Generator
│       └── submission.md              # Unified EAS Submit & Fastlane Automation
│
├── store-operations/                  # Product Page & Asset Operations Subsystem
│   ├── README.md                      # Subsystem Index & Operations Architecture
│   ├── app-listing.md                 # Store Listing Strategy & Brand Rules
│   ├── metadata.md                    # Character Limit Assertions & Keyword Optimization
│   ├── screenshots.md                 # Display Class Screenshots & Alpha Stripping
│   ├── app-previews.md                # App Preview Video Specs & Framing Rules
│   ├── localization.md                # Store Page Localization (40+ Locales)
│   ├── privacy-information.md         # App Privacy Label Configuration
│   ├── content-ratings.md             # IARC & South Korea GRAC Rating RCN Overrides
│   ├── data-safety.md                 # Google Play Data Safety Form Declarations
│   ├── app-review.md                  # Store Review Submission & Demo Credentials
│   ├── rejection-handling.md          # Resolution Center Rejection Triage Protocols
│   └── resubmission.md                # Expedited Resubmission & Appeal Workflows
│
├── monetization/                      # Commerce & In-App Purchase Subsystem
│   ├── README.md                      # Subsystem Index & Monetization Architecture
│   ├── subscriptions.md               # Auto-Renewable Subscriptions & Billing
│   ├── in-app-purchases.md            # StoreKit 2 & Play Billing Library 8/9+
│   ├── consumables.md                 # Consumable Product Purchasing & Balance
│   ├── non-consumables.md             # Non-Consumable Restores & Entitlements
│   ├── pricing.md                     # App Store Price Tiers & Play Equalized Prices
│   ├── offers.md                      # Promotional Offers & Introductory Discounts
│   ├── entitlements.md                # Server-Side Entitlement Management
│   ├── receipt-validation.md          # Signed JWS Receipt Validation & Verification
│   └── subscription-lifecycle.md      # Grace Periods, Billing Engine Retention, & Refunds
│
├── privacy-compliance/                # Global Privacy & Regulatory Compliance Subsystem
│   ├── README.md                      # Subsystem Index & Regulatory Architecture
│   ├── privacy-policy.md              # Privacy Policy Requirements & Hosting Rules
│   ├── data-collection.md             # Data Collection Audits & Required Reason APIs
│   ├── permissions.md                 # Runtime Permissions & Android 14+ Selected Photos
│   ├── tracking.md                    # App Tracking Transparency (ATT) & IDFA Rules
│   ├── analytics.md                   # Privacy-Preserving Telemetry & PII Filtering
│   ├── advertising.md                 # Google UMP SDK Ad Consent & Privacy Sandbox
│   ├── data-retention.md              # Secure Store Data Retention & Limits
│   ├── data-deletion.md               # In-App & Web Account Deletion URL Rules
│   ├── children.md                    # COPPA Compliance & Neutral Age Gates
│   ├── health-data.md                 # HealthKit & Health Connect Entitlements
│   └── regional-requirements.md       # EU GDPR, CCPA/CPRA, & Korea GRAC Overrides
│
├── integrations/                      # SDK & Native Module Integrations Subsystem
│   ├── README.md                      # Master Integrations Subsystem Index
│   ├── push-notifications/            # APNs & FCM HTTP v1 Integration Handbooks
│   │   ├── README.md                  # Push Notifications Index
│   │   └── firebase-fcm.md            # FCM HTTP v1 API Migration & Payload Spec
│   ├── deep-links/                    # React Navigation Deep Linking Architecture
│   │   ├── README.md                  # Deep Links Index
│   │   └── react-navigation.md        # URL Scheme & Intent Handling Implementation
│   ├── universal-links/               # Apple Universal Links (`apple-app-site-association`)
│   │   ├── README.md                  # Universal Links Index
│   │   └── configuration.md           # AASA JSON Host Validation & Entitlements
│   ├── app-links/                     # Android App Links (`assetlinks.json`)
│   │   ├── README.md                  # App Links Index
│   │   └── configuration.md           # AssetLinks JSON SHA-256 Fingerprint Setup
│   ├── oauth/                         # Social Authentication & Single Sign-On
│   │   ├── README.md                  # OAuth Index
│   │   └── apple-google-auth.md       # Sign in with Apple & Google Identity SDKs
│   ├── maps/                          # MapKit & Google Maps SDK Integrations
│   │   ├── README.md                  # Maps Index
│   │   └── google-maps.md             # Native Maps API Key Security & Layouts
│   ├── payments/                      # Stripe & Payment Gateway Integrations
│   │   ├── README.md                  # Payments Index
│   │   └── stripe.md                  # Stripe PaymentSheet & Apple/Google Pay
│   ├── analytics/                     # Segment, Mixpanel, Amplitude, & Firebase
│   │   ├── README.md                  # Analytics Index
│   │   └── segment-mixpanel.md        # Analytics PII Filtering & Privacy Manifests
│   ├── crash-reporting/               # Sentry & Crashlytics Telemetry
│   │   ├── README.md                  # Crash Reporting Index
│   │   └── sentry-crashlytics.md      # Hermes JS Source Maps & .dSYM Symbolication
│   ├── attribution/                   # AppsFlyer & Adjust Attribution SDKs
│   │   ├── README.md                  # Attribution Index
│   │   └── appsflyer-adjust.md        # SKAdNetwork 4.0 & Conversion Value Rules
│   ├── camera/                        # Native Camera & Media Picker APIs
│   │   ├── README.md                  # Camera Index
│   │   └── expo-camera.md             # Camera Permissions & Media Library Access
│   ├── location/                      # GPS & Geofencing Location Services
│   │   ├── README.md                  # Location Index
│   │   └── location-services.md       # Background Location Entitlements & Compliance
│   └── background-processing/         # Background Tasks & WorkManager
│       ├── README.md                  # Background Processing Index
│       └── background-fetch.md        # BGTaskScheduler & WorkManager Execution Rules
│
├── release-engineering/               # Build Systems & Automation Subsystem
│   ├── README.md                      # Subsystem Index & Release Infrastructure
│   ├── build-systems.md               # Xcode xcodebuild & Android ./gradlew bundleRelease
│   ├── release-configurations.md      # Multi-Environment Configurations & Profiles
│   ├── environment-management.md      # Environment Variables & Secret Vault Scoping
│   ├── release-pipelines.md           # CI/CD Release Pipeline Branching & Tags
│   ├── app-store-connect-api.md       # App Store Connect API Automation (.p8 keys)
│   ├── google-play-api.md             # Google Play Developer API v3 Automation
│   ├── eas/                           # Expo Application Services
│   │   ├── README.md                  # EAS Subsystem Index
│   │   ├── eas-build.md               # EAS Build Profiles & Cloud Runners
│   │   ├── eas-submit.md              # EAS Submit Auto-Upload Configurations
│   │   └── eas-update.md              # EAS Update OTA Patching & Runtime Versions
│   ├── fastlane/                      # Fastlane Automation Toolchain
│   │   ├── README.md                  # Fastlane Subsystem Index
│   │   ├── fastfile.md                # Fastlane Fastfile Lane Architectures
│   │   ├── appfile.md                 # Fastlane Appfile Bundle ID Mappings
│   │   └── matchfile.md               # Fastlane Matchfile Certificate Storage
│   └── github-actions/                # GitHub Actions Matrix CI/CD Workflows
│       ├── README.md                  # GitHub Actions Subsystem Index
│       ├── workflows.md               # Production Release Pipeline YAML Specifications
│       └── action-templates.md        # Reusable Composite Actions (Node, Java, Xcode)
│
├── release-strategy/                  # Progressive Deployment & Cadence Subsystem
│   ├── README.md                      # Subsystem Index & Progressive Release Architecture
│   ├── release-trains.md              # Bi-Weekly 14-Day Release Train Schedules
│   ├── phased-release.md              # iOS 7-Day Phased Release (Day 1: 1% -> Day 7: 100%)
│   ├── staged-rollouts.md             # Android Google Play Staged Rollouts & Vitals Gates
│   ├── beta-testing.md                # TestFlight External & Play Closed Testing (12/14)
│   ├── feature-flags.md               # Remote Feature Flags (LaunchDarkly, PostHog)
│   ├── kill-switches.md               # Dynamic Remote Kill Switches & Fallback UI
│   ├── hotfixes.md                    # Emergency Hotfix Branching (hotfix/v1.2.1) & OTA
│   └── emergency-release.md           # Severe Incident Escalation & Forced Updates
│
├── post-release/                      # Telemetry, Monitoring, & Retrospectives Subsystem
│   ├── README.md                      # Subsystem Index & Operational Telemetry Architecture
│   ├── monitoring.md                  # Live Production Telemetry (Sentry / APM)
│   ├── crash-analysis.md              # Production Crash Triage & Hermes De-Obfuscation
│   ├── performance.md                 # Android Vitals ANR Tracking (< 0.47%) & Cold Starts
│   ├── analytics.md                   # Release Adoption Cohort Tracking & Retention
│   ├── reviews-and-ratings.md         # In-App Review Prompts (StoreReview) & SLAs
│   ├── rollout-monitoring.md          # Phased Release & Staged Rollout Halting Gates
│   ├── incident-response.md           # Emergency Outage Response & P0 SLAs
│   ├── hotfix.md                      # Emergency Native Hotfixes & EAS OTA Deployments
│   ├── rollback.md                    # Production Rollback & Phased Release Halting
│   ├── version-recall.md              # Server-Driven Forced Update UI Modals
│   └── release-retrospective.md       # Blameless 5 Whys Retrospectives & Audits
│
├── ai/                                # AI Orchestration, Agents, & Context Engineering
│   ├── README.md                      # Master AI Subsystem Architecture & OWASP Security
│   ├── getting-started/               # AI Integration & Tool Onboarding
│   │   ├── README.md                  # Getting Started Index
│   │   ├── when-to-use-ai.md          # Guidance on Effective AI Agent Deployment
│   │   └── when-not-to-use-ai.md      # Boundaries & Prohibited AI Execution Scenarios
│   ├── tools/                         # AI Coding Assistant Tool Integrations
│   │   ├── README.md                  # Tools Index
│   │   ├── chatgpt.md                 # OpenAI ChatGPT & Custom GPT Integration
│   │   ├── claude.md                  # Anthropic Claude & System Prompt Caching
│   │   ├── claude-code.md             # Claude Code CLI Agent Rules & Integration
│   │   ├── codex.md                   # Codex Code Generation Guidelines
│   │   ├── github-copilot.md          # GitHub Copilot Extension Configuration
│   │   └── github-agents.md           # GitHub Copilot Workspace Agents
│   ├── agents/                        # Autonomous Specialized AI Agents
│   │   ├── README.md                  # Autonomous Agents Index
│   │   ├── release-auditor.md         # Autonomous Pre-Flight Release Audit Agent
│   │   ├── qa-agent.md                # Autonomous Quality Assurance & E2E Test Agent
│   │   ├── debugging-agent.md         # Autonomous Hermes Crash Debugging Agent
│   │   ├── metadata-agent.md          # Store Metadata & Character Assertion Agent
│   │   ├── privacy-auditor.md         # PrivacyInfo.xcprivacy & Data Safety Auditor
│   │   ├── security-auditor.md        # OWASP Mobile Top 10 Security Audit Agent
│   │   ├── dependency-auditor.md      # Dependency Vulnerability Audit Agent
│   │   ├── rejection-analyzer.md      # Resolution Center Rejection Analysis Agent
│   │   └── release-manager.md         # Autonomous Release Orchestration Manager
│   ├── workflows/                     # Automated Multi-Step AI Release Workflows
│   │   ├── README.md                  # Workflows Index
│   │   ├── release-audit.md           # Automated Pre-Flight Quality Gate Workflow
│   │   ├── ai-qa.md                   # Automated QA Execution & Testing Workflow
│   │   ├── metadata-generation.md     # Multi-Language Store Metadata Generator
│   │   ├── release-notes.md           # Dynamic Changelog & Release Notes Generator
│   │   ├── rejection-analysis.md      # Store Rejection Analysis & Response Workflow
│   │   ├── debugging.md               # Hermes Stack Trace Debugging Workflow
│   │   └── release-preparation.md     # Complete Release Candidate Preparation
│   ├── orchestration/                 # Multi-Agent Coordination & MCP Routing
│   │   ├── README.md                  # Orchestration Index
│   │   ├── agent-workflows.md         # Agent Workflow Chaining Architecture
│   │   ├── subagents.md               # Subagent Delegation & Context Isolation
│   │   ├── mcp.md                     # Model Context Protocol (MCP) Tool Routing
│   │   ├── tool-permissions.md        # Execution Permission Scopes & Guardrails
│   │   ├── human-approval.md          # Human-in-the-Loop Approval Boundaries
│   │   └── bounded-autonomy.md        # Bounded Autonomy Limits & Safety Rules
│   ├── prompts/                       # Production Prompt Templates (Context Engineered)
│   │   ├── README.md                  # Prompt Templates Index
│   │   ├── release-audit.md           # Pre-Release Audit Prompt Template
│   │   ├── qa.md                      # QA & Test Automation Prompt Template
│   │   ├── debugging.md               # Crash Debugging Prompt Template
│   │   ├── metadata.md                # Metadata Generation Prompt Template
│   │   ├── aso-optimization.md        # App Store Optimization (Keywords, Copy, A/B Variants)
│   │   ├── rejection-analysis.md      # Store Rejection Analysis Prompt Template
│   │   └── release-notes.md           # Release Notes Prompt Template
│   ├── context/                       # Static Prompt Caching & Context Layouts
│   │   ├── README.md                  # Context Layouts Index
│   │   ├── repository-context.md      # Repository Structure Context Schema
│   │   ├── release-context.md         # Release Candidate State Context Schema
│   │   └── store-context.md           # App Store & Play Console Policy Context
│   ├── evals/                         # AI Evaluation & Regression Test Frameworks
│   │   ├── README.md                  # Evaluation Index
│   │   ├── agent-evaluation.md        # Agent Task Accuracy Benchmark Suite
│   │   ├── regression-tests.md        # Prompt Regression Test Suite
│   │   ├── hallucination-tests.md     # Specification Hallucination Detector
│   │   └── workflow-evaluation.md     # End-to-End Workflow Evaluation Benchmarks
│   └── security/                      # OWASP GenAI Security Project (2026 Standard)
│       ├── README.md                  # AI Security Index
│       ├── prompt-injection.md        # LLM01 Prompt Injection Shielding Rules
│       ├── secret-protection.md       # LLM02 Sensitive Information Redaction
│       ├── agent-permissions.md       # LLM06 Excessive Agency & Least Privilege
│       └── destructive-actions.md     # Dangerous Action Guardrails & Controls
│
├── frameworks/                        # Mobile Application Frameworks Subsystem
│   ├── README.md                      # Subsystem Parent Index & Architecture Overview
│   ├── react-native/                  # React Native CLI Native Toolchain
│   │   ├── README.md                  # React Native Subsystem Index
│   │   ├── local-setup.md             # Local Machine Setup for Release Builds
│   │   ├── ios-release.md             # Native iOS Release Configuration
│   │   ├── android-release.md         # Native Android Gradle Release Config
│   │   ├── signing.md                 # Native Xcode & Gradle Signing Setups
│   │   ├── fastlane.md                # Fastlane Automation for React Native CLI
│   │   ├── eas.md                     # EAS Integration for Bare React Native Apps
│   │   └── common-failures.md         # Native Compilation Failure Triage
│   ├── expo/                          # Expo Framework & Managed Workflows
│   │   ├── README.md                  # Expo Subsystem Index
│   │   ├── local-setup.md             # Local Machine Setup: EAS Cloud vs. Local Builds
│   │   ├── development-builds.md      # Expo Development Client Build Setup
│   │   ├── build.md                   # Local & Cloud EAS Build Executions
│   │   ├── submit.md                  # EAS Submit Store Upload Setup
│   │   ├── eas.md                     # EAS Services Configuration (eas.json)
│   │   ├── app-config.md              # Dynamic App Configuration (app.config.ts)
│   │   ├── credentials.md             # EAS Credentials & Secret Sync
│   │   ├── updates.md                 # EAS Update Over-The-Air (OTA) Patches
│   │   ├── ci-cd.md                   # GitHub Actions CI/CD Integration for Expo
│   │   └── common-failures.md         # Expo Build & Prebuild Failure Triage
│   └── community/                     # Third-Party Community Libraries
│       ├── README.md                  # Community Modules Index
│       └── adding-a-framework.md      # Guidelines for Adding Framework Modules
│
├── troubleshooting/                   # Diagnostic Triage & Incident Resolution Subsystem
│   ├── README.md                      # Subsystem Index & Troubleshooting Architecture
│   ├── build-fails.md                 # Gradle, CocoaPods, & Metro Compilation Failures
│   ├── signing-fails.md               # ITMS-90161 Code Signing & Keystore Failures
│   ├── archive-fails.md               # Xcode Archive & R8 ProGuard Class Stripping
│   ├── upload-fails.md                # Transporter CLI & Play Console Upload Failures
│   ├── ci-fails.md                    # GitHub Actions & EAS Cloud Build Failures
│   ├── review-rejected.md             # Resolution Center Store Review Rejection Triage
│   ├── metadata-rejected.md           # Guideline 2.3 Store Metadata Rejection Triage
│   ├── privacy-rejected.md            # Privacy Manifest & Data Safety Rejection Triage
│   ├── crash-after-release.md         # Production Crash Triage & Hermes Symbolication
│   ├── push-notifications-broken.md   # APNs & FCM HTTP v1 Delivery Failure Triage
│   ├── deep-links-broken.md           # AASA & AssetLinks Universal Link Breakdowns
│   ├── payments-broken.md             # StoreKit 2 & Play Billing Transaction Failures
│   └── app-not-visible.md             # Store Search Visibility & Region Availability
│
├── checklists/                        # Operational Release Checklists
│   ├── README.md                      # Subsystem Parent Index & Checklist Architecture
│   ├── first-release.md               # Initial App Submission Audit Checklist
│   ├── ios.md                         # iOS App Store Release Checklist
│   ├── android.md                     # Android Google Play Release Checklist
│   ├── cross-platform.md              # Cross-Platform Release Sync Checklist
│   ├── pre-release.md                 # Pre-Flight Quality Gate Checklist
│   ├── production.md                  # Production Store Deployment Checklist
│   └── post-release.md                # Post-Release Rollout & Monitoring Checklist
│
├── templates/                         # Operational Release Engineering Templates
│   ├── README.md                      # Subsystem Index & Template Usage Architecture
│   ├── release-plan.md                # Master Release Plan Schedule & Risk Matrix
│   ├── release-checklist.md           # Pre-Flight Verification Checklist Template
│   ├── rejection-response.md          # Resolution Center Rejection Appeal Form
│   ├── release-notes.md               # Multi-Language App Store Release Notes
│   ├── changelog.md                   # Keep a Changelog Documentation Template
│   ├── app-store-description.md       # Apple App Store Product Page Description
│   ├── play-store-description.md      # Google Play Store Listing & Short Description
│   ├── privacy-questionnaire.md       # Privacy Label & Data Safety Questionnaire
│   └── incident-report.md             # Post-Release Incident Post-Mortem Template
│
├── scripts/                           # Release Automation CLI Tooling
│   ├── README.md                      # Subsystem Index & CLI Automation Architecture
│   ├── release-audit/                 # Pre-Release Quality Audit Scripts
│   │   ├── README.md                  # Release Audit Index
│   │   └── audit-release.js           # Automated Pre-Flight Quality Audit Script
│   ├── version-bump/                  # Automated Version Bumping Tools
│   │   ├── README.md                  # Version Bump Index
│   │   └── version-bump.js            # Cross-Platform Version Synchronization Script
│   ├── build-validation/              # Binary Artifact Inspectors
│   │   ├── README.md                  # Build Validation Index
│   │   └── validate-build.js          # .ipa / .aab Hermes & Asset Inspection Script
│   ├── metadata-validation/           # Metadata & Policy Limit Checkers
│   │   ├── README.md                  # Metadata Validation Index
│   │   └── validate-metadata.js       # Character Limit & Guideline 2.3.7 Validator
│   └── store-validation/              # Store API Handshake Validators
│       ├── README.md                  # Store Validation Index
│       └── validate-store.js          # App Store Connect & Play API Connection Check
│
├── sources/                           # Official Platform Documentation Registries
│   ├── README.md                      # Subsystem Index & Authority Hierarchy Rules
│   ├── apple.md                       # Apple Developer Documentation Registry
│   ├── google.md                      # Google Play Console & Android Dev Registry
│   ├── react-native.md                # React Native Core Documentation Registry
│   ├── expo.md                        # Expo Application Services Registry
│   ├── fastlane.md                    # Fastlane Toolchain Documentation Registry
│   └── tooling.md                     # CLI & Dependency Registry (Jest, Sentry, Stripe)
│
├── governance/                        # Repository Governance & Research Standards
│   ├── README.md                      # Subsystem Index & Editorial Rules
│   ├── source-verification.md         # Source Verification & Research Standards
│   ├── documentation-style.md         # CLAUDE.md Editorial & ASCII Diagram Rules
│   ├── contribution-guide.md          # Pull Request Workflow & Quality Gates
│   ├── versioning.md                  # SemVer 2.0.0 Documentation Versioning
│   └── maintenance.md                 # 90-Day Quarterly Audit & Deprecation Policy
│
└── .github/                           # GitHub Repository Configuration
    ├── workflows/                     # GitHub Actions Workflow Definitions
    ├── ISSUE_TEMPLATE/                # GitHub Issue Templates
    └── pull_request_template.md       # Production GitHub Pull Request Template
```

---

**Last verified:** August 14, 2026