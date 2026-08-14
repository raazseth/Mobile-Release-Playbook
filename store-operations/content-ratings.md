# Content Ratings & International Age Rating Coalition (IARC) Handbook

This handbook details the age rating questionnaires, International Age Rating Coalition (IARC) system, regional rating authority rules (ESRB, PEGI, USK, GRAC), and compliance standards for **Content Ratings** in App Store Connect and Google Play Console.

Engineered in alignment with **2026 platform specifications**, it provides exhaustive technical and operational guidance for completing age rating questionnaires accurately to prevent app age restriction blocks or store takedowns.

This guide is **not**:

- an authorization mechanism to under-declare mature content (violence, gambling, user-generated content)
- a substitute for implementing parental controls
- a guide to manipulating regional age rating compliance

---

# 1. Content Rating System Architecture

Mobile stores enforce age rating questionnaires to generate official regional content ratings across global jurisdictions.

```text
[ Complete Store Age Rating Questionnaire ]
                   │
                   ▼
┌────────────────────────────────────────────────────────┐
│         INTERNATIONAL AGE RATING COALITION (IARC)      │
│  - Evaluates Questionnaire Answers Automatically       │
└──────────────────────────┬─────────────────────────────┘
                   │
                   ▼
┌────────────────────────────────────────────────────────┐
│            REGIONAL CONTENT RATING GENERATION          │
│  - ESRB (North America): Everyone / Teen / Mature 17+  │
│  - PEGI (Europe): PEGI 3 / 7 / 12 / 16 / 18            │
│  - USK (Germany): USK 0 / 6 / 12 / 16 / 18             │
│  - GRAC (South Korea): All / 12+ / 15+ / 19+           │
└────────────────────────────────────────────────────────┘
```

---

# 2. Key Content Rating Questionnaire Categories

The IARC questionnaire evaluates five core content dimensions:

1. **Violence**: Presence of realistic, cartoon, or fantasy violence.
2. **User-Generated Content (UGC)**: Features allowing users to interact, chat, or share un-moderated content. Apps with un-filtered chat require a **17+ (Apple)** or **Mature (Google)** rating unless robust reporting/blocking mechanisms are enforced.
3. **Controlled Substances & Gambling**: Depictions of tobacco, alcohol, drugs, or simulated gambling.
4. **Sexuality**: Depictions of nudity, sexual themes, or suggestive content.
5. **Personal Data Sharing & Location**: Features sharing precise location or personal details with other users.

---

# 3. South Korea GRAC Regional Compliance Rules

For apps distributed in South Korea:

- **Game Rating & Administration Committee (GRAC)**: Games distributed in South Korea MUST complete GRAC rating disclosures. Apps containing simulated gambling or real-money gaming features are subject to strict South Korea legal restrictions.

---

# 4. Operational Verification Checklist

- [ ] **IARC Questionnaire Completed**: Complete, accurate questionnaire answers submitted in Play Console.
- [ ] **Apple Age Rating Set**: Age rating questions answered accurately in App Store Connect.
- [ ] **UGC Moderation Declared**: User-generated content apps disclose chat/sharing features and report mechanisms.
- [ ] **Simulated Gambling Disclosed**: Simulated gambling or loot box mechanics declared accurately.
- [ ] **South Korea Ratings Compliant**: Regional rating compliance verified for Republic of Korea storefronts.

---

# 5. Related Documentation

- [App Listing Handbook](app-listing.md) - Listing setup.
- [App Review Handbook](app-review.md) - Review submission.
- [Store Accounts Handbook](../store-accounts/README.md) - Account administration.

---

# 6. Official Sources

- IARC Rating System Overview: https://www.globalratings.com/
- Google Play Content Ratings Guide: https://support.google.com/googleplay/android-developer/answer/9859655
- Apple App Store Age Ratings: https://developer.apple.com/help/app-store-connect/#/dev84b80958f

---

**Last verified:** August 14, 2026
