# UX Mockup — Wine Inventory App

**Project:** WineInventory
**Generated:** 2026-05-15
**Based on:** UserStories-WineInventory.md, PRD-WineInventory.md, FRD-WineInventory.md, JOURNEYS-WineInventory.md

---

## Overview

### UX Approach

The Wine Inventory App is designed **mobile-first** with three distinct use contexts in mind:

1. **Desk/tablet** — Marcus logs new bottles after an auction or shipment arrives; full-form entry with all metadata
2. **Party/shop** — Sophie quick-captures a bottle with one hand and one thumb; speed over completeness
3. **Cellar-side** — Daniel filters and retrieves physical location in under 30 seconds, one-handed, possibly offline

Every screen decision is weighed against these three scenarios. When in conflict, **cellar-side readability and speed wins** for display, and **quick-capture ease wins** for data entry.

### Design Principles

| Principle | Implementation |
|-----------|---------------|
| **Speed over completeness** | Only Name + Vintage are required; all other fields optional and visually subordinate |
| **Glanceable information** | Location, vintage, consumed status visible on list cards — no drill-in required |
| **One-handed usability** | FAB in bottom-right thumb zone; filter chips large (min 44px tap target); sticky Save button |
| **Offline-first** | All writes go to local storage immediately; sync is background and non-blocking |
| **Progressive disclosure** | Optional fields are visible but de-emphasized; advanced filters in a panel, not the main view |
| **Continuous flows** | Mark Consumed + Tasting Note = single uninterrupted form; no navigation mid-task |
| **Clear state communication** | Consumed bottles visually distinct (greyed + icon); active filter count shown in badge |

### Navigation Structure

```
┌─────────────────────────────────────────────┐
│                Bottom Nav Bar               │
│   [Cellar]   [Journal]   [Stats]            │
└─────────────────────────────────────────────┘

Cellar   → Inventory List Screen (home)
Journal  → Consumed / Tasting History Screen
Stats    → Dashboard / Statistics Screen (Post-MVP)

FAB (+)  → Add Bottle Form (always visible from Cellar)
```

### Screen Inventory

| Screen | File | User Stories |
|--------|------|-------------|
| Inventory List | Screen-00-inventory-list.md | US-1.1, US-1.2, US-1.3, US-1.4, US-2.1, US-2.4 |
| Filter Panel | Screen-01-filter-panel.md | US-2.2, US-2.3, US-2.4, US-4.4 |
| Add / Edit Bottle Form | Screen-02-bottle-form.md | US-0.1, US-0.2, US-0.3, US-0.5, US-4.1 |
| Bottle Detail View | Screen-03-bottle-detail.md | US-1.4, US-0.3, US-0.4, US-3.1, US-3.5, US-4.2 |
| Consume + Tasting Flow | Screen-04-consume-tasting.md | US-3.1, US-3.2, US-3.4 |
| Journal / Consumed List | Screen-05-journal.md | US-3.3, US-1.3 |
| Statistics Dashboard | Screen-06-statistics.md | US-5.1, US-5.2, US-5.3 |
| Empty States | Screen-07-empty-states.md | US-1.1, US-2.1, US-5.1 |

### Flow Inventory

| Flow | File | Journeys |
|------|------|---------|
| Add New Bottle | Flow-00-add-bottle.md | JRN-01.1, JRN-02.1, JRN-03.2 |
| Search & Filter | Flow-01-search-filter.md | JRN-03.1, JRN-02.2 |
| Mark Consumed + Tasting Note | Flow-02-consume-tasting.md | JRN-01.2 |
| Edit Bottle Record | Flow-03-edit-bottle.md | US-0.3 |
| Delete Bottle | Flow-04-delete-bottle.md | US-0.4 |
| Restore to Active | Flow-05-restore-bottle.md | US-3.5 |
| View Statistics | Flow-06-statistics.md | JRN-01.3 |

---
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
---

## Flow 02: Mark Consumed + Log Tasting Note

**Trigger:** User taps "Mark as Consumed" from an active bottle's Detail View
**User Stories:** US-3.1, US-3.2, US-3.4
**Journey:** JRN-01.2 (Marcus — at the dinner table)

```
[Bottle Detail View — active bottle]
        │
        ▼  tap "Mark as Consumed"
[Consume + Tasting Form — single continuous screen]
        │
        │  Fields shown:
        │  • Tasting Date (pre-filled: today)        [required for consume; editable]
        │  • Rating — 1–5 star tap control           [optional]
        │  • Tasting Notes — large text area         [optional, up to 5000 chars]
        │
        ├── tap "Confirm & Save"
        │       │
        │       ├── Validation passes
        │       │       │
        │       │       ▼
        │       │   [Bottle marked consumed in DB]
        │       │   [Tasting entry created]
        │       │   [Navigate to Bottle Detail — consumed state]
        │       │   [Success toast: "Bottle consumed. Tasting note saved."]
        │       │
        │       └── Validation fails (future tasting date / rating out of range)
        │               │
        │               ▼
        │           [Form stays open — inline error messages]
        │
        └── tap "Cancel" / swipe down
                │
                ▼
            [Return to Bottle Detail — bottle remains active, no changes]
```

**Edit Tasting Notes (post-consume):**

```
[Bottle Detail View — consumed bottle]
        │
        ▼  tap "Edit Tasting Notes"
[Tasting Edit Form — pre-populated with existing values]
        │
        ├── modify notes / rating / date
        │
        ├── tap Save → [updated tasting entry] → [Bottle Detail reflects new values]
        │
        └── tap Cancel → [Bottle Detail — unchanged]
```

**Steps:**

1. **Single Continuous Flow** — The consume action and tasting note form are one screen, not two. Marking consumed and entering notes happens without any navigation between steps. This directly addresses the dinner-table friction identified in JRN-01.2.

2. **Tasting Date** — Pre-populated with today's date in the user's local timezone. Editable via a date picker. Future dates rejected with inline error: "Tasting date cannot be in the future." (US-3.2).

3. **Star Rating Control** — Five tappable star icons. Tapping a star fills 1–N stars. Tapping the same star again deselects (sets to null — rating is optional). Stars are minimum 44px touch targets (US-3.2).

