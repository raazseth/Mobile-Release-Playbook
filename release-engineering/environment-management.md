# Environment Variables & Secret Vault Management Handbook

This handbook details environment variable management (`.env.production`), `react-native-config`, Expo `extra` app config variables, secret vaults, and staging vs production endpoint isolation for **Environment Management** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it specifies how to inject environment variables securely at build time.

This guide is **not**:

- an authorization mechanism to commit `.env.production` files to git
- a substitute for using `expo-constants` / `react-native-config`
- a guide to storing secret private keys in client-accessible environment variables

---

# 1. Environment Variable Architecture & Security Layers

Client-side environment variables embedded in JS bundles are readable via reverse engineering. Secret API keys MUST be handled via secure backend proxies or secret vaults.

```text
┌────────────────────────────────────────────────────────┐
│             ENVIRONMENT VARIABLE CLASSIFICATION        │
│                                                        │
│  - Public Config (Client Safe): API Base URLs, Sentry  │
│    DSN, Segment Write Key ──→ Embedded in App Bundle │
│                                                        │
│  - Private Secrets (FORBIDDEN IN CLIENT): Database     │
│    Passwords, AWS Secret Keys, Stripe Secret Keys      │
│    ──→ Stored in Backend Proxy / AWS Secrets Manager   │
└────────────────────────────────────────────────────────┘
```

---

# 2. Expo Dynamic App Config Pattern (`app.config.ts`)

```typescript
import { ExpoConfig, ConfigContext } from 'expo/config';

export default ({ config }: ConfigContext): ExpoConfig => {
  const isProd = process.env.APP_ENV === 'production';

  return {
    ...config,
    name: isProd ? 'Mobile App' : 'Mobile App (Staging)',
    slug: 'mobile-app',
    extra: {
      apiBaseUrl: process.env.API_BASE_URL || 'https://api.example.com',
      sentryDsn: process.env.SENTRY_DSN,
    },
  };
};
```

---

# 3. Operational Verification Checklist

- [ ] **`.env` Files Git-Ignored**: `.env*` added to `.gitignore`.
- [ ] **Public Config Separated**: Only client-safe public variables included in mobile build bundle.
- [ ] **Dynamic Config Tested**: `app.config.ts` evaluates environment variables correctly at build time.

---

# 4. Official Sources

- Expo Environment Variables Guide: https://docs.expo.dev/guides/environment-variables/

---

**Last verified:** August 14, 2026
