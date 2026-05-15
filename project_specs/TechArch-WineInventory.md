# Technical Architecture — Wine Inventory App

**Project:** WineInventory  
**Version:** 1.0  
**Date:** 2026-05-15  
**Status:** Draft  
**Source Documents:** PRD-WineInventory.md v1.0, FRD-WineInventory.md v1.0

---

## 1. Architectural Overview

### Architecture Pattern

The Wine Inventory App follows a **local-first, single-tier REST architecture** for v1. The application is a React (web) or React Native (mobile) client that talks to a lightweight Node.js/Express API server. The API server reads and writes to a SQLite database embedded on the same host, eliminating the need for a separate database server and enabling fully offline operation.

This pattern was chosen because:
- The app is single-user with no multi-tenancy requirement (PRD §6, FRD §Platform).
- Offline capability is a hard NFR — core browse, search, and add must work without network (FRD Non-Functional Requirements).
- SQLite is mature, embedded, zero-server, and ACID-compliant — ideal for a local-first personal app with collections up to 1,000 bottles (FRD Y3 §Storage).
- The REST API layer keeps the data model clean and testable independently of the UI.

For a pure web deployment, the same architecture applies with PostgreSQL substituted for SQLite, plus a service worker cache layer for offline reads. The API contract is identical in both cases.

---

