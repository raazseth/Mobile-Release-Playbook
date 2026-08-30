# Integration Testing & API Mocking Frameworks

This document details integration testing patterns, state management integration, API network mocking via Mock Service Worker (MSW v2), and React Navigation testing for **Integration Testing** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it specifies how to test multi-component screens and state flows with deterministic API responses.

This guide is **not**:

- an authorization mechanism to hit production API servers during test suite execution
- a substitute for E2E UI testing on real viewports
- a guide to hardcoding network fetch overrides inside component code

---

# 1. Integration Testing Architecture & MSW v2 Setup

Mock Service Worker (MSW v2) intercepts network requests at the network layer, allowing integration tests to run without modifying application network code.

```text
┌────────────────────────────────────────────────────────┐
│             INTEGRATION TEST EXECUTION (Jest)          │
│  - Mount Screen Component with Redux/Zustand Provider  │
│  - App fires `fetch('/api/v1/user/profile')`           │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│             MOCK SERVICE WORKERS (MSW v2)              │
│  - Intercepts HTTP request at network boundary         │
│  - Returns deterministic JSON fixture response         │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
[ RNTL Asserts Rendered Profile Data on Screen ]
```

---

# 2. MSW Handler Setup (`src/mocks/handlers.ts`)

```typescript
// src/mocks/handlers.ts
import { http, HttpResponse } from 'msw';

export const handlers = [
  http.get('https://api.example.com/v1/user/profile', () => {
    return HttpResponse.json({
      id: 'usr_123',
      name: 'Jane Doe',
      email: 'jane@example.com',
      membershipTier: 'GOLD',
    });
  }),
];
```

---

# 3. Screen Integration Test Example

```tsx
// src/screens/__tests__/ProfileScreen.test.tsx
import React from 'react';
import { render, waitFor } from '@testing-library/react-native';
import { ProfileScreen } from '../ProfileScreen';
import { QueryClientProvider, QueryClient } from '@tanstack/react-query';

const createTestQueryClient = () => new QueryClient({ defaultOptions: { queries: { retry: false } } });

describe('ProfileScreen Integration Test', () => {
  it('fetches and displays user profile data', async () => {
    const queryClient = createTestQueryClient();
    const { getByText } = render(
      <QueryClientProvider client={queryClient}>
        <ProfileScreen />
      </QueryClientProvider>
    );

    // Assert loading indicator initially present
    expect(getByText('Loading profile...')).toBeTruthy();

    // Wait for MSW mock response to resolve
    await waitFor(() => {
      expect(getByText('Jane Doe')).toBeTruthy();
      expect(getByText('GOLD')).toBeTruthy();
    });
  });
});
```

---

# 4. Operational Verification Checklist

- [ ] **MSW Active**: MSW v2 server setup intercepts fetch/axios requests in test environment.
- [ ] **No Live API Calls**: Test suite execution verified free of network leaks to live API backends.
- [ ] **State Providers Wrapped**: Integration tests wrap component trees with state and query providers.
- [ ] **Async UI Handled**: `waitFor` and async queries used to handle asynchronous data rendering.

---

# 5. Official Sources

- Mock Service Worker (MSW) Documentation: https://mswjs.io/
- React Query Testing Guide: https://tanstack.com/query/v5/docs/framework/react/guides/testing

---

**Last verified:** August 14, 2026

---

# Related documentation

### Testing

- `testing/README.md`
- `testing/accessibility.md`
- `testing/android.md`
- `testing/deep-links.md`
- `testing/device-matrix.md`
- `testing/device-testing.md`
- `testing/e2e.md`
- `testing/ios.md`
- `testing/localization.md`
- `testing/network.md`
- `testing/offline.md`
- `testing/payments.md`
- `testing/performance.md`
- `testing/push-notifications.md`
- `testing/release-smoke-tests.md`
- `testing/unit.md`
- `testing/upgrade-migrations.md`

### Pre-release

- `pre-release/README.md`
- `pre-release/final-release-checklist.md`

### Checklists

- `checklists/pre-release.md`

### Troubleshooting

- `troubleshooting/README.md`

### Release strategy

- `release-strategy/beta-testing.md`
