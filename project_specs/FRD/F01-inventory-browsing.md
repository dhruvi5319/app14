
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
