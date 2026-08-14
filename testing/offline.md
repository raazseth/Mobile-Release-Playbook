# Offline Support & Network State Testing

This document details Airplane Mode testing protocols, `@react-native-community/netinfo` state listeners, offline data queue persistence, and sync reconciliation for **Offline Testing** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it specifies how to test mobile applications when network connectivity is lost or intermittent.

This guide is **not**:

- an authorization mechanism to crash when `fetch` throws a network error
- a substitute for verifying local database / AsyncStorage data persistence
- a guide to silently dropping offline user actions

---

# 1. Offline Network State Architecture

When connectivity drops, offline-capable applications switch to local data stores, queuing user actions until internet connectivity is restored.

```text
┌────────────────────────────────────────────────────────┐
│             OFFLINE WORKFLOW ARCHITECTURE              │
│                                                        │
│  [ Device Enters Airplane Mode / Network Loss ]        │
│                         │                              │
│                         ▼                              │
│  [ NetInfo Listener Fires `isConnected: false` ]       │
│                         │                              │
│                         ▼                              │
│  [ Offline Action Queued in Local Store / SQLite ]     │
│                         │                              │
│                         ▼                              │
│  [ Network Restored → Auto-Sync Queue Flushed to API ] │
└────────────────────────────────────────────────────────┘
```

---

# 2. NetInfo State Listener & Offline Queue Pattern

```typescript
import NetInfo from '@react-native-community/netinfo';
import { useEffect, useState } from 'react';

export function useNetworkStatus() {
  const [isConnected, setIsConnected] = useState<boolean | null>(true);

  useEffect(() => {
    const unsubscribe = NetInfo.addEventListener((state) => {
      setIsConnected(state.isConnected);
    });

    return () => unsubscribe();
  }, []);

  return { isConnected };
}
```

---

# 3. Operational Verification Checklist

- [ ] **Airplane Mode Tested**: App launches and functions in Airplane Mode without crashing.
- [ ] **Offline Banner Displayed**: Offline banner renders when network connectivity is lost.
- [ ] **Offline Actions Queued**: User actions (e.g., logging a workout) saved locally while offline.
- [ ] **Auto-Sync Executed**: Saved offline actions automatically sync to API when network returns.

---

# 4. Official Sources

- React Native NetInfo Docs: https://github.com/react-native-netinfo/react-native-netinfo

---

**Last verified:** August 14, 2026
