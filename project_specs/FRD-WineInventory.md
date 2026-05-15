# Functional Requirements Document — Wine Inventory App

**Project:** WineInventory  
**Acronym:** WineInventory  
**Version:** 1.0  
**Date:** 2026-05-15  
**Status:** Draft  
**Source PRD:** PRD-WineInventory.md v1.0  

---

## Scope

This document specifies the detailed functional behavior of all features in the Wine Inventory App v1. It is derived from PRD-WineInventory.md and covers the six PRD features (F0–F5) with exact input/output contracts, validation rules, error states, REST API endpoints, and database schema. It is intended as the authoritative implementation reference for developers, QA engineers, and technical reviewers.

Single-user personal use only. Multi-tenancy, social sharing, barcode scanning, and marketplace integrations are explicitly out of scope for v1 (see PRD §9).

---

## Table of Contents

| Section | File | Description |
|---------|------|-------------|
| F00 | `F00-bottle-entry.md` | Bottle Entry & Management |
| F01 | `F01-inventory-browsing.md` | Inventory Browsing |
| F02 | `F02-search-filter.md` | Search & Filter |
| F03 | `F03-consumption-tasting.md` | Consumption Tracking & Tasting Notes |
| F04 | `F04-location-tracking.md` | Bottle Location Tracking |
| F05 | `F05-statistics-dashboard.md` | Inventory Statistics & Dashboard |
| Y0 | `Y0-schema.md` | Database Schema (full DDL) |
| Y1 | `Y1-api.md` | REST API Endpoints (full spec) |
| Y2 | `Y2-errors.md` | Cross-Feature Error Catalog |
| Y3 | `Y3-integrations.md` | External Integration Points |

---

## Conventions

- **Feature IDs:** F00–F05 map to PRD features F0–F5. Zero-padded for consistent sort order.
- **Field naming:** `snake_case` for database columns and JSON fields; `camelCase` for UI variable references.
- **Required vs. Optional:** Fields marked `(required)` must be non-null/non-empty. Fields marked `(optional)` may be omitted or null.
- **ID format:** All primary keys are UUIDs (v4) unless otherwise specified.
- **Timestamps:** All timestamps stored as ISO 8601 UTC strings (`YYYY-MM-DDTHH:MM:SSZ`).
- **API prefix:** All endpoints are prefixed with `/api/v1`.
- **HTTP status codes:** Follow RFC 7231 conventions. See Y2 for error catalog.
- **Priority:**
  - P0 = MVP Critical (must ship in v1)
  - P1 = High (important, may ship post-MVP)

---

## Shared Terminology

| Term | Definition |
|------|-----------|
| **Bottle** | A single wine bottle record in the user's inventory. Represents one physical bottle. |
| **Active bottle** | A bottle that has not been marked as consumed; still physically in the cellar. |
| **Consumed bottle** | A bottle that has been marked as consumed (opened and drunk). |
| **Tasting entry** | A record attached to a consumed bottle capturing the date, notes, and rating for that drinking occasion. |
| **Varietal** | The grape variety used to make the wine (e.g., Cabernet Sauvignon, Chardonnay). |
| **Region** | The geographic wine-producing area (e.g., Bordeaux, Napa Valley, Barossa Valley). |
| **Vintage** | The year the grapes were harvested (a 4-digit calendar year). |
| **Producer** | The winery or estate that made the wine. |
| **Location** | A free-text label describing the bottle's physical storage position (e.g., "Rack 3 / Bin 12"). |
| **Rating** | A numeric score from 1 to 5 (integer) representing the user's enjoyment of the wine. |
| **Collection** | The complete set of bottle records belonging to the user, both active and consumed. |
| **Cellar** | Informal term for the user's active (non-consumed) collection. |
| **Dashboard** | The statistics view (F05) summarizing cellar composition. |

---

## Non-Functional Requirements Summary

| Category | Requirement |
|----------|-------------|
| Performance | Search/filter results within 200ms for ≤1,000 bottles |
| Usability | Core actions (add, search, consume) completable in ≤3 taps/clicks |
| Data integrity | All writes confirmed before UI updates; no data loss on crash |
| Offline | Core browse, search, add work without network |
| Accessibility | WCAG AA contrast; accessible labels on interactive elements |
| Scalability | Performant with 500+ bottle collections |
| Platform | Single-user; no multi-tenancy in v1 |

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

---

## F01: Inventory Browsing

**PRD Reference:** F1 | **Priority:** P0 (MVP Critical)

**Description:** This is the primary everyday interface of the app — the view users see when they open it. It displays the user's complete wine collection in either a scrollable list view or a visual card/grid view. Users can instantly see key details for each bottle and identify at a glance which bottles have been consumed. The browsing view is the entry point from which users navigate to bottle details, search (F02), and the statistics dashboard (F05).

---

### Terminology

- **List view:** A vertically scrollable single-column layout where each row shows a bottle summary.
- **Grid view:** A multi-column card layout for visual scanning; card shows bottle name, producer, vintage.
- **Consumed indicator:** A visual badge or style differentiation (e.g., strikethrough, greyed-out, "consumed" tag) applied to consumed bottles.
- **Active inventory:** The subset of the collection where `is_consumed = false`.

---

### Sub-features

- **F01.1** — Display full bottle collection (list view)
- **F01.2** — Toggle to grid/card view
- **F01.3** — At-a-glance bottle summary (name, producer, vintage, varietal)
- **F01.4** — Consumed indicator
- **F01.5** — Navigate to bottle detail view
- **F01.6** — Paginate or virtual-scroll for large collections

---

### Process

#### F01.1 — Load Inventory List
1. User opens the app or navigates to the inventory screen.
2. System fetches all bottle records ordered by default sort (most recently added first — descending `created_at`).
3. System renders each bottle as a list row with summary fields.
4. If the collection is empty, system shows an empty-state message: "No bottles in your cellar yet. Tap + to add your first bottle."
5. If the collection exceeds the page size (default 50), system implements virtual scrolling or pagination; additional records load automatically as the user scrolls.

