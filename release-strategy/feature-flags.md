# Remote Feature Flagging Architecture Handbook

This handbook details remote feature flag integration (LaunchDarkly, Firebase Remote Config, PostHog), decoupling code deployment from feature release, percentage-based feature rollouts, and targeting rules for **Feature Flags** in Expo and React Native.

Engineered in alignment with **2026 platform specifications**, it details how to manage dynamic feature toggles.

This guide is **not**:

- an authorization mechanism to leave stale feature flags in codebase indefinitely
- a substitute for local default fallback values when network offline
- a guide to evaluating feature flags synchronously on main UI rendering threads

---

# 1. Remote Feature Flagging Architecture

Feature flagging separates the technical act of deploying code to app stores from the business decision of releasing features to end-users.

```text
┌────────────────────────────────────────────────────────┐
│             FEATURE FLAGGING ARCHITECTURE              │
│                                                        │
│  [ Code Deployed to App Stores (Disabled by Default) ] │
│                         │                              │
│                         ▼                              │
│  [ Remote Config Provider (LaunchDarkly / Firebase) ]  │
│                         │                              │
│                         ▼                              │
│  [ Target Rule Evaluation (User ID / App Version) ]    │
│                         │                              │
│             ┌───────────┴───────────┐                  │
│             ▼                       ▼                  │
│  [ Feature Enabled (10%) ]  [ Feature Disabled (90%) ]  │
└────────────────────────────────────────────────────────┘
```

---

# 2. React Native Feature Flag Implementation Example

```tsx
import React from 'react';
import { useFeatureFlag } from '@posthog/react-native';
import { NewPaymentCheckout } from './NewPaymentCheckout';
import { LegacyPaymentCheckout } from './LegacyPaymentCheckout';

export const CheckoutScreen = () => {
  const isNewCheckoutEnabled = useFeatureFlag('new-payment-checkout');

  if (isNewCheckoutEnabled) {
    return <NewPaymentCheckout />;
  }

  return <LegacyPaymentCheckout />;
};
```

---

# 3. Operational Verification Checklist

- [ ] **Offline Fallbacks Set**: Default flag values defined locally in case remote config call fails.
- [ ] **Stale Flags Removed**: Quarterly flag cleanup audits scheduled to purge retired flags.
- [ ] **Targeting Rules Tested**: Flag evaluation verified across user cohorts.

---

# 4. Official Sources

- PostHog Feature Flags for React Native: https://posthog.com/docs/feature-flags

---

**Last verified:** August 14, 2026

---

# Related documentation

### Release strategy

- `release-strategy/README.md`
- `release-strategy/beta-testing.md`
- `release-strategy/emergency-release.md`
- `release-strategy/hotfixes.md`
- `release-strategy/kill-switches.md`
- `release-strategy/phased-release.md`
- `release-strategy/release-trains.md`
- `release-strategy/staged-rollouts.md`

### Post-release

- `post-release/rollout-monitoring.md`
- `post-release/rollback.md`

### Release engineering

- `release-engineering/release-pipelines.md`

### Expo

- `frameworks/expo/updates.md`

### Foundations

- `foundations/release-environments.md`
