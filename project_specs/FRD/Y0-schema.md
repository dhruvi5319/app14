
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