#### F01.2 — Toggle View Mode
1. User taps/clicks the view mode toggle (list icon / grid icon).
2. System re-renders the collection in the selected layout without re-fetching data.
3. The selected view mode persists for the session (and optionally in local preferences).

#### F01.3 — Bottle Summary
Each bottle row/card displays:
- Bottle `name` (primary label)
- `producer` (secondary label, shown if present)
- `vintage` (year)
- `varietal` (shown if present)
- Consumed indicator (see F01.4)

#### F01.4 — Consumed Indicator
- Consumed bottles (`is_consumed = true`) are visually differentiated from active bottles.
- Default display: all bottles (active + consumed) shown together, with consumed bottles styled distinctly.
- An optional toggle allows the user to show only active bottles or only consumed bottles (filter integration with F02).

#### F01.5 — Navigate to Detail
1. User taps/clicks any bottle row or card.
2. System navigates to the bottle detail view, loading the full record for that `bottle_id`.

#### F01.6 — Large Collection Handling
- Virtual scrolling or pagination ensures the list renders within 200ms for collections ≤1,000 bottles.
- Page size: 50 bottles per load.
- On scroll-to-bottom (or "Load More" tap), system appends the next 50 records.

---

### Inputs

| Input | Type | Description |
|-------|------|-------------|
| `page` | integer | Page number for pagination (default: 1) |
| `limit` | integer | Records per page (default: 50, max: 100) |
| `sort` | string | Sort field: `created_at` (default), `name`, `vintage`, `producer` |
| `order` | string | Sort direction: `desc` (default), `asc` |
| `status` | string | Filter: `all` (default), `active`, `consumed` |

---

### Outputs

**On Success:**
- HTTP 200 OK
- Response body:
  ```json
  {
    "data": [ /* array of bottle summary objects */ ],
    "meta": {
      "total": 142,
      "page": 1,
      "limit": 50,
      "total_pages": 3
    }
  }
  ```
- Each bottle summary object includes: `id`, `name`, `producer`, `vintage`, `varietal`, `is_consumed`, `location`, `created_at`.

**Empty Collection:**
- HTTP 200 OK
- `data: []`, `meta.total: 0`
- UI shows empty-state message.

---

### Validation

- `page` must be a positive integer ≥ 1; defaults to 1 if omitted.
- `limit` must be between 1 and 100 inclusive; defaults to 50 if omitted.
- `sort` must be one of: `created_at`, `name`, `vintage`, `producer`; defaults to `created_at` if omitted or invalid.
- `order` must be `asc` or `desc`; defaults to `desc` if omitted or invalid.
- `status` must be `all`, `active`, or `consumed`; defaults to `all` if omitted or invalid.

---

### Error States

| Scenario | HTTP Status | Error Code | Message |
|----------|-------------|------------|---------|
| `limit` exceeds 100 | 400 | BAD_REQUEST | "Limit must not exceed 100." |
| `page` is zero or negative | 400 | BAD_REQUEST | "Page must be a positive integer." |
| Storage/database unavailable | 503 | SERVICE_UNAVAILABLE | "Unable to load inventory. Please try again." |

---

### API Surface (this feature)

Full schema in `Y1-api.md` §Bottles.

| Method | Path | Action |
|--------|------|--------|
| GET | `/api/v1/bottles` | List all bottles (with pagination & sort) |

---

### Schema Surface (this feature)

Reads from table `bottles`. Full DDL in `Y0-schema.md` §bottles.

Key columns read: `id`, `name`, `producer`, `vintage`, `varietal`, `is_consumed`, `location`, `created_at`.

---

## F02: Search & Filter

**PRD Reference:** F2 | **Priority:** P0 (MVP Critical)

**Description:** This feature provides the search and filtering system that allows users to instantly narrow their collection by the attributes that matter most — particularly when standing in the cellar and needing a fast answer. It supports full-text search across text fields and discrete filters on structured fields (varietal, region, vintage, producer). Multiple filters may be combined simultaneously, and all filters can be cleared in a single action. Results must be returned within 200ms for collections up to 1,000 bottles.

---

### Terminology

- **Search query:** A free-text string matched against bottle name, producer, and notes fields.
- **Filter:** A discrete constraint applied to a structured field (varietal, region, vintage, producer).
- **Combined filter set:** The simultaneous application of one or more search queries and/or discrete filters.
- **Clear all:** A single action that resets all active filters and search query, returning the full collection.
- **Match:** A bottle record that satisfies all active filters and the search query.

---

### Sub-features

- **F02.1** — Full-text search across name, producer, notes
- **F02.2** — Filter by varietal
- **F02.3** — Filter by region
- **F02.4** — Filter by vintage year or range
- **F02.5** — Filter by producer
- **F02.6** — Combine multiple filters simultaneously
- **F02.7** — Clear all filters with a single action
- **F02.8** — Display result count

---

### Process

#### F02.1 — Full-Text Search
1. User types into the search input field.
2. System debounces input (250ms delay after last keystroke) before executing the search.
3. System performs case-insensitive substring match against `name`, `producer`, and `notes` fields.
4. System returns all bottles where at least one of those three fields contains the search string.
5. Results update in the inventory browsing view (F01) in real time as the user types.

#### F02.2–F02.5 — Discrete Filters
1. User opens the filter panel or selects a filter control.
2. User selects one or more values for a given filter dimension (varietal, region, producer) or enters a vintage range.
3. System applies the filter immediately; results update.
4. Multiple values within the same dimension are treated as OR (e.g., Cabernet Sauvignon OR Merlot).
5. Filters across different dimensions are combined with AND (e.g., varietal = Cabernet AND region = Bordeaux).

#### F02.4 — Vintage Year Range
1. User specifies a `vintage_from` year and/or a `vintage_to` year.
2. If only `vintage_from` is provided: match all bottles with `vintage >= vintage_from`.
3. If only `vintage_to` is provided: match all bottles with `vintage <= vintage_to`.
4. If both are provided: match all bottles with `vintage_from <= vintage <= vintage_to`.
5. `vintage_from` must be ≤ `vintage_to` when both are provided.