4. **Tasting Notes** — Large text area with character counter (e.g., "0 / 5000"). Placeholder: "Describe aromas, palate, finish…". Optional guided prompts can appear as faded placeholder text to help beginners (JRN-01.2 opportunity).

5. **Confirm & Save** — Primary action button, sticky at the bottom. On success: bottle status changes to consumed, tasting entry is created, user is navigated to the Bottle Detail in consumed state. Toast: "Bottle consumed. Tasting note saved." (2s).

6. **Cancel** — Secondary action. Returns to Bottle Detail with no changes. No confirmation dialog needed (no data has been committed yet).

7. **Edit Tasting Notes** — Accessible from any consumed bottle's detail view. Form is pre-populated. Same validation rules apply. Save updates the tasting entry and reflects changes immediately (US-3.4).

**Key UX Decisions:**

- No navigation between "mark consumed" and "enter notes" — single form, single submit
- All tasting fields are optional — "Confirm & Save" without filling anything is valid
- The consume CTA in Bottle Detail View is a prominent button, not a menu item — discoverable at a glance
- Edit Tasting Notes is a separate entry point from the consumed bottle detail (not the same flow as initial consume)

---
---

## Flow 03: Edit Bottle Record

**Trigger:** User taps "Edit" from a Bottle Detail View
**User Stories:** US-0.3, US-0.5, US-4.1 (location update)

```
[Bottle Detail View — any bottle]
        │
        ▼  tap "Edit" (top-right action or action menu)
[Edit Bottle Form — pre-populated with current values]
        │
        ├── modify any field(s)
        │
        ├── tap Save
        │       │
        │       ├── Validation passes
        │       │       │
        │       │       ▼
        │       │   [Updated record persisted]
        │       │   [Navigate to Bottle Detail — updated values visible immediately]
        │       │   [Success toast: "Bottle updated."]
        │       │
        │       └── Validation fails
        │               │
        │               ▼
        │           [Form stays open — inline errors per failing field]
        │           [Previously entered values retained]
        │
        └── tap Cancel / back
                │
                ▼
            [Bottle Detail — original values unchanged]
```

**Steps:**

1. **Entry Point** — "Edit" is accessible from the Bottle Detail View via a clearly labeled button (or pencil icon) in the top-right header area. Maximum 2 taps from the inventory list.

2. **Pre-populated Form** — All current field values appear in their respective inputs. The user sees exactly what will be saved and can modify any field including required fields (US-0.3).

3. **Validation** — Same rules as Add Bottle. Inline errors appear beneath failing fields. Other entered values are retained (US-0.5).

4. **Save** — On success, navigates back to Bottle Detail with updated values. The inventory list also reflects changes immediately.

5. **Cancel / Back** — Discards all edits. If unsaved changes exist, a brief confirmation: "Discard changes?" with "Discard" / "Keep Editing" options.

**Key UX Decisions:**

- Edit form is identical in structure to Add form — same field order, same validation, zero learning curve
- Location field is the same optional field in Edit as in Add — updating location is the primary location workflow (F04.5)

---

## Flow 04: Delete Bottle

**Trigger:** User taps "Delete" from a Bottle Detail View
**User Stories:** US-0.4

```
[Bottle Detail View]
        │
        ▼  tap "Delete" (destructive action — in overflow menu or bottom danger zone)
[Confirmation Dialog]
  "Delete [Bottle Name]? This cannot be undone."
        │
        ├── tap "Delete" (confirm)
        │       │
        │       ▼
        │   [Bottle + tasting entry permanently deleted]
        │   [Navigate to Inventory List]
        │   [Bottle absent from list, search, and stats]
        │   [Toast: "Bottle deleted."]
        │
        └── tap "Cancel"
                │
                ▼
            [Dialog closes — Bottle Detail unchanged]
```

**Steps:**

1. **Entry Point** — "Delete" is in an overflow (⋮) menu or at the bottom of the Bottle Detail View in a danger zone (red text, separated from primary actions). This reduces accidental taps while keeping it accessible.

2. **Confirmation Dialog** — Modal dialog with the bottle name in the message: "Delete [Bottle Name]? This cannot be undone." Two buttons: "Cancel" (left, neutral) and "Delete" (right, red/destructive). This pattern is required by US-0.4.

3. **Post-delete** — User is returned to the Inventory List. The deleted bottle does not appear in any list, search result, or statistics view. Toast: "Bottle deleted." (2s).

**Key UX Decisions:**

- Delete is visually separated from non-destructive actions to prevent accidental activation
- Confirmation dialog uses the bottle's actual name for specificity and to make the user pause
- No undo (out of scope for v1) — the confirmation dialog is the sole safety net

---

## Flow 05: Restore Bottle to Active

**Trigger:** User taps "Restore to Cellar" from a consumed Bottle Detail View
**User Stories:** US-3.5

```
[Bottle Detail View — consumed bottle]
        │
        ▼  tap "Restore to Cellar"
[Confirmation Dialog]
  "Restore [Bottle Name] to your active cellar?
   This will delete the tasting note."
        │
        ├── tap "Restore" (confirm)
        │       │
        │       ▼
        │   [is_consumed = false; consumed_at cleared]
        │   [Tasting entry deleted]
        │   [Bottle Detail now shows active state]
        │   [Active bottle count updated]
        │   [Toast: "[Bottle Name] restored to cellar."]
        │
        └── tap "Cancel"
                │
                ▼
            [Dialog closes — consumed state unchanged]
```

**Steps:**

1. **Entry Point** — "Restore to Cellar" button visible on a consumed bottle's detail view, distinct from the "Edit Tasting Notes" action. Placed in a secondary action area (not primary CTA position).

2. **Confirmation** — Dialog warns that the tasting entry will be deleted: "Restore [Bottle Name] to your active cellar? This will delete the tasting note." This is an irreversible action for the tasting data.

3. **Post-restore** — Bottle Detail view immediately shows the active state (Mark as Consumed CTA reappears, tasting section disappears). Active count updates. Toast confirms action.

**Key UX Decisions:**

- Restoration warning explicitly mentions tasting note deletion — users must understand the data consequence
- This is a rare recovery action; it's accessible but not prominent in the UI

