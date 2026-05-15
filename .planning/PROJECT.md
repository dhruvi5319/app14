# Wine Inventory App

## What This Is

A personal wine inventory application that lets users catalog, track, and manage their wine collection. Users can log bottles they own, record details like vintage, varietal, region, and producer, track what they've consumed, and manage their cellar with notes and ratings.

## Core Value

A wine collector can always know exactly what bottles they have, where they are, and which to open next.

## Requirements

### Validated

(None yet — ship to validate)

### Active

- [ ] User can add a wine bottle to their collection with details (name, producer, vintage, varietal, region)
- [ ] User can view their full wine inventory as a list or grid
- [ ] User can search and filter their collection by varietal, region, vintage, or producer
- [ ] User can mark a bottle as consumed and log tasting notes
- [ ] User can edit wine details after entry
- [ ] User can delete a wine from their inventory
- [ ] User can rate wines they've tasted
- [ ] User can track bottle location (e.g., rack number, bin)
- [ ] User can see inventory statistics (total bottles, by varietal, by region, etc.)

### Out of Scope

- Multi-user collaboration — keeping scope focused on personal use for v1
- Wine marketplace / purchase integration — complex external API, defer to later
- Barcode/label scanning — adds hardware dependency, v1 focuses on manual entry
- Social sharing — v1 is personal-only

## Context

- Greenfield project; no existing codebase
- Targeted at wine enthusiasts who want a personal digital cellar book
- Primary use case: managing a home collection of 20–500+ bottles
- Users need fast lookup when standing in the cellar deciding what to open

## Constraints

- **Scope**: Personal-use app for v1 — no multi-tenancy
- **Timeline**: Standard depth (5-8 phases)

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| Personal-use only for v1 | Keeps scope manageable, validates core value first | — Pending |

---
*Last updated: 2026-05-15 after initialization*