#### F02.6 — Combined Filters
1. All active filters (search query + discrete filters) are applied simultaneously using AND logic across dimensions.
2. System recomputes the result set on each filter change.
3. The inventory browsing view displays only matching bottles.

#### F02.7 — Clear All
1. User triggers the "Clear all" / "Reset" action.
2. System clears the search query and all discrete filter selections.
3. System returns the full collection (unfiltered), defaulting to the standard sort order.

#### F02.8 — Result Count
- System displays the count of matching bottles (e.g., "12 bottles found") whenever any filter is active.
- When no filter is active, the standard total count from F01 is shown.

---

### Inputs

All inputs are query parameters on `GET /api/v1/bottles`:

| Parameter | Type | Description |
|-----------|------|-------------|
| `q` | string | Full-text search string (matched against name, producer, notes) |
| `varietal` | string (repeatable) | Filter by varietal; multiple values = OR |
| `region` | string (repeatable) | Filter by region; multiple values = OR |
| `producer` | string (repeatable) | Filter by producer; multiple values = OR |
| `vintage_from` | integer | Minimum vintage year (inclusive) |
| `vintage_to` | integer | Maximum vintage year (inclusive) |
| `status` | string | `all` (default), `active`, `consumed` |

Combined with pagination inputs from F01 (`page`, `limit`, `sort`, `order`).

---

### Outputs

**On Success:**
- HTTP 200 OK
- Response body identical to F01 list response, filtered to matching records:
  ```json
  {
    "data": [ /* matching bottle summary objects */ ],
    "meta": {
      "total": 12,
      "page": 1,
      "limit": 50,
      "total_pages": 1,
      "filters_active": true
    }
  }
  ```

**No Matches:**
- HTTP 200 OK
- `data: []`, `meta.total: 0`
- UI shows: "No bottles match your search. Try adjusting the filters."

---

### Validation

- `q` if provided, must be a string of 1–200 characters; pure whitespace is treated as empty (filter ignored).
- `varietal`, `region`, `producer` if provided, each value must be 1–200 characters. Values are matched case-insensitively (exact match on normalized field values).
- `vintage_from` and `vintage_to` must each be a 4-digit integer between 1800 and the current year.
- When both `vintage_from` and `vintage_to` are provided, `vintage_from` must be ≤ `vintage_to`; if not, a 400 error is returned.
- Unrecognised filter parameters are silently ignored (permissive forward compatibility).
- All discrete filter values are trimmed of leading/trailing whitespace before matching.

---

### Error States

| Scenario | HTTP Status | Error Code | Message |
|----------|-------------|------------|---------|
| `vintage_from` > `vintage_to` | 400 | VALIDATION_ERROR | "vintage_from must not exceed vintage_to." |
| `vintage_from` or `vintage_to` out of range | 400 | VALIDATION_ERROR | "Vintage year must be between 1800 and [current year]." |
| `q` exceeds 200 characters | 400 | VALIDATION_ERROR | "Search query must not exceed 200 characters." |
| Storage unavailable | 503 | SERVICE_UNAVAILABLE | "Unable to search. Please try again." |

---

### API Surface (this feature)

Full schema in `Y1-api.md` §Bottles.

| Method | Path | Action |
|--------|------|--------|
| GET | `/api/v1/bottles` | Search & filter bottles (query params) |
| GET | `/api/v1/bottles/filter-options` | List distinct varietal/region/producer values for filter UI |

---

### Schema Surface (this feature)

Reads from table `bottles`. Full DDL in `Y0-schema.md` §bottles.

Search fields: `name`, `producer`, `notes` (text search).  
Filter fields: `varietal`, `region`, `producer`, `vintage` (indexed for fast filtering).

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

---

## F05: Inventory Statistics & Dashboard

**PRD Reference:** F5 | **Priority:** P1 (High — Post-MVP)

**Description:** This feature provides a summary view that gives the collector a high-level picture of their cellar composition. It answers questions like "How many bottles do I have?", "Which varietals dominate my collection?", and "What vintages am I holding?". The dashboard displays aggregate counts and breakdowns across key dimensions (varietal, region, vintage, producer) and supports simple visual representations (bar charts or count lists). It is computed on demand from the current bottle records with no separate aggregation table required for v1.

---

### Terminology

- **Active count:** Number of bottles where `is_consumed = false`.
- **Consumed count:** Number of bottles where `is_consumed = true`.
- **Total count:** Active count + consumed count.
- **Breakdown:** A sorted list of (value, count) pairs for a given dimension (e.g., varietal).
- **Vintage distribution:** A breakdown by vintage year showing how many bottles the user holds from each year.

---

### Sub-features

- **F05.1** — Total bottle count (active / consumed / total)
- **F05.2** — Breakdown by varietal
- **F05.3** — Breakdown by region
- **F05.4** — Breakdown by vintage year
- **F05.5** — Breakdown by producer
- **F05.6** — Visual representation (chart or count list)

---

### Process

#### F05.1 — Total Count
1. User navigates to the Statistics / Dashboard screen.
2. System computes and displays three counts from the `bottles` table:
   - Active bottles (`is_consumed = false`)
   - Consumed bottles (`is_consumed = true`)
   - Total bottles (all records)

#### F05.2 — Breakdown by Varietal
1. System groups all bottle records by the `varietal` field.
2. Bottles with null/empty `varietal` are grouped under "Unknown" or "Not specified".
3. Results are sorted by count descending (most common varietal first).
4. System returns an array of `{ varietal, count }` objects.
5. UI renders as a bar chart or sorted count list.

#### F05.3 — Breakdown by Region
1. System groups all bottle records by the `region` field.
2. Null/empty region grouped under "Unknown".
3. Sorted by count descending.
4. Returns `{ region, count }` array.

#### F05.4 — Breakdown by Vintage Year
1. System groups all active bottle records by the `vintage` field.
2. Results sorted by vintage year ascending (oldest first) for chronological display.
3. Returns `{ vintage, count }` array.
4. Consumed bottles are excluded by default (shows what the user currently holds).

