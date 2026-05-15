---

## 4. API Design

### API Conventions

- **Base prefix:** `/api/v1`
- **Content-Type:** `application/json` (request and response)
- **Authentication:** None in v1 (single-user; auth deferred per PRD §4)
- **IDs:** UUID v4 strings
- **Timestamp format:** ISO 8601 UTC — `YYYY-MM-DDTHH:MM:SSZ`
- **Date format:** ISO 8601 — `YYYY-MM-DD`
- **Pagination:** Cursor-free offset pagination (`page` + `limit`)
- **Partial updates:** PATCH semantics — only fields present in the body are updated
- **Error shape:** `{ "error": "ERROR_CODE", "message": "...", "fields": [...] }`

---

### TypeScript Interfaces

```typescript
// ============================================================
// Core domain types
// ============================================================

/** A wine bottle record. Represents one physical bottle. */
interface Bottle {
  id: string;                    // UUID v4
  name: string;                  // 1–200 chars; required
  vintage: number;               // integer 1800–current year; required
  producer: string | null;       // 0–200 chars
  varietal: string | null;       // 0–100 chars
  region: string | null;         // 0–150 chars
  notes: string | null;          // 0–2000 chars (bottle notes)
  purchase_date: string | null;  // YYYY-MM-DD
  purchase_price: number | null; // ≥ 0.00
  quantity: number;              // 1–9999; default 1
  location: string | null;       // 0–200 chars
  is_consumed: boolean;          // false = active; true = consumed
  consumed_at: string | null;    // ISO 8601 UTC; null if active
  tasting_entry: TastingEntry | null; // populated when is_consumed=true
  created_at: string;            // ISO 8601 UTC
  updated_at: string;            // ISO 8601 UTC
}

/** Summary view of a bottle — used in list/grid responses. */
interface BottleSummary {
  id: string;
  name: string;
  vintage: number;
  producer: string | null;
  varietal: string | null;
  region: string | null;
  is_consumed: boolean;
  location: string | null;
  rating: number | null;         // from tasting_entry if consumed
  created_at: string;
}

/** A tasting entry attached to a consumed bottle. */
interface TastingEntry {
  id: string;                    // UUID v4
  bottle_id: string;             // FK → Bottle.id
  tasting_date: string | null;   // YYYY-MM-DD
  notes: string | null;          // 0–5000 chars
  rating: number | null;         // integer 1–5; null if not rated
  created_at: string;            // ISO 8601 UTC
  updated_at: string;            // ISO 8601 UTC
}

// ============================================================
// Paginated list response
// ============================================================

interface PaginationMeta {
  total: number;
  page: number;
  limit: number;
  total_pages: number;
  filters_active: boolean;
}

interface BottleListResponse {
  data: BottleSummary[];
  meta: PaginationMeta;
}

// ============================================================
// Request bodies
// ============================================================

/** POST /api/v1/bottles — create a new bottle */
interface CreateBottleRequest {
  name: string;                           // required
  vintage: number;                        // required
  producer?: string | null;
  varietal?: string | null;
  region?: string | null;
  notes?: string | null;
  purchase_date?: string | null;          // YYYY-MM-DD
  purchase_price?: number | null;
  quantity?: number;                      // default 1
  location?: string | null;
}

/** PATCH /api/v1/bottles/:id — partial update */
interface UpdateBottleRequest {
  name?: string;
  vintage?: number;
  producer?: string | null;
  varietal?: string | null;
  region?: string | null;
  notes?: string | null;
  purchase_date?: string | null;
  purchase_price?: number | null;
  quantity?: number;
  location?: string | null;
}

/** POST /api/v1/bottles/:id/consume — mark consumed + create tasting */
interface ConsumeBottleRequest {
  tasting_date?: string | null;   // YYYY-MM-DD; defaults to today
  notes?: string | null;          // 0–5000 chars
  rating?: number | null;         // 1–5 integer
}

/** PATCH /api/v1/bottles/:id/tasting — edit tasting entry */
interface UpdateTastingRequest {
  tasting_date?: string | null;
  notes?: string | null;
  rating?: number | null;
}

// ============================================================
// Query parameter interfaces
// ============================================================

/** GET /api/v1/bottles — list, search, filter, sort, paginate */
interface BottleListQuery {
  q?: string;               // full-text search (name, producer, notes)
  varietal?: string[];      // filter by varietal (OR)
  region?: string[];        // filter by region (OR)
  producer?: string[];      // filter by producer (OR)
  location?: string;        // substring match on location
  vintage_from?: number;    // min vintage year (inclusive)
  vintage_to?: number;      // max vintage year (inclusive)
  status?: 'all' | 'active' | 'consumed';  // default: 'all'
  sort?: 'created_at' | 'name' | 'vintage' | 'producer' | 'location';
  order?: 'asc' | 'desc';
  page?: number;            // default: 1
  limit?: number;           // default: 50; max: 100
}

/** GET /api/v1/stats — dashboard query params */
interface StatsQuery {
  include_consumed?: boolean;  // default varies by dimension
  limit?: number;              // max entries per breakdown; default 20
}

// ============================================================
// Response types
// ============================================================

/** GET /api/v1/bottles/filter-options */
interface FilterOptionsResponse {
  varietals: string[];
  regions: string[];
  producers: string[];
}

/** GET /api/v1/stats */
interface StatsResponse {
  summary: {
    total: number;
    active: number;
    consumed: number;
  };
  by_varietal: Array<{ varietal: string; count: number }>;
  by_region: Array<{ region: string; count: number }>;
  by_vintage: Array<{ vintage: number; count: number }>;
  by_producer: Array<{ producer: string; count: number }>;
}

// ============================================================
// Error response
// ============================================================

interface FieldError {
  field: string;
  message: string;
}

interface ApiErrorResponse {
  error: string;           // e.g., "VALIDATION_ERROR", "NOT_FOUND"
  message: string;         // human-readable
  fields?: FieldError[];   // only present on 422 responses
}
```

