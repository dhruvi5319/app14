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
