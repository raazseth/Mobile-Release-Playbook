# App Upgrade & Database Migration Testing

This document details over-the-install data persistence testing, SQLite / AsyncStorage schema migration verification, session token preservation, and state hydration for **App Upgrade Testing** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it specifies how to test app updates installed over existing production versions without causing user data loss or app crash loops.

This guide is **not**:

- an authorization mechanism to test fresh installs exclusively
- a substitute for verifying SQLite database schema migration scripts
- a guide to clearing user data during app updates

---

# 1. Over-the-Install Upgrade Testing Protocol

Existing users update their apps directly over their current version without uninstalling. Testing MUST verify that local user data, auth tokens, and database schemas persist across the update boundary.

```text
┌────────────────────────────────────────────────────────┐
│             OVER-THE-INSTALL UPGRADE QA PROTOCOL       │
│                                                        │
│  1. Install Current Production Version (v1.1.0).       │
│  2. Populate User Data (Log in, save local settings).  │
│  3. Install Release Candidate Version (v1.2.0) over    │
│     v1.1.0 via Xcode / `adb install -r`.               │
│  4. Launch v1.2.0 & Assert User Remains Logged In.     │
│  5. Assert SQLite / AsyncStorage Data Persisted.       │
└────────────────────────────────────────────────────────┘
```

---

# 2. SQLite Database Schema Migration Testing

When adding or altering database columns in local SQLite / WatermelonDB databases:

```sql
-- Migration Script: v1 to v2 Database Migration
ALTER TABLE workouts ADD COLUMN syncStatus TEXT DEFAULT 'PENDING';
```

### QA Verification Step
Execute migration script against a production v1 database export. Assert zero `SQLiteException` errors during migration execution and verify existing table records remain intact.

---

# 3. Operational Verification Checklist

- [ ] **Over-the-Install Upgrade Passed**: New build installed directly over previous production build.
- [ ] **Auth Token Persisted**: User remains authenticated post-upgrade without re-entering password.
- [ ] **Database Schema Migrated**: SQLite / AsyncStorage migration scripts execute cleanly without data loss.
- [ ] **Settings Preserved**: Custom user preferences and settings remain active in new version.

---

# 4. Official Sources

- Expo SQLite Migration Guide: https://docs.expo.dev/versions/latest/sdk/sqlite/

---

**Last verified:** August 14, 2026
