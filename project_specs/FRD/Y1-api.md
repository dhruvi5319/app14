
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