### High-Level Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                        Client Layer                             │
│                                                                 │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │             React / React Native UI                      │   │
│  │                                                          │   │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌────────┐  │   │
│  │  │ Inventory│  │  Search  │  │  Tasting │  │  Stats │  │   │
│  │  │  Browse  │  │  Filter  │  │  Journal │  │  Dash  │  │   │
│  │  └──────────┘  └──────────┘  └──────────┘  └────────┘  │   │
│  │                                                          │   │
│  │  ┌──────────────────────┐  ┌───────────────────────┐    │   │
│  │  │   API Client Layer   │  │  Local State / Cache  │    │   │
│  │  │  (fetch / axios)     │  │  (React Query / SWR)  │    │   │
│  │  └──────────────────────┘  └───────────────────────┘    │   │
│  └──────────────────────────────────────────────────────────┘   │
└───────────────────────────┬─────────────────────────────────────┘
                            │ HTTP/REST  /api/v1/*
                            │ (localhost in local deploy;
                            │  HTTPS in web deploy)
┌───────────────────────────▼─────────────────────────────────────┐
│                        API Server Layer                          │
│                                                                 │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │              Node.js + Express                           │   │
│  │                                                          │   │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌────────┐  │   │
│  │  │ Bottles  │  │ Tasting  │  │  Search  │  │  Stats │  │   │
│  │  │  Router  │  │  Router  │  │  Router  │  │ Router │  │   │
│  │  └──────────┘  └──────────┘  └──────────┘  └────────┘  │   │
│  │                                                          │   │
│  │  ┌────────────────┐  ┌──────────────────────────────┐   │   │
│  │  │  Validation    │  │       Business Logic          │   │   │
│  │  │  (Zod/Joi)    │  │   (Service Layer classes)     │   │   │
│  │  └────────────────┘  └──────────────────────────────┘   │   │
│  │                                                          │   │
│  │  ┌──────────────────────────────────────────────────┐   │   │
│  │  │           Data Access Layer (DAL)                │   │   │
│  │  │         better-sqlite3 / knex.js                 │   │   │
│  │  └──────────────────────────────────────────────────┘   │   │
│  └──────────────────────────────────────────────────────────┘   │
└───────────────────────────┬─────────────────────────────────────┘
                            │ SQL
┌───────────────────────────▼─────────────────────────────────────┐
│                      Data Layer                                  │
│                                                                 │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │                    SQLite Database                       │   │
│  │                                                          │   │
│  │   ┌────────────────────┐  ┌─────────────────────────┐   │   │
│  │   │      bottles       │  │     tasting_entries     │   │   │
│  │   └────────────────────┘  └─────────────────────────┘   │   │
│  └──────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────┘
```

---

### Deployment Topology

#### Option A — Local Desktop / Electron App (Recommended for v1)

```
┌─────────────────────────────────┐
│         User's Machine          │
│                                 │
│  ┌───────────────────────────┐  │
│  │   Electron Shell          │  │
│  │  ┌─────────┐ ┌─────────┐  │  │
│  │  │ React   │ │ Express │  │  │
│  │  │  UI     │ │   API   │  │  │
│  │  └─────────┘ └─────────┘  │  │
│  │         ┌─────────┐       │  │
│  │         │ SQLite  │       │  │
│  │         │  .db    │       │  │
│  │         └─────────┘       │  │
│  └───────────────────────────┘  │
└─────────────────────────────────┘
  ✓ Fully offline
  ✓ No server setup
  ✓ Data on local disk
```

#### Option B — Web App (Cloud Deployment)

```
┌───────────────┐   HTTPS    ┌──────────────────────────┐
│   Browser     │◄──────────►│  Node.js + Express API   │
│               │            │  (e.g., Railway / Fly.io)│
│  React SPA    │            │                          │
│  + Service    │            │  ┌────────────────────┐  │
│  Worker cache │            │  │   PostgreSQL DB    │  │
└───────────────┘            │  └────────────────────┘  │
                             └──────────────────────────┘
  ✓ Accessible from any device
  ✓ Data backed up in cloud
  ~ Requires connectivity for writes (reads cached offline)
```

#### Option C — Mobile App (React Native)

```
┌──────────────────────────────┐
│       Mobile Device           │
│                               │
│  ┌────────────────────────┐   │
│  │   React Native App     │   │
│  │                        │   │
│  │  ┌──────┐  ┌────────┐  │   │
│  │  │  UI  │  │ API    │  │   │
│  │  │      │  │ Client │  │   │
│  │  └──────┘  └────────┘  │   │
│  │       ┌──────────┐     │   │
│  │       │ SQLite   │     │   │
│  │       │ (expo-   │     │   │
│  │       │  sqlite) │     │   │
│  │       └──────────┘     │   │
│  └────────────────────────┘   │
└──────────────────────────────┘
  ✓ Fully offline
  ✓ Native mobile UX
  ✓ Camera-ready for future barcode scanning
```

**v1 Recommendation:** Option A (Electron + SQLite) or Option C (React Native) for full offline support. Option B for web-first teams comfortable adding a service worker cache layer.

---

### Key Architectural Decisions

| Decision | Choice | Rationale |
|----------|--------|-----------|
| Database | SQLite (primary) / PostgreSQL (web) | Single-user local-first; zero server overhead; ACID-compliant |
| API style | REST over HTTP | Simple, well-understood, client-agnostic; sufficient for single-user |
| Auth | None in v1 | Single-user personal app; PRD explicitly defers authentication |
| Search | SQL `LIKE '%q%'` in v1 | Adequate for ≤1,000 bottles; replace with FTS index if needed |
| State management | React Query / SWR | Handles cache, loading states, and revalidation without custom code |
| Validation | Zod (server-side) | Schema-first, TypeScript-native; used to generate TS interfaces |
| ORM / Query builder | knex.js | Thin, composable; supports SQLite and PostgreSQL with same API |

---
---

## 2. Component Architecture

### Backend Components

```
server/
├── app.ts                  ← Express app factory (middleware, routes)
├── server.ts               ← Entry point — starts HTTP server
├── db/
│   ├── connection.ts       ← SQLite/PostgreSQL connection singleton
│   └── migrations/         ← Schema migration files (knex migrate)
│       ├── 001_bottles.ts
│       └── 002_tasting_entries.ts
├── routes/
│   ├── bottles.router.ts   ← /api/v1/bottles endpoints (F00, F01, F02, F04)
│   ├── tasting.router.ts   ← /api/v1/bottles/:id/consume + /tasting (F03)
│   └── stats.router.ts     ← /api/v1/stats (F05)
├── services/
│   ├── bottles.service.ts  ← CRUD, search, filter business logic
│   ├── tasting.service.ts  ← Consume/restore/edit tasting entries
│   └── stats.service.ts    ← Aggregation queries for dashboard
├── dal/
│   ├── bottles.dal.ts      ← SQL queries for bottles table
│   └── tasting.dal.ts      ← SQL queries for tasting_entries table
├── validators/
│   ├── bottle.schema.ts    ← Zod schemas for bottle create/edit
│   └── tasting.schema.ts   ← Zod schemas for consume/tasting edit
├── middleware/
│   ├── error-handler.ts    ← Global error handler → standard error shape
│   └── validate.ts         ← Request validation middleware (Zod)
└── types/
    └── index.ts            ← Shared TypeScript types and interfaces
```

**Component Responsibilities:**

| Component | Layer | Responsibility |
|-----------|-------|----------------|
| `bottles.router.ts` | Routing | HTTP verb → handler mapping for bottle CRUD and list/search |
| `tasting.router.ts` | Routing | Consume, restore, and tasting entry edit endpoints |
| `stats.router.ts` | Routing | Dashboard statistics endpoint |
| `bottles.service.ts` | Business Logic | UUID generation, validation orchestration, consume-state enforcement |
| `tasting.service.ts` | Business Logic | Consume/restore state machine, tasting entry lifecycle |
| `stats.service.ts` | Business Logic | Aggregation, null-grouping ("Unknown"), limit/include_consumed logic |
| `bottles.dal.ts` | Data Access | parameterized SQL for bottles table; LIKE search construction |
| `tasting.dal.ts` | Data Access | JOIN queries, cascade-delete coordination |
| `error-handler.ts` | Middleware | Catches thrown errors, maps to standard `{ error, message, fields }` shape |
| `validate.ts` | Middleware | Runs Zod parse, returns 422 with field-level errors on failure |

---

### Frontend Components

```
client/
├── main.tsx                 ← React entry point
├── App.tsx                  ← Router root, global providers
├── api/
│   └── client.ts            ← Typed fetch wrapper; base URL; error parsing
├── hooks/
│   ├── useBottles.ts        ← React Query hook: list, search, filter
│   ├── useBottle.ts         ← React Query hook: single bottle detail
│   └── useStats.ts          ← React Query hook: dashboard statistics
├── pages/
│   ├── InventoryPage.tsx    ← F01: main list/grid view
│   ├── BottleDetailPage.tsx ← F00/F03/F04: detail, edit, consume
│   ├── AddBottlePage.tsx    ← F00: create form
│   └── StatsPage.tsx        ← F05: dashboard
├── components/
│   ├── BottleList.tsx       ← Scrollable list with consumed indicator
│   ├── BottleGrid.tsx       ← Card grid layout
│   ├── BottleCard.tsx       ← Single card: name, producer, vintage, varietal
│   ├── SearchBar.tsx        ← F02: debounced text input
│   ├── FilterPanel.tsx      ← F02: varietal/region/producer/vintage filters
│   ├── ConsumeModal.tsx     ← F03: consume confirmation + tasting form
│   ├── TastingForm.tsx      ← F03: date, notes, rating fields
│   ├── StatsChart.tsx       ← F05: bar chart via Recharts
│   ├── EmptyState.tsx       ← Reusable empty-collection message
│   └── ErrorBanner.tsx      ← Offline / error display
├── forms/
│   ├── BottleForm.tsx       ← Shared create/edit form with validation
│   └── formSchemas.ts       ← Zod schemas mirrored client-side for UX
└── utils/
    ├── dateUtils.ts         ← ISO 8601 formatting, "not in future" checks
    └── uuidUtils.ts         ← UUID v4 generation (client-side IDs)
```

**Component Responsibilities:**

| Component | Responsibility |
|-----------|----------------|
| `useBottles.ts` | Fetches paginated, sorted, filtered bottle list; manages cache invalidation |
| `InventoryPage.tsx` | Orchestrates list/grid toggle, search bar, filter panel, pagination |
| `BottleDetailPage.tsx` | Full bottle record display, edit trigger, consume trigger, delete trigger |
| `ConsumeModal.tsx` | Two-step consume flow: confirmation → tasting form → submit |
| `FilterPanel.tsx` | Renders varietal/region/producer checkboxes from filter-options API |
| `StatsChart.tsx` | Consumes raw stats API data; renders horizontal bar charts (Recharts) |
| `BottleForm.tsx` | Controlled form for create and edit; client-side Zod validation for fast feedback |

---

### Data Flow: Add Bottle

```
User fills form → BottleForm validates (Zod, client) → POST /api/v1/bottles
       → validate.ts middleware (Zod, server) → bottles.service.ts → bottles.dal.ts
       → SQLite INSERT → 201 response → React Query invalidates list cache
       → InventoryPage re-renders with new bottle
```

### Data Flow: Search & Filter

```
User types in SearchBar → 250ms debounce → useBottles hook updates query params
       → GET /api/v1/bottles?q=...&varietal=...&vintage_from=...
       → bottles.router.ts → bottles.service.ts → bottles.dal.ts
       → LIKE query + WHERE clauses → 200 response → BottleList re-renders
```

### Data Flow: Consume Bottle

```
User taps "Mark as Consumed" → ConsumeModal opens → User fills tasting form
       → POST /api/v1/bottles/:id/consume { tasting_date, notes, rating }
       → tasting.service.ts: checks is_consumed=false, sets is_consumed=true
       → bottles DAL UPDATE + tasting_entries DAL INSERT (in transaction)
       → 200 response with updated bottle + tasting_entry
       → React Query invalidates bottle detail + list cache
```

---
---

## 3. Data Model

### Entity-Relationship Diagram

```
┌──────────────────────────────────────────────────────────────┐
│                         bottles                              │
├──────────────────────────────────────────────────────────────┤
│  PK  id              TEXT    UUID v4                         │
│      name            TEXT    NOT NULL  (1–200 chars)         │
│      vintage         INTEGER NOT NULL  (1800–current year)   │
│      producer        TEXT    NULL      (0–200 chars)         │
│      varietal        TEXT    NULL      (0–100 chars)         │
│      region          TEXT    NULL      (0–150 chars)         │
│      notes           TEXT    NULL      (0–2000 chars)        │
│      purchase_date   TEXT    NULL      (YYYY-MM-DD)          │
│      purchase_price  REAL    NULL      (≥ 0.00)              │
│      quantity        INTEGER NOT NULL  DEFAULT 1 (1–9999)    │
│      location        TEXT    NULL      (0–200 chars)         │
│      is_consumed     INTEGER NOT NULL  DEFAULT 0 (0 or 1)    │
│      consumed_at     TEXT    NULL      (ISO 8601 UTC)        │
│      created_at      TEXT    NOT NULL  (ISO 8601 UTC)        │
│      updated_at      TEXT    NOT NULL  (ISO 8601 UTC)        │
└──────────────────────────┬───────────────────────────────────┘
                           │ 1
                           │
                           │ 0..1  (UNIQUE FK, CASCADE DELETE)
                           │
┌──────────────────────────▼───────────────────────────────────┐
│                     tasting_entries                          │
├──────────────────────────────────────────────────────────────┤
│  PK  id              TEXT    UUID v4                         │
│  FK  bottle_id       TEXT    NOT NULL UNIQUE → bottles.id    │
│      tasting_date    TEXT    NULL      (YYYY-MM-DD)          │
│      notes           TEXT    NULL      (0–5000 chars)        │
│      rating          INTEGER NULL      (1–5)                 │
│      created_at      TEXT    NOT NULL  (ISO 8601 UTC)        │
│      updated_at      TEXT    NOT NULL  (ISO 8601 UTC)        │
└──────────────────────────────────────────────────────────────┘

Cardinality:
  bottles (1) ────────────── (0..1) tasting_entries
  - A bottle may have zero or one tasting entry.
  - A tasting entry must belong to exactly one bottle.
  - Deleting a bottle cascades to delete its tasting entry.
  - A tasting entry is created only when a bottle is consumed.
  - A tasting entry is deleted when a bottle is restored to active.
```

---

### Complete DDL — SQLite

The target database for v1 is SQLite. All types use SQLite's TEXT/INTEGER/REAL affinity. The same DDL applies to PostgreSQL with minor type substitutions noted in the section below.

#### Table: `bottles`

```sql
-- ============================================================
-- TABLE: bottles
-- Description: Core wine bottle records. One row per physical
--              bottle in the user's collection.
-- ============================================================

CREATE TABLE bottles (
    -- Identity
    id              TEXT        NOT NULL PRIMARY KEY,
    -- UUID v4 string generated by the application layer
    -- e.g., "a1b2c3d4-e5f6-7890-abcd-ef1234567890"

    -- Required wine metadata
    name            TEXT        NOT NULL
                                CHECK (length(name) >= 1 AND length(name) <= 200),
    -- Wine bottle display name (1–200 characters)

    vintage         INTEGER     NOT NULL
                                CHECK (vintage >= 1800),
    -- Harvest year. Upper bound (≤ current year) enforced at app layer.

    -- Optional wine metadata
    producer        TEXT        NULL
                                CHECK (producer IS NULL OR length(producer) <= 200),
    -- Winery or estate name (0–200 characters)

    varietal        TEXT        NULL
                                CHECK (varietal IS NULL OR length(varietal) <= 100),
    -- Grape variety, e.g., "Cabernet Sauvignon" (0–100 characters)

    region          TEXT        NULL
                                CHECK (region IS NULL OR length(region) <= 150),
    -- Geographic wine region, e.g., "Bordeaux" (0–150 characters)

    notes           TEXT        NULL
                                CHECK (notes IS NULL OR length(notes) <= 2000),
    -- General bottle notes / description (0–2000 characters)

    -- Purchase metadata
    purchase_date   TEXT        NULL,
    -- ISO 8601 date: YYYY-MM-DD. Validated at app layer (not future).

    purchase_price  REAL        NULL
                                CHECK (purchase_price IS NULL OR purchase_price >= 0),
    -- Purchase price in user's local currency. Non-negative.

    quantity        INTEGER     NOT NULL DEFAULT 1
                                CHECK (quantity >= 1 AND quantity <= 9999),
    -- Number of physical bottles represented by this record (1–9999).

    -- Physical location (Feature F04)
    location        TEXT        NULL
                                CHECK (location IS NULL OR length(location) <= 200),
    -- Free-text location label, e.g., "Rack 3 / Bin 12" (0–200 characters)

    -- Consumption state (Feature F03)
    is_consumed     INTEGER     NOT NULL DEFAULT 0
                                CHECK (is_consumed IN (0, 1)),
    -- Boolean flag: 0 = active (in cellar), 1 = consumed (drunk/opened)

    consumed_at     TEXT        NULL,
    -- ISO 8601 UTC timestamp of when the bottle was marked consumed.
    -- Must be set when is_consumed = 1; must be NULL when is_consumed = 0.

    -- Audit timestamps
    created_at      TEXT        NOT NULL,
    -- ISO 8601 UTC timestamp of record creation. Set once; never updated.

    updated_at      TEXT        NOT NULL
    -- ISO 8601 UTC timestamp of last modification. Updated on every PATCH.
);

-- ============================================================
-- INDEXES: bottles
-- Purpose: Support fast filtering (F02), sorting (F01, F04),
--          and statistics aggregation (F05).
-- ============================================================

-- Filter by vintage year or range (F02.4)
CREATE INDEX idx_bottles_vintage
    ON bottles (vintage);

-- Filter by varietal (F02.2); GROUP BY for stats (F05.2)
CREATE INDEX idx_bottles_varietal
    ON bottles (varietal);

-- Filter by region (F02.3); GROUP BY for stats (F05.3)
CREATE INDEX idx_bottles_region
    ON bottles (region);

-- Filter by producer (F02.5); GROUP BY for stats (F05.5)
CREATE INDEX idx_bottles_producer
    ON bottles (producer);

-- Sort and filter by location (F04.3, F04.4)
CREATE INDEX idx_bottles_location
    ON bottles (location);

-- Filter active vs. consumed (F01.4, F03.3); stats WHERE clause (F05)
CREATE INDEX idx_bottles_is_consumed
    ON bottles (is_consumed);

-- Default sort: most recently added first (F01.1)
CREATE INDEX idx_bottles_created_at
    ON bottles (created_at DESC);

-- Sort consumed bottles by consumption date (F03.3)
CREATE INDEX idx_bottles_consumed_at
    ON bottles (consumed_at DESC);

-- Compound index: consumed bottles sorted by date (F03.3 common query)
CREATE INDEX idx_bottles_consumed_date
    ON bottles (is_consumed, consumed_at DESC);
```

#### Table: `tasting_entries`

```sql
-- ============================================================
-- TABLE: tasting_entries
-- Description: Tasting notes and ratings for consumed bottles.
--              One record per bottle (one-to-one via UNIQUE FK).
--              Created when a bottle is consumed (F03.1).
--              Deleted when a bottle is restored to active (F03.5).
-- ============================================================

CREATE TABLE tasting_entries (
    -- Identity
    id              TEXT        NOT NULL PRIMARY KEY,
    -- UUID v4 string generated by the application layer

    -- Foreign key (one-to-one with bottles)
    bottle_id       TEXT        NOT NULL UNIQUE
                                REFERENCES bottles (id) ON DELETE CASCADE,
    -- FK → bottles.id. UNIQUE enforces one tasting entry per bottle.
    -- CASCADE DELETE: removing a bottle auto-removes its tasting entry.

    -- Tasting data (all optional — a minimal consume creates an empty entry)
    tasting_date    TEXT        NULL,
    -- ISO 8601 date YYYY-MM-DD: the calendar date the bottle was opened.
    -- Defaults to today if omitted. Must not be in the future.

    notes           TEXT        NULL
                                CHECK (notes IS NULL OR length(notes) <= 5000),
    -- Free-text tasting notes: aromas, flavors, finish, impressions.
    -- 0–5000 characters.

    rating          INTEGER     NULL
                                CHECK (rating IS NULL OR (rating >= 1 AND rating <= 5)),
    -- 1–5 integer star rating (1 = poor, 5 = excellent). NULL if not rated.

    -- Audit timestamps
    created_at      TEXT        NOT NULL,
    -- ISO 8601 UTC timestamp of entry creation.

    updated_at      TEXT        NOT NULL
    -- ISO 8601 UTC timestamp of last edit.
);

-- ============================================================
-- INDEXES: tasting_entries
-- ============================================================

-- Lookup tasting entry by bottle ID (JOIN from bottles table)
CREATE INDEX idx_tasting_bottle_id
    ON tasting_entries (bottle_id);

-- Sort consumed journal by tasting date (F03.3)
CREATE INDEX idx_tasting_date
    ON tasting_entries (tasting_date DESC);

-- Filter/sort by rating (future feature; low overhead now)
CREATE INDEX idx_tasting_rating
    ON tasting_entries (rating);
```

---

### PostgreSQL Type Substitutions

If deploying with PostgreSQL instead of SQLite, apply the following type changes:

| SQLite DDL | PostgreSQL equivalent | Notes |
|-----------|----------------------|-------|
| `TEXT PRIMARY KEY` | `UUID PRIMARY KEY DEFAULT gen_random_uuid()` | Or keep TEXT and generate in app |
| `INTEGER` (boolean) | `BOOLEAN NOT NULL DEFAULT FALSE` | Use native boolean; update `is_consumed` checks accordingly |
| `TEXT` (timestamps) | `TIMESTAMPTZ NOT NULL` | Store as real timestamp columns |
| `TEXT` (dates) | `DATE NULL` | Use native date type |
| `REAL` | `NUMERIC(10,2)` | More precise decimal for `purchase_price` |
| `CHECK (length(...))` | `VARCHAR(200)` column type | Enforce length at column level |
| Foreign key inline | Use `REFERENCES` with `ON DELETE CASCADE` | Same syntax, enforced by default |

Example PostgreSQL equivalent for `bottles.id`:
```sql
-- PostgreSQL version
id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
is_consumed BOOLEAN NOT NULL DEFAULT FALSE,
consumed_at TIMESTAMPTZ NULL,
created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
purchase_price NUMERIC(10, 2) NULL CHECK (purchase_price >= 0)
```

---

### Data Integrity Rules

| Rule | Enforcement Level | Detail |
|------|------------------|--------|
| `updated_at` always current | Application layer | Every PATCH/UPDATE sets `updated_at = NOW()` |
| UUID generation | Application layer | `uuid` npm package generates v4 UUIDs before INSERT |
| `vintage` upper bound | Application layer | Reject `vintage > currentYear()` in Zod schema |
| `is_consumed` + `consumed_at` sync | Application layer | Setting `is_consumed=1` must set `consumed_at`; restoring clears it |
| Tasting entry lifecycle | Application layer | Create on consume; delete on restore; never orphaned |
| Soft delete | Not implemented | v1 deletes are permanent; cascade handles tasting_entries |
| Transactions | DAL layer | Consume (UPDATE bottles + INSERT tasting_entries) wrapped in a transaction |
| Full-text search | Application layer | `LIKE '%q%'` on `name`, `producer`, `notes`; upgrade to FTS5 if >1,000 bottles |

---
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
---

## 5. Security Architecture

### Authentication

**v1 decision: No authentication.**

The Wine Inventory App v1 is explicitly scoped to single-user personal use. Authentication has been deferred per PRD §4 ("Single-user personal app — authentication deferred or lightweight"). The application runs on the user's own device (local/Electron deployment) or as a private web app accessible only by the user.

**Post-v1 authentication path** (if web deployment with broader access is needed):
- Add a simple session token or JWT-based login with a single set of credentials
- Or integrate a lightweight OAuth provider (Google, GitHub) for convenience
- The REST API is already structured to add an `Authorization` header middleware with zero breaking changes

---

### Authorization

Since v1 has no multi-tenancy and no authentication, there is no access-control layer. All API endpoints are accessible to any caller on the same host.

**Threat model for v1 (local deployment):**
- Risk is limited to the local device; standard OS file-system permissions protect the SQLite `.db` file
- No secrets are stored in the application (no API keys, no user passwords)
- The app does not make outbound API calls to external services

**Threat model for v1 (web deployment):**
- Deploy behind a reverse proxy (nginx/Caddy) that restricts access by IP or requires HTTP Basic Auth at the proxy level
- Do not expose the API publicly without at least a shared secret or network restriction

---

### Data Protection

| Category | Practice |
|----------|---------|
| Data at rest | SQLite `.db` file stored in user's app data directory; protected by OS file permissions |
| Data in transit | HTTPS enforced for web deployments; localhost for local deployments |
| PII handling | No PII collected; data is wine metadata only; no user accounts or emails |
| Backups | User is responsible for backing up the SQLite file; app may expose an export/import feature post-v1 |
| Sensitive fields | `purchase_price` is the only potentially sensitive financial field; not encrypted at rest in v1 |

---

### Input Security

| Threat | Mitigation |
|--------|-----------|
| SQL injection | All queries use parameterized statements via knex.js; no string concatenation in SQL |
| XSS | React escapes all rendered content by default; no `dangerouslySetInnerHTML` usage |
| CSRF | Not applicable in v1 (no auth/session cookies); add CSRF token if sessions are introduced |
| Path traversal | No file path inputs; not applicable |
| Oversized requests | Express body parser limited to 1MB; field-level character limits enforced by Zod |
| Mass assignment | Zod schemas explicitly define allowed fields; extra keys are stripped before DB writes |

---

### API Security Headers

The Express server sets the following security headers via `helmet.js`:

```javascript
// server/app.ts
import helmet from 'helmet';
app.use(helmet());  // Sets: X-Content-Type-Options, X-Frame-Options,
                    // X-XSS-Protection, Referrer-Policy, HSTS (HTTPS only)
```

Additional headers for CORS (web deployment only):

```javascript
import cors from 'cors';
app.use(cors({
  origin: process.env.ALLOWED_ORIGIN || 'http://localhost:3000',
  methods: ['GET', 'POST', 'PATCH', 'DELETE'],
  allowedHeaders: ['Content-Type']
}));
```

---

### Rate Limiting

Not implemented in v1 (single-user, local deployment). If deployed as a web app, add `express-rate-limit`:

```javascript
import rateLimit from 'express-rate-limit';
const limiter = rateLimit({ windowMs: 60_000, max: 200 });
app.use('/api/', limiter);
```

---
---

## 6. Technology Stack

### Full Stack Table

| Layer | Technology | Version | Purpose |
|-------|-----------|---------|---------|
| **Frontend Framework** | React | 18.x | Component-based UI; hooks-first architecture |
| **UI Build Tool** | Vite | 5.x | Fast HMR dev server; ES module bundling |
| **Routing (web)** | React Router | 6.x | Client-side navigation for SPA |
| **Server State** | TanStack Query (React Query) | 5.x | API data fetching, caching, cache invalidation |
| **Charting** | Recharts | 2.x | Composable bar charts for F05 dashboard |
| **CSS** | Tailwind CSS | 3.x | Utility-first; rapid UI iteration; accessible color primitives |
| **Backend Framework** | Node.js + Express | 20 LTS / 4.x | Lightweight REST API server |
| **Database** | SQLite (via better-sqlite3) | 3.x | Embedded, zero-server, ACID-compliant; local-first |
| **DB (web deploy alt.)** | PostgreSQL | 16.x | Drop-in alternative for cloud deployments |
| **Query Builder** | knex.js | 3.x | SQL query builder; supports SQLite + PostgreSQL with same API |
| **Schema Validation** | Zod | 3.x | Schema-first TypeScript validation (server + client shared) |
| **Language** | TypeScript | 5.x | Static typing across frontend and backend |
| **Package Manager** | pnpm | 9.x | Faster installs; workspace support for monorepo |
| **Runtime** | Node.js | 20 LTS | JavaScript runtime for the API server |
| **HTTP Security** | helmet.js | 7.x | Security headers on Express responses |
| **UUID generation** | uuid | 9.x | UUID v4 generation for primary keys |
| **Date handling** | date-fns | 3.x | ISO 8601 formatting, date arithmetic |
| **Testing (unit)** | Vitest | 1.x | Fast unit tests; same config as Vite |
| **Testing (API)** | Supertest | 6.x | HTTP assertion layer for Express route testing |
| **Linting** | ESLint + typescript-eslint | 8.x | Code quality enforcement |
| **Formatting** | Prettier | 3.x | Consistent code style |
| **Desktop (optional)** | Electron | 30.x | Wraps React + Express for desktop deployment (Option A) |
| **Mobile (optional)** | React Native + Expo | SDK 51 | Mobile deployment (Option C); uses expo-sqlite |

---

### Directory Structure (Monorepo)

```
wine-inventory/
├── packages/
│   ├── client/             ← React frontend
│   │   ├── src/
│   │   ├── public/
│   │   ├── vite.config.ts
│   │   └── package.json
│   ├── server/             ← Node.js/Express API
│   │   ├── src/
│   │   ├── migrations/
│   │   └── package.json
│   └── shared/             ← Shared TypeScript types (Zod schemas, interfaces)
│       ├── src/
│       └── package.json
├── pnpm-workspace.yaml
├── package.json            ← Root scripts (dev, build, test)
└── tsconfig.base.json      ← Shared TypeScript config
```

---

### Key Dependency Rationale

| Dependency | Why chosen | Alternative considered |
|-----------|-----------|----------------------|
| **SQLite / better-sqlite3** | Zero-server, file-based, fully offline, ACID. Synchronous API is simpler than async for a single-user app. | PostgreSQL (more setup; better for web multi-instance deploy) |
| **knex.js** | Thin SQL builder; same API for SQLite and PostgreSQL; easy migration files. | Drizzle ORM (newer, less mature ecosystem); raw SQL (verbose) |
| **Zod** | TypeScript-native; schemas can be shared between server and client for consistent validation. | Joi (JavaScript-only, no TS inference); Yup (heavier) |
| **TanStack Query** | Handles caching, background refetch, and cache invalidation — eliminates custom state management for server data. | SWR (lighter but less feature-rich); Redux Toolkit Query (heavier) |
| **Recharts** | Composable React components; accessible; MIT license; good bar chart support. | Chart.js (requires a wrapper); D3 (too low-level for v1) |
| **Tailwind CSS** | Rapid iteration; built-in accessible color utilities; no CSS file sprawl. | CSS Modules (more boilerplate); styled-components (heavier) |
| **Vite** | Fastest dev server; native ESM; excellent TypeScript support. | Create React App (deprecated); Webpack (slower) |
| **Vitest** | Same config as Vite; fast; compatible with Jest API. | Jest (separate config; slower with TypeScript) |

---

### Environment Configuration

```bash
# server/.env (local development)
DATABASE_PATH=./data/wine_inventory.db   # SQLite file path
PORT=3001                                # API server port
NODE_ENV=development
ALLOWED_ORIGIN=http://localhost:5173     # Vite dev server origin (CORS)

# server/.env (production web deploy)
DATABASE_URL=postgresql://...            # PostgreSQL connection string (Option B)
PORT=3001
NODE_ENV=production
ALLOWED_ORIGIN=https://your-domain.com
```

---
---

## 7. Integration Points

### Integration Summary

The Wine Inventory App v1 is intentionally minimal in external dependencies to maintain simplicity, offline capability, and single-user focus. All integrations are either embedded libraries or platform APIs — there are no external network-dependent third-party services required for core functionality.

| Integration | Type | Required? | Features | Notes |
|------------|------|----------|---------|-------|
| SQLite (better-sqlite3) | Embedded DB | Yes | All | Primary data store; local-first |
| PostgreSQL | External DB | No (alt) | All | Drop-in replacement for web deploy |
| System date/time API | Platform API | Yes | F00, F03 | Current date for defaults and "not in future" validation |
| Recharts | UI library | Optional | F05 | Client-side chart rendering; degrades to text list |
| Network connectivity API | Platform API | Optional | F01, F02 | Browser `navigator.onLine`; React Native `NetInfo` |

---

### §Storage: SQLite / PostgreSQL

**Contract:** The data layer must satisfy the full DDL in Section 3 (Data Model) and support:

| Capability | Required by |
|-----------|-----------|
| `LIKE '%query%'` substring search | F02 (full-text search) |
| `GROUP BY` with `COUNT(*)` | F05 (statistics aggregation) |
| `COALESCE` / `NULLIF` | F05 (null-grouping to "Unknown") |
| `ORDER BY ... NULLS LAST` | F04 (location sort; null locations last) |
| `LIMIT / OFFSET` | F01, F02 (pagination) |
| ACID transactions | F03 (consume: UPDATE + INSERT atomic) |
| `ON DELETE CASCADE` | F00, F03 (bottle delete cascades tasting entry) |
| Persistent across restarts | All |

**knex.js migration flow:**
```bash
# Create migration
knex migrate:make 001_create_bottles

# Run migrations (dev)
knex migrate:latest

# Rollback (dev only)
knex migrate:rollback
```

**Connection setup (SQLite example):**
```typescript
// server/src/db/connection.ts
import knex from 'knex';
import path from 'path';

const db = knex({
  client: 'better-sqlite3',
  connection: {
    filename: process.env.DATABASE_PATH ?? path.join(__dirname, '../data/wine.db'),
  },
  useNullAsDefault: true,  // SQLite: treat undefined as NULL
});

export default db;
```

---

### §System Date / Time API

**Used by:** F00 (`purchase_date` default), F03 (`tasting_date` default, `consumed_at` timestamp)

**Contract:**
- The server reads `new Date()` (Node.js system clock) for all timestamp generation
- All timestamps written to the database are converted to ISO 8601 UTC strings: `new Date().toISOString()`
- "Not in future" validation compares against the current system date at request time
- The app does not depend on a network time source (NTP is handled by the OS)

```typescript
// utils/dateUtils.ts
export const nowUtc = (): string => new Date().toISOString();
export const todayDate = (): string => new Date().toISOString().slice(0, 10); // YYYY-MM-DD
export const isInFuture = (dateStr: string): boolean =>
  new Date(dateStr) > new Date();
```

---

### §Client-Side Charting: Recharts

**Used by:** F05 (Inventory Statistics & Dashboard)

**Status:** Optional — the `GET /api/v1/stats` endpoint returns raw `{ label, count }` arrays. If Recharts is not available or the chart fails to render, the UI falls back to a plain numbered list.

**Integration contract:**
```typescript
// The API returns this shape:
interface StatsResponse {
  by_varietal: Array<{ varietal: string; count: number }>;
  // ... etc.
}

// Recharts consumes it as:
import { BarChart, Bar, XAxis, YAxis, Tooltip, ResponsiveContainer } from 'recharts';

<ResponsiveContainer width="100%" height={300}>
  <BarChart data={stats.by_varietal} layout="vertical">
    <XAxis type="number" />
    <YAxis type="category" dataKey="varietal" width={150} />
    <Tooltip />
    <Bar dataKey="count" fill="#7c3aed" />
  </BarChart>
</ResponsiveContainer>
```

**Accessibility requirements:**
- All charts must include `aria-label` on the chart container
- Color contrast must meet WCAG AA (4.5:1 for text; 3:1 for non-text UI components)
- Tooltips must be keyboard-accessible

---

### §Network Connectivity Detection

**Used by:** F01, F02 (offline browse and search)

**Status:** Optional enhancement — the app shows a banner when offline and serves cached data.

**Web implementation (browser):**
```typescript
// hooks/useOnlineStatus.ts
import { useState, useEffect } from 'react';

export const useOnlineStatus = (): boolean => {
  const [isOnline, setIsOnline] = useState(navigator.onLine);
  useEffect(() => {
    const handleOnline = () => setIsOnline(true);
    const handleOffline = () => setIsOnline(false);
    window.addEventListener('online', handleOnline);
    window.addEventListener('offline', handleOffline);
    return () => {
      window.removeEventListener('online', handleOnline);
      window.removeEventListener('offline', handleOffline);
    };
  }, []);
  return isOnline;
};
```

**React Native implementation:**
```typescript
import NetInfo from '@react-native-community/netinfo';
NetInfo.addEventListener(state => {
  setIsOnline(state.isConnected ?? false);
});
```

**Offline behavior contract:**
- When offline: display a non-blocking banner — "You are offline. Showing cached data."
- TanStack Query serves stale cached data from its in-memory cache automatically
- Write operations (POST, PATCH, DELETE) display an error banner: "You appear to be offline. Changes cannot be saved."
- When connectivity is restored: banner clears; TanStack Query refetches stale queries in background

---

### Out-of-Scope Integrations (v1)

The following integrations are explicitly excluded from v1:

| Integration | Reason Excluded | Roadmap? |
|------------|----------------|---------|
| Wine marketplace / price lookup APIs (Vivino, Wine-Searcher) | Complex external dependency; manual entry validated first | Post-v1 |
| Barcode / label scanning (camera API) | Hardware dependency; adds complexity; v1 focuses on manual entry | Post-v1 |
| OAuth / social login providers | No multi-user; personal-use only | Post-v1 |
| Multi-user sync / real-time collaboration | Single-user scope in v1 | Post-v1 |
| Push notification services | No scheduling or reminder features in v1 | Post-v1 |
| Export to CSV / PDF | Useful but not core; defer | Post-v1 |
| Cloud backup / sync | Deferred; users manage SQLite file backup manually | Post-v1 |

---

## 8. Architecture Decision Log

| ID | Decision | Alternatives Considered | Rationale | Date |
|----|---------|------------------------|-----------|------|
| ADR-001 | Use SQLite as primary database | PostgreSQL, MongoDB, IndexedDB | Single-user local-first app; zero server overhead; ACID-compliant; offline by default | 2026-05-15 |
| ADR-002 | REST API over Express (not GraphQL) | GraphQL, tRPC | Simplest fit for CRUD + filter use case; well-understood; client-agnostic | 2026-05-15 |
| ADR-003 | Defer authentication entirely for v1 | JWT, session cookies, OAuth | PRD explicitly scopes to personal-use single-user; adds complexity without v1 value | 2026-05-15 |
| ADR-004 | Full-text search via SQL LIKE | SQLite FTS5, Elasticsearch | Adequate for ≤1,000 bottles (NFR); simpler to implement; upgrade path documented | 2026-05-15 |
| ADR-005 | Zod for validation (server + client shared) | Joi, Yup, custom validators | TypeScript-native inference; single schema definition for both layers | 2026-05-15 |
| ADR-006 | TanStack Query for client state | Redux, SWR, Zustand | Eliminates custom caching logic; handles loading states, revalidation, optimistic updates | 2026-05-15 |
| ADR-007 | knex.js as query builder (not full ORM) | Drizzle, Prisma, raw SQL | Thin abstraction; supports both SQLite and PostgreSQL with same code; explicit SQL control | 2026-05-15 |
| ADR-008 | One tasting entry per bottle (1:1) | Multiple tasting entries per bottle | PRD/FRD define one tasting event per bottle (the consume event); keeps data model simple | 2026-05-15 |

---

*Generated: 2026-05-15 | Model: claude-sonnet-4-6*  
*Source: PRD-WineInventory.md v1.0, FRD-WineInventory.md v1.0*