---

### Endpoint Reference

#### Bottles

| Method | Path | Status Codes | Description |
|--------|------|-------------|-------------|
| `POST` | `/api/v1/bottles` | 201, 422, 500 | Create a new bottle |
| `GET` | `/api/v1/bottles` | 200, 400, 503 | List / search / filter bottles |
| `GET` | `/api/v1/bottles/filter-options` | 200, 503 | Distinct filter values |
| `GET` | `/api/v1/bottles/:id` | 200, 404, 500 | Get single bottle (full record) |
| `PATCH` | `/api/v1/bottles/:id` | 200, 404, 422, 500 | Partial update a bottle |
| `DELETE` | `/api/v1/bottles/:id` | 204, 404, 500 | Delete bottle permanently |

##### POST /api/v1/bottles

```
Request:  CreateBottleRequest (application/json)
Response: 201 Bottle (full record)
Errors:   422 VALIDATION_ERROR (field-level)
          500 INTERNAL_ERROR
```

**Behavior:**
1. Validate request body with Zod `CreateBottleRequest` schema.
2. Generate UUID v4 for `id`; set `created_at` and `updated_at` to `NOW()`.
3. Set `quantity` default to 1 if omitted; `is_consumed` = 0; `consumed_at` = NULL.
4. INSERT into `bottles` table.
5. Return full `Bottle` record with `tasting_entry: null`.

---

##### GET /api/v1/bottles

```
Request:  Query params (BottleListQuery)
Response: 200 BottleListResponse
Errors:   400 BAD_REQUEST (bad pagination/filter params)
          503 SERVICE_UNAVAILABLE
```

**Query construction logic:**
1. Start with `SELECT ... FROM bottles`.
2. If `q` provided: add `AND (name LIKE '%q%' OR producer LIKE '%q%' OR notes LIKE '%q%')`.
3. If `varietal[]` provided: add `AND varietal IN (...)`.
4. If `region[]` provided: add `AND region IN (...)`.
5. If `producer[]` provided: add `AND producer IN (...)`.
6. If `location` provided: add `AND location LIKE '%location%'`.
7. If `vintage_from` provided: add `AND vintage >= vintage_from`.
8. If `vintage_to` provided: add `AND vintage <= vintage_to`.
9. If `status = 'active'`: add `AND is_consumed = 0`; `status = 'consumed'`: add `AND is_consumed = 1`.
10. Apply `ORDER BY {sort} {order}` (nulls last for `location` sort).
11. Apply `LIMIT limit OFFSET (page - 1) * limit`.
12. Run `COUNT(*)` on the same filtered query for `meta.total`.
13. Set `filters_active = true` if any filter/search param is present.

