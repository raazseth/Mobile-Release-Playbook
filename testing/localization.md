# Localization & Right-to-Left (RTL) Layout Testing

This document details localization QA procedures, Right-to-Left (RTL) layout mirroring (`I18nManager.forceRTL`), pseudo-localization text expansion, and dynamic currency/date formatting for **Localization Testing** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it specifies how to test multi-language and multi-region app builds to prevent layout truncation and missing translations.

This guide is **not**:

- an authorization mechanism to test English-only UI viewports
- a substitute for human native-speaker translation QA
- a guide to hardcoding currency symbols (use `Intl.NumberFormat`)

---

# 1. Right-to-Left (RTL) Layout Mirroring Testing Protocol

Arabic (`ar`) and Hebrew (`he`) require complete UI layout mirroring. Test dynamic RTL execution using React Native's `I18nManager`:

```text
┌────────────────────────────────────────────────────────┐
│             RTL LAYOUT MIRRORING VERIFICATION          │
│                                                        │
│  - Text alignment switches from Left to Right          │
│  - Back buttons move from top-left to top-right       │
│  - Horizontal scroll direction reverses                │
│  - Chevron icons (▶) mirror horizontally (◀)           │
└────────────────────────────────────────────────────────┘
```

### Forcing RTL Mode in Test Execution

```typescript
import { I18nManager } from 'react-native';

// Force RTL layout mode in test runner
I18nManager.allowRTL(true);
I18nManager.forceRTL(true);
```

---

# 2. Pseudo-Localization for Text Expansion Testing

German (`de`) and French (`fr`) text strings are often 30% to 50% longer than English equivalents. Use **Pseudo-Localization** during QA to expose layout truncation bugs early:

```text
English: "Add to Cart"
Pseudo-Localized: "[!!! Áðð ţó Çàяţ !!!]"
German Expansion: "In den Warenkorb legen"
```

---

# 3. Operational Verification Checklist

- [ ] **RTL Mirroring Verified**: Layout flips cleanly in Arabic (`ar`) mode; chevrons and back buttons mirror.
- [ ] **No Un-translated Strings**: Zero fallback key strings (e.g., `common.submit_btn`) visible in UI.
- [ ] **Pseudo-Localization Clean**: Text fields tested with expanded strings; zero truncation or clipping.
- [ ] **Currency & Dates Dynamic**: Prices formatted via `Intl.NumberFormat` with correct currency symbols (`$`, `€`, `¥`, `SAR`).

---

# 4. Official Sources

- React Native RTL Support: https://reactnative.dev/blog/2016/08/19/right-to-left-support-for-react-native
- Store Operations Localization Handbook: [../store-operations/localization.md](../store-operations/localization.md)

---

**Last verified:** August 14, 2026
