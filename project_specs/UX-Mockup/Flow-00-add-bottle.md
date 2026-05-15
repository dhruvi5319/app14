---

## Flow 00: Add New Bottle

**Trigger:** User taps the FAB (+) from the Inventory List screen
**User Stories:** US-0.1, US-0.2, US-0.5
**Journeys:** JRN-01.1 (Marcus — full record), JRN-02.1 (Sophie — quick-add), JRN-03.2 (Daniel — one-handed cellar)

```
[Inventory List — FAB visible]
        │
        ▼  tap FAB (+)
[Add Bottle Form — blank]
        │
        ├── Fill Name + Vintage only
        │       │
        │       ▼  tap Save
        │   [Validation: passes]
        │       │
        │       ├── tap "Save & Add Another"
        │       │       │
        │       │       ▼
        │       │   [Add Bottle Form — pre-filled with previous producer/location]
        │       │       └── (loop back for next bottle)
        │       │
        │       └── tap "Save"
        │               │
        │               ▼
        │           [Bottle Detail View — new bottle]
        │               │
        │               └── back → [Inventory List — new bottle at top]
        │
        ├── Fill all fields (full record)
        │       │
        │       └── (same Save path as above)
        │
        └── Validation fails (blank name / bad vintage)
                │
                ▼
            [Form stays open — inline error messages per field]
                │
                └── User corrects → retry Save
```

**Steps:**

1. **Entry Point** — FAB (+) is pinned to the bottom-right corner of the Inventory List screen, always visible regardless of scroll position. One tap, zero navigation.

2. **Form Presentation** — The Add Bottle Form slides up as a bottom sheet (mobile) or full-page view (tablet). Fields are ordered: Name → Vintage → Producer → Varietal → Region → Location → Notes → Purchase Date → Purchase Price → Quantity. Name and Vintage are visually separated as the "required" group at the top.

3. **Required vs. Optional** — Required fields (Name, Vintage) have no "optional" label. Optional fields show a small `(optional)` tag in muted text next to the label. This sets expectations before the user starts typing.

4. **Validation on Submit** — Validation fires on Save tap, not on blur. Failing fields receive a red border + inline message beneath. All other entered values are preserved. The form scrolls to the first failing field.

5. **Save** — On success, the new bottle appears at the top of the Inventory List (most-recently-added sort). The user is navigated to the Bottle Detail View for the new record. A brief "Added!" success toast appears (2s, non-blocking).

6. **Save & Add Another** — A secondary action alongside the primary Save button. On tap, the form resets but pre-fills: Producer, Region, Location from the previous entry. This supports Marcus's and Daniel's multi-bottle sessions without navigational friction (JRN-01.1 Save & Repeat, JRN-03.2 Rapid Repeat).

**Key UX Decisions:**

- `Save & Add Another` pre-fills location field — eliminates the primary pain point for multi-bottle cellar sessions (JRN-03.2)
- No "draft" saved automatically — simplicity preferred over complexity for v1
- Vintage field is a numeric input (keyboard type: number) with placeholder "e.g. 2019"
- Location field is near the top of optional fields (after Region) because it is Daniel's primary use case

---