---
---

## Flow 06: View Statistics Dashboard

**Trigger:** User taps "Stats" in the bottom navigation bar
**User Stories:** US-5.1, US-5.2, US-5.3
**Journey:** JRN-01.3 (Marcus — weekly composition review)

```
[Any Screen]
        │
        ▼  tap "Stats" in bottom nav (≤2 taps from anywhere)
[Statistics Dashboard Screen]
        │
        ├── [Summary Cards] — Total / Active / Consumed counts
        │
        ├── [Varietal Breakdown] — horizontal bar chart, sorted by count desc
        │       │
        │       └── tap a bar → [Inventory List pre-filtered by that varietal]
        │
        ├── [Region Breakdown] — horizontal bar chart, sorted by count desc
        │       │
        │       └── tap a bar → [Inventory List pre-filtered by that region]
        │
        ├── [Vintage Distribution] — horizontal bar chart, chronological, active only
        │
        └── [Producer Breakdown] — count list, sorted by count desc
                │
                └── "Show more" if > 20 producers
```

**Steps:**

1. **Entry** — "Stats" is a tab in the bottom navigation, accessible in 1 tap from any screen.

2. **Summary Cards** — Three stat cards at the top: Total Bottles, Active, Consumed. Always fresh (no stale data). Empty collection shows: "Add bottles to your collection to see statistics." (US-5.1)

3. **Varietal & Region Charts** — Horizontal bar charts. Bars are tappable — tapping navigates to the Inventory List pre-filtered by that varietal or region (JRN-01.3 Drill by Varietal opportunity). "Unknown" grouped at the bottom. Up to 20 entries shown; "Show more" for longer lists. (US-5.2)

4. **Vintage Distribution** — Shows only active bottles by default (what the user currently holds). Sorted oldest → newest. (US-5.3)

5. **Producer Breakdown** — Count list with horizontal bars. Top 20 by default; "Show more" expands the list. (US-5.3)

**Key UX Decisions:**

- Tappable chart bars create a direct link from insight → filtered inventory view (JRN-01.3 delight opportunity)
- Vintage distribution defaults to active-only because the user's primary question is "what am I holding?"
- Statistics tab in bottom nav = ≤2 taps from anywhere, not buried in a hamburger menu (JRN-01.3 Navigate stage risk)

---
---

## Screen 00: Inventory List

**Purpose:** Primary everyday interface — the home screen. Shows the full collection with at-a-glance bottle summaries, search, and filter access. Entry point for all other major flows.
**User Stories:** US-1.1, US-1.2, US-1.3, US-1.4, US-2.1, US-2.4

### Layout — List View (Mobile)

```
┌─────────────────────────────────────────────┐
│ ≡  My Cellar              [⊞ grid] [sort ▾] │  ← Header
├─────────────────────────────────────────────┤
│ 🔍 Search by name, producer, notes…  [⧩ 2] │  ← Search bar + filter badge
│ ─────────────────────────────────────────── │
│ 142 bottles  ·  Clear all                   │  ← Result count + clear (when active)
├─────────────────────────────────────────────┤
│                                             │
│ ┌─────────────────────────────────────────┐ │
│ │ Château Margaux                     2015│ │  ← Bottle name + vintage (right)
│ │ Château Margaux  ·  Cab. Sauvignon      │ │  ← Producer · Varietal
│ │ Rack 3 / Bin 12             [Active ●] │ │  ← Location + status badge
│ └─────────────────────────────────────────┘ │
│                                             │
│ ┌─────────────────────────────────────────┐ │
│ │ Domaine Leflaive Puligny          2018  │ │
│ │ Domaine Leflaive  ·  Chardonnay         │ │
│ │ Rack 1 / Bin 4              [Active ●] │ │
│ └─────────────────────────────────────────┘ │
│                                             │
│ ┌─────────────────────────────────────────┐ │
│ │ ~~Pichon Baron~~                  2010  │ │  ← Consumed: name greyed + strikethrough
│ │ Pichon Baron  ·  Cab. Sauvignon         │ │  ← Producer/varietal dimmed
│ │ Rack 5 / Bin 3          [Consumed ✓]   │ │  ← Consumed badge
│ └─────────────────────────────────────────┘ │
│                                             │
│           · · · (scroll) · · ·             │
│                                             │
├─────────────────────────────────────────────┤
│  [Cellar]       [Journal]      [Stats]      │  ← Bottom nav
│                                     [  +  ] │  ← FAB
└─────────────────────────────────────────────┘
```

### Layout — Grid View (Mobile)

```
┌─────────────────────────────────────────────┐
│ ≡  My Cellar              [☰ list] [sort ▾] │
├─────────────────────────────────────────────┤
│ 🔍 Search…                            [⧩ 2]│
├─────────────────────────────────────────────┤
│ ┌──────────────┐  ┌──────────────┐          │
│ │  Château     │  │  Domaine     │          │
│ │  Margaux     │  │  Leflaive    │          │
│ │  2015        │  │  2018        │          │
│ │  [Active ●]  │  │  [Active ●]  │          │
│ └──────────────┘  └──────────────┘          │
│ ┌──────────────┐  ┌──────────────┐          │
│ │ ~~Pichon~~   │  │  ...         │          │
│ │ ~~Baron~~    │  │              │          │
│ │  2010        │  │              │          │
│ │ [Consumed ✓] │  │              │          │
│ └──────────────┘  └──────────────┘          │
├─────────────────────────────────────────────┤
│  [Cellar]       [Journal]      [Stats]      │
│                                     [  +  ] │
└─────────────────────────────────────────────┘
```

### Information Hierarchy

| Priority | Content | Placement |
|----------|---------|-----------|
| Primary | Bottle name + vintage | Top of card, large text |
| Secondary | Producer · Varietal | Second line, muted text |
| Secondary | Location string | Third line, muted text with pin icon |
| Indicator | Consumed/Active status badge | Right side of third line |
| Action | FAB (+) | Bottom-right, always visible |
| Action | Search bar | Below header, always visible |
| Action | Filter button + badge | Right of search bar |

### States

