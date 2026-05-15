---

## Screen 01: Filter Panel

**Purpose:** Discrete filter controls for narrowing the collection by varietal, region, vintage range, and status. Accessible from the Inventory List as a bottom sheet (mobile) or side drawer (tablet).
**User Stories:** US-2.2, US-2.3, US-2.4, US-4.4

### Layout — Mobile Bottom Sheet

```
┌─────────────────────────────────────────────┐
│ ▔▔▔▔▔▔▔▔ (drag handle) ▔▔▔▔▔▔▔▔            │
│ Filters                          [Reset all] │  ← Header + reset
├─────────────────────────────────────────────┤
│                                             │
│ STATUS                                      │
│ ┌─────────┐  ┌──────────────┐  ┌─────────┐ │
│ │  All    │  │  Active only │  │Consumed │ │  ← Segment control
│ └─────────┘  └──────────────┘  └─────────┘ │
│                                             │
│ VARIETAL                                    │
│ ┌────────────────────┐ ┌─────────────────┐  │
│ │ Cabernet Sauvignon │ │   Chardonnay    │  │  ← Chip — unselected
│ └────────────────────┘ └─────────────────┘  │
│ ┌───────────┐ ┌──────────────────────────┐  │
│ │ [✓ Merlot]│ │  Pinot Noir              │  │  ← Chip — selected (filled)
│ └───────────┘ └──────────────────────────┘  │
│  + Show more varietals                      │
│                                             │
│ REGION                                      │
│ ┌──────────┐ ┌──────────┐ ┌─────────────┐  │
│ │ Bordeaux │ │[✓ Rhône] │ │ Napa Valley │  │
│ └──────────┘ └──────────┘ └─────────────┘  │
│  + Show more regions                        │
│                                             │
│ VINTAGE RANGE                               │
│  From year  [        ]  To year  [        ] │  ← Numeric inputs
│  ⚠ vintage_from must not exceed vintage_to  │  ← Inline error (when invalid)
│                                             │
│ ─────────── POST-MVP ──────────────────────  │
│ LOCATION (contains)                         │
│  [ Rack 3                                 ] │
│                                             │
├─────────────────────────────────────────────┤
│  [      Cancel      ]  [  Apply Filters  ]  │  ← Sticky footer
└─────────────────────────────────────────────┘
```

### Information Hierarchy

| Priority | Content | Placement |
|----------|---------|-----------|
| Primary | Active filter selections (filled chips) | Chips — filled state |
| Primary | Apply Filters CTA | Sticky footer, right button |
| Secondary | Available filter options | Chips — outline state |
| Tertiary | Vintage range inputs | Below chips |
| Tertiary | Location filter (Post-MVP) | Below vintage |
| Utility | Reset all | Top-right header text link |
| Utility | Cancel | Sticky footer, left button |

### States

| State | Appearance | User Feedback |
|-------|------------|---------------|
| No filters selected | All chips in outline style; Apply button shows "Apply Filters" | Neutral state |
| Filter(s) selected | Selected chips filled/highlighted; Apply button shows "Apply (N)" | Count in CTA |
| Vintage error | Red border on inputs + "vintage_from must not exceed vintage_to" below | Inline error |
| Loading filter options | Chips show shimmer placeholders while values load | Skeleton chips |
| No values available | Section header hidden if no options exist for that dimension | Clean — no empty sections |

### Interactive Elements

| Element | Type | Behavior |
|---------|------|----------|
| Varietal chips | Multi-select toggle | Tap to select/deselect; multiple = OR logic |
| Region chips | Multi-select toggle | Same as varietal |
| Status segment | Single-select segment | All / Active only / Consumed only |
| Vintage From / To | Numeric input | Accepts 1800–current year; validates on Apply tap |
| Show more | Text link | Expands collapsed chip list |
| Apply Filters | Primary button | Closes panel; applies all selections to list |
| Cancel | Secondary button | Closes panel; no changes applied |
| Reset all | Text link | Clears all selections; stays open for new selection |

### Key UX Decisions

- Chips use minimum 44px height for one-handed thumb use (JRN-03.1 cellar-side requirement)
- Varietal and Region appear before Vintage range — matches priority order across all personas
- "Apply" is explicit (not auto-close on selection) — prevents accidental filter commits
- "Reset all" is in the panel header (not just in the main list) for quick re-selection during cellar browsing
- Post-MVP location filter is included in layout but visually separated with a label

---
