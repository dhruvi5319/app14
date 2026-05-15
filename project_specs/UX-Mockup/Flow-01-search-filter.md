---

## Flow 01: Search & Filter

**Trigger:** User taps the search bar or the Filter button from the Inventory List
**User Stories:** US-2.1, US-2.2, US-2.3, US-2.4
**Journeys:** JRN-03.1 (Daniel — cellar-side pairing), JRN-02.2 (Sophie — shop lookup)

```
[Inventory List]
        │
        ├── tap Search bar
        │       │
        │       ▼
        │   [Search input active — keyboard opens]
        │       │
        │       ├── type characters (250ms debounce)
        │       │       │
        │       │       ▼
        │       │   [Results update in-line — "N bottles found" badge]
        │       │       │
        │       │       ├── no matches → empty state message
        │       │       │
        │       │       └── tap a result → [Bottle Detail View]
        │       │
        │       └── tap ✕ in search bar → clears query, shows full list
        │
        ├── tap Filter button (or filter icon)
        │       │
        │       ▼
        │   [Filter Panel — slides up as bottom sheet]
        │       │
        │       ├── select Varietal chips (multi-select, OR logic)
        │       ├── select Region chips (multi-select, OR logic)
        │       ├── enter Vintage From / Vintage To
        │       ├── enter Location keyword (Post-MVP)
        │       │
        │       ├── tap Apply Filters
        │       │       │
        │       │       ▼
        │       │   [Filter Panel closes — List shows filtered results]
        │       │   [Active filter count badge on Filter button]
        │       │   ["N bottles found" count below search bar]
        │       │
        │       └── tap Reset (inside panel) → clears all panel selections
        │
        └── [Any filter/search active] → "Clear all" appears near result count
                │
                ▼  tap "Clear all"
            [All filters + search cleared — full list restored]
```

**Steps:**

1. **Search Bar** — Always visible at the top of the Inventory List below the header. Placeholder: "Search by name, producer, notes…". Tap to focus, keyboard opens. Results update live with 250ms debounce (US-2.1).

2. **Result Count** — When any filter or search is active, a count label appears below the search bar: "12 bottles found". Hidden when no filters are active (US-2.1, US-2.4).

3. **Filter Button** — Located to the right of the search bar. Shows a filled badge with active filter count (e.g., "2") when filters are applied. Tapping opens the Filter Panel.

4. **Filter Panel** — Bottom sheet on mobile, side drawer on tablet. Contains:
   - **Varietal** — Scrollable chip list (multi-select). Values populated from `GET /api/v1/bottles/filter-options`. Selection = OR logic within dimension.
   - **Region** — Scrollable chip list (multi-select). Same behavior.
   - **Vintage Range** — Two numeric inputs: "From year" and "To year". If From > To, inline error shown.
   - **Status toggle** — "All / Active only / Consumed only" (segment control)
   - *(Post-MVP)* **Location** — Text input, substring match

5. **Combining Filters** — All active dimensions apply with AND logic between dimensions, OR within. System recomputes on each change (US-2.2, US-2.3).

6. **Clear All** — A text link `Clear all` appears next to the result count whenever any filter or search is active. One tap resets everything and restores the full unfiltered list with default sort (US-2.4). Hidden when no filters are active.

7. **Cellar-Side Optimization** — Filter chips use minimum 44px touch targets. The panel is thumb-reachable (bottom sheet). Most common filters (Varietal, Region) appear first (JRN-03.1).

**Key UX Decisions:**

- Search and filter coexist simultaneously — both apply at once
- Filter panel does not auto-close on selection; user controls when to apply (prevents accidental filter loss)
- "Clear all" is visible in the main list view (not buried inside the filter panel) for one-tap reset
- Varietal and Region chip lists are pre-populated from the user's own collection values — no global wine database

---
