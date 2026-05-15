
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
