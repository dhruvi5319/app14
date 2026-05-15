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