#### F05.5 — Breakdown by Producer
1. System groups all bottle records by the `producer` field.
2. Null/empty producer grouped under "Unknown".
3. Sorted by count descending.
4. Returns `{ producer, count }` array.
5. For producers with very long lists, the UI may limit to top 20 with a "Show more" option.

#### F05.6 — Visual Representation
- The preferred representation is a horizontal bar chart for each breakdown dimension.
- Fallback: a numbered list with the value and count (e.g., "1. Cabernet Sauvignon — 34 bottles").
- Chart rendering is client-side; the API returns raw data only.

---

### Inputs

| Parameter | Type | Description |
|-----------|------|-------------|
| `include_consumed` | boolean | Include consumed bottles in breakdowns (default: `false` for vintage, `true` for all other dimensions) |
| `limit` | integer | Max entries per breakdown (default: 20, max: 100) |

---

### Outputs

**On Success:**
- HTTP 200 OK
- Response body:
  ```json
  {
    "summary": {
      "total": 142,
      "active": 118,
      "consumed": 24
    },
    "by_varietal": [
      { "varietal": "Cabernet Sauvignon", "count": 34 },
      { "varietal": "Chardonnay", "count": 28 },
      { "varietal": "Unknown", "count": 5 }
    ],
    "by_region": [
      { "region": "Bordeaux", "count": 22 },
      { "region": "Napa Valley", "count": 19 }
    ],
    "by_vintage": [
      { "vintage": 2015, "count": 12 },
      { "vintage": 2018, "count": 20 }
    ],
    "by_producer": [
      { "producer": "Château Margaux", "count": 8 }
    ]
  }
  ```

**Empty Collection:**
- HTTP 200 OK
- `summary.total: 0`, all breakdown arrays empty.
- UI shows: "Add bottles to your collection to see statistics."

---

### Validation

- `include_consumed` defaults to `false` for the vintage dimension and `true` for varietal, region, and producer (configurable).
- `limit` must be between 1 and 100; defaults to 20.
- No user input is required; the dashboard is computed from the existing bottle records.

---

### Error States

| Scenario | HTTP Status | Error Code | Message |
|----------|-------------|------------|---------|
| `limit` exceeds 100 | 400 | BAD_REQUEST | "Limit must not exceed 100." |
| Storage unavailable | 503 | SERVICE_UNAVAILABLE | "Unable to load statistics. Please try again." |
| Computation timeout (>500ms) | 504 | GATEWAY_TIMEOUT | "Statistics are taking too long to compute. Please try again." |

---

### API Surface (this feature)

Full schema in `Y1-api.md` §Statistics.

| Method | Path | Action |
|--------|------|--------|
| GET | `/api/v1/stats` | Retrieve full dashboard statistics |

---

### Schema Surface (this feature)

Reads from table `bottles` (aggregation queries only — no dedicated stats table in v1). Full DDL in `Y0-schema.md` §bottles.

Key queries: `COUNT(*) GROUP BY varietal`, `COUNT(*) GROUP BY region`, `COUNT(*) GROUP BY vintage`, `COUNT(*) GROUP BY producer`, with `WHERE is_consumed = false/true` filters as appropriate.

---

## Y0: Database Schema

This section contains the full DDL for all database entities in the Wine Inventory App v1. The schema is designed for a single-user relational database (SQLite, PostgreSQL, or equivalent). All tables use UUID primary keys, and timestamps are stored as ISO 8601 UTC.

---

### Entity Overview

| Table | Description | Used By |
|-------|-------------|---------|
| `bottles` | Core wine bottle records | F00, F01, F02, F03, F04, F05 |
| `tasting_entries` | Tasting notes and ratings for consumed bottles | F03 |

---

### §bottles

Stores one record per physical wine bottle in the user's collection.

```sql
CREATE TABLE bottles (
    -- Identity
    id              TEXT        PRIMARY KEY,          -- UUID v4
    
    -- Required fields
    name            TEXT        NOT NULL,             -- Bottle name (1–200 chars)
    vintage         INTEGER     NOT NULL,             -- Harvest year (1800–current year)
    
    -- Optional metadata
    producer        TEXT        NULL,                 -- Winery/estate name (0–200 chars)
    varietal        TEXT        NULL,                 -- Grape variety (0–100 chars)
    region          TEXT        NULL,                 -- Geographic region (0–150 chars)
    notes           TEXT        NULL,                 -- General notes/description (0–2000 chars)
    
    -- Purchase info
    purchase_date   TEXT        NULL,                 -- ISO 8601 date YYYY-MM-DD
    purchase_price  REAL        NULL CHECK (purchase_price IS NULL OR purchase_price >= 0),
    quantity        INTEGER     NOT NULL DEFAULT 1    -- Number of bottles (1–9999)
                                CHECK (quantity >= 1 AND quantity <= 9999),
    
    -- Physical location (F04)
    location        TEXT        NULL,                 -- Free-text location string (0–200 chars)
    
    -- Consumption state (F03)
    is_consumed     INTEGER     NOT NULL DEFAULT 0    -- Boolean: 0 = active, 1 = consumed
                                CHECK (is_consumed IN (0, 1)),
    consumed_at     TEXT        NULL,                 -- ISO 8601 UTC timestamp when consumed
    
    -- Auditing
    created_at      TEXT        NOT NULL,             -- ISO 8601 UTC creation timestamp
    updated_at      TEXT        NOT NULL              -- ISO 8601 UTC last-update timestamp
);

-- Indexes for search and filter performance (F02)
CREATE INDEX idx_bottles_vintage        ON bottles (vintage);
CREATE INDEX idx_bottles_varietal       ON bottles (varietal);
CREATE INDEX idx_bottles_region         ON bottles (region);
CREATE INDEX idx_bottles_producer       ON bottles (producer);
CREATE INDEX idx_bottles_location       ON bottles (location);
CREATE INDEX idx_bottles_is_consumed    ON bottles (is_consumed);
CREATE INDEX idx_bottles_created_at     ON bottles (created_at DESC);
CREATE INDEX idx_bottles_consumed_at    ON bottles (consumed_at DESC);
```