| State | Appearance | User Feedback |
|-------|------------|---------------|
| Default (list) | Rows with all summary fields | Standard layout |
| Default (grid) | 2-column cards with name, producer, vintage, status | Toggle persists for session |
| Loading | Skeleton rows (shimmer) in list shape | No text — skeleton implies loading |
| Empty collection | Illustration + "No bottles in your cellar yet. Tap + to add your first bottle." | Empty state with FAB callout |
| Filter active | "N bottles found · Clear all" bar; filter badge on button | Count + clear action visible |
| No search results | "No bottles match your search. Try adjusting the filters." | Empty state with clear suggestion |
| Consumed bottle | Greyed card + strikethrough name + "Consumed ✓" badge | Visually distinct at a glance |
| Active bottle | Full-color card + "Active ●" badge | Standard appearance |

### Interactive Elements

| Element | Type | Behavior |
|---------|------|----------|
| FAB (+) | Primary CTA | Opens Add Bottle Form |
| Bottle row / card | Tap target | Navigates to Bottle Detail View |
| Search bar | Text input | Activates search; 250ms debounce on type |
| Filter button (⧩) | Button + badge | Opens Filter Panel bottom sheet |
| Sort dropdown (▾) | Select | Changes sort field (name / vintage / created / location) |
| View toggle (⊞/☰) | Toggle icon | Switches list ↔ grid without re-fetch |
| Clear all | Text link | Clears all active search + filters |

### Sort Options

| Sort Label | Field | Default Order |
|------------|-------|--------------|
| Recently Added (default) | `created_at` | desc |
| Name A–Z | `name` | asc |
| Vintage (oldest first) | `vintage` | asc |
| Vintage (newest first) | `vintage` | desc |
| Producer A–Z | `producer` | asc |
| Location A–Z | `location` | asc (Post-MVP, US-4.3) |

---
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
---

## Screen 02: Add / Edit Bottle Form

**Purpose:** Data entry form for creating a new bottle record or editing an existing one. Must support both the full-metadata entry (Marcus) and the quick-capture in under 60 seconds (Sophie, Daniel).
**User Stories:** US-0.1, US-0.2, US-0.3, US-0.5, US-4.1

### Layout — Mobile (Add Mode)

```
┌─────────────────────────────────────────────┐
│ ✕  Add Bottle                               │  ← Header: close + title
├─────────────────────────────────────────────┤
│                                             │
│  REQUIRED                                   │  ← Section label
│                                             │
│  Bottle Name *                              │
│  ┌─────────────────────────────────────────┐│
│  │ e.g. Château Margaux                    ││  ← Text input
│  └─────────────────────────────────────────┘│
│  ⚠ Bottle name is required.                 │  ← Inline error (when triggered)
│                                             │
│  Vintage Year *                             │
│  ┌─────────────────────────────────────────┐│
│  │ e.g. 2019                               ││  ← Numeric input
│  └─────────────────────────────────────────┘│
│  ⚠ Vintage must be between 1800 and 2026.   │  ← Inline error (when triggered)
│                                             │
│  ─────────────────────────────────────────  │
│  OPTIONAL                                   │  ← Section label
│                                             │
│  Producer              (optional)           │
│  ┌─────────────────────────────────────────┐│
│  │                                         ││
│  └─────────────────────────────────────────┘│
│                                             │
│  Varietal              (optional)           │
│  ┌─────────────────────────────────────────┐│
│  │                                         ││
│  └─────────────────────────────────────────┘│
│                                             │
│  Region                (optional)           │
│  ┌─────────────────────────────────────────┐│
│  │                                         ││
│  └─────────────────────────────────────────┘│
│                                             │
│  Location              (optional)           │
│  ┌─────────────────────────────────────────┐│
│  │ e.g. Rack 3 / Bin 12                   ││
│  └─────────────────────────────────────────┘│
│                                             │
│  Notes                 (optional)           │
│  ┌─────────────────────────────────────────┐│
│  │                                    0/2000││  ← Char counter
│  │                                         ││
│  └─────────────────────────────────────────┘│
│                                             │
│  Purchase Date         (optional)           │
│  ┌─────────────────────────────────────────┐│
│  │ YYYY-MM-DD                              ││  ← Date picker
│  └─────────────────────────────────────────┘│
│                                             │
│  Purchase Price        (optional)           │
│  ┌─────────────────────────────────────────┐│
│  │ 0.00                                    ││
│  └─────────────────────────────────────────┘│
│                                             │
│  Quantity              (optional, default 1)│
│  ┌─────────────────────────────────────────┐│
│  │ 1                                       ││
│  └─────────────────────────────────────────┘│
│                                             │
├─────────────────────────────────────────────┤
│  [Save & Add Another]        [      Save   ]│  ← Sticky footer
└─────────────────────────────────────────────┘
```

### Layout — Edit Mode

Edit mode is identical to Add mode, with these differences:
- Header reads "Edit Bottle" with a back arrow (←) instead of ✕
- All fields pre-populated with current values
- Only one Save button (no "Save & Add Another" — irrelevant for edit)
- Destructive delete action accessible from the overflow (⋮) menu in the header

### Information Hierarchy

| Priority | Content | Placement |
|----------|---------|-----------|
| Primary | Bottle Name field | Top of form, above the fold |
| Primary | Vintage Year field | Second field, above the fold |
| Primary | Save button | Sticky footer, always visible |
| Secondary | Producer, Varietal, Region, Location | First block of optional fields |
| Tertiary | Notes, Purchase Date, Purchase Price, Quantity | Below location |
| Error | Inline validation messages | Directly below the failing field |

### States

| State | Appearance | User Feedback |
|-------|------------|---------------|
| Default (blank) | All inputs empty; Save button enabled | Ready to type |
| Partially filled | Required fields filled; optional fields empty | Normal |
| Validation error | Failing field: red border + error message below; form scrolls to first error | Specific message per field |
| Saving | Save button: "Saving…" + spinner; form disabled | Async feedback |
| Save success | Toast "Added!" / "Updated!" | 2s non-blocking toast |
| Edit pre-filled | All fields show current values | Immediately editable |

### Validation Error Messages

