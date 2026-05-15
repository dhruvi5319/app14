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