**Constraints:**
- `name` must not be blank; enforced at application layer.
- `vintage` validated at application layer (1800 ≤ vintage ≤ current year).
- `purchase_date` stored as text `YYYY-MM-DD`; validated at application layer.
- `is_consumed` uses integer boolean (0/1) for SQLite compatibility.
- When `is_consumed` is set to 1, `consumed_at` must also be set.
- When `is_consumed` is set to 0 (restored), `consumed_at` is cleared to NULL.

---

### §tasting_entries

Stores one tasting entry per consumed bottle. A tasting entry is created when a bottle is marked as consumed (F03). It is deleted if the bottle is restored to active status.

```sql
CREATE TABLE tasting_entries (
    -- Identity
    id              TEXT        PRIMARY KEY,          -- UUID v4
    bottle_id       TEXT        NOT NULL UNIQUE,      -- FK → bottles.id (one entry per bottle)
    
    -- Tasting data
    tasting_date    TEXT        NULL,                 -- ISO 8601 date YYYY-MM-DD (date tasted)
    notes           TEXT        NULL,                 -- Free-text tasting notes (0–5000 chars)
    rating          INTEGER     NULL                  -- 1–5 star rating
                                CHECK (rating IS NULL OR (rating >= 1 AND rating <= 5)),
    
    -- Auditing
    created_at      TEXT        NOT NULL,             -- ISO 8601 UTC creation timestamp
    updated_at      TEXT        NOT NULL              -- ISO 8601 UTC last-update timestamp
);

-- Foreign key
ALTER TABLE tasting_entries
    ADD CONSTRAINT fk_tasting_bottle
    FOREIGN KEY (bottle_id) REFERENCES bottles (id) ON DELETE CASCADE;

-- Index for lookup by bottle
CREATE INDEX idx_tasting_bottle_id ON tasting_entries (bottle_id);
```

**Constraints:**
- `bottle_id` is UNIQUE: each bottle may have at most one tasting entry (one-to-one relationship).
- `rating` is NULL if not provided; when set, must be 1–5 integer inclusive.
- `tasting_date` is NULL if not provided; when set, must not be in the future.
- Cascades delete: deleting a bottle record also deletes its tasting entry.

---

### Relationship Diagram (text)

```
bottles (1) ──────────── (0..1) tasting_entries
  id ◄──────────────────── bottle_id
```

- One bottle may have zero or one tasting entry.
- A tasting entry must belong to exactly one bottle.
- Cascade delete: removing a bottle removes its tasting entry.

---

### Data Integrity Rules

1. All writes to `bottles` must update `updated_at` to the current UTC timestamp.
2. `id` fields are generated by the application layer (UUID v4) before insertion.
3. Soft delete is not implemented in v1; delete is permanent and cascades.
4. Statistics queries (F05) must use `WHERE is_consumed = 0` for active-only counts.
5. Full-text search (F02) is implemented as `LIKE '%query%'` on `name`, `producer`, and `notes` for v1; replace with FTS index if performance degrades beyond 1,000 records.

---

## Y1: REST API Endpoints

All endpoints are prefixed with `/api/v1`. The app is single-user; no authentication token is required for v1 (authentication deferred per PRD). All request and response bodies use `Content-Type: application/json`.

Timestamp format: ISO 8601 UTC (`YYYY-MM-DDTHH:MM:SSZ`).  
Date format: ISO 8601 (`YYYY-MM-DD`).  
IDs: UUID v4 strings.

---

### §Bottles

#### POST /api/v1/bottles
Create a new bottle record.

**Request Body:**
```json
{
  "name": "Château Margaux",
  "vintage": 2015,
  "producer": "Château Margaux",
  "varietal": "Cabernet Sauvignon",
  "region": "Bordeaux",
  "notes": "Gift from a friend.",
  "purchase_date": "2023-06-01",
  "purchase_price": 450.00,
  "quantity": 1,
  "location": "Rack 3 / Bin 12"
}
```

**Required fields:** `name`, `vintage`.  
**Optional fields:** all others.

**Response 201 Created:**
```json
{
  "id": "a1b2c3d4-...",
  "name": "Château Margaux",
  "vintage": 2015,
  "producer": "Château Margaux",
  "varietal": "Cabernet Sauvignon",
  "region": "Bordeaux",
  "notes": "Gift from a friend.",
  "purchase_date": "2023-06-01",
  "purchase_price": 450.00,
  "quantity": 1,
  "location": "Rack 3 / Bin 12",
  "is_consumed": false,
  "consumed_at": null,
  "tasting_entry": null,
  "created_at": "2026-05-15T10:00:00Z",
  "updated_at": "2026-05-15T10:00:00Z"
}
```

**Error Responses:** 422 (validation), 500 (server error).

---

#### GET /api/v1/bottles
List all bottles with optional filtering, search, sort, and pagination.

**Query Parameters:**

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `q` | string | — | Full-text search on name, producer, notes |
| `varietal` | string (repeatable) | — | Filter by varietal (OR within param) |
| `region` | string (repeatable) | — | Filter by region (OR within param) |
| `producer` | string (repeatable) | — | Filter by producer (OR within param) |
| `location` | string | — | Substring match on location field |
| `vintage_from` | integer | — | Minimum vintage year (inclusive) |
| `vintage_to` | integer | — | Maximum vintage year (inclusive) |
| `status` | string | `all` | `all`, `active`, or `consumed` |
| `sort` | string | `created_at` | `created_at`, `name`, `vintage`, `producer`, `location` |
| `order` | string | `desc` | `asc` or `desc` |
| `page` | integer | `1` | Page number |
| `limit` | integer | `50` | Records per page (max 100) |

**Response 200 OK:**
```json
{
  "data": [
    {
      "id": "a1b2c3d4-...",
      "name": "Château Margaux",
      "vintage": 2015,
      "producer": "Château Margaux",
      "varietal": "Cabernet Sauvignon",
      "region": "Bordeaux",
      "is_consumed": false,
      "location": "Rack 3 / Bin 12",
      "rating": null,
      "created_at": "2026-05-15T10:00:00Z"
    }
  ],
  "meta": {
    "total": 142,
    "page": 1,
    "limit": 50,
    "total_pages": 3,
    "filters_active": false
  }
}
```

