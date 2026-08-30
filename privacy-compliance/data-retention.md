# Data Retention Policies & Storage Limits Handbook

This handbook details data retention policies, automated database purging SLAs, secure local storage encryption, and regulatory disclosures for **Data Retention** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it specifies how to enforce storage limits and data purging schedules.

This guide is **not**:

- an authorization mechanism to store un-encrypted sensitive tokens in AsyncStorage
- a substitute for establishing backend data retention schedules
- a guide to storing data past legal compliance necessity

---

# 1. Data Retention & Secure Storage Architecture

Mobile apps MUST handle data retention across both local client storage (SecureStore / Encrypted SQLite) and backend cloud databases.

```text
┌────────────────────────────────────────────────────────┐
│             DATA RETENTION & STORAGE LAYERS            │
│                                                        │
│  - Client Storage: Expo `SecureStore` / Keychain      │
│    (Encrypts auth tokens & sensitive secrets)          │
│                                                        │
│  - Cloud Retention: Automated Purging Cron Jobs        │
│    (Inactive accounts purged after 24 months)          │
└────────────────────────────────────────────────────────┘
```

---

# 2. Secure Local Storage Implementation (`expo-secure-store`)

Store sensitive authentication tokens securely in iOS Keychain / Android KeyStore using `expo-secure-store`:

```typescript
import * as SecureStore from 'expo-secure-store';

// Save JWT Auth Token securely
export async function saveAuthToken(token: string) {
  await SecureStore.setItemAsync('user_jwt_token', token, {
    keychainAccessible: SecureStore.WHEN_UNLOCKED,
  });
}

// Retrieve JWT Auth Token
export async function getAuthToken(): Promise<string | null> {
  return await SecureStore.getItemAsync('user_jwt_token');
}
```

---

# 3. Operational Verification Checklist

- [ ] **Secure Store Active**: Sensitive tokens stored via `expo-secure-store` / Keychain, not plain `AsyncStorage`.
- [ ] **Retention Schedules Defined**: Data retention periods disclosed in Privacy Policy.
- [ ] **Purge Cron Jobs Configured**: Inactive server logs purged automatically after retention window.

---

# 4. Official Sources

- Expo SecureStore Documentation: https://docs.expo.dev/versions/latest/sdk/securestore/

---

**Last verified:** August 14, 2026

---

# Related documentation

### Privacy compliance

- `privacy-compliance/README.md`
- `privacy-compliance/advertising.md`
- `privacy-compliance/analytics.md`
- `privacy-compliance/children.md`
- `privacy-compliance/data-collection.md`
- `privacy-compliance/data-deletion.md`
- `privacy-compliance/health-data.md`
- `privacy-compliance/permissions.md`
- `privacy-compliance/privacy-policy.md`
- `privacy-compliance/regional-requirements.md`
- `privacy-compliance/tracking.md`

### Store operations

- `store-operations/data-safety.md`
- `store-operations/privacy-information.md`

### Pre-release

- `pre-release/privacy-audit.md`

### AI agents

- `ai/agents/privacy-auditor.md`

### Monetization

- `monetization/receipt-validation.md`

### Troubleshooting

- `troubleshooting/privacy-rejected.md`
