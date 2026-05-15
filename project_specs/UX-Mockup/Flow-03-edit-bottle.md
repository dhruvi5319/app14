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