**Error Responses:** 400 (bad params), 503 (unavailable).

---

#### GET /api/v1/bottles/:id
Retrieve a single bottle by ID (full record including tasting entry if consumed).

**Response 200 OK:**
```json
{
  "id": "a1b2c3d4-...",
  "name": "Château Margaux",
  "vintage": 2015,
  "producer": "Château Margaux",
  "varietal": "Cabernet Sauvignon",
  "region": "Bordeaux",
  "notes": "Gift from a friend.",
  "purchase_date": "2023-06-01",
  "purchase_price": 450.00,
  "quantity": 1,
  "location": "Rack 3 / Bin 12",
  "is_consumed": false,
  "consumed_at": null,
  "tasting_entry": null,
  "created_at": "2026-05-15T10:00:00Z",
  "updated_at": "2026-05-15T10:00:00Z"
}
```

**Error Responses:** 404 (not found), 500 (server error).

---

#### PATCH /api/v1/bottles/:id
Partial update of a bottle record. Only fields present in the request body are updated.

**Request Body (any subset of bottle fields):**
```json
{
  "location": "Rack 4 / Bin 2",
  "notes": "Updated tasting impressions."
}
```

**Response 200 OK:** Full updated bottle record (same shape as GET single).

**Error Responses:** 404 (not found), 422 (validation), 500 (server error).

---

#### DELETE /api/v1/bottles/:id
Permanently delete a bottle and its associated tasting entry.

**Response 204 No Content** (empty body).

**Error Responses:** 404 (not found), 500 (server error).

---

#### GET /api/v1/bottles/filter-options
Returns distinct values for filter dropdowns (varietal, region, producer) across the user's collection.

**Response 200 OK:**
```json
{
  "varietals": ["Cabernet Sauvignon", "Chardonnay", "Merlot"],
  "regions": ["Bordeaux", "Burgundy", "Napa Valley"],
  "producers": ["Château Margaux", "Opus One", "Ridge Vineyards"]
}
```

All arrays sorted alphabetically. Null/empty values excluded. Consumed bottles included.

---

### §Tasting

#### POST /api/v1/bottles/:id/consume
Mark a bottle as consumed and create a tasting entry.

**Request Body:**
```json
{
  "tasting_date": "2026-05-15",
  "notes": "Incredible complexity, long finish.",
  "rating": 5
}
```

All fields optional. If body is omitted, a minimal tasting entry is created with today's date.

**Response 200 OK:** Full bottle record with embedded `tasting_entry`:
```json
{
  "id": "a1b2c3d4-...",
  "name": "Château Margaux",
  "is_consumed": true,
  "consumed_at": "2026-05-15T20:30:00Z",
  "tasting_entry": {
    "id": "e5f6g7h8-...",
    "bottle_id": "a1b2c3d4-...",
    "tasting_date": "2026-05-15",
    "notes": "Incredible complexity, long finish.",
    "rating": 5,
    "created_at": "2026-05-15T20:30:00Z",
    "updated_at": "2026-05-15T20:30:00Z"
  },
  "...": "... all other bottle fields ..."
}
```

**Error Responses:** 404 (bottle not found), 409 (already consumed), 422 (validation).

---

#### GET /api/v1/bottles/:id/tasting
Retrieve the tasting entry for a consumed bottle.

**Response 200 OK:** Tasting entry object.

**Response 404 Not Found:** `{ "error": "NOT_FOUND", "message": "No tasting entry for this bottle." }` if bottle exists but is not consumed.

---

#### PATCH /api/v1/bottles/:id/tasting
Edit a tasting entry (notes, rating, or date). Partial update.

**Request Body (any subset):**
```json
{
  "rating": 4,
  "notes": "Revised after reflection."
}
```

**Response 200 OK:** Updated tasting entry object.

**Error Responses:** 404 (bottle or entry not found), 422 (validation).

---

#### DELETE /api/v1/bottles/:id/consume
Restore a consumed bottle to active status. Deletes the tasting entry.

**Response 200 OK:** Full updated bottle record with `is_consumed: false`, `consumed_at: null`, `tasting_entry: null`.

**Error Responses:** 404 (not found), 409 (already active).

---

### §Statistics

#### GET /api/v1/stats
Retrieve dashboard statistics across the entire collection.

**Query Parameters:**

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `include_consumed` | boolean | varies by dimension | Include consumed bottles in breakdowns |
| `limit` | integer | `20` | Max entries per breakdown array (max 100) |

**Response 200 OK:**
```json
{
  "summary": {
    "total": 142,
    "active": 118,
    "consumed": 24
  },
  "by_varietal": [
    { "varietal": "Cabernet Sauvignon", "count": 34 },
    { "varietal": "Chardonnay", "count": 28 },
    { "varietal": "Unknown", "count": 5 }
  ],
  "by_region": [
    { "region": "Bordeaux", "count": 22 },
    { "region": "Napa Valley", "count": 19 },
    { "region": "Unknown", "count": 3 }
  ],
  "by_vintage": [
    { "vintage": 2015, "count": 12 },
    { "vintage": 2018, "count": 20 }
  ],
  "by_producer": [
    { "producer": "Château Margaux", "count": 8 },
    { "producer": "Unknown", "count": 6 }
  ]
}
```

**Error Responses:** 400 (bad params), 503 (unavailable), 504 (timeout).

---

### API Summary Table

| Method | Path | Feature | Description |
|--------|------|---------|-------------|
| POST | `/api/v1/bottles` | F00 | Create bottle |
| GET | `/api/v1/bottles` | F01, F02 | List / search / filter bottles |
| GET | `/api/v1/bottles/filter-options` | F02 | Filter dropdown values |
| GET | `/api/v1/bottles/:id` | F00 | Get single bottle |
| PATCH | `/api/v1/bottles/:id` | F00, F04 | Edit bottle (partial) |
| DELETE | `/api/v1/bottles/:id` | F00 | Delete bottle |
| POST | `/api/v1/bottles/:id/consume` | F03 | Mark consumed + create tasting entry |
| GET | `/api/v1/bottles/:id/tasting` | F03 | Get tasting entry |
| PATCH | `/api/v1/bottles/:id/tasting` | F03 | Edit tasting entry |
| DELETE | `/api/v1/bottles/:id/consume` | F03 | Restore bottle to active |
| GET | `/api/v1/stats` | F05 | Dashboard statistics |

