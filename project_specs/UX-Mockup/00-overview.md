# UX Mockup — Wine Inventory App

**Project:** WineInventory
**Generated:** 2026-05-15
**Based on:** UserStories-WineInventory.md, PRD-WineInventory.md, FRD-WineInventory.md, JOURNEYS-WineInventory.md

---

## Overview

### UX Approach

The Wine Inventory App is designed **mobile-first** with three distinct use contexts in mind:

1. **Desk/tablet** — Marcus logs new bottles after an auction or shipment arrives; full-form entry with all metadata
2. **Party/shop** — Sophie quick-captures a bottle with one hand and one thumb; speed over completeness
3. **Cellar-side** — Daniel filters and retrieves physical location in under 30 seconds, one-handed, possibly offline

Every screen decision is weighed against these three scenarios. When in conflict, **cellar-side readability and speed wins** for display, and **quick-capture ease wins** for data entry.

### Design Principles

| Principle | Implementation |
|-----------|---------------|
| **Speed over completeness** | Only Name + Vintage are required; all other fields optional and visually subordinate |
| **Glanceable information** | Location, vintage, consumed status visible on list cards — no drill-in required |
| **One-handed usability** | FAB in bottom-right thumb zone; filter chips large (min 44px tap target); sticky Save button |
| **Offline-first** | All writes go to local storage immediately; sync is background and non-blocking |
| **Progressive disclosure** | Optional fields are visible but de-emphasized; advanced filters in a panel, not the main view |
| **Continuous flows** | Mark Consumed + Tasting Note = single uninterrupted form; no navigation mid-task |
| **Clear state communication** | Consumed bottles visually distinct (greyed + icon); active filter count shown in badge |

### Navigation Structure

```
┌─────────────────────────────────────────────┐
│                Bottom Nav Bar               │
│   [Cellar]   [Journal]   [Stats]            │
└─────────────────────────────────────────────┘

Cellar   → Inventory List Screen (home)
Journal  → Consumed / Tasting History Screen
Stats    → Dashboard / Statistics Screen (Post-MVP)

FAB (+)  → Add Bottle Form (always visible from Cellar)
```

### Screen Inventory

| Screen | File | User Stories |
|--------|------|-------------|
| Inventory List | Screen-00-inventory-list.md | US-1.1, US-1.2, US-1.3, US-1.4, US-2.1, US-2.4 |
| Filter Panel | Screen-01-filter-panel.md | US-2.2, US-2.3, US-2.4, US-4.4 |
| Add / Edit Bottle Form | Screen-02-bottle-form.md | US-0.1, US-0.2, US-0.3, US-0.5, US-4.1 |
| Bottle Detail View | Screen-03-bottle-detail.md | US-1.4, US-0.3, US-0.4, US-3.1, US-3.5, US-4.2 |
| Consume + Tasting Flow | Screen-04-consume-tasting.md | US-3.1, US-3.2, US-3.4 |
| Journal / Consumed List | Screen-05-journal.md | US-3.3, US-1.3 |
| Statistics Dashboard | Screen-06-statistics.md | US-5.1, US-5.2, US-5.3 |
| Empty States | Screen-07-empty-states.md | US-1.1, US-2.1, US-5.1 |

### Flow Inventory

| Flow | File | Journeys |
|------|------|---------|
| Add New Bottle | Flow-00-add-bottle.md | JRN-01.1, JRN-02.1, JRN-03.2 |
| Search & Filter | Flow-01-search-filter.md | JRN-03.1, JRN-02.2 |
| Mark Consumed + Tasting Note | Flow-02-consume-tasting.md | JRN-01.2 |
| Edit Bottle Record | Flow-03-edit-bottle.md | US-0.3 |
| Delete Bottle | Flow-04-delete-bottle.md | US-0.4 |
| Restore to Active | Flow-05-restore-bottle.md | US-3.5 |
| View Statistics | Flow-06-statistics.md | JRN-01.3 |

---
