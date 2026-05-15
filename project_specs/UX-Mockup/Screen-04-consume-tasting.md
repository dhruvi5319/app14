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