---

## Y2: Cross-Feature Error Catalog

This section catalogs all error states across the Wine Inventory App, organized by HTTP status code. Each entry specifies the trigger condition, error code (returned in the response body), the human-readable message, and any relevant retry guidance.

**Error Response Shape (all errors):**
```json
{
  "error": "ERROR_CODE",
  "message": "Human-readable description.",
  "fields": [
    { "field": "vintage", "message": "Vintage must be between 1800 and 2026." }
  ]
}
```
- `fields` array is only present for 422 validation errors (one entry per failing field).
- `error` is a machine-readable constant in `UPPER_SNAKE_CASE`.
- `message` is suitable for display to the user.

---

### HTTP 400 — Bad Request

Returned when the request is structurally valid but contains logically invalid parameters.

| Error Code | Trigger | Message | Features |
|------------|---------|---------|---------|
| `BAD_REQUEST` | `page` ≤ 0 | "Page must be a positive integer." | F01, F02 |
| `BAD_REQUEST` | `limit` > 100 | "Limit must not exceed 100." | F01, F02, F05 |
| `BAD_REQUEST` | `vintage_from` > `vintage_to` | "vintage_from must not exceed vintage_to." | F02 |
| `BAD_REQUEST` | `sort` value not in allowed list | "Invalid sort field. Allowed: created_at, name, vintage, producer, location." | F01 |
| `BAD_REQUEST` | `order` not `asc` or `desc` | "Invalid sort order. Use 'asc' or 'desc'." | F01 |
| `BAD_REQUEST` | `status` not `all`, `active`, or `consumed` | "Invalid status filter. Use 'all', 'active', or 'consumed'." | F01, F02 |
| `BAD_REQUEST` | Delete requested without confirmation flag | "Delete must be confirmed." | F00 |

---

### HTTP 404 — Not Found

Returned when the requested resource does not exist.

| Error Code | Trigger | Message | Features |
|------------|---------|---------|---------|
| `NOT_FOUND` | `GET/PATCH/DELETE /bottles/:id` — ID not found | "Bottle not found." | F00, F03, F04 |
| `NOT_FOUND` | `GET /bottles/:id/tasting` — bottle exists but not consumed | "No tasting entry for this bottle." | F03 |
| `NOT_FOUND` | `PATCH /bottles/:id/tasting` — tasting entry not found | "Tasting entry not found." | F03 |

---

### HTTP 409 — Conflict

Returned when the request conflicts with the current state of the resource.

| Error Code | Trigger | Message | Features |
|------------|---------|---------|---------|
| `CONFLICT` | Attempt to consume an already-consumed bottle | "This bottle is already marked as consumed." | F03 |
| `CONFLICT` | Attempt to restore an already-active bottle | "This bottle is already in your active cellar." | F03 |

---

### HTTP 422 — Unprocessable Entity

Returned when input fails validation. The `fields` array identifies each failing field.

| Error Code | Field | Trigger | Message | Features |
|------------|-------|---------|---------|---------|
| `VALIDATION_ERROR` | `name` | Blank or whitespace-only | "Bottle name is required." | F00 |
| `VALIDATION_ERROR` | `name` | Exceeds 200 chars | "Name must not exceed 200 characters." | F00 |
| `VALIDATION_ERROR` | `vintage` | Not an integer | "Vintage must be a valid year." | F00 |
| `VALIDATION_ERROR` | `vintage` | < 1800 | "Vintage must be 1800 or later." | F00 |
| `VALIDATION_ERROR` | `vintage` | > current year | "Vintage cannot be in the future." | F00 |
| `VALIDATION_ERROR` | `vintage_from` | Out of 1800–current range | "Vintage year must be between 1800 and [year]." | F02 |
| `VALIDATION_ERROR` | `vintage_to` | Out of 1800–current range | "Vintage year must be between 1800 and [year]." | F02 |
| `VALIDATION_ERROR` | `producer` | Exceeds 200 chars | "Producer must not exceed 200 characters." | F00 |
| `VALIDATION_ERROR` | `varietal` | Exceeds 100 chars | "Varietal must not exceed 100 characters." | F00 |
| `VALIDATION_ERROR` | `region` | Exceeds 150 chars | "Region must not exceed 150 characters." | F00 |
| `VALIDATION_ERROR` | `notes` | Exceeds 2000 chars (bottle notes) | "Notes must not exceed 2000 characters." | F00 |
| `VALIDATION_ERROR` | `purchase_date` | Not a valid date | "Purchase date must be a valid date (YYYY-MM-DD)." | F00 |
| `VALIDATION_ERROR` | `purchase_date` | In the future | "Purchase date cannot be in the future." | F00 |
| `VALIDATION_ERROR` | `purchase_price` | Negative value | "Purchase price must be zero or greater." | F00 |
| `VALIDATION_ERROR` | `quantity` | < 1 or > 9999 | "Quantity must be between 1 and 9999." | F00 |
| `VALIDATION_ERROR` | `location` | Exceeds 200 chars | "Location must not exceed 200 characters." | F00, F04 |
| `VALIDATION_ERROR` | `tasting_date` | In the future | "Tasting date cannot be in the future." | F03 |
| `VALIDATION_ERROR` | `notes` | Exceeds 5000 chars (tasting notes) | "Tasting notes must not exceed 5000 characters." | F03 |
| `VALIDATION_ERROR` | `rating` | Not 1–5 integer | "Rating must be an integer between 1 and 5." | F03 |
| `VALIDATION_ERROR` | `q` | Exceeds 200 chars | "Search query must not exceed 200 characters." | F02 |

---

### HTTP 500 — Internal Server Error

Returned for unexpected server-side failures.

