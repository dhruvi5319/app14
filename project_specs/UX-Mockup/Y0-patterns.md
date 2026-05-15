---

## Interaction Patterns

### Pattern: Inline Validation on Submit

**When to use:** All form submissions (Add Bottle, Edit Bottle, Consume + Tasting, Edit Tasting)
**User Stories:** US-0.5

**Behavior:**
- Validation fires only on Save/Submit tap — not on field blur or change
- Failing fields receive a red left-border or red underline
- Error message appears directly below the failing field: specific, not generic
- Form auto-scrolls to the first failing field
- All other entered values are preserved — user only fixes what's broken

**Error message format:**
```
[Field label]
┌─────────────────────────────────────────────────────┐
│ [user's entered value]                              │
└─────────────────────────────────────────────────────┘
⚠ [Specific error message]
```

**Examples:**
- "Bottle name is required."
- "Vintage must be between 1800 and 2026."
- "Purchase date cannot be in the future."
- "Tasting date cannot be in the future."
- "Rating must be between 1 and 5."

---

### Pattern: Confirmation Dialog for Destructive Actions

**When to use:** Delete Bottle (US-0.4), Restore to Cellar (US-3.5)
**Trigger:** User taps a destructive action button/menu item

**Behavior:**
- Modal overlay blocks the background
- Dialog includes the bottle's name in the message (specificity prevents accidental confirmation)
- Two buttons: Cancel (left, neutral) | Confirm action (right, destructive red)
- Cancel is the default/safe option — positioned on the left
- Tapping outside the dialog = Cancel behavior

**Dialog template:**
```
┌──────────────────────────────────────────┐
│                                          │
│  Delete "Château Margaux 2015"?          │
│  This cannot be undone.                  │
│                                          │
│  ┌────────────┐    ┌────────────────┐   │
│  │   Cancel   │    │    Delete      │   │
│  │ (neutral)  │    │   (red/bold)   │   │
│  └────────────┘    └────────────────┘   │
│                                          │
└──────────────────────────────────────────┘
```

---

### Pattern: Toast Notifications

**When to use:** Post-save success feedback (non-blocking)

**Behavior:**
- Appears at the top of the screen (below the header) or at the bottom (above the FAB)
- Auto-dismisses after 2 seconds
- Does not interrupt the user flow
- No tap required to dismiss
- Does not stack — replaces any prior toast

**Toast messages:**
| Action | Message |
|--------|---------|
| Add Bottle | "Bottle added." |
| Edit Bottle | "Bottle updated." |
| Delete Bottle | "Bottle deleted." |
| Mark Consumed | "Bottle consumed. Tasting note saved." |
| Edit Tasting | "Tasting note updated." |
| Restore to Cellar | "[Bottle Name] restored to cellar." |

---

### Pattern: Save & Add Another

**When to use:** Add Bottle Form only (multi-bottle sessions)
**User Stories:** US-0.1
**Journeys:** JRN-01.1, JRN-03.2

**Behavior:**
- Secondary button alongside primary Save
- On tap: saves current bottle → opens new blank form
- Pre-fills from previous entry: Producer, Region, Location
- Clears: Name, Vintage, Notes, Purchase Date, Purchase Price, Quantity
- Allows rapid consecutive entry (Daniel's 6-bottle session)

**Pre-fill logic:**
```
Previous: { Name: "Rossignol-Trapet", Vintage: 2022, Producer: "Rossignol-Trapet",
            Region: "Bourgogne", Location: "Rack 6 / Bin 1" }

New form:  { Name: "", Vintage: "", Producer: "Rossignol-Trapet",
             Region: "Bourgogne", Location: "Rack 6 / Bin 1" }
```
User only changes: Name, Vintage, and possibly Location (bin number).

---

### Pattern: Active Filter Badge

**When to use:** Inventory List — when any search or filter is active

**Behavior:**
- Filter button shows a filled circular badge with the count of active filter dimensions
- Example: 2 filters active → badge shows "2"
- "N bottles found · Clear all" bar appears below the search bar
- Clear all is a text link (not a button) — compact, visible, one-tap

```
Filter button states:
  Inactive:  [⧩]         (outline icon, no badge)
  1 active:  [⧩ 1]       (filled badge, count = 1)
  3 active:  [⧩ 3]       (filled badge, count = 3)
```

---

### Pattern: Consumed Visual State

**When to use:** Any bottle card or detail view where `is_consumed = true`
**User Stories:** US-1.3

**Behavior:**
- Name text: 50% opacity + strikethrough
- Card background: slightly muted/desaturated (e.g., 5–10% grey overlay)
- Status badge: "Consumed ✓" in muted green or grey
- Location and other metadata remain readable (not greyed out)
- Active bottle badge: "Active ●" with a small green dot

This state is visible in:
- Inventory List (list and grid)
- Journal cards
- Bottle Detail header
- Search results

---

### Pattern: Offline-First Write

**When to use:** All write operations (Add, Edit, Delete, Consume)

**Behavior:**
- Write immediately to local storage; update UI optimistically
- Sync to persistent store in the background
- If network unavailable: write still succeeds from the user's perspective
- Sync failure: silent retry (not surfaced to user unless persistent)
- No "you're offline" blocker — the app works normally without network

This pattern is critical for Daniel's cellar-side use case (JRN-03.1, JRN-03.2).

---
