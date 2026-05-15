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