| Field | Condition | Message |
|-------|-----------|---------|
| Name | Blank | "Bottle name is required." |
| Vintage | Blank | "Vintage year is required." |
| Vintage | < 1800 or > current year | "Vintage must be between 1800 and [current year]." |
| Purchase Date | Future date | "Purchase date cannot be in the future." |
| Purchase Price | Negative | "Purchase price must be zero or greater." |
| Location | > 200 chars | "Location must not exceed 200 characters." |
| Notes | > 2000 chars | "Notes must not exceed 2000 characters." |

### Interactive Elements

| Element | Type | Behavior |
|---------|------|----------|
| Bottle Name | Text input (required) | Free text; auto-focus on form open |
| Vintage Year | Numeric input (required) | Numeric keyboard; 4-digit year |
| Producer | Text input (optional) | Free text |
| Varietal | Text input (optional) | Free text; future: autocomplete from collection |
| Region | Text input (optional) | Free text; future: autocomplete from collection |
| Location | Text input (optional) | Free text; placeholder "e.g. Rack 3 / Bin 12" |
| Notes | Textarea (optional) | Multi-line; character counter shown |
| Purchase Date | Date picker (optional) | Max date: today |
| Purchase Price | Decimal input (optional) | Numeric keyboard; currency format |
| Quantity | Integer input (optional) | Numeric keyboard; default 1 |
| Save | Primary button | Submits form; validates first |
| Save & Add Another | Secondary button | Saves + opens new blank form with pre-filled fields |
| ✕ / Back | Icon button | Closes/exits form; "Discard changes?" if dirty |
| Delete (Edit mode) | Overflow menu item | Triggers delete confirmation flow |

### Key UX Decisions

- Auto-focus on Bottle Name field when form opens — first keystroke is immediately captured (JRN-02.1 Trigger)
- "REQUIRED" / "OPTIONAL" section dividers set expectations before the user starts — reduces anxiety about long forms (US-0.2, JRN-02.1 Initiate)
- "Save & Add Another" pre-fills: Producer, Region, Location from previous save — supports JRN-03.2 Rapid Repeat
- Location field intentionally placed above Notes/Purchase fields — it's used more frequently than purchase metadata
- Sticky footer ensures Save is always visible and thumb-reachable without scrolling (JRN-03.2 one-handed)
- Validation fires on Save tap (not blur) — prevents premature error messages while the user is still typing

---
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
---

## Screen 04: Consume + Tasting Form

**Purpose:** Single-screen flow to mark a bottle as consumed and optionally record a tasting entry (date, rating, notes) in one continuous action without navigation.
**User Stories:** US-3.1, US-3.2, US-3.4

### Layout — Consume + Tasting (Mobile)

```
┌─────────────────────────────────────────────┐
│ ✕  Record Consumption                       │  ← Header: close + title
├─────────────────────────────────────────────┤
│                                             │
│  Château Margaux  ·  2015                   │  ← Bottle context (non-editable)
│                                             │
│  ─────────── CONSUMPTION ─────────────────  │
│                                             │
│  Tasting Date                               │
│  ┌─────────────────────────────────────────┐│
│  │ 2026-05-15  (today)                     ││  ← Pre-filled; editable date picker
│  └─────────────────────────────────────────┘│
│  ⚠ Tasting date cannot be in the future.   │  ← Inline error (if triggered)
│                                             │
│  ─────────── TASTING NOTE  (optional) ────  │
│                                             │
│  Rating                                     │
│  ┌─────────────────────────────────────────┐│
│  │   ★   ★   ★   ★   ☆                   ││  ← Star tap control (1–5)
│  └─────────────────────────────────────────┘│
│  ⚠ Rating must be between 1 and 5.          │  ← Inline error (if triggered)
│                                             │
│  Tasting Notes                              │
│  ┌─────────────────────────────────────────┐│
│  │ Describe aromas, palate, finish…        ││  ← Placeholder text (guides beginners)
│  │                                         ││
│  │                                         ││
│  │                                    0/5000││  ← Character counter
│  └─────────────────────────────────────────┘│
│  ⚠ Tasting notes must not exceed 5000 chars.│  ← Inline error (if triggered)
│                                             │
├─────────────────────────────────────────────┤
│  [      Cancel      ]  [  Confirm & Save  ] │  ← Sticky footer
└─────────────────────────────────────────────┘
```

### Layout — Edit Tasting Notes (Mobile)

Identical to above except:
- Header reads "Edit Tasting Notes"
- All fields pre-populated with existing tasting entry values
- "Confirm & Save" becomes "Save Changes"
- No Cancel note about data commitment (edits are not yet committed until Save)

### Information Hierarchy

| Priority | Content | Placement |
|----------|---------|-----------|
| Primary | Tasting Date (required for consume) | Top of form, above the fold |
| Primary | Confirm & Save button | Sticky footer |
| Secondary | Star rating control | Below date; large tap targets |
| Secondary | Tasting notes textarea | Main content area |
| Context | Bottle name + vintage | Non-editable header context |
| Error | Inline validation messages | Directly below the failing element |

### States

| State | Appearance | User Feedback |
|-------|------------|---------------|
| Default | Date pre-filled; rating unset; notes empty | Ready to confirm immediately |
| Rating selected | N stars filled | Visual star fill |
| Notes filled | Char counter updates live | "N / 5000" |
| Validation error | Red border + inline message | Specific per field |
| Saving | Buttons disabled; "Saving…" on confirm button | Spinner + disabled state |
| Save success | Navigates to Bottle Detail (consumed) + toast | "Bottle consumed. Tasting note saved." |

### Star Rating Control

```
  Unset:    ☆  ☆  ☆  ☆  ☆   (tap any star to set; no rating = null)
  1 star:   ★  ☆  ☆  ☆  ☆
  3 stars:  ★  ★  ★  ☆  ☆
  5 stars:  ★  ★  ★  ★  ★
  Deselect: tap active star again → returns to unset
```

Each star: minimum 48px touch target. Stars are evenly distributed across the row with clear visual fill for selected state.

### Interactive Elements

