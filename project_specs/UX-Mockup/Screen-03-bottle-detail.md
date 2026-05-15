---

## Screen 03: Bottle Detail View

**Purpose:** Full record view for a single bottle. Shows all metadata, location, and tasting entry (if consumed). Entry point for Edit, Delete, Mark as Consumed, and Restore actions.
**User Stories:** US-1.4, US-0.3, US-0.4, US-3.1, US-3.5, US-4.2

### Layout — Active Bottle (Mobile)

```
┌─────────────────────────────────────────────┐
│ ←  Back                               [⋮]  │  ← Back nav + overflow menu
├─────────────────────────────────────────────┤
│                                             │
│  📍 Rack 3 / Bin 12                        │  ← Location — prominent, near top (US-4.2)
│     (No location recorded.)                 │  ← Placeholder when blank
│                                             │
│  ┌──────────────────────────── [Active ●] ┐ │
│  │ Château Margaux                        │ │  ← Bottle name — large
│  │ 2015  ·  Cabernet Sauvignon            │ │  ← Vintage · Varietal
│  │ Château Margaux  ·  Bordeaux           │ │  ← Producer · Region
│  └────────────────────────────────────────┘ │
│                                             │
│  ─────────── DETAILS ──────────────────────  │
│                                             │
│  Notes                                      │
│  Gift from a friend. Shows great structure  │
│  and will age beautifully.                  │
│                                             │
│  Purchase Date   2023-06-01                 │
│  Purchase Price  $450.00                    │
│  Quantity        1                          │
│                                             │
│  Added           2026-05-15                 │
│                                             │
│  ─────────────────────────────────────────  │
│                                             │
│  ┌─────────────────────────────────────────┐│
│  │   🍷  Mark as Consumed                 ││  ← Primary CTA — active bottle only
│  └─────────────────────────────────────────┘│
│                                             │
│  ┌─────────────────────────────────────────┐│
│  │   ✏️  Edit Bottle                      ││  ← Secondary action
│  └─────────────────────────────────────────┘│
│                                             │
└─────────────────────────────────────────────┘
```

**Overflow menu (⋮) contains:**
- Edit Bottle (duplicate of inline button for discoverability)
- Delete Bottle (destructive — red text)

---

### Layout — Consumed Bottle (Mobile)

```
┌─────────────────────────────────────────────┐
│ ←  Back                               [⋮]  │
├─────────────────────────────────────────────┤
│                                             │
│  📍 Rack 5 / Bin 3                         │  ← Location still shown for consumed
│                                             │
│  ┌──────────────────────────── [Consumed ✓]┐│
│  │ ~~Pichon Baron~~                       │ │  ← Greyed name + strikethrough
│  │ 2010  ·  Cabernet Sauvignon             │ │
│  │ Pichon Baron  ·  Bordeaux               │ │
│  └────────────────────────────────────────┘ │
│                                             │
│  ─────────── TASTING ENTRY ───────────────  │
│                                             │
│  Consumed      2024-11-30                   │
│  Rating        ★★★★☆  (4 / 5)             │  ← Star display
│                                             │
│  Tasting Notes                              │
│  "Dried cherry, forest floor, silky         │
│  tannins — this aged beautifully."          │
│                                             │
│  ─────────── DETAILS ──────────────────────  │
│  [same purchase / notes section as active]  │
│                                             │
│  ─────────────────────────────────────────  │
│                                             │
│  ┌─────────────────────────────────────────┐│
│  │   ✏️  Edit Tasting Notes               ││  ← Primary action for consumed
│  └─────────────────────────────────────────┘│
│                                             │
│  ┌─────────────────────────────────────────┐│
│  │   ✏️  Edit Bottle                      ││  ← Edit bottle metadata
│  └─────────────────────────────────────────┘│
│                                             │
│  ┌─────────────────────────────────────────┐│
│  │   ↩  Restore to Cellar                 ││  ← Secondary/recovery action
│  └─────────────────────────────────────────┘│
│                                             │
└─────────────────────────────────────────────┘
```

---

### Information Hierarchy

| Priority | Content | Placement |
|----------|---------|-----------|
| Primary | Location string | Top of content, before bottle name |
| Primary | Bottle name | Large heading below location |
| Primary | Vintage · Varietal | Subtitle line |
| Primary | Producer · Region | Second subtitle line |
| Primary | Status badge | Top-right of name block |
| Secondary | Tasting entry (consumed bottles) | Below name block, above details |
| Secondary | Rating stars | Within tasting entry section |
| Tertiary | Purchase Date, Price, Quantity | Details section |
| Tertiary | Notes (bottle notes) | Details section |
| Utility | Mark as Consumed / Edit / Restore | Bottom action area |

### States

| State | Appearance | User Feedback |
|-------|------------|---------------|
| Active bottle | Full-color name; "Active ●" badge; "Mark as Consumed" CTA visible | Normal |
| Consumed bottle | Greyed name + strikethrough; "Consumed ✓" badge; Tasting entry section visible | Clear status |
| No location | "No location recorded." in muted italics | Placeholder — still near top |
| No tasting entry | Tasting section shows "No tasting notes recorded." with "Edit Tasting Notes" CTA | Prompts completion |
| No tasting notes (but consumed) | Rating shown if present; notes area shows "No notes recorded." | Partial record |
| Loading | Skeleton layout with shimmer blocks | Non-blocking |
| Save in progress | Action buttons disabled; spinner on active button | Prevents double-submit |

### Interactive Elements

| Element | Type | Behavior |
|---------|------|----------|
| ← Back | Nav | Returns to previous screen (inventory list, journal, search results) |
| ⋮ Overflow | Menu | Edit + Delete options |
| Mark as Consumed | Primary button | Opens Consume + Tasting Form |
| Edit Bottle | Button | Opens Edit Bottle Form (pre-populated) |
| Edit Tasting Notes | Button (consumed only) | Opens Tasting Edit Form (pre-populated) |
| Restore to Cellar | Button (consumed only) | Opens restore confirmation dialog |
| Delete (via overflow) | Destructive | Opens delete confirmation dialog |

### Key UX Decisions

- Location string at the very top of content — Daniel reads it at a glance in the cellar without scrolling (US-4.2, JRN-03.1)
- "Mark as Consumed" is a full-width button, not a menu item — primary action for active bottles, immediately visible
- Consumed bottle: name uses greyed + strikethrough — consistent with list view consumed indicator
- Action buttons are at the bottom (thumb zone) — one-handed mobile use
- Delete is only in the overflow menu — prevents accidental taps on the primary action area

---
