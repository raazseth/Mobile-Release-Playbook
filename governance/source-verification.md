# Source Verification Protocols & Research Standards Handbook

This handbook details source verification protocols, live web search verification standards, official Apple Developer and Google Play Console policy citations, and factual validation rules for **Source Verification** in the Mobile Release Playbook.

Engineered in alignment with **2026 platform specifications**, it details how to verify technical assertions before incorporating them into documentation.

This guide is **not**:

- an authorization mechanism to cite un-verified third-party blogs or forum posts
- a substitute for performing live web verification searches
- a guide to guessing platform character limits or API deprecation timelines

---

# 1. Source Verification Workflow

All documentation assertions must pass a three-stage verification pipeline before being written to playbook files:

```text
┌────────────────────────────────────────────────────────┐
│             SOURCE VERIFICATION PIPELINE               │
│                                                        │
│  Stage 1: Live Web Search Query (`search_web`)          │
│           Query official Apple / Google developer docs │
│                                                        │
│  Stage 2: Factual Extraction & Date Verification       │
│           Confirm specification is active in 2026      │
│                                                        │
│  Stage 3: Documentation Citation                       │
│           Cite primary portal URL in # Official Sources│
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ Verified Factual Content Written to Playbook File ]
```

---

# 2. Approved Primary Source Authority Hierarchy

Contributors and automated agents MUST prioritize primary official sources over secondary sources:

1. **Tier 1 (Primary Platform Portals)**:
   - Apple Developer Documentation (`developer.apple.com/documentation/`)
   - App Store Review Guidelines (`developer.apple.com/app-store/review/guidelines/`)
   - Google Play Console Help (`support.google.com/googleplay/android-developer/`)
   - Android Developer Guides (`developer.android.com/guide/`)
2. **Tier 2 (Official Framework Documentation)**:
   - Expo Documentation (`docs.expo.dev`)
   - React Native Core Docs (`reactnative.dev/docs`)
   - Fastlane Documentation (`docs.fastlane.tools`)
3. **Tier 3 (Forbidden Un-Verified Sources)**:
   - Un-verified StackOverflow threads, Reddit posts, or personal blogs without primary citations.

---

# 3. Operational Verification Checklist

- [ ] **Live Web Search Executed**: Platform facts verified via `search_web` prior to writing.
- [ ] **Tier 1 Authority Cited**: Primary Apple or Google developer link cited in `# Official Sources`.
- [ ] **2026 Timestamps Verified**: Specs confirmed active for current platform versions (Android 16 API 36, iOS 18).
- [ ] **`Last verified` Footer Added**: File footed with `Last verified: August 14, 2026`.

---

# 4. Official Sources

- Sources Registry Subsystem: [../sources/README.md](../sources/README.md)

---

**Last verified:** August 14, 2026

---

# Related documentation

### Governance

- `governance/README.md`
- `governance/contribution-guide.md`
- `governance/documentation-style.md`
- `governance/maintenance.md`
- `governance/versioning.md`

### Sources

- `sources/README.md`
