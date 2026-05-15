
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
