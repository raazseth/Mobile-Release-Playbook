# Apple App Store Metadata & Product Page Template

Use this template to format store text metadata for **App Store Connect** in full compliance with Apple Guideline 2.3.

---

# App Store Metadata Fields

### 1. App Name (Title) — Max 30 Characters
```text
[FitnessTracker Pro: Gym Log]
```
*(Character count check: 27 / 30 characters max)*

### 2. Subtitle — Max 30 Characters
```text
[Workout Tracker & Calorie Counter]
```
*(Character count check: 30 / 30 characters max)*

### 3. Keywords — Max 100 Characters (Comma-separated, no spaces)
```text
fitness,tracker,workout,gym,log,exercise,calorie,weight,lift,muscle,health,routine,diet,planner
```
*(Character count check: 96 / 100 characters max)*

### 4. Promotional Text — Max 170 Characters (Editable without build)
```text
NEW VERSION 1.2! Track your gym workouts with brand new interactive analytics charts. Download today and start your 7-day free trial!
```
*(Character count check: 139 / 170 characters max)*

### 5. Full Description — Max 4,000 Characters
```text
Take your workout routine to the next level with FitnessTracker Pro — the ultimate gym log and fitness tracking companion engineered for serious athletes and beginners alike.

KEY FEATURES:

• LOG WORKOUTS EFFORTLESSLY: Track sets, reps, weights, and rest timers with our intuitive interface.
• INTERACTIVE ANALYTICS: Visualize your progress over time with detailed strength charts and personal records.
• CUSTOM ROUTINES: Build custom workout routines or choose from expert-designed training programs.
• OFFLINE SYNC: Log workouts anywhere, anytime — zero internet connection required.
• PRIVACY FOCUSED: Your fitness data stays private on your device and secure cloud backups.

SUBSCRIPTION INFORMATION:
FitnessTracker Pro offers a monthly auto-renewable subscription ($9.99/month). Payment will be charged to your Apple ID Account at confirmation of purchase. Subscription automatically renews unless cancelled at least 24 hours before the end of the current period. Manage or cancel subscriptions in your App Store Account Settings.

Privacy Policy: https://example.com/privacy
Terms of Use (EULA): https://example.com/terms
```

---

# Operational Verification Checklist

- [ ] **App Name ≤ 30 Characters**: App Name verified strictly within 30c boundary.
- [ ] **Subtitle ≤ 30 Characters**: Subtitle verified strictly within 30c boundary.
- [ ] **Keywords ≤ 100 Characters**: Keywords formatted comma-separated without spaces.
- [ ] **No Competitor Brands**: Verified zero competitor brand names in keywords or subtitle.
- [ ] **EULA & Privacy Links**: Subscription disclosure includes active Privacy Policy and Terms of Use web links.

---

# AI-Assisted Draft Generation

Don't draft this from scratch — use [`ai/prompts/aso-optimization.md`](../ai/prompts/aso-optimization.md) to generate keyword-optimized copy for the fields below, then run the result through [`ai/prompts/metadata.md`](../ai/prompts/metadata.md) to catch character-limit and policy violations before it goes anywhere near App Store Connect.

---

# Official Sources

- Apple Product Page Guidance: https://developer.apple.com/app-store/product-page/

---

**Last verified:** September 5, 2026

---

# Related documentation

### Templates

- `templates/README.md`
- `templates/changelog.md`
- `templates/incident-report.md`
- `templates/play-store-description.md`
- `templates/privacy-questionnaire.md`
- `templates/rejection-response.md`
- `templates/release-checklist.md`
- `templates/release-notes.md`
- `templates/release-plan.md`

### AI prompts

- `ai/prompts/aso-optimization.md`
- `ai/prompts/metadata.md`

### Store operations

- `store-operations/README.md`

### Checklists

- `checklists/README.md`

### Post-release

- `post-release/incident-response.md`

### Release strategy

- `release-strategy/release-trains.md`
