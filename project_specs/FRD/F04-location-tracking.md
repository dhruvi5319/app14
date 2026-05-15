
---

## F04: Bottle Location Tracking

**PRD Reference:** F4 | **Priority:** P1 (High — Post-MVP)

**Description:** This feature allows users to record where a bottle physically lives in their cellar — rack number, bin, shelf, or any custom label string. When a collector has dozens or hundreds of bottles, physical retrieval is the primary friction point. By recording and displaying location prominently, this feature makes finding the right bottle fast without searching the entire cellar. Location is a single free-text field on the bottle record; it is displayed prominently in the detail view and can be used for sorting and filtering.

---

### Terminology

- **Location string:** A user-defined free-text label describing the bottle's physical storage position (e.g., "Rack 3 / Bin 12", "Fridge Bottom Shelf", "Cellar A Row 2").
- **Location sort:** Sorting the inventory list alphabetically by the location field, grouping bottles in nearby physical positions together.

---

### Sub-features

- **F04.1** — Assign a location string when adding or editing a bottle (part of F00 form)
- **F04.2** — Display location prominently in bottle detail view
- **F04.3** — Sort inventory by location
- **F04.4** — Filter/browse by location prefix or value
- **F04.5** — Update location when a bottle is moved

---

### Process

#### F04.1 — Assign Location
1. Location is captured as part of the bottle entry form (F00).
2. The `location` field is optional; if omitted, the bottle has no recorded location.
3. User enters a free-text string describing the physical position.
4. System stores the location string with the bottle record.

#### F04.2 — Display Location in Detail View
1. When user opens a bottle's detail view, the system displays the `location` field prominently (e.g., near the top of the record, adjacent to the bottle name).
2. If `location` is null or empty, the field is either hidden or shows a placeholder: "No location recorded."
3. Location is shown for both active and consumed bottles.

#### F04.3 — Sort by Location
1. User selects "Sort by location" in the inventory browsing view (F01 sort options).
2. System returns bottles sorted alphabetically by `location` (ascending), with null/empty locations sorted last.
3. This groups bottles in the same physical area together in the list.

#### F04.4 — Filter by Location
1. User enters a location value in the filter panel.
2. System performs a case-insensitive substring match on the `location` field.
3. Only bottles whose `location` contains the search string are returned.

#### F04.5 — Update Location
1. User opens a bottle's detail view.
2. User triggers "Edit" (F00.2).
3. User modifies the `location` field.
4. System persists the updated location.
5. The new location is immediately reflected in the detail view and inventory list.

---

### Inputs

| Field | Type | Required | Constraints |
|-------|------|----------|-------------|
| `location` | string | No | 0–200 characters; any UTF-8 text |

Location filtering query parameter (on `GET /api/v1/bottles`):

| Parameter | Type | Description |
|-----------|------|-------------|
| `location` | string | Substring match against the location field |

---

### Outputs

**In Bottle Record:**
- `location` field included in all bottle detail and list responses.
- Null if no location has been set.

**In Inventory List:**
- `location` included in bottle summary objects.
- Supports sort by `location` (via `sort=location` query parameter).

---

### Validation

- `location` is optional; if provided, must not exceed 200 characters.
- `location` may contain any UTF-8 characters including slashes, dashes, and spaces.
- Empty string for `location` is treated as null (no location recorded).
- Location filter parameter: if provided, must be 1–200 characters; pure whitespace is ignored.

---

### Error States

| Scenario | HTTP Status | Error Code | Message |
|----------|-------------|------------|---------|
| `location` exceeds 200 characters | 422 | VALIDATION_ERROR | "Location must not exceed 200 characters." |
| Bottle not found (on update) | 404 | NOT_FOUND | "Bottle not found." |

---

### API Surface (this feature)

Location is a field on the bottle record; no dedicated endpoints beyond F00 PATCH.

| Method | Path | Notes |
|--------|------|-------|
| PATCH | `/api/v1/bottles/:id` | Update `location` field (F00 edit) |
| GET | `/api/v1/bottles` | Sort by `sort=location`; filter by `location=<string>` |

Full schema in `Y1-api.md` §Bottles.

---

### Schema Surface (this feature)

Uses column `location` on table `bottles`. Full DDL in `Y0-schema.md` §bottles.

`location VARCHAR(200) NULL` — indexed for sort performance; substring filtering performed in application or via `LIKE '%value%'` query.
