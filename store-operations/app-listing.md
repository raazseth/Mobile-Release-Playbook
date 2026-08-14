# Store Listing Setup & Category Selection Handbook

This handbook details the configuration requirements, category selection taxonomy, developer contact information declarations, privacy policy URL management, support portal setup, and promotional text rules for **Store Listings** in Apple App Store Connect and Google Play Console.

Engineered in alignment with **2026 platform specifications**, it provides exhaustive technical and operational guidance for setting up, configuring, and maintaining compliant store listing entries for Expo and React Native applications.

This guide is **not**:

- an A/B testing experiment runner
- an authorization mechanism to obfuscate developer contact information
- a substitute for providing valid public developer support contact channels

---

# 1. Store Listing Architecture & Component Components

A store listing acts as the public digital storefront for your mobile application. It combines text metadata, visual media assets, category tags, public support contact information, and legally mandated privacy URLs.

```text
┌────────────────────────────────────────────────────────┐
│                   PUBLIC STORE LISTING                 │
│                                                        │
│  - App Icon (1024x1024 / 512x512)                      │
│  - App Title (30c) & Subtitle (30c) / Short Desc (80c) │
│  - Primary & Secondary Category Classification          │
│  - Screenshots (iPhone 6.9" / iPad 13" / Android)      │
│  - Promotional Text (170c - Dynamic Update without App)│
│  - Public Support Email, Website, & Privacy Policy URL │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│             STORE BACKEND REVIEW VERIFICATION          │
│  - Privacy Policy URL HTTPS response code (HTTP 200)   │
│  - Category alignment check with app features          │
└────────────────────────────────────────────────────────┘
```

---

# 2. Category Selection Taxonomy & Guidelines

Selecting appropriate primary and secondary categories ensures your app reaches the target user audience and undergoes review by the correct store inspection team.

```text
┌────────────────────────────────────────────────────────┐
│             CATEGORY SELECTION CONSTRAINTS             │
│                                                        │
│  - Primary Category: Defines primary search discovery  │
│  - Secondary Category (iOS): Expands discovery reach   │
│  - App Type (Android): Application vs Game             │
└────────────────────────────────────────────────────────┘
```

### Apple App Store Category Taxonomy

| Primary Category | Ideal Application Scope | Key Review Inspection Focus |
|---|---|---|
| **Business** | Enterprise collaboration, document management, CRM, hiring tools | Employee authentication, corporate data security. |
| **Finance** | Banking, budgeting, cryptocurrency, investment tracking | Security disclosures, financial licensing compliance. |
| **Health & Fitness** | Workout tracking, meditation, calorie logging, sleep monitoring | HealthKit entitlement accuracy, medical advice disclaimers. |
| **Medical** | Clinical decision tools, telehealth, symptom tracking | Strict regulatory review, medical practitioner validation. |
| **Productivity** | Note-taking, task management, cloud storage, PDF editors | Background processing, file access entitlements. |
| **Utilities** | Calculators, file converters, VPNs, device tools | Core device capability compliance, battery usage. |

> **REJECTION WARNING (Guideline 2.3)**: Selecting an inaccurate category (e.g., categorizing a simple utility app as "Medical" or "Finance" to manipulate search rankings) will trigger an immediate rejection under Apple Guideline 2.3.

---

# 3. Required Legal & Support URLs

Store consoles mandate active, accessible web links before allowing build submission:

1. **Privacy Policy URL**: MUST be a valid `https://` web URL returning HTTP status `200 OK`. The privacy policy page must explicitly detail what user data is collected, how it is processed, and how users can request data deletion.
2. **Support URL**: MUST provide a functional support portal, contact form, or help desk email for end users.
3. **Marketing URL (Optional on iOS)**: Link to company product website.

---

# 4. Promotional Text Management (iOS)

App Store Connect provides a **Promotional Text** field (170 characters max) that appears at the top of the App Store product page above the description:

- **No Build Submission Required**: Unlike descriptions, titles, or screenshots, Promotional Text can be edited **at any time** without submitting a new app version or undergoing App Review.
- **Ideal Use Cases**: Timely promotion announcements, seasonal sales, event notices, or major press quotes.

---

# 5. Operational Verification Checklist

- [ ] **Privacy Policy URL Active**: `https://` privacy policy URL tested and returns HTTP 200 OK.
- [ ] **Support Contact Functional**: Public support email or contact portal monitored by customer support staff.
- [ ] **Categories Aligned**: Primary and secondary categories accurately reflect app functionality.
- [ ] **Developer Address Public**: Public developer physical address verified for Google Play Console requirements.
- [ ] **Promotional Text Configured**: Promotional text configured for upcoming release announcements.
- [ ] **App Icon High-Res Uploaded**: 1024x1024 PNG icon uploaded without alpha channels or rounded corners.

---

# 6. Related Documentation

- [Metadata Handbook](metadata.md) - Text metadata character limits.
- [Screenshots Handbook](screenshots.md) - Visual asset specifications.
- [Privacy Information Handbook](privacy-information.md) - App privacy labels.

---

# 7. Official Sources

- Apple App Store Category Guidelines: https://developer.apple.com/app-store/categories/
- Google Play Store Listing Requirements: https://support.google.com/googleplay/android-developer/answer/9866151

---

**Last verified:** August 14, 2026