| Element | Type | Behavior |
|---------|------|----------|
| Tasting Date | Date picker | Defaults to today; max = today |
| Star rating | Tap control (1–5) | Tap to select; re-tap same star = deselect |
| Tasting Notes | Textarea | Multi-line; char counter; optional |
| Confirm & Save | Primary button | Validates → marks consumed → creates tasting entry |
| Cancel | Secondary button | Closes form; no data committed; back to Bottle Detail |

### Key UX Decisions

- Single screen, no navigation between "mark consumed" and "enter notes" — addresses JRN-01.2 friction
- All tasting fields are optional — "Confirm & Save" with only the date set is valid (US-3.1)
- Tasting date pre-filled with today — zero-tap for the common case (JRN-01.2 Mark Consumed stage)
- Placeholder text in notes area: "Describe aromas, palate, finish…" — guided prompts for casual users (Sophie, JRN-01.2 opportunity)
- Character counter is always visible — prevents surprise truncation at 5000 chars

---
---

## Screen 05: Journal / Consumed List

**Purpose:** Dedicated view of all consumed bottles ordered by consumption date (most recent first), with tasting data visible on each card. Entry point for reviewing tasting history and editing notes.
**User Stories:** US-3.3, US-1.3

### Layout — Mobile

```
┌─────────────────────────────────────────────┐
│  Journal                           [sort ▾] │  ← Header
├─────────────────────────────────────────────┤
│ 🔍 Search journal…                          │  ← Search (same engine as inventory)
│ 24 bottles consumed                         │  ← Total consumed count
├─────────────────────────────────────────────┤
│                                             │
│ ┌─────────────────────────────────────────┐ │
│ │ Chambolle-Musigny                  2017  │ │  ← Name + vintage
│ │ Rossignol-Trapet  ·  Pinot Noir         │ │  ← Producer · Varietal
│ │ Consumed: 2026-05-10   ★★★★☆            │ │  ← Date + star rating
│ │ "Dried cherry, forest floor, silky…"    │ │  ← First line of tasting notes
│ └─────────────────────────────────────────┘ │
│                                             │
│ ┌─────────────────────────────────────────┐ │
│ │ Pichon Baron                       2010  │ │
│ │ Pichon Baron  ·  Cab. Sauvignon          │ │
│ │ Consumed: 2026-03-22   ★★★★★             │ │
│ │ "Exceptional age — perfect drinking now" │ │
│ └─────────────────────────────────────────┘ │
│                                             │
│ ┌─────────────────────────────────────────┐ │
│ │ Sancerre                           2023  │ │
│ │ Henri Bourgeois  ·  Sauvignon Blanc      │ │
│ │ Consumed: 2026-01-14   (no rating)       │ │  ← No rating placeholder
│ │ (No tasting notes)                      │ │  ← No notes placeholder
│ └─────────────────────────────────────────┘ │
│                                             │
│           · · · (scroll) · · ·             │
│                                             │
├─────────────────────────────────────────────┤
│  [Cellar]       [Journal ●]    [Stats]      │  ← Bottom nav; Journal active
└─────────────────────────────────────────────┘
```

### Information Hierarchy

| Priority | Content | Placement |
|----------|---------|-----------|
| Primary | Bottle name + vintage | Top of card |
| Primary | Consumption date | Third line, left |
| Primary | Star rating | Third line, right of date |
| Secondary | Producer · Varietal | Second line |
| Secondary | First line of tasting notes | Fourth line (truncated) |
| Tertiary | No rating / No notes placeholders | Muted italic text |
| Utility | Total consumed count | Below search bar |

### States

| State | Appearance | User Feedback |
|-------|------------|---------------|
| Default | Consumed bottles sorted by consumed_at desc | Most recent at top |
| Empty journal | Illustration + "You haven't consumed any bottles yet. Start by marking a bottle as consumed." | Gentle prompt |
| No tasting notes | Card shows "(No tasting notes)" in muted text | Not hidden — visible gap |
| No rating | Card shows "(no rating)" | Not hidden |
| Loading | Skeleton cards (shimmer) | Non-blocking |
| Search active | Results filtered by journal search; count updates | "N bottles found" |

### Interactive Elements

| Element | Type | Behavior |
|---------|------|----------|
| Bottle card | Tap target | Navigates to Bottle Detail View (consumed state) |
| Search bar | Text input | Filters journal entries by name, producer, notes |
| Sort ▾ | Select | Consumed date (default), Name, Vintage, Rating |
| Total count | Static label | Shows total consumed bottle count |

### Card Design — Tasting Preview

The journal card intentionally shows:
1. Rating stars inline (no tap needed) — Sophie can see her rating at a glance in a wine shop (JRN-02.2 Read Result)
2. First ~60 chars of tasting notes — enough context to confirm memory without opening the detail
3. Consumed date — chronological context

This eliminates the need to tap into the detail view for the most common lookup scenario (JRN-02.2).

### Key UX Decisions

