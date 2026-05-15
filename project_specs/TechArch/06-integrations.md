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
