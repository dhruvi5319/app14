# Functional Requirements Document — Wine Inventory App

**Project:** WineInventory  
**Acronym:** WineInventory  
**Version:** 1.0  
**Date:** 2026-05-15  
**Status:** Draft  
**Source PRD:** PRD-WineInventory.md v1.0  

---

## Scope

This document specifies the detailed functional behavior of all features in the Wine Inventory App v1. It is derived from PRD-WineInventory.md and covers the six PRD features (F0–F5) with exact input/output contracts, validation rules, error states, REST API endpoints, and database schema. It is intended as the authoritative implementation reference for developers, QA engineers, and technical reviewers.

Single-user personal use only. Multi-tenancy, social sharing, barcode scanning, and marketplace integrations are explicitly out of scope for v1 (see PRD §9).

---

## Table of Contents

| Section | File | Description |
|---------|------|-------------|
| F00 | `F00-bottle-entry.md` | Bottle Entry & Management |
| F01 | `F01-inventory-browsing.md` | Inventory Browsing |
| F02 | `F02-search-filter.md` | Search & Filter |
| F03 | `F03-consumption-tasting.md` | Consumption Tracking & Tasting Notes |
| F04 | `F04-location-tracking.md` | Bottle Location Tracking |
| F05 | `F05-statistics-dashboard.md` | Inventory Statistics & Dashboard |
| Y0 | `Y0-schema.md` | Database Schema (full DDL) |
| Y1 | `Y1-api.md` | REST API Endpoints (full spec) |
| Y2 | `Y2-errors.md` | Cross-Feature Error Catalog |
| Y3 | `Y3-integrations.md` | External Integration Points |

---

## Conventions

- **Feature IDs:** F00–F05 map to PRD features F0–F5. Zero-padded for consistent sort order.
- **Field naming:** `snake_case` for database columns and JSON fields; `camelCase` for UI variable references.
- **Required vs. Optional:** Fields marked `(required)` must be non-null/non-empty. Fields marked `(optional)` may be omitted or null.
- **ID format:** All primary keys are UUIDs (v4) unless otherwise specified.
- **Timestamps:** All timestamps stored as ISO 8601 UTC strings (`YYYY-MM-DDTHH:MM:SSZ`).
- **API prefix:** All endpoints are prefixed with `/api/v1`.
- **HTTP status codes:** Follow RFC 7231 conventions. See Y2 for error catalog.
- **Priority:**
  - P0 = MVP Critical (must ship in v1)
  - P1 = High (important, may ship post-MVP)

---

## Shared Terminology

| Term | Definition |
|------|-----------|
| **Bottle** | A single wine bottle record in the user's inventory. Represents one physical bottle. |
| **Active bottle** | A bottle that has not been marked as consumed; still physically in the cellar. |
| **Consumed bottle** | A bottle that has been marked as consumed (opened and drunk). |
| **Tasting entry** | A record attached to a consumed bottle capturing the date, notes, and rating for that drinking occasion. |
| **Varietal** | The grape variety used to make the wine (e.g., Cabernet Sauvignon, Chardonnay). |
| **Region** | The geographic wine-producing area (e.g., Bordeaux, Napa Valley, Barossa Valley). |
| **Vintage** | The year the grapes were harvested (a 4-digit calendar year). |
| **Producer** | The winery or estate that made the wine. |
| **Location** | A free-text label describing the bottle's physical storage position (e.g., "Rack 3 / Bin 12"). |
| **Rating** | A numeric score from 1 to 5 (integer) representing the user's enjoyment of the wine. |
| **Collection** | The complete set of bottle records belonging to the user, both active and consumed. |
| **Cellar** | Informal term for the user's active (non-consumed) collection. |
| **Dashboard** | The statistics view (F05) summarizing cellar composition. |

---

## Non-Functional Requirements Summary

| Category | Requirement |
|----------|-------------|
| Performance | Search/filter results within 200ms for ≤1,000 bottles |
| Usability | Core actions (add, search, consume) completable in ≤3 taps/clicks |
| Data integrity | All writes confirmed before UI updates; no data loss on crash |
| Offline | Core browse, search, add work without network |
| Accessibility | WCAG AA contrast; accessible labels on interactive elements |
| Scalability | Performant with 500+ bottle collections |
| Platform | Single-user; no multi-tenancy in v1 |