---

##### GET /api/v1/bottles/filter-options

```
Request:  (no params)
Response: 200 FilterOptionsResponse
Errors:   503 SERVICE_UNAVAILABLE
```

**Behavior:** Run three `SELECT DISTINCT` queries on `bottles` table for `varietal`, `region`, and `producer`. Exclude NULL and empty-string values. Sort alphabetically. Return combined response.

> **Route ordering note:** This endpoint must be registered *before* `GET /api/v1/bottles/:id` in Express to prevent the path segment `filter-options` from being matched as an `:id` parameter.

---

##### GET /api/v1/bottles/:id

```
Request:  path param id (UUID)
Response: 200 Bottle (full record with tasting_entry if consumed)
Errors:   404 NOT_FOUND
          500 INTERNAL_ERROR
```

**Behavior:** LEFT JOIN `tasting_entries` on `bottle_id = id`. If bottle not found, return 404. Return full `Bottle` object; `tasting_entry` is null if not consumed.

---

##### PATCH /api/v1/bottles/:id

```
Request:  UpdateBottleRequest (partial — only fields to change)
Response: 200 Bottle (full updated record)
Errors:   404 NOT_FOUND
          422 VALIDATION_ERROR
          500 INTERNAL_ERROR
```

**Behavior:** Validate only the fields present in the request body. Build a dynamic UPDATE query with only those columns. Set `updated_at = NOW()`. Return full updated record.

---

##### DELETE /api/v1/bottles/:id

```
Request:  path param id (UUID)
Response: 204 No Content
Errors:   404 NOT_FOUND
          500 INTERNAL_ERROR
```

**Behavior:** Check bottle exists (404 if not). DELETE from `bottles`; cascade delete removes associated `tasting_entries` row automatically. Return 204 with empty body.

---

#### Tasting / Consumption

| Method | Path | Status Codes | Description |
|--------|------|-------------|-------------|
| `POST` | `/api/v1/bottles/:id/consume` | 200, 404, 409, 422, 500 | Mark consumed + create tasting entry |
| `GET` | `/api/v1/bottles/:id/tasting` | 200, 404, 500 | Get tasting entry |
| `PATCH` | `/api/v1/bottles/:id/tasting` | 200, 404, 422, 500 | Edit tasting entry |
| `DELETE` | `/api/v1/bottles/:id/consume` | 200, 404, 409, 500 | Restore bottle to active |

##### POST /api/v1/bottles/:id/consume

```
Request:  ConsumeBottleRequest (all fields optional; body may be omitted)
Response: 200 Bottle (full record with tasting_entry embedded)
Errors:   404 NOT_FOUND ("Bottle not found.")
          409 CONFLICT ("This bottle is already marked as consumed.")
          422 VALIDATION_ERROR (tasting_date, notes, rating)
          500 INTERNAL_ERROR
```

**Behavior (atomic transaction):**
1. Fetch bottle by `id`; return 404 if not found.
2. If `is_consumed = 1`, return 409 CONFLICT.
3. Validate `ConsumeBottleRequest` body.
4. Begin transaction:
   - `UPDATE bottles SET is_consumed = 1, consumed_at = NOW(), updated_at = NOW() WHERE id = :id`
   - `INSERT INTO tasting_entries (id, bottle_id, tasting_date, notes, rating, created_at, updated_at) VALUES (...)`
5. Commit; return full `Bottle` record with `tasting_entry` embedded.

---

##### GET /api/v1/bottles/:id/tasting

```
Request:  path param id (UUID)
Response: 200 TastingEntry
Errors:   404 NOT_FOUND ("Bottle not found." or "No tasting entry for this bottle.")
          500 INTERNAL_ERROR
```

**Behavior:** Check bottle exists (404 if not). Check `is_consumed = 1` and `tasting_entries` row exists (404 "No tasting entry" if not consumed). Return `TastingEntry` object.

---

##### PATCH /api/v1/bottles/:id/tasting

