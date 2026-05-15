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
