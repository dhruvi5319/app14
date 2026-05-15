---

## Screen 07: Empty States

**Purpose:** Designed empty state screens for all scenarios where no data is available — preventing confusion and guiding users toward the appropriate action.
**User Stories:** US-1.1, US-2.1, US-5.1

### Empty State: No Bottles in Collection (US-1.1)

```
┌─────────────────────────────────────────────┐
│ ≡  My Cellar              [⊞ grid] [sort ▾] │
├─────────────────────────────────────────────┤
│ 🔍 Search…                            [⧩]  │
├─────────────────────────────────────────────┤
│                                             │
│                                             │
│                 🍾                          │
│                                             │
│       No bottles in your cellar yet.        │
│                                             │
│     Tap + to add your first bottle.         │
│                                             │
│           ↓ (arrow pointing to FAB)         │
│                                             │
│                                             │
│                                             │
│                                             │
├─────────────────────────────────────────────┤
│  [Cellar]       [Journal]      [Stats]      │
│                                     [  +  ] │  ← FAB with pulse animation on first visit
└─────────────────────────────────────────────┘
```

**Copy:** "No bottles in your cellar yet. Tap + to add your first bottle."
**CTA:** FAB pulses with a subtle animation on first visit to draw attention.

---

### Empty State: No Search / Filter Results (US-2.1)

```
┌─────────────────────────────────────────────┐
│ ≡  My Cellar                                │
├─────────────────────────────────────────────┤
│ 🔍 "bordeaux rouge"                   [⧩ 1]│  ← Search query shown
│ 0 bottles found  ·  Clear all              │
├─────────────────────────────────────────────┤
│                                             │
│                                             │
│                 🔍                          │
│                                             │
│   No bottles match your search.             │
│   Try adjusting the filters.                │
│                                             │
│       [ Clear all filters ]                 │  ← Button to reset
│                                             │
│                                             │
├─────────────────────────────────────────────┤
│  [Cellar]       [Journal]      [Stats]      │
│                                     [  +  ] │
└─────────────────────────────────────────────┘
```

**Copy:** "No bottles match your search. Try adjusting the filters."
**CTA:** "Clear all filters" button — direct path to reset.

---

### Empty State: Journal Empty (US-3.3)

```
┌─────────────────────────────────────────────┐
│  Journal                                    │
├─────────────────────────────────────────────┤
│ 0 bottles consumed                          │
├─────────────────────────────────────────────┤
│                                             │
│                 📓                          │
│                                             │
│   You haven't consumed any bottles yet.     │
│                                             │
│   When you open a bottle, tap               │
│   "Mark as Consumed" to start your          │
│   tasting journal.                          │
│                                             │
├─────────────────────────────────────────────┤
│  [Cellar]       [Journal ●]    [Stats]      │
└─────────────────────────────────────────────┘
```

---

### Empty State: Statistics — Empty Collection (US-5.1)

```
┌─────────────────────────────────────────────┐
│  My Cellar Stats                            │
├─────────────────────────────────────────────┤
│                                             │
│  ┌────────────┐ ┌──────────┐ ┌───────────┐ │
│  │   Total    │ │  Active  │ │ Consumed  │ │
│  │     0      │ │    0     │ │     0     │ │
│  └────────────┘ └──────────┘ └───────────┘ │
│                                             │
│              📊                             │
│                                             │
│  Add bottles to your collection             │
│  to see statistics.                         │
│                                             │
│  [ Go to Cellar ]                           │  ← CTA to navigate to Inventory
│                                             │
├─────────────────────────────────────────────┤
│  [Cellar]       [Journal]      [Stats ●]   │
└─────────────────────────────────────────────┘
```

---

### Empty State Design Principles

| Principle | Implementation |
|-----------|---------------|
| **Always explain why** | Copy explains what the empty state means, not just "nothing here" |
| **Always suggest an action** | Every empty state has a clear next step (CTA or pointer) |
| **Avoid negative language** | "You haven't consumed any yet" not "No data found" |
| **Icon/illustration** | Simple emoji-level illustration for visual break — not decorative noise |
| **No dead ends** | Empty states never trap users — always provide a path forward |

---
