# Privacy Manifest & Label Audit Handbook

This handbook details privacy manifest audits (`PrivacyInfo.xcprivacy`), Xcode Privacy Report inspections, Google Play Data Safety alignment, and Required Reason API declarations for **Privacy Audits** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it specifies how to audit data collection declarations before submitting to store review.

This guide is **not**:

- an authorization mechanism to misrepresent data collection practices in store consoles
- a substitute for generating Xcode Privacy Reports
- a guide to omitting third-party SDK telemetry from Data Safety forms

---

# 1. Privacy Audit Pipeline

```text
┌────────────────────────────────────────────────────────┐
│             PRIVACY MANIFEST AUDIT PIPELINE            │
│                                                        │
│  - Step 1: Generate Xcode Privacy Report               │
│  - Step 2: Audit `PrivacyInfo.xcprivacy` Files         │
│  - Step 3: Verify Required Reason APIs                 │
│  - Step 4: Map Data Collection to Store Consoles       │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ 100% Alignment: Binary Manifests == Store Declarations ]
```

---

# 2. Key Privacy Audit Checkpoints

1. **Required Reason APIs**: Verify `PrivacyInfo.xcprivacy` includes valid reason codes for User Defaults (`NSPrivacyAccessedAPICategoryUserDefaults`).
2. **App Privacy Nutrition Labels**: Ensure App Store Connect labels accurately declare all data types listed in the Xcode Privacy Report.
3. **Google Play Data Safety Form**: Verify Data Safety disclosures in Play Console match requested permissions in `AndroidManifest.xml`.

---

# 3. Operational Verification Checklist

- [ ] **Xcode Privacy Report Inspected**: Privacy report generated and verified against store declarations.
- [ ] **Required Reason APIs Declared**: Reason codes present for all accessed system APIs.
- [ ] **Play Data Safety Aligned**: Data Safety declarations match `AndroidManifest.xml` permissions 100%.

---

# 4. Official Sources

- Apple Describing Use of Required Reason API: https://developer.apple.com/documentation/bundleresources/describing_use_of_required_reason_api

---

**Last verified:** August 14, 2026
