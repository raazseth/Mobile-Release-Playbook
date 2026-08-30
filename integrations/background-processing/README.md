# Background Processing Integration

This guide details the integration architecture, background execution modes, system scheduling frameworks (iOS Background Tasks, Android WorkManager), battery optimization policies, and release verification rules for **Background Processing** in React Native and Expo applications.

It specifies how to schedule background sync, upload offline queues, and process periodic tasks while respecting OS battery restrictions and store review guidelines (Apple Guideline 2.5.4).

This guide is **not**:

- an authorization mechanism to run continuous background processing without OS approval
- a bypass for Android Doze mode or Standby bucket restrictions
- a substitute for declaring background modes in `Info.plist` or `AndroidManifest.xml`

---

# 1. Integration Architecture & OS Scheduling

Mobile operating systems enforce strict resource and battery restrictions on background execution. Background tasks are scheduled with the OS, which determines exact execution timing based on battery level, network state, and device usage patterns.

```text
[ App Requests Background Task Scheduling ]
                       │
                       ▼
┌────────────────────────────────────────────────────────┐
│            OPERATING SYSTEM TASK SCHEDULER             │
│  - iOS: `BGTaskScheduler` (App Refresh / Processing)   │
│  - Android: `WorkManager` (PeriodicWorkRequest)        │
└──────────────────────────┬─────────────────────────────┘
                       │
                       ▼ (OS Triggers Task Execution)
┌────────────────────────────────────────────────────────┐
│              BACKGROUND TASK EXECUTION                 │
│  - Perform Data Sync / Upload Queue                    │
│  - MUST call completion handler within ~30 seconds     │
└────────────────────────────────────────────────────────┘
```

---

# 2. iOS Background Modes & `Info.plist` Declarations

To enable background processing on iOS, background modes must be declared in `app.json` / `Info.plist` and entitlements:

```json
{
  "expo": {
    "ios": {
      "infoPlist": {
        "UIBackgroundModes": ["fetch", "processing"]
      }
    }
  }
}
```

### Required `BGTaskScheduler` Permitted Identifiers

For custom background processing tasks on iOS 13+, identifiers must be explicitly declared in `Info.plist`:

```xml
<key>BGTaskSchedulerPermittedIdentifiers</key>
<array>
    <string>com.example.app.refresh</string>
    <string>com.example.app.upload</string>
</array>
```

> **WARNING (Apple Guideline 2.5.4)**: Apps declaring `UIBackgroundModes` MUST use the background execution features for their intended purpose. Declaring background modes without performing legitimate background work will trigger app rejection.

---

# 3. Android WorkManager & Battery Restrictions

On Android, WorkManager handles background tasks across device power states:

- **Battery Not Low**: Require sufficient charge for heavy background sync.
- **Network Constraint**: Require `CONNECTED` or `UNMETERED` network for upload queues.
- **Doze Mode Compliance**: Respect Android Doze mode; do not prompt users to disable battery optimizations unless strictly necessary for core app functionality (e.g., navigation or VoIP).

```xml
<!-- AndroidManifest.xml Foreground Service Declaration if required -->
<uses-permission android:name="android.permission.FOREGROUND_SERVICE" />
<uses-permission android:name="android.permission.FOREGROUND_SERVICE_DATA_SYNC" />
```

---

# 4. Code Implementation Example (Expo Background Fetch)

```javascript
import * as BackgroundFetch from 'expo-background-fetch';
import * as TaskManager from 'expo-task-manager';

const BACKGROUND_SYNC_TASK = 'BACKGROUND_SYNC_TASK';

TaskManager.defineTask(BACKGROUND_SYNC_TASK, async () => {
  try {
    // Perform light background sync
    await syncPendingData();
    return BackgroundFetch.BackgroundFetchResult.NewData;
  } catch (error) {
    return BackgroundFetch.BackgroundFetchResult.Failed;
  }
});

export async function registerBackgroundSync() {
  return BackgroundFetch.registerTaskAsync(BACKGROUND_SYNC_TASK, {
    minimumInterval: 15 * 60, // 15 minutes minimum
    stopOnTerminate: false,
    startOnBoot: true,
  });
}
```

---

# 5. Operational Verification Checklist

- [ ] **Background Modes Declared**: `UIBackgroundModes` declared in `Info.plist` (`fetch`, `processing`).
- [ ] **Task Identifiers Listed**: `BGTaskSchedulerPermittedIdentifiers` matches task names.
- [ ] **Task Completion Called**: Background execution code calls OS completion handlers within 30 seconds.
- [ ] **Battery Restrictions Respect**: Android WorkManager uses appropriate battery/network constraints.
- [ ] **Apple Guideline 2.5.4 Compliant**: Declared background modes perform active, verifiable background tasks.

---

# 6. Related Documentation

- [Push Notifications Guide](../push-notifications/README.md) - Remote background push payloads.
- [Location Services Guide](../location/README.md) - Background location tracking.
- [Release Audit Workflow](../../ai/workflows/release-audit.md) - Pre-release audits.

---

# 7. Official Sources

- Apple BGTaskScheduler Documentation: https://developer.apple.com/documentation/backgroundtasks
- Android WorkManager Documentation: https://developer.android.com/topic/libraries/architecture/workmanager

---

**Last verified:** August 13, 2026

