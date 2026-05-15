
---

## F00: Bottle Entry & Management

**PRD Reference:** F0 | **Priority:** P0 (MVP Critical)

**Description:** This feature is the foundational data-entry mechanism of the app. It allows users to create a new bottle record with all relevant wine metadata, edit any field on an existing record at any time, and permanently delete a bottle. Without this feature, no other feature has data to operate on. All other features (browsing, search, tasting, location, statistics) depend on records created here.

---

### Terminology

- **Bottle record:** The database entity storing all metadata for a single physical wine bottle.
- **Required field:** A field that must be non-empty for a record to be saved.
- **Draft state:** A bottle record that has been started but not yet saved (client-side only; not persisted).

---

### Sub-features

- **F00.1** — Create a new bottle record
- **F00.2** — Edit an existing bottle record
- **F00.3** — Delete a bottle record permanently
- **F00.4** — Input validation on create and edit

---

### Process

#### F00.1 — Create Bottle
1. User navigates to "Add Bottle" action (button/FAB/menu item).
2. System presents the bottle entry form with all fields.
3. User fills in at minimum: `name` and `vintage`.
4. User optionally fills in: `producer`, `varietal`, `region`, `notes`, `purchase_date`, `purchase_price`, `quantity`.
5. User submits the form.
6. System validates all inputs (see Validation).
7. If validation fails, system highlights the failing field(s) with inline error messages; form remains open.
8. If validation passes, system persists the record and returns the new bottle's `id`.
9. System updates the inventory list to include the new bottle.
10. System navigates the user to the bottle detail view for the newly created bottle.

#### F00.2 — Edit Bottle
1. User opens a bottle's detail view.
2. User triggers "Edit" action.
3. System presents the edit form pre-populated with current values.
4. User modifies one or more fields.
5. User submits the form.
6. System validates inputs (same rules as create).
7. If validation fails, inline errors shown; form remains open.
8. If validation passes, system persists the changes and returns the updated record.
9. System reflects updated values in the detail view and inventory list immediately.

#### F00.3 — Delete Bottle
1. User opens a bottle's detail view.
2. User triggers "Delete" action.
3. System presents a confirmation dialog: "Delete [bottle name]? This cannot be undone."
4. User confirms.
5. System permanently deletes the bottle record and any associated tasting entry.
6. System navigates the user back to the inventory list.
7. The deleted bottle no longer appears in any list, search, or statistics view.

#### F00.4 — Validation (shared for create & edit)
- See Validation section below.

---

### Inputs

**Bottle Form Fields:**

| Field | Type | Required | Constraints |
|-------|------|----------|-------------|
| `name` | string | Yes | 1–200 characters |
| `vintage` | integer | Yes | 1800–current year (inclusive) |
| `producer` | string | No | 0–200 characters |
| `varietal` | string | No | 0–100 characters |
| `region` | string | No | 0–150 characters |
| `notes` | string | No | 0–2000 characters (free text) |
| `purchase_date` | date | No | ISO 8601 date (`YYYY-MM-DD`); must not be in the future |
| `purchase_price` | decimal | No | ≥ 0.00; up to 2 decimal places |
| `quantity` | integer | No | 1–9999; defaults to 1 if omitted |
| `location` | string | No | 0–200 characters; see F04 |

**Delete Confirmation:**
- `bottle_id` (UUID, required): Supplied by the system from the current record context.

---

### Outputs

**On Successful Create:**
- HTTP 201 Created
- Response body: full bottle record JSON (see Y1-api.md §Bottles)
- UI: navigates to bottle detail view

**On Successful Edit:**
- HTTP 200 OK
- Response body: updated bottle record JSON
- UI: detail view reflects new values

**On Successful Delete:**
- HTTP 204 No Content
- UI: navigates to inventory list; bottle absent

**On Validation Failure:**
- HTTP 422 Unprocessable Entity
- Response body: error object with `field` and `message` per failing field (see Y2-errors.md)
- UI: inline field-level error messages; form stays open

---

### Validation

- `name` must not be blank or whitespace-only.
- `name` must not exceed 200 characters.
- `vintage` must be a 4-digit integer between 1800 and the current calendar year (inclusive). Vintages in the future are rejected.
- `producer`, `varietal`, `region` may be empty strings or omitted entirely; if provided, each must not exceed its character limit.
- `notes` may be empty; if provided, must not exceed 2,000 characters.
- `purchase_date` if provided, must be a valid calendar date and must not be in the future.
- `purchase_price` if provided, must be a non-negative number with at most two decimal places.
- `quantity` if provided, must be a positive integer between 1 and 9,999. Defaults to 1.
- `location` if provided, must not exceed 200 characters.
- On edit, all fields present in the request are re-validated; fields omitted from the request are left unchanged (PATCH semantics).
- Delete requires an existing `bottle_id`; attempting to delete a non-existent ID returns 404.

---

### Error States

| Scenario | HTTP Status | Error Code | Message |
|----------|-------------|------------|---------|
| `name` is blank | 422 | VALIDATION_ERROR | "Bottle name is required." |
| `vintage` out of range | 422 | VALIDATION_ERROR | "Vintage must be between 1800 and [current year]." |
| `vintage` is in the future | 422 | VALIDATION_ERROR | "Vintage cannot be in the future." |
| `purchase_date` is in the future | 422 | VALIDATION_ERROR | "Purchase date cannot be in the future." |
| `purchase_price` is negative | 422 | VALIDATION_ERROR | "Purchase price must be zero or greater." |
| Field exceeds max length | 422 | VALIDATION_ERROR | "[Field] must not exceed [N] characters." |
| Bottle ID not found (edit/delete) | 404 | NOT_FOUND | "Bottle not found." |
| Delete without confirmation | 400 | BAD_REQUEST | "Delete must be confirmed." |
| Server/storage error | 500 | INTERNAL_ERROR | "An unexpected error occurred. Please try again." |

---

### API Surface (this feature)

Full request/response schemas are in `Y1-api.md` §Bottles.

| Method | Path | Action |
|--------|------|--------|
| POST | `/api/v1/bottles` | Create a new bottle |
| GET | `/api/v1/bottles/:id` | Retrieve a single bottle |
| PATCH | `/api/v1/bottles/:id` | Edit (partial update) a bottle |
| DELETE | `/api/v1/bottles/:id` | Delete a bottle permanently |

---

### Schema Surface (this feature)

Uses table `bottles`. Full DDL in `Y0-schema.md` §bottles.

Key columns: `id`, `name`, `vintage`, `producer`, `varietal`, `region`, `notes`, `purchase_date`, `purchase_price`, `quantity`, `location`, `is_consumed`, `created_at`, `updated_at`.
