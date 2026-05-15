
---

## Y3: External Integration Points

This section documents all external system dependencies and integration contracts for the Wine Inventory App v1.

---

### Integration Overview

The Wine Inventory App v1 is intentionally minimal in external dependencies to maintain simplicity, offline capability, and single-user focus. The following table summarizes all integration points:

| Integration | Type | Status | Features |
|-------------|------|--------|---------|
| Local/cloud persistent storage | Database | Required | All features |
| Client-side charting library | UI library | Optional (F05) | F05 |
| Operating system date/time API | System | Required | F00, F03 |
| Network connectivity detection | Platform API | Optional | F01, F02 |

---

### §Storage

**Type:** Persistent relational database  
**Scope:** All features — primary data store for bottle records and tasting entries.

**v1 Options (in order of preference for greenfield):**
1. **SQLite** — embedded, file-based, zero-server, ideal for local-first single-user desktop or mobile apps.
2. **PostgreSQL** — server-based, suitable if the app is deployed as a web service.
3. **IndexedDB** — browser-native, suitable if the platform is a pure web app with offline support.

**Contract:**
- Must support the full DDL defined in `Y0-schema.md`.
- Must support `LIKE '%query%'` or equivalent for full-text search (F02).
- Must support `GROUP BY` aggregations for statistics (F05).
- Must guarantee ACID transactions for write operations (F00, F03).
- Must persist data across app restarts and device reboots.

**Offline Requirement:**  
If SQLite or IndexedDB is chosen, all operations work fully offline. If PostgreSQL is chosen (web deployment), the client must implement a local cache layer (e.g., service worker + cache API) to support offline browse and search as required by the Non-Functional Requirements.

---

### §Client-Side Charting (F05)

**Type:** JavaScript/native charting library  
**Scope:** F05 — Inventory Statistics & Dashboard (visual representations).  
**Status:** Optional; the statistics API returns raw data arrays, and the UI may render as a plain text list if no chart library is integrated.

**Recommended libraries (greenfield choice):**
- **Chart.js** — lightweight, good bar chart support, MIT license.
- **Recharts** (React) — composable, accessible, MIT license.
- **Victory** (React Native / web) — cross-platform chart components.

**Contract:**
- The API (`GET /api/v1/stats`) returns raw `{ label, count }` arrays.
- The chart library consumes this data client-side.
- No server-side chart rendering required.
- Charts must meet WCAG AA accessibility requirements (color contrast, accessible labels).

---

### §Operating System Date / Time API

**Type:** Platform system API  
**Scope:** F00 (default purchase date), F03 (default tasting date, `consumed_at` timestamp).

**Contract:**
- The app reads the current local date for defaulting date fields.
- All timestamps stored in the database are converted to UTC before persistence.
- The app must not depend on a network time source; system clock is authoritative.
- Validation of "not in the future" uses the current system date at the moment of the request.

---

### §Network Connectivity Detection

**Type:** Platform/browser API  
**Scope:** F01, F02 — offline browse and search support.

**Contract:**
- The app listens for online/offline events (e.g., browser `navigator.onLine`, React Native `NetInfo`).
- When offline: display a non-blocking banner "You are offline. Showing cached data."
- Core features (browse, search, view) must work from local cache.
- Write operations (add, edit, delete, consume) require connectivity unless a full offline-sync architecture is implemented (deferred to post-v1).
- When connectivity is restored: the banner clears automatically; any queued writes may be synced.

---

### Out-of-Scope Integrations (v1)

The following integrations are explicitly excluded from v1:

| Integration | Reason Excluded | PRD Reference |
|-------------|----------------|---------------|
| Wine marketplace / price lookup APIs | Complex external dependency; deferred | PRD §9 |
| Barcode / label scanning API or device camera | Hardware dependency; manual entry only in v1 | PRD §9 |
| Social sharing / OAuth providers | Personal use only; no social features in v1 | PRD §9 |
| Multi-user sync / real-time collaboration | Single-user scope in v1 | PRD §9 |
| Push notification services | No scheduling or reminder features in v1 | — |
| Wine database APIs (Vivino, Wine-Searcher) | Out of scope; manual entry validated first | PRD §9 |

---

### Dependency Risk Summary

| Dependency | Risk | Mitigation |
|------------|------|-----------|
| SQLite / local storage | Low — mature, embedded, no external service | Choose based on target platform early |
| Chart library | Low — optional; degrade gracefully to text list | Test chart accessibility before ship |
| System clock | Low — universally available | Validate "future date" server-side, not only client-side |
| Network connectivity | Medium — mobile networks are unreliable | Cache inventory list on load; queue writes if needed |
