# Production Rollback & Rollout Halting Strategies

This handbook details rollout halting procedures, pausing iOS Phased Releases, halting Google Play Staged Rollouts, and EAS Update OTA JS bundle rollbacks for **Production Rollbacks** in Expo and React Native.

Engineered in alignment with **2026 platform specifications**, it specifies how to stop active distribution of broken release builds.

This guide is **not**:

- an authorization mechanism to delay halting a broken staged rollout
- a substitute for rolling back EAS Update OTA JS channels
- a guide to uninstalling client apps on user devices (stores do not support forced uninstalls)

---

# 1. Rollout Halting Architecture

When a release candidate exhibits severe defects, distribution MUST be halted across both Apple App Store Connect and Google Play Console.

```text
┌────────────────────────────────────────────────────────┐
│             ROLLOUT HALTING WORKFLOW                   │
│                                                        │
│  - iOS: Click 'Pause Phased Release' in App Store Conn.│
│  - Android: Click 'Halt Rollout' in Play Console       │
│  - OTA JS: Roll back channel via `eas update:re-publish`│
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ Halts New Downloads; Existing Users Remain on Current Version ]
```

---

# 2. Rolling Back EAS Update OTA JS Bundles

If a broken JS update was published via EAS Update, roll back the production channel to the previous stable update group:

```bash
# Re-publish previous stable update group to production channel
eas update:re-publish --group [STABLE_GROUP_ID] --target-channel production
```

---

# 3. Operational Verification Checklist

- [ ] **iOS Phased Release Paused**: Phased release paused in App Store Connect.
- [ ] **Android Rollout Halted**: Staged rollout halted in Google Play Console.
- [ ] **EAS OTA Rolled Back**: Broken JS bundle update group rolled back via `eas update:re-publish`.

---

# 4. Official Sources

- Apple Phased Release Halting: https://developer.apple.com/help/app-store-connect/#/dev3b92cdd7c
- Google Play Staged Rollout Halting: https://support.google.com/googleplay/android-developer/answer/9859348

---

**Last verified:** August 14, 2026
