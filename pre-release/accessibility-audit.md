# Accessibility & Universal Design Audit Handbook

This handbook details mobile accessibility audits, `accessible={true}`, `accessibilityLabel` strings, iOS VoiceOver / Android TalkBack screen reader testing, and WCAG 2.1 AA color contrast compliance (4.5:1 ratio) for **Accessibility Audits** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it specifies how to audit mobile applications for accessibility compliance.

This guide is **not**:

- an authorization mechanism to omit accessibility labels on interactive elements
- a substitute for testing screen navigation with live screen readers
- a guide to disabling dynamic font scaling (`allowFontScaling={false}` is discouraged)

---

# 1. Accessibility Audit Architecture

Accessibility auditing ensures the application is usable by individuals with visual, motor, or cognitive impairments.

```text
┌────────────────────────────────────────────────────────┐
│             ACCESSIBILITY AUDIT CHECKPOINTS            │
│                                                        │
│  - Screen Reader Navigation: VoiceOver & TalkBack     │
│  - Touch Target Size: Min 44x44 pt (iOS) / 48x48 dp    │
│  - Color Contrast Ratio: Minimum 4.5:1 (WCAG AA)       │
│  - Dynamic Font Scaling: Text scales without clipping  │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ Verified Accessible and Compliant with WCAG 2.1 AA ]
```

---

# 2. Key Accessibility Verification Criteria

1. **Screen Reader Audit**: VoiceOver (iOS) and TalkBack (Android) navigate all interactive screens smoothly; all icon-only buttons have descriptive `accessibilityLabel` strings.
2. **Touch Target Dimensions**: All interactive touch targets (buttons, inputs) meet minimum size boundaries (44x44 pt on iOS, 48x48 dp on Android).
3. **Color Contrast AA**: Text elements maintain minimum contrast ratio of **4.5:1** against background colors.

---

# 3. Operational Verification Checklist

- [ ] **VoiceOver / TalkBack Tested**: All screens navigated cleanly with screen readers enabled.
- [ ] **Icon Buttons Labeled**: All icon-only buttons include descriptive `accessibilityLabel` strings.
- [ ] **Touch Targets Compliant**: All interactive elements meet 44x44 pt / 48x48 dp size requirements.
- [ ] **Dynamic Scaling Verified**: UI scales gracefully at maximum system font size.

---

# 4. Official Sources

- W3C WCAG 2.1 Guidelines: https://www.w3.org/TR/WCAG21/
- React Native Accessibility Guide: https://reactnative.dev/docs/accessibility

---

**Last verified:** August 14, 2026

---

# Related documentation

### Pre-release

- `pre-release/README.md`
- `pre-release/asset-audit.md`
- `pre-release/configuration-audit.md`
- `pre-release/dependency-audit.md`
- `pre-release/final-release-checklist.md`
- `pre-release/metadata-audit.md`
- `pre-release/performance-audit.md`
- `pre-release/permissions-audit.md`
- `pre-release/privacy-audit.md`
- `pre-release/release-readiness.md`
- `pre-release/security-audit.md`

### Testing

- `testing/release-smoke-tests.md`
- `testing/README.md`

### Checklists

- `checklists/pre-release.md`
- `checklists/production.md`

### Store operations

- `store-operations/app-review.md`

### Privacy compliance

- `privacy-compliance/README.md`

### AI agents

- `ai/agents/release-auditor.md`
- `ai/agents/security-auditor.md`
- `ai/agents/privacy-auditor.md`

### Publishing

- `publishing/ios/README.md`
- `publishing/android/README.md`
