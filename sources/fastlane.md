# Fastlane Automation Tooling Documentation Registry

This document details the primary documentation portals, Fastlane tool suites (Match, Deliver, Supply, Snapshot, Pilot, Frameit), Fastfile configurations, Appfile setups, and authentication rules for **Fastlane Automation**.

Engineered in alignment with **2026 platform specifications**, it provides an authoritative citation registry for Fastlane build automation, code signing, and store metadata synchronization.

This guide is **not**:

- an un-verified secondary tutorial list
- an authorization mechanism to embed plain-text API keys in Fastfiles
- a substitute for inspecting active Fastlane documentation

---

# 1. Primary Fastlane Tooling Portals

| Fastlane Action / Tool | Primary Official URL | Key Scope & Governance Rules |
|---|---|---|
| **Fastlane Main Documentation** | `https://docs.fastlane.tools/` | Core Fastlane documentation, action index, and installation guides. |
| **Fastlane Match** | `https://docs.fastlane.tools/actions/match/` | iOS code signing management via encrypted git repository storage. |
| **Fastlane Deliver** | `https://docs.fastlane.tools/actions/deliver/` | Uploads screenshots, metadata, and `.ipa` builds to App Store Connect. |
| **Fastlane Supply** | `https://docs.fastlane.tools/actions/supply/` | Uploads `.aab` builds, metadata, and screenshots to Google Play Console. |
| **Fastlane Snapshot** | `https://docs.fastlane.tools/actions/snapshot/` | Automated localized iOS screenshot capture using Xcode Simulators. |
| **Fastlane Pilot** | `https://docs.fastlane.tools/actions/pilot/` | Manages TestFlight builds, testers, and beta distribution. |
| **Fastlane Frameit** | `https://docs.fastlane.tools/actions/frameit/` | Adds device frames and localized text headlines to screenshots. |

---

# 2. Key Fastlane Authentication & Actions Index

```text
┌────────────────────────────────────────────────────────┐
│             FASTLANE ACTIONS CITATION INDEX            │
│                                                        │
│  - `match`: Managed iOS certificates & profiles.      │
│  - `deliver`: App Store Connect metadata & builds.    │
│  - `supply`: Google Play Console metadata & `.aab`.   │
│  - `pilot`: TestFlight distribution & tester management│
└────────────────────────────────────────────────────────┘
```

---

# 3. Operational Verification Checklist

- [ ] **Fastlane URLs Active**: Official `docs.fastlane.tools` URLs verified returning HTTP 200 OK.
- [ ] **Deliver & Supply Actions Cited**: Metadata sync and build upload actions documented.
- [ ] **Match Code Signing Linked**: Git-based encrypted code signing workflow cited.

---

# 4. Related Documentation

- [Expo Documentation Registry](expo.md) - Expo sources.
- [Tooling Documentation Registry](tooling.md) - CI/CD tooling.
- [Cross-Platform Publishing Handbook](../../publishing/cross-platform/README.md) - Publishing workflows.

---

# 5. Official Sources

- Fastlane Documentation Portal: https://docs.fastlane.tools/

---

**Last verified:** August 14, 2026
