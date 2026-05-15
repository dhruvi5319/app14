
---

## F03: Consumption Tracking & Tasting Notes

**PRD Reference:** F3 | **Priority:** P0 (MVP Critical)

**Description:** This feature transforms the app from a static inventory into a personal tasting journal. When a user opens and drinks a bottle, they can mark it as consumed and record a tasting entry (date, free-text notes, and a 1–5 star rating). Consumed bottles are removed from the active inventory count but remain accessible in the collection history. Tasting entries can be edited after initial creation. Users can view consumed bottles separately from active inventory.

---

### Terminology

- **Consume action:** The act of marking a bottle as drunk/opened, transitioning it from active to consumed status.
- **Tasting entry:** The structured record attached to a consumed bottle capturing the tasting date, notes, and rating.
- **Rating:** An integer from 1 to 5 representing the user's enjoyment (1 = poor, 5 = excellent).
- **Tasting date:** The calendar date on which the bottle was opened and consumed.
- **Tasting notes:** Free-text description of aromas, flavors, finish, and impressions.

---

### Sub-features

- **F03.1** — Mark a bottle as consumed
- **F03.2** — Log a tasting entry (date, notes, rating) at consumption time
- **F03.3** — View consumed bottles separately from active inventory
- **F03.4** — Edit tasting notes and rating after initial entry
- **F03.5** — Un-consume (restore) a bottle to active status

---

### Process

#### F03.1 — Mark Bottle as Consumed
1. User opens a bottle detail view for an active bottle.
2. User triggers the "Mark as Consumed" action.
3. System presents the tasting entry form (date, notes, rating) — all fields optional at this step.
4. User optionally fills in tasting details.
5. User confirms the consumption action.
6. System sets `is_consumed = true` and `consumed_at = now()` on the bottle record.
7. System creates a `tasting_entries` record linked to the bottle (even if all tasting fields are blank).
8. System removes the bottle from active inventory count.
9. System navigates to the bottle detail view (now showing consumed state).

#### F03.2 — Log Tasting Entry
- The tasting entry form is presented as part of the consume flow (F03.1) and can also be accessed directly from a consumed bottle's detail view.
- Fields: `tasting_date` (defaults to today), `notes` (free text), `rating` (1–5 integer).
- All tasting fields are optional; a minimal consume action creates an entry with only the timestamp.

#### F03.3 — View Consumed Bottles
1. User navigates to the "Consumed" / "Journal" tab or applies the `status=consumed` filter.
2. System returns all bottles where `is_consumed = true`, ordered by `consumed_at` descending.
3. Each consumed bottle row/card shows: name, vintage, producer, tasting date, and rating (if recorded).
4. User can tap a consumed bottle to view its full detail including tasting notes.

#### F03.4 — Edit Tasting Entry
1. User opens the detail view of a consumed bottle.
2. User triggers "Edit Tasting Notes" action.
3. System presents the tasting entry form pre-populated with existing values.
4. User modifies `notes` and/or `rating`.
5. User submits.
6. System validates and persists the updated tasting entry.
7. System reflects updated values in the detail view immediately.

#### F03.5 — Un-consume (Restore)
1. User opens the detail view of a consumed bottle.
2. User triggers "Mark as Active" / "Restore to Cellar" action.
3. System presents a confirmation: "Restore [bottle name] to your active cellar?"
4. User confirms.
5. System sets `is_consumed = false` and clears `consumed_at` on the bottle record.
6. System deletes the associated tasting entry.
7. System returns the bottle to the active inventory count.

---

### Inputs

**Consume Action:**

| Field | Type | Required | Constraints |
|-------|------|----------|-------------|
| `bottle_id` | UUID | Yes | Must be an existing active bottle |
| `tasting_date` | date | No | ISO 8601 (`YYYY-MM-DD`); must not be in the future; defaults to today |
| `notes` | string | No | 0–5000 characters |
| `rating` | integer | No | 1–5 inclusive |

**Edit Tasting Entry:**

| Field | Type | Required | Constraints |
|-------|------|----------|-------------|
| `tasting_entry_id` | UUID | Yes | Must be an existing tasting entry |
| `tasting_date` | date | No | As above |
| `notes` | string | No | 0–5000 characters |
| `rating` | integer | No | 1–5 inclusive |

---

### Outputs

**On Successful Consume:**
- HTTP 200 OK
- Response body: updated bottle record with `is_consumed: true` and embedded `tasting_entry` object.

**On Successful Tasting Entry Edit:**
- HTTP 200 OK
- Response body: updated `tasting_entry` object.

**On Successful Un-consume:**
- HTTP 200 OK
- Response body: updated bottle record with `is_consumed: false`.

**On Validation Failure:**
- HTTP 422 Unprocessable Entity
- Error object with field-level messages.

---

### Validation

- `bottle_id` must refer to an existing bottle.
- To consume: bottle must currently be active (`is_consumed = false`). Attempting to consume an already-consumed bottle returns 409 Conflict.
- `tasting_date` if provided, must be a valid date and must not be in the future.
- `notes` if provided, must not exceed 5,000 characters.
- `rating` if provided, must be an integer between 1 and 5 inclusive. Decimal values are rejected.
- `tasting_entry_id` on edit must refer to an existing tasting entry belonging to the specified bottle.
- To un-consume: bottle must currently be consumed (`is_consumed = true`). Attempting to restore an already-active bottle returns 409 Conflict.

---

### Error States

| Scenario | HTTP Status | Error Code | Message |
|----------|-------------|------------|---------|
| Bottle already consumed | 409 | CONFLICT | "This bottle is already marked as consumed." |
| Bottle not found | 404 | NOT_FOUND | "Bottle not found." |
| `rating` out of range | 422 | VALIDATION_ERROR | "Rating must be between 1 and 5." |
| `tasting_date` in the future | 422 | VALIDATION_ERROR | "Tasting date cannot be in the future." |
| `notes` exceed 5000 characters | 422 | VALIDATION_ERROR | "Tasting notes must not exceed 5000 characters." |
| Tasting entry not found (on edit) | 404 | NOT_FOUND | "Tasting entry not found." |
| Bottle already active (on restore) | 409 | CONFLICT | "This bottle is already in your active cellar." |
| Server error | 500 | INTERNAL_ERROR | "An unexpected error occurred. Please try again." |

---

### API Surface (this feature)

Full schema in `Y1-api.md` §Tasting.

| Method | Path | Action |
|--------|------|--------|
| POST | `/api/v1/bottles/:id/consume` | Mark bottle as consumed + create tasting entry |
| GET | `/api/v1/bottles/:id/tasting` | Retrieve tasting entry for a bottle |
| PATCH | `/api/v1/bottles/:id/tasting` | Edit tasting entry (notes, rating, date) |
| DELETE | `/api/v1/bottles/:id/consume` | Un-consume (restore) a bottle to active |

---

### Schema Surface (this feature)

Uses tables `bottles` (updated `is_consumed`, `consumed_at`) and `tasting_entries`. Full DDL in `Y0-schema.md` §tasting_entries.

Key columns: `id`, `bottle_id`, `tasting_date`, `notes`, `rating`, `created_at`, `updated_at`.