- Journal is a dedicated bottom nav tab, not a filter on the Inventory List — matches the "tasting history review" mental model (Marcus's JRN-01.3, US-3.3)
- Rating and note preview on the card surface — critical for Sophie's wine-shop recall scenario (JRN-02.2)
- Consumed count shown prominently — reinforces the "journal is growing" feedback (JRN-01.2 milestone)
- Consumed bottles are also visible in the Inventory List with consumed styling — the Journal tab is additive, not the only way to see them

---
---

## Screen 06: Statistics Dashboard (Post-MVP — F5)

**Purpose:** High-level cellar composition summary with counts and breakdowns by varietal, region, vintage, and producer. Helps collectors identify gaps and plan purchases.
**User Stories:** US-5.1, US-5.2, US-5.3
**Priority:** P1 (Post-MVP)

### Layout — Mobile

```
┌─────────────────────────────────────────────┐
│  My Cellar Stats                            │  ← Header
├─────────────────────────────────────────────┤
│                                             │
│  ┌───────────┐ ┌──────────┐ ┌────────────┐ │
│  │   Total   │ │  Active  │ │  Consumed  │ │  ← Summary cards
│  │    142    │ │   118    │ │     24     │ │
│  │  bottles  │ │ bottles  │ │  bottles   │ │
│  └───────────┘ └──────────┘ └────────────┘ │
│                                             │
│  ─────────── BY VARIETAL ─────────────────  │
│                                             │
│  Cabernet Sauvignon  ████████████████  34   │  ← Horizontal bar
│  Chardonnay          ████████████     28   │
│  Pinot Noir          ██████████       22   │
│  Merlot              ██████           14   │
│  Unknown             ██                5   │
│                          + Show more (3)    │
│                                             │
│  ─────────── BY REGION ────────────────────  │
│                                             │
│  Bordeaux            ████████████     22   │
│  Napa Valley         ██████████       19   │
│  Burgundy            ████████         16   │
│  Rhône Valley        ██████           12   │
│  Unknown             ██                4   │
│                          + Show more (5)    │
│                                             │
│  ─── VINTAGE DISTRIBUTION (active) ───────  │
│                                             │
│  2015  ██████████████                  12   │
│  2016  ████████████                    10   │
│  2017  ████████████████████████        20   │
│  2018  ██████████████████              16   │
│  2019  ███████████                      9   │
│                                             │
│  ─────────── BY PRODUCER ──────────────────  │
│                                             │
│  1. Château Margaux             8 bottles   │  ← Count list with mini bar
│  2. Domaine Leflaive            6 bottles   │
│  3. Pichon Baron                5 bottles   │
│  4. Rossignol-Trapet            4 bottles   │
│  5. Unknown                     3 bottles   │
│                          + Show more (12)   │
│                                             │
├─────────────────────────────────────────────┤
│  [Cellar]       [Journal]      [Stats ●]   │  ← Bottom nav; Stats active
└─────────────────────────────────────────────┘
```

### Information Hierarchy

| Priority | Content | Placement |
|----------|---------|-----------|
| Primary | Summary cards (Total / Active / Consumed) | Top of screen |
| Primary | Varietal breakdown chart | First section |
| Primary | Region breakdown chart | Second section |
| Secondary | Vintage distribution | Third section |
| Secondary | Producer breakdown | Fourth section |
| Utility | "Show more" for long lists | End of each section |

### States

| State | Appearance | User Feedback |
|-------|------------|---------------|
| Default (populated) | All sections rendered with data | Charts and counts |
| Empty collection | "Add bottles to your collection to see statistics." centered | Illustration + message |
| Loading | Skeleton bars in chart shape | Shimmer effect |
| Error | "Unable to load statistics. Please try again." with retry button | 503 / 504 error state |
| Partial data | "Unknown" entry shown at bottom of each breakdown | Always visible |

### Interactive Elements

| Element | Type | Behavior |
|---------|------|----------|
| Varietal bar | Tappable bar | Navigates to Inventory List pre-filtered by that varietal |
| Region bar | Tappable bar | Navigates to Inventory List pre-filtered by that region |
| Summary cards | Static | Non-interactive (counts only) |
| Show more | Text link | Expands section to show all entries |
| Vintage bars | Static | Non-interactive (informational) |

### Key UX Decisions

- Summary cards (Total / Active / Consumed) are always at the very top — Marcus's primary question is "how many do I have?" (US-5.1)
- Tappable chart bars link to filtered inventory view — creates the insight → browse → add loop (JRN-01.3 delight opportunity)
- Vintage distribution shows active bottles only by default — "what am I currently holding?" is the primary question (US-5.3)
- Statistics tab is in the primary navigation (bottom nav) — accessible in ≤2 taps (JRN-01.3 Navigate stage risk mitigation)
- "Unknown" always shown in breakdowns — doesn't suppress incomplete data

---
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
---

## Responsive Considerations

### Breakpoints

| Breakpoint | Label | Range | Primary Use Context |
|------------|-------|-------|-------------------|
| Mobile | Small | < 768px | Cellar-side (Daniel), party quick-add (Sophie) |
| Tablet | Medium | 768px – 1024px | Desk entry after auction (Marcus on tablet) |
| Desktop | Large | > 1024px | Optional; not primary use case for v1 |

---

### Mobile (< 768px) — Primary Design Target

**Inventory List:**
- Single-column list view
- Grid view: 2 columns
- FAB pinned bottom-right, always visible
- Bottom navigation bar with 3 tabs + FAB
- Search bar full-width below header
- Filter panel as bottom sheet (slides up from bottom, max 90vh, scrollable)

**Bottle Cards:**
- Full-width cards with all summary fields visible
- Location string on third line — no truncation beyond 2 lines (ellipsis)
- Status badge always visible (right-aligned)
- Minimum tap target: 44px height for any interactive element

**Add / Edit Form:**
- Full-screen form (not modal)
- Sticky footer with Save + Save & Add Another
- Vintage field uses `inputmode="numeric"` for numeric keyboard
- Date fields use native date picker
- Auto-scroll to first error field

**Consume + Tasting:**
- Full-screen form
- Star rating: evenly spaced 48px tap targets
- Textarea: min 120px height; grows with content

**Statistics:**
- Single-column layout
- Horizontal bar charts full-width
- Summary cards: 3 across in a row (compact card size)

---

### Tablet (768px – 1024px) — Secondary Design Target

**Inventory List:**
- Two-column layout: sidebar (filters) + main content area
- Filter panel is persistent left sidebar (not bottom sheet)
- Grid view: 3 columns
- FAB remains bottom-right; can also have a header "Add Bottle" button

**Bottle Cards:**
- Wider cards; location on same line as status badge (more horizontal space)

**Add / Edit Form:**
- Center-column form (max 600px wide), not full-bleed
- Left/right padding to prevent overly wide input fields
- "Save & Add Another" has more visual breathing room

**Bottle Detail:**
- Two-column: left = bottle metadata, right = tasting entry / actions
- Location prominently displayed top-left

**Statistics:**
- Two-column grid for chart sections (Varietal | Region side-by-side)
- Summary cards: larger with more detail

---

### Desktop (> 1024px) — Tertiary (Best Effort in v1)

**Layout approach:**
- Max content width: 1200px, centered
- Left sidebar navigation (persistent) replaces bottom nav
- Inventory list + filter panel in two-column split layout
- Add / Edit form in a modal dialog (centered overlay) rather than full page

**Key adaptations:**
- Hover states on all interactive elements (buttons, cards, chips)
- Keyboard navigation for form fields (Tab order follows visual order)
- Right-click / context menu for bottle cards (Edit, Delete shortcuts)
- Filter panel always visible as left sidebar (no bottom sheet)

---

### Touch Target Compliance (All Breakpoints)

| Element | Minimum Size |
|---------|-------------|
| FAB | 56px × 56px |
| Filter chips | 44px height minimum |
| Star rating stars | 48px × 48px each |
| List row / card | 64px height minimum |
| Navigation tab | 48px height minimum |
| Action buttons | 44px height minimum |
| Text inputs | 48px height minimum |

---

### One-Handed Mobile Usability

Priority elements in the **bottom 60% of the screen** (thumb-reachable zone on a standard phone):

- FAB (Add Bottle) — bottom-right corner
- Bottom navigation tabs
- Filter panel chips (bottom sheet)
- "Save" / "Save & Add Another" buttons (sticky footer)
- "Confirm & Save" / "Cancel" (sticky footer on consume form)

Elements that require two-handed interaction are acceptable in the top area:
- Header navigation (Back, title)
- Overflow menu (⋮)

---
---

## Accessibility Notes

**Standard:** WCAG 2.1 AA (per PRD Non-Functional Requirements)

---

### Color Contrast

| Element | Minimum Ratio | Application |
|---------|--------------|-------------|
| Body text on background | 4.5:1 | All list text, detail text, form labels |
| Large text (≥18px bold / ≥24px regular) | 3:1 | Bottle name heading, section headers |
| UI component borders (inputs, buttons) | 3:1 | Form field borders, button outlines |
| Consumed state (greyed text) | 4.5:1 | Greyed/dimmed consumed bottle names must still pass — use opacity carefully |
| Status badges | 4.5:1 | "Active ●" and "Consumed ✓" text against badge background |
| Error messages (red text) | 4.5:1 | Inline error messages must pass against white/light background |
| Star rating — filled stars | 3:1 | Gold/filled stars against card background |

**Note on consumed styling:** Name strikethrough + opacity reduction is visual only. The text must still meet minimum contrast ratios. Use a specific muted color value (e.g., `#767676` on white) rather than a percentage opacity on the original color.

---

### Keyboard Navigation

| Context | Behavior |
|---------|----------|
| Inventory List | Tab through bottle cards; Enter to open detail |
| Filter Panel | Tab through chips; Space to toggle chip; Enter to Apply; Esc to close |
| Add / Edit Form | Tab order follows visual field order (Name → Vintage → Producer → Varietal → Region → Location → Notes → Purchase Date → Price → Quantity → Save) |
| Star Rating | Arrow keys to change rating (← decrease, → increase); Tab to move away |
| Confirmation Dialog | Tab trapped within dialog; Enter = confirm; Esc = cancel |
| Bottom Sheet | Esc closes the sheet; Tab cycles through interactive elements within |
| FAB | Always reachable via Tab; Enter activates |

---

### Screen Reader Support

| Element | ARIA / Label |
|---------|-------------|
| FAB (+) | `aria-label="Add new bottle"` |
| Filter button | `aria-label="Filter (N active)"` — count announced dynamically |
| View toggle | `aria-label="Switch to grid view"` / `aria-label="Switch to list view"` |
| Sort dropdown | `aria-label="Sort inventory"` |
| Consumed badge | `aria-label="Consumed"` (not just a visual checkmark) |
| Active badge | `aria-label="Active"` |
| Star rating | `role="radiogroup"` with each star as `role="radio"` + `aria-label="N stars"` |
| Inline error | `aria-describedby` links input to its error message element |
| Required fields | `aria-required="true"` on Name and Vintage inputs |
| Loading state | `aria-busy="true"` on the list container during load |
| Result count | `aria-live="polite"` region — announced when filter count changes |
| Bottle name in dialog | Included in dialog description: `aria-describedby` |
| Toast notification | `role="status"` with `aria-live="polite"` |

---

### Focus Management

| Scenario | Focus Behavior |
|----------|---------------|
| Open Add Bottle Form | Focus moves to Bottle Name input |
| Open Filter Panel | Focus moves to first interactive element in panel |
| Close Filter Panel (Apply) | Focus returns to Filter button in Inventory List |
| Close Confirmation Dialog (Cancel) | Focus returns to the button that opened it |
| Confirm Delete | Focus returns to Inventory List (bottle is gone) |
| Form validation error | Focus moves to the first failing field |
| Save success (stay on page) | Focus stays on Save button |
| Navigate to new screen | Focus moves to the screen's heading (`<h1>`) |

---

### Motion & Animation

| Scenario | Behavior |
|----------|----------|
| Filter panel slides | Respect `prefers-reduced-motion` — disable slide animation; use instant show/hide |
| Toast notifications | Fade-in/out; reduce to instant on/off with `prefers-reduced-motion` |
| FAB pulse animation | Disabled with `prefers-reduced-motion` |
| Skeleton shimmer | Static grey block with `prefers-reduced-motion` |
| Bottle card hover effects | Minimal — no flicker or rapid color changes |

---

### Form Accessibility

| Requirement | Implementation |
|-------------|---------------|
| All inputs have visible labels | Labels above inputs (not placeholder-only) |
| Placeholder text is supplementary | Placeholders show examples only; labels carry semantic meaning |
| Error association | `aria-describedby` links each input to its error `<span>` |
| Required field indication | Both visual asterisk (*) and `aria-required="true"` |
| Character counter | Announced via `aria-live="polite"` on blur or when limit approached |
| Date picker | Native `<input type="date">` or accessible custom picker with ARIA |
| Numeric inputs | `inputmode="numeric"` for mobile keyboards; accepts keyboard number entry |

---

### Color-Blind Considerations

| Scenario | Solution |
|----------|----------|
| Active vs. Consumed status | Not distinguished by color alone — badge includes text label ("Active" / "Consumed") and icon (●/✓) |
| Star rating | Stars use shape (filled/outline) in addition to color |
| Error states | Error border + icon (⚠) + text message — not red border alone |
| Filter chip selected state | Filled chip + bold text + checkmark icon — not color alone |
| Chart bars (Statistics) | If colored bars are used, include text count values alongside |

---