| Error Code | Trigger | Message | Retry |
|------------|---------|---------|-------|
| `INTERNAL_ERROR` | Unhandled exception / database error | "An unexpected error occurred. Please try again." | Yes — safe to retry |

---

### HTTP 503 — Service Unavailable

Returned when the storage backend is temporarily unavailable.

| Error Code | Trigger | Message | Retry |
|------------|---------|---------|-------|
| `SERVICE_UNAVAILABLE` | Database connection failure | "Unable to load data. Please try again." | Yes — after delay |

---

### HTTP 504 — Gateway Timeout

Returned when a request exceeds the processing time limit.

| Error Code | Trigger | Message | Retry |
|------------|---------|---------|-------|
| `GATEWAY_TIMEOUT` | Stats computation > 500ms | "Statistics are taking too long to compute. Please try again." | Yes — after delay |

---

### Client-Side Handling Guidelines

1. **422 errors** — Display inline field-level messages adjacent to each failing input. Highlight the field in red. Keep the form open.
2. **404 errors** — Show a toast/banner: "Item not found." Navigate back to the list.
3. **409 errors** — Show a banner with the specific message. Do not retry automatically.
4. **400 errors** — Log the invalid parameter; show a generic "Invalid request" message to the user.
5. **500/503/504** — Show a retry banner. Implement exponential backoff for automatic retries (max 3 attempts).
6. **Offline** — Detect network absence client-side; show "You appear to be offline. Some features may be unavailable." Use cached data where possible (F01, F02).

---

## Y3: External Integration Points

This section documents all external system dependencies and integration contracts for the Wine Inventory App v1.

---

### Integration Overview

The Wine Inventory App v1 is intentionally minimal in external dependencies to maintain simplicity, offline capability, and single-user focus. The following table summarizes all integration points:

| Integration | Type | Status | Features |
|-------------|------|--------|---------|
| Local/cloud persistent storage | Database | Required | All features |
| Client-side charting library | UI library | Optional (F05) | F05 |
| Operating system date/time API | System | Required | F00, F03 |
| Network connectivity detection | Platform API | Optional | F01, F02 |

---

### §Storage

**Type:** Persistent relational database  
**Scope:** All features — primary data store for bottle records and tasting entries.

**v1 Options (in order of preference for greenfield):**
1. **SQLite** — embedded, file-based, zero-server, ideal for local-first single-user desktop or mobile apps.
2. **PostgreSQL** — server-based, suitable if the app is deployed as a web service.
3. **IndexedDB** — browser-native, suitable if the platform is a pure web app with offline support.

**Contract:**
- Must support the full DDL defined in `Y0-schema.md`.
- Must support `LIKE '%query%'` or equivalent for full-text search (F02).
- Must support `GROUP BY` aggregations for statistics (F05).
- Must guarantee ACID transactions for write operations (F00, F03).
- Must persist data across app restarts and device reboots.

**Offline Requirement:**  
If SQLite or IndexedDB is chosen, all operations work fully offline. If PostgreSQL is chosen (web deployment), the client must implement a local cache layer (e.g., service worker + cache API) to support offline browse and search as required by the Non-Functional Requirements.

---

### §Client-Side Charting (F05)

**Type:** JavaScript/native charting library  
**Scope:** F05 — Inventory Statistics & Dashboard (visual representations).  
**Status:** Optional; the statistics API returns raw data arrays, and the UI may render as a plain text list if no chart library is integrated.

**Recommended libraries (greenfield choice):**
- **Chart.js** — lightweight, good bar chart support, MIT license.
- **Recharts** (React) — composable, accessible, MIT license.
- **Victory** (React Native / web) — cross-platform chart components.

**Contract:**
- The API (`GET /api/v1/stats`) returns raw `{ label, count }` arrays.
- The chart library consumes this data client-side.
- No server-side chart rendering required.
- Charts must meet WCAG AA accessibility requirements (color contrast, accessible labels).

---

### §Operating System Date / Time API

**Type:** Platform system API  
**Scope:** F00 (default purchase date), F03 (default tasting date, `consumed_at` timestamp).

**Contract:**
- The app reads the current local date for defaulting date fields.
- All timestamps stored in the database are converted to UTC before persistence.
- The app must not depend on a network time source; system clock is authoritative.
- Validation of "not in the future" uses the current system date at the moment of the request.

---

### §Network Connectivity Detection

**Type:** Platform/browser API  
**Scope:** F01, F02 — offline browse and search support.

**Contract:**
- The app listens for online/offline events (e.g., browser `navigator.onLine`, React Native `NetInfo`).
- When offline: display a non-blocking banner "You are offline. Showing cached data."
- Core features (browse, search, view) must work from local cache.
- Write operations (add, edit, delete, consume) require connectivity unless a full offline-sync architecture is implemented (deferred to post-v1).
- When connectivity is restored: the banner clears automatically; any queued writes may be synced.

---

### Out-of-Scope Integrations (v1)

The following integrations are explicitly excluded from v1:

| Integration | Reason Excluded | PRD Reference |
|-------------|----------------|---------------|
| Wine marketplace / price lookup APIs | Complex external dependency; deferred | PRD §9 |
| Barcode / label scanning API or device camera | Hardware dependency; manual entry only in v1 | PRD §9 |
| Social sharing / OAuth providers | Personal use only; no social features in v1 | PRD §9 |
| Multi-user sync / real-time collaboration | Single-user scope in v1 | PRD §9 |
| Push notification services | No scheduling or reminder features in v1 | — |
| Wine database APIs (Vivino, Wine-Searcher) | Out of scope; manual entry validated first | PRD §9 |

---

### Dependency Risk Summary

| Dependency | Risk | Mitigation |
|------------|------|-----------|
| SQLite / local storage | Low — mature, embedded, no external service | Choose based on target platform early |
| Chart library | Low — optional; degrade gracefully to text list | Test chart accessibility before ship |
| System clock | Low — universally available | Validate "future date" server-side, not only client-side |
| Network connectivity | Medium — mobile networks are unreliable | Cache inventory list on load; queue writes if needed |
