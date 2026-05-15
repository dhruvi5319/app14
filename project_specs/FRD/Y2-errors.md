
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
