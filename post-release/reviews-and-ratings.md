# App Store Ratings & User Feedback Management Handbook

This handbook details in-app review prompts (`StoreReview.requestReview()`), rating trend tracking, negative review response workflows, and feedback sentiment analysis for **Reviews & Ratings** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it specifies how to prompt for user reviews legally and manage store feedback.

This guide is **not**:

- an authorization mechanism to spam users with rating prompts on cold start
- a substitute for responding to negative store reviews in App Store Connect / Play Console
- a guide to offering financial incentives for positive 5-star reviews (violates store policies)

---

# 1. In-App Review Prompt Architecture (`expo-store-review`)

Store guidelines strictly limit how frequently an application can display native review prompts (Apple caps prompts at **3 times per 365-day period**).

```text
┌────────────────────────────────────────────────────────┐
│             IN-APP REVIEW PROMPT TIMING                │
│                                                        │
│  - Trigger ONLY after a positive user accomplishment   │
│    (e.g., Successfully completed 5 workouts)           │
│  - Check `StoreReview.hasAction()`                     │
│  - Call `StoreReview.requestReview()`                  │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ Native OS Rating Dialog Renders Without Leaving App ]
```

---

# 2. In-App Review Prompt Implementation Example

```typescript
import * as StoreReview from 'expo-store-review';

export async function requestAppRatingIfEligible(completedActionCount: number) {
  // Trigger prompt only after user completes 5 positive actions
  if (completedActionCount >= 5) {
    if (await StoreReview.hasAction()) {
      await StoreReview.requestReview();
    }
  }
}
```

---

# 3. Operational Verification Checklist

- [ ] **Prompt Timing Verified**: In-app review prompt triggers ONLY after a positive accomplishment, never on app launch.
- [ ] **Native API Used**: `StoreReview.requestReview()` used without custom pre-prompt dialogs.
- [ ] **Negative Reviews Handled**: Customer support responds to 1-star / 2-star reviews within 24 hours.

---

# 4. Official Sources

- Expo StoreReview Docs: https://docs.expo.dev/versions/latest/sdk/storereview/
- Apple Rating and Review Guidelines: https://developer.apple.com/app-store/ratings-and-reviews/

---

**Last verified:** August 14, 2026

---

# Related documentation

### Post-release

- `post-release/README.md`
- `post-release/analytics.md`
- `post-release/crash-analysis.md`
- `post-release/hotfix.md`
- `post-release/incident-response.md`
- `post-release/monitoring.md`
- `post-release/performance.md`
- `post-release/release-retrospective.md`
- `post-release/rollback.md`
- `post-release/rollout-monitoring.md`
- `post-release/version-recall.md`

### Release strategy

- `release-strategy/staged-rollouts.md`
- `release-strategy/kill-switches.md`

### Troubleshooting

- `troubleshooting/crash-after-release.md`

### Pre-release

- `pre-release/release-readiness.md`

### AI workflows

- `ai/workflows/debugging.md`
- `ai/agents/debugging-agent.md`

### Templates

- `templates/incident-report.md`