```
Request:  UpdateTastingRequest (partial)
Response: 200 TastingEntry (full updated entry)
Errors:   404 NOT_FOUND
          422 VALIDATION_ERROR
          500 INTERNAL_ERROR
```

**Behavior:** Validate only present fields. UPDATE the `tasting_entries` row; set `updated_at = NOW()`. Return updated `TastingEntry`.

---

##### DELETE /api/v1/bottles/:id/consume

```
Request:  path param id (UUID)
Response: 200 Bottle (full updated record: is_consumed=false, consumed_at=null, tasting_entry=null)
Errors:   404 NOT_FOUND ("Bottle not found.")
          409 CONFLICT ("This bottle is already in your active cellar.")
          500 INTERNAL_ERROR
```

**Behavior (atomic transaction):**
1. Fetch bottle by `id`; return 404 if not found.
2. If `is_consumed = 0`, return 409 CONFLICT.
3. Begin transaction:
   - `DELETE FROM tasting_entries WHERE bottle_id = :id`
   - `UPDATE bottles SET is_consumed = 0, consumed_at = NULL, updated_at = NOW() WHERE id = :id`
4. Commit; return full updated `Bottle` with `is_consumed: false`, `consumed_at: null`, `tasting_entry: null`.

---

#### Statistics

| Method | Path | Status Codes | Description |
|--------|------|-------------|-------------|
| `GET` | `/api/v1/stats` | 200, 400, 503, 504 | Full dashboard statistics |

##### GET /api/v1/stats

```
Request:  Query params (StatsQuery)
Response: 200 StatsResponse
Errors:   400 BAD_REQUEST (limit > 100)
          503 SERVICE_UNAVAILABLE
          504 GATEWAY_TIMEOUT (computation > 500ms)
```

**Behavior:** Run the following SQL aggregations against the `bottles` table:

```sql
-- Summary counts
SELECT
  COUNT(*) AS total,
  SUM(CASE WHEN is_consumed = 0 THEN 1 ELSE 0 END) AS active,
  SUM(CASE WHEN is_consumed = 1 THEN 1 ELSE 0 END) AS consumed
FROM bottles;

-- By varietal (default: all bottles)
SELECT
  COALESCE(NULLIF(TRIM(varietal), ''), 'Unknown') AS varietal,
  COUNT(*) AS count
FROM bottles
-- WHERE is_consumed = 0  (if include_consumed=false)
GROUP BY COALESCE(NULLIF(TRIM(varietal), ''), 'Unknown')
ORDER BY count DESC
LIMIT :limit;

-- By region (default: all bottles)
SELECT
  COALESCE(NULLIF(TRIM(region), ''), 'Unknown') AS region,
  COUNT(*) AS count
FROM bottles
GROUP BY COALESCE(NULLIF(TRIM(region), ''), 'Unknown')
ORDER BY count DESC
LIMIT :limit;

-- By vintage (default: active bottles only)
SELECT vintage, COUNT(*) AS count
FROM bottles
WHERE is_consumed = 0
GROUP BY vintage
ORDER BY vintage ASC
LIMIT :limit;

-- By producer (default: all bottles)
SELECT
  COALESCE(NULLIF(TRIM(producer), ''), 'Unknown') AS producer,
  COUNT(*) AS count
FROM bottles
GROUP BY COALESCE(NULLIF(TRIM(producer), ''), 'Unknown')
ORDER BY count DESC
LIMIT :limit;
```

All five queries run sequentially; the server enforces a 500ms total timeout. If exceeded, return 504.

---

### Error Response Examples

**422 Validation Error:**
```json
{
  "error": "VALIDATION_ERROR",
  "message": "Validation failed. Please correct the highlighted fields.",
  "fields": [
    { "field": "name", "message": "Bottle name is required." },
    { "field": "vintage", "message": "Vintage cannot be in the future." }
  ]
}
```

**404 Not Found:**
```json
{
  "error": "NOT_FOUND",
  "message": "Bottle not found."
}
```

**409 Conflict:**
```json
{
  "error": "CONFLICT",
  "message": "This bottle is already marked as consumed."
}
```

**503 Service Unavailable:**
```json
{
  "error": "SERVICE_UNAVAILABLE",
  "message": "Unable to load data. Please try again."
}
```

---
