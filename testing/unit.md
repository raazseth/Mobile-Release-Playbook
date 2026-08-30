# Unit Testing Architecture & Component Testing

This document details unit testing patterns, Jest 29+ configuration, React Native Testing Library (RNTL), custom hook testing, and native module mocking for **Unit Testing** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it specifies how to write fast, maintainable unit tests for pure logic, state utilities, and React components.

This guide is **not**:

- an authorization mechanism to test internal implementation details (test user-observable behavior)
- a substitute for integration or E2E UI testing
- a guide to over-mocking React component trees

---

# 1. Unit Testing Setup & Jest Configuration

Configure Jest 29+ with the React Native preset in `jest.config.js`:

```javascript
// jest.config.js
module.exports = {
  preset: 'react-native',
  setupFilesAfterEnv: ['@testing-library/jest-native/extend-expect', './jest.setup.js'],
  transformIgnorePatterns: [
    'node_modules/(?!(jest-)?react-native|@react-native(-community)?|expo(nent)?|@expo(nent)?/.*|@expo-google-fonts/.*|react-navigation|@react-navigation/.*|@unimodules/.*|unimodules|sentry-expo|native-base|react-native-svg)',
  ],
  moduleFileExtensions: ['ts', 'tsx', 'js', 'jsx', 'json'],
};
```

---

# 2. Component Testing with React Native Testing Library (`RNTL`)

Test React Native UI components by querying elements accessible to end users (e.g., text, accessibility labels, button presses):

```tsx
// src/components/__tests__/Button.test.tsx
import React from 'react';
import { render, fireEvent } from '@testing-library/react-native';
import { PrimaryButton } from '../PrimaryButton';

describe('PrimaryButton Component', () => {
  it('renders title correctly and triggers onPress when tapped', () => {
    const onPressMock = jest.fn();
    const { getByText, getByRole } = render(
      <PrimaryButton title="Submit Order" onPress={onPressMock} />
    );

    // Verify title text renders
    expect(getByText('Submit Order')).toBeTruthy();

    // Trigger user press interaction
    fireEvent.press(getByRole('button'));

    // Assert callback was called
    expect(onPressMock).toHaveBeenCalledTimes(1);
  });
});
```

---

# 3. Custom Hook Testing with `renderHook`

Test custom React hooks without creating wrapper components:

```typescript
// src/hooks/__tests__/useCounter.test.ts
import { renderHook, act } from '@testing-library/react-native';
import { useCounter } from '../useCounter';

describe('useCounter Custom Hook', () => {
  it('increments counter state correctly', () => {
    const { result } = renderHook(() => useCounter(0));

    expect(result.current.count).toBe(0);

    act(() => {
      result.current.increment();
    });

    expect(result.current.count).toBe(1);
  });
});
```

---

# 4. Operational Verification Checklist

- [ ] **Jest Config Active**: Jest preset configured for React Native and TypeScript.
- [ ] **Behavior Queried**: RNTL tests query elements via `getByText` or `getByRole` rather than test IDs.
- [ ] **Hooks Isolated**: Custom hooks tested using `renderHook` and `act` wrappers.
- [ ] **Coverage Threshold Met**: Unit test suite maintains > 80% coverage on core business logic.

---

# 5. Official Sources

- React Native Testing Library Docs: https://callstack.github.io/react-native-testing-library/
- Jest Framework Documentation: https://jestjs.io/

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
- `testing/integration.md`
- `testing/ios.md`
- `testing/localization.md`
- `testing/network.md`
- `testing/offline.md`
- `testing/payments.md`
- `testing/performance.md`
- `testing/push-notifications.md`
- `testing/release-smoke-tests.md`
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
