# Mobile Accessibility QA & Screen Reader Testing

This document details mobile accessibility testing, screen reader automation (VoiceOver on iOS, TalkBack on Android), dynamic font scaling (`AccessibleText`), color contrast ratios, and `accessibilityLabel` auditing for **Mobile Accessibility QA** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it specifies how to make React Native apps fully accessible to users with visual, auditory, or motor impairments.

This guide is **not**:

- an authorization mechanism to omit accessibility labels on icon-only buttons
- a substitute for testing with live screen readers
- a guide to disabling dynamic font scaling (`allowFontScaling={false}` is discouraged)

---

# 1. Mobile Accessibility Architecture (`RNTL` & Native Screen Readers)

React Native exposes native accessibility APIs that map directly to iOS Accessibility and Android Accessibility Frameworks.

```text
┌────────────────────────────────────────────────────────┐
│             REACT NATIVE ACCESSIBILITY PROPS           │
│                                                        │
│  - `accessible={true}` ──→ Marks element as accessible │
│  - `accessibilityLabel` ─→ Read aloud by screen reader │
│  - `accessibilityRole`  ─→ Defines element role (button)│
│  - `accessibilityState` ─→ Expresses state (disabled)  │
└──────────────────────────┬─────────────────────────────┘
                           │
             ┌─────────────┴─────────────┐
             ▼                           ▼
┌────────────────────────┐               ┌────────────────────────┐
│   iOS VoiceOver        │               │   Android TalkBack     │
│  Swipes & Screen Reader│               │  Touch Exploration     │
└────────────────────────┘               └────────────────────────┘
```

---

# 2. Accessibility Props Component Implementation

```tsx
import React from 'react';
import { TouchableOpacity, Text, Image } from 'react-native';

export function CartIconButton({ itemCount, onPress }: { itemCount: number; onPress: () => void }) {
  return (
    <TouchableOpacity
      onPress={onPress}
      accessible={true}
      accessibilityRole="button"
      accessibilityLabel={`Shopping cart with ${itemCount} items`}
      accessibilityHint="Navigates to your shopping cart"
    >
      <Image source={require('./cart-icon.png')} />
      {itemCount > 0 && <Text>{itemCount}</Text>}
    </TouchableOpacity>
  );
}
```

---

# 3. Dynamic Font Scaling & Contrast Verification

- **Dynamic Type (iOS) / Font Scaling (Android)**: Ensure text elements scale gracefully when users increase system font sizes. Do NOT set `allowFontScaling={false}` globally.
- **Color Contrast (WCAG 2.1 AA)**: Text elements MUST maintain a minimum contrast ratio of **4.5:1** against background colors (**3:1** for large text).

---

# 4. Operational Verification Checklist

- [ ] **Icon Buttons Labeled**: All icon-only buttons include descriptive `accessibilityLabel` strings.
- [ ] **VoiceOver Audited**: Screen navigation tested with iOS VoiceOver enabled.
- [ ] **TalkBack Audited**: Screen navigation tested with Android TalkBack enabled.
- [ ] **Dynamic Scaling Tested**: App tested with maximum system font size; zero text clipping.
- [ ] **Contrast Ratio Compliant**: Text colors maintain 4.5:1 WCAG contrast ratio.

---

# 5. Official Sources

- React Native Accessibility Documentation: https://reactnative.dev/docs/accessibility
- Apple Accessibility Guidelines: https://developer.apple.com/accessibility/

---

**Last verified:** August 14, 2026
