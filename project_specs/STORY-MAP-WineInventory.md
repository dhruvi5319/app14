# Story Map — Wine Inventory App

**Product:** Wine Inventory App
**Project Acronym:** WineInventory
**Date:** 2026-05-15
**Status:** Draft
**Version:** 1.0
**Related Artifacts:** PRD-WineInventory.md · PERSONAS-WineInventory.md · JOURNEYS-WineInventory.md · JTBD-WineInventory.md · UserStories-WineInventory.md

---

## 1. Overview

This Story Map organizes all 25 user stories (US-0.1 – US-5.3) across two axes:

- **X-axis (columns):** Journey stages drawn from JOURNEYS-WineInventory.md — the major touchpoints each persona passes through to accomplish their primary job.
- **Y-axis (rows):** Epics and individual stories within each stage, ordered by priority (P0 → P1).

Each story is annotated with a **Natural Acceptance Criterion (NaC)** — a testable statement derived from the intersection of a specific JTBD outcome and the journey stage where the story lives. NaC are not invented; they are read directly from the JTBD hiring criteria and contextualized for the stage.

**Story Map Entry IDs** follow the convention `SM-{Epic}.{NN}` (e.g., SM-0.1 maps to Epic 0, entry 1).

**Release assignments:**
- **R1 (MVP):** P0 stories — delivers complete core journeys for all three personas.
- **R2 (Post-MVP):** P1 stories — adds location tracking and statistics to deepen value for Marcus and Daniel.

---

## 2. Story Map Matrix

The table below maps each story to its primary journey stage(s). Where a story spans multiple stages, the **primary** stage is used for placement; secondary stages are noted. All 25 stories appear; none are orphaned.

### 2A. Marcus Delacroix (PER-01) — Primary Journey Stages

**JRN-01.1:** Logging a New Bottle · **JRN-01.2:** Consuming + Tasting Notes · **JRN-01.3:** Weekly Review & Stats

| SM-ID | Journey Stage | Epic | Story | NaC (derived from JTBD) | Release |
|-------|--------------|------|-------|--------------------------|---------|
| SM-0.1 | JRN-01.1: Initiate | Epic 0 — Bottle Entry | US-0.1: Add a New Bottle | JTBD-01.1 → "A complete record (name, producer, vintage, varietal, region, location) is saved in under 60 seconds from first tap on Add Bottle" | R1 |
| SM-0.3 | JRN-01.1: Enter Details | Epic 0 — Bottle Entry | US-0.3: Edit an Existing Bottle Record | JTBD-01.1 → "An edited record reflects updated values immediately in both list and detail view, with no manual refresh required" | R1 |
| SM-0.4 | JRN-01.1: Verify | Epic 0 — Bottle Entry | US-0.4: Delete a Bottle | JTBD-01.1 → "A deleted bottle does not appear anywhere in the inventory, search results, or statistics after confirmation — active count updates immediately" | R1 |
| SM-1.1 | JRN-01.1: Arrive | Epic 1 — Inventory Browsing | US-1.1: View All Bottles in a List | JTBD-01.1 → "All bottles in the collection load within 200ms; new entries added today appear at the top without any refresh action" | R1 |
| SM-1.4 | JRN-01.1: Verify | Epic 1 — Inventory Browsing | US-1.4: Navigate to Bottle Detail View | JTBD-01.1 → "Tapping any bottle opens its full detail record — all metadata, location, and tasting entry — in a single navigation step" | R1 |
| SM-2.3 | JRN-01.2: Locate | Epic 2 — Search & Filter | US-2.3: Filter by Vintage Year or Range | JTBD-01.3 → "Filtering to a single vintage year returns only matching bottles within 200ms, accessible in ≤2 taps, so Marcus can locate the exact bottle he's about to open" | R1 |
| SM-3.1 | JRN-01.2: Mark Consumed | Epic 3 — Consumption | US-3.1: Mark a Bottle as Consumed | JTBD-01.3 → "Marking a bottle consumed and entering a tasting note is a single continuous flow completed in under 2 minutes; bottle moves out of active count immediately" | R1 |
| SM-3.3 | JRN-01.2: Save & Return | Epic 3 — Consumption | US-3.3: View Consumed Bottles & Tasting History | JTBD-01.3 → "The consumed/journal view is accessible from the inventory screen and displays each entry with tasting date, rating, and notes ordered most-recent first" | R1 |
| SM-3.4 | JRN-01.2: Save & Return | Epic 3 — Consumption | US-3.4: Edit Tasting Notes After Initial Entry | JTBD-01.3 → "A previously recorded tasting note can be updated and saved from the consumed bottle detail view without re-marking the bottle consumed" | R1 |
| SM-3.5 | JRN-01.2: Mark Consumed | Epic 3 — Consumption | US-3.5: Restore a Consumed Bottle to Active | JTBD-01.1 → "A mistakenly consumed bottle is restored to active status in one confirmation step; active count updates immediately and the bottle reappears in the inventory" | R1 |
| SM-5.1 | JRN-01.3: Review Totals | Epic 5 — Statistics | US-5.1: View Cellar Summary Counts | JTBD-01.2 → "Total, active, and consumed counts are displayed on the Statistics screen, reachable in ≤2 taps from the home screen, updating automatically after every add or consume event" | R2 |
| SM-5.2 | JRN-01.3: Drill by Varietal / Region | Epic 5 — Statistics | US-5.2: View Breakdown by Varietal and Region | JTBD-01.2 → "Varietal and region breakdowns are visible on the same Statistics screen within one scroll; Marcus can identify the most underrepresented region within 60 seconds of opening the view" | R2 |
| SM-5.3 | JRN-01.3: Drill by Region | Epic 5 — Statistics | US-5.3: View Vintage Distribution and Producer Breakdown | JTBD-01.2 → "Vintage distribution and producer counts are visible in the Statistics view; no additional navigation required beyond the Statistics screen" | R2 |
| SM-4.3 | JRN-01.1: Enter Details | Epic 4 — Location | US-4.3: Sort the Inventory by Location | JTBD-01.1 → "Selecting 'Sort by location' groups all bottles from the same rack together in alphabetical order; can be combined with active filters" | R2 |

### 2B. Sophie Nguyen (PER-02) — Primary Journey Stages

**JRN-02.1:** Quick-Adding a Gifted Bottle · **JRN-02.2:** Recalling a Past Tasting Note

| SM-ID | Journey Stage | Epic | Story | NaC (derived from JTBD) | Release |
|-------|--------------|------|-------|--------------------------|---------|
| SM-0.2 | JRN-02.1: Initiate | Epic 0 — Bottle Entry | US-0.2: Quick-Add a Bottle with Minimal Fields | JTBD-02.1 → "A new bottle is added in under 60 seconds from cold app start by entering only name and vintage; all other fields are visually optional and do not block save" | R1 |
| SM-0.5 | JRN-02.1: Enter & Save | Epic 0 — Bottle Entry | US-0.5: Receive Validation Feedback on Invalid Input | JTBD-02.1 → "Invalid input shows an inline error beside the failing field; all other entered values are retained so Sophie can fix the one field without retyping" | R1 |
| SM-1.2 | JRN-02.1: Trigger | Epic 1 — Inventory Browsing | US-1.2: Toggle Between List and Grid View | JTBD-02.3 → "Sophie can switch from list to grid layout in one tap and browse her collection visually to answer 'what do I have?' without scrolling a dense list" | R1 |
| SM-2.1 | JRN-02.2: Search | Epic 2 — Search & Filter | US-2.1: Search Bottles by Text | JTBD-02.2 → "Typing a partial producer or wine name returns matching results in real time (250ms debounce); Sophie locates a past bottle in under 10 seconds without recalling the exact name" | R1 |
| SM-3.2 | JRN-02.2: Decide | Epic 3 — Consumption | US-3.2: Log a Tasting Note and Star Rating | JTBD-02.2 → "A 1–5 star rating and free-text note are captured within 90 seconds of opening the tasting form; all tasting fields are optional so Sophie can rate without writing prose" | R1 |

### 2C. Daniel Reyes (PER-03) — Primary Journey Stages

**JRN-03.1:** Cellar-Side Pairing Decision · **JRN-03.2:** Logging a Case Delivery

| SM-ID | Journey Stage | Epic | Story | NaC (derived from JTBD) | Release |
|-------|--------------|------|-------|--------------------------|---------|
| SM-1.3 | JRN-03.1: Confirm Stock | Epic 1 — Inventory Browsing | US-1.3: Distinguish Consumed Bottles at a Glance | JTBD-03.2 → "Consumed bottles display a distinct visual indicator (greyed card / strikethrough / icon) on every list and grid card without requiring a tap into the detail view" | R1 |
| SM-2.2 | JRN-03.1: Apply Filter 1 + 2 | Epic 2 — Search & Filter | US-2.2: Filter by Varietal and Region | JTBD-03.1 → "Multi-filter (varietal AND region, with OR within each dimension) returns results in under 10 seconds; filter controls are accessible in ≤2 taps and operable one-handed; app functions offline" | R1 |
| SM-2.4 | JRN-03.1: Apply Filter | Epic 2 — Search & Filter | US-2.4: Clear All Active Filters | JTBD-03.1 → "A 'Clear all' control appears whenever any filter is active and resets the full unfiltered collection in a single tap, allowing Daniel to change his pairing criteria without restarting" | R1 |
| SM-4.1 | JRN-03.2: First Entry | Epic 4 — Location | US-4.1: Assign a Location to a Bottle | JTBD-03.3 → "A free-text rack/bin location (up to 200 chars) is saved with the bottle record in the minimal add flow; offline saves succeed without network; location is included when logging case arrivals" | R2 |
| SM-4.2 | JRN-03.1: Retrieve | Epic 4 — Location | US-4.2: See Location Prominently in Bottle Detail | JTBD-03.2 → "The rack/bin location is displayed near the top of the bottle detail view, readable at standard mobile font sizes in low-light conditions; visible for both active and consumed bottles" | R2 |
| SM-4.4 | JRN-03.1: Scan Results | Epic 4 — Location | US-4.4: Filter the Inventory by Location | JTBD-03.2 → "Entering a partial location string (e.g., 'Rack 3') returns all bottles in that area in under 200ms; filter combines with varietal/region filters for precise cellar-side queries" | R2 |

---

## 3. NaC Derivation Table

Full traceability chain: JTBD outcome → journey stage → NaC statement → story.

| NaC-ID | JTBD-ID | JTBD Outcome (hiring criterion) | Journey Stage | Natural Acceptance Criterion | Story |
|--------|---------|----------------------------------|---------------|------------------------------|-------|
| NaC-01 | JTBD-01.1 | Complete record in under 60 seconds | JRN-01.1: Initiate | A complete record (name, producer, vintage, varietal, region, location) is saved and visible in the inventory list in under 60 seconds from first tap on "Add Bottle" | US-0.1 |
| NaC-02 | JTBD-01.1 | Edited records save instantly | JRN-01.1: Enter Details | An edited bottle record reflects all updated values immediately in both list and detail view without a manual refresh | US-0.3 |
| NaC-03 | JTBD-01.1 | Consumed bottles clearly removed from active count | JRN-01.1: Verify | A deleted bottle does not appear in the inventory list, search results, or statistics after confirmation; active count decrements immediately | US-0.4 |
| NaC-04 | JTBD-01.1 | Complete inventory always current | JRN-01.1: Arrive | All bottles load within 200ms; newly added bottles appear at the top of the list in most-recently-added order with no manual refresh | US-1.1 |
| NaC-05 | JTBD-01.1 | No "ghost" or missing bottles | JRN-01.1: Verify | Tapping any list row opens the full bottle detail record — all metadata, location, and tasting entry — in a single navigation step with a back control to return | US-1.4 |
| NaC-06 | JTBD-01.3 | Tasting note and rating in under 2 minutes | JRN-01.2: Mark Consumed | Marking a bottle consumed and entering tasting notes and a rating completes in a single continuous flow in under 2 minutes; active count decrements immediately on confirmation | US-3.1 |
| NaC-07 | JTBD-01.3 | Journal builds value over time | JRN-01.2: Save & Return | The consumed/journal view is accessible from the inventory screen and shows every consumed bottle with tasting date, rating, and notes, ordered most-recent first | US-3.3 |
| NaC-08 | JTBD-01.3 | Previous entries searchable by bottle/varietal/rating | JRN-01.2: Save & Return | A saved tasting note can be edited from the consumed bottle detail view; updates are reflected immediately without re-consuming the bottle | US-3.4 |
| NaC-09 | JTBD-01.1 | No network required for add | JRN-01.2: Mark Consumed | A mistakenly consumed bottle is restored to active status in one confirmation step; active count updates and the bottle reappears in the list immediately | US-3.5 |
| NaC-10 | JTBD-01.3 | Locate bottle by search in under 5 seconds | JRN-01.2: Locate | Filtering to a single vintage year returns only matching bottles within 200ms, accessible in ≤2 taps — used to locate the bottle Marcus is about to open | US-2.3 |
| NaC-11 | JTBD-01.2 | Total bottles, varietal, region breakdown in a single dashboard view | JRN-01.3: Review Totals | Total, active, and consumed counts display on the Statistics screen in ≤2 taps from home; counts update automatically after any add, edit, or consume event | US-5.1 |
| NaC-12 | JTBD-01.2 | Reachable in no more than 2 taps | JRN-01.3: Drill by Varietal / Region | Varietal and region breakdowns are visible on the Statistics screen within one scroll; Marcus can identify the most underrepresented region within 60 seconds of opening the view | US-5.2 |
| NaC-13 | JTBD-01.2 | Statistics update automatically after any action | JRN-01.3: Drill by Region | Vintage distribution and producer counts display in the Statistics view; no additional navigation beyond the Statistics screen is required | US-5.3 |
| NaC-14 | JTBD-01.1 | Track physical cellar locations | JRN-01.1: Enter Details | Selecting "Sort by location" groups all bottles from the same rack together alphabetically; the sort is combinable with any active filter | US-4.3 |
| NaC-15 | JTBD-02.1 | New bottle in under 60 seconds, minimum fields | JRN-02.1: Initiate | A new bottle is added from cold app start in under 60 seconds by entering only name and vintage; all other fields are clearly marked optional and do not block save | US-0.2 |
| NaC-16 | JTBD-02.1 | Optional fields don't block save | JRN-02.1: Enter & Save | Invalid input (blank name, bad vintage year, future purchase date) shows an inline error beside only the failing field; all other entered values are preserved | US-0.5 |
| NaC-17 | JTBD-02.3 | Filter accessible in 2 taps; results visible without navigating away | JRN-02.1: Trigger | Sophie can switch from list to grid layout in one tap; grid cards show bottle name, producer, and vintage at minimum; the selected layout persists for the session | US-1.2 |
| NaC-18 | JTBD-02.2 | Search past tasting entries by wine name, varietal, or producer in under 10 seconds | JRN-02.2: Search | Typing a partial name or producer returns real-time results (250ms debounce, 200ms response); Sophie locates a past consumed bottle in under 10 seconds without the exact name | US-2.1 |
| NaC-19 | JTBD-02.2 | Rating and notes visible without opening detail | JRN-02.2: Decide | A 1–5 star rating and free-text tasting note are captured within 90 seconds of opening the tasting form; all tasting fields are optional | US-3.2 |
| NaC-20 | JTBD-03.2 | Consumed status glanceable on list card | JRN-03.1: Confirm Stock | Consumed bottles show a distinct visual state (greyed / strikethrough / icon) on every list and grid card without requiring a tap into the detail view | US-1.3 |
| NaC-21 | JTBD-03.1 | Multi-filter returns results in under 10 seconds, offline | JRN-03.1: Apply Filter 1 + 2 | Varietal AND region multi-filter (OR within each dimension) returns results in under 10 seconds; filter controls accessible in ≤2 taps, operable one-handed; works fully offline | US-2.2 |
| NaC-22 | JTBD-03.1 | Filter controls accessible in 2 taps | JRN-03.1: Apply Filter | "Clear all" appears whenever any filter is active and resets to the full unfiltered collection in a single tap | US-2.4 |
| NaC-23 | JTBD-03.3 | Location field in minimal entry flow; saves offline | JRN-03.2: First Entry | A free-text rack/bin location (≤200 chars) saves with the bottle record in the minimal add flow; the app saves to local storage immediately with no network required | US-4.1 |
| NaC-24 | JTBD-03.2 | Location visible in list without opening detail | JRN-03.1: Retrieve | The rack/bin location is displayed near the top of the bottle detail view, readable at standard mobile font sizes in low-light; visible for both active and consumed bottles | US-4.2 |
| NaC-25 | JTBD-03.2 | One-handed interaction | JRN-03.1: Scan Results | Entering a partial location string (e.g., "Rack 3") returns all matching bottles in under 200ms; filter combines with varietal and region filters | US-4.4 |

---

## 4. Release Planning

### R1 — "Core Cellar" (MVP)

**Theme:** Deliver complete, end-to-end journeys for all three personas — add bottles, browse the collection, search and filter, and record tasting notes. No journey is left half-complete.

**Stories (18 total, all P0):**

| Story | Title | Primary Persona | JTBD Addressed |
|-------|-------|-----------------|----------------|
| US-0.1 | Add a New Bottle to the Collection | Marcus | JTBD-01.1 |
| US-0.2 | Quick-Add a Bottle with Minimal Fields | Sophie | JTBD-02.1 |
| US-0.3 | Edit an Existing Bottle Record | Marcus | JTBD-01.1 |
| US-0.4 | Delete a Bottle from the Inventory | Marcus | JTBD-01.1 |
| US-0.5 | Receive Validation Feedback on Invalid Input | Sophie | JTBD-02.1 |
| US-1.1 | View All Bottles in a List | Marcus | JTBD-01.1 |
| US-1.2 | Toggle Between List and Grid View | Sophie | JTBD-02.3 |
| US-1.3 | Distinguish Consumed Bottles at a Glance | Daniel | JTBD-03.2 |
| US-1.4 | Navigate to a Bottle's Detail View | Marcus | JTBD-01.1 |
| US-2.1 | Search Bottles by Text | Sophie | JTBD-02.2 |
| US-2.2 | Filter by Varietal and Region | Daniel | JTBD-03.1 |
| US-2.3 | Filter by Vintage Year or Range | Marcus | JTBD-01.3 |
| US-2.4 | Clear All Active Filters | Daniel | JTBD-03.1 |
| US-3.1 | Mark a Bottle as Consumed | Marcus | JTBD-01.3 |
| US-3.2 | Log a Tasting Note and Star Rating | Sophie | JTBD-02.2 |
| US-3.3 | View Consumed Bottles and Tasting History | Marcus | JTBD-01.3 |
| US-3.4 | Edit Tasting Notes After Initial Entry | Marcus | JTBD-01.3 |
| US-3.5 | Restore a Consumed Bottle to Active Status | Marcus | JTBD-01.1 |

**R1 Personas Served:** PER-01 Marcus ✅ · PER-02 Sophie ✅ · PER-03 Daniel ✅

**R1 JTBD Addressed:** JTBD-01.1 ✅ · JTBD-01.3 ✅ · JTBD-02.1 ✅ · JTBD-02.2 ✅ · JTBD-02.3 ✅ · JTBD-03.1 ✅ · JTBD-03.2 ✅

**R1 JTBD Partially Addressed (no stories yet):** JTBD-01.2 (stats/composition) · JTBD-03.3 (offline case logging — location field deferred to R2)

**Complete journeys delivered by R1:**

| Journey | Stages Covered | Status |
|---------|---------------|--------|
| JRN-01.1: Marcus logs a new bottle | Arrive → Initiate → Enter Details → Save & Repeat → Verify | ✅ Complete (without location pre-fill — R2) |
| JRN-01.2: Marcus consumes + records tasting note | Locate → Open Record → Mark Consumed → Record Notes → Save & Return | ✅ Complete |
| JRN-02.1: Sophie quick-adds a gifted bottle | Trigger → Initiate → Enter & Save → Optional Add | ✅ Complete |
| JRN-02.2: Sophie recalls a past tasting note | Trigger → Search → Read Result → Decide → Close | ✅ Complete |
| JRN-03.1: Daniel pairing decision (cellar-side) | Enter Cellar → Apply Filter 1 → Apply Filter 2 → Scan Results → Confirm Stock → Retrieve | ✅ Complete (location on card needs R2 for full retrieval UX) |
| JRN-01.3: Marcus weekly review | Navigate → Review Totals → Drill by Varietal → Drill by Region → Act on Insight | ❌ Deferred to R2 |
| JRN-03.2: Daniel logs case delivery | Initiate → First Entry → Rapid Repeat → Verify Count → Return Upstairs | ⚠️ Partially deferred (location field in R2) |

---

### R2 — "Cellar Intelligence" (Post-MVP)

**Theme:** Add physical location tracking and inventory statistics, completing the cellar-side retrieval experience for Daniel and the composition planning workflow for Marcus. JRN-01.3 and JRN-03.2 are fully delivered.

**Stories (7 total, all P1):**

| Story | Title | Primary Persona | JTBD Addressed |
|-------|-------|-----------------|----------------|
| US-4.1 | Assign a Location to a Bottle | Daniel | JTBD-03.3 |
| US-4.2 | See Location Prominently in Bottle Detail | Daniel | JTBD-03.2 |
| US-4.3 | Sort the Inventory by Location | Marcus | JTBD-01.1 |
| US-4.4 | Filter the Inventory by Location | Daniel | JTBD-03.2 |
| US-5.1 | View Cellar Summary Counts | Marcus | JTBD-01.2 |
| US-5.2 | View Breakdown by Varietal and Region | Marcus | JTBD-01.2 |
| US-5.3 | View Vintage Distribution and Producer Breakdown | Marcus | JTBD-01.2 |

**R2 Personas Served:** PER-01 Marcus ✅ · PER-03 Daniel ✅ · PER-02 Sophie (secondary benefit from stats)

**R2 JTBD Addressed:** JTBD-01.2 ✅ · JTBD-03.2 (deepened) ✅ · JTBD-03.3 ✅

**Complete journeys delivered by R2:**

| Journey | Stages Completed in R2 | Status |
|---------|------------------------|--------|
| JRN-01.3: Marcus weekly review | Navigate → Review Totals → Drill by Varietal → Drill by Region → Act on Insight | ✅ Complete |
| JRN-03.1: Daniel pairing decision | Retrieve (location on list card via US-4.2 detail + US-4.4 filter) | ✅ Deepened |
| JRN-03.2: Daniel logs case delivery | First Entry (location field from US-4.1) + Rapid Repeat + Verify Count | ✅ Complete |

---

## 5. Coverage Analysis

### 5A. Persona Coverage by Release

| Persona | R1 | R2 | Notes |
|---------|----|----|-------|
| PER-01 Marcus | ✅ Full core journeys | ✅ Stats + location sort complete JTBD-01.2 | Primary beneficiary of both releases |
| PER-02 Sophie | ✅ All primary stories | — (secondary benefit from stats) | All Sophie JTBD fully addressed in R1 |
| PER-03 Daniel | ✅ Filter + consumed indicator | ✅ Location assignment + filter complete JTBD-03.3 | JTBD-03.2 deepened in R2 with location detail |

### 5B. JTBD Coverage by Release

| JTBD-ID | Job Statement (abbreviated) | R1 | R2 | Gap? |
|---------|----------------------------|----|----|------|
| JTBD-01.1 | Complete, current inventory | US-0.1, 0.3, 0.4, 1.1, 1.4, 3.5 | US-4.3 | ✅ No gap |
| JTBD-01.2 | Cellar composition at a glance | — | US-5.1, 5.2, 5.3 | ✅ No gap (R2) |
| JTBD-01.3 | Personal tasting journal | US-2.3, 3.1, 3.2, 3.3, 3.4 | — | ✅ No gap |
| JTBD-02.1 | Frictionless mobile capture | US-0.2, 0.5 | — | ✅ No gap |
| JTBD-02.2 | Taste memory & repeat-buy | US-2.1, 3.2 | — | ✅ No gap |
| JTBD-02.3 | Instant collection awareness | US-1.2, 2.2, 2.4 | — | ✅ No gap |
| JTBD-03.1 | Pairing decision under 30 seconds | US-2.2, 2.4 | — | ✅ No gap |
| JTBD-03.2 | Location + stock confirmation before walking | US-1.3 | US-4.2, 4.4 | ✅ No gap (R2 deepens) |
| JTBD-03.3 | Quick arrival logging without leaving cellar | — (partial) | US-4.1 | ✅ No gap (R2) |

### 5C. Journey Stage Coverage

| Journey | Stages | R1 Coverage | R2 Coverage | Gap / Note |
|---------|--------|-------------|-------------|------------|
| JRN-01.1 | 5 stages | Arrive, Initiate, Enter Details, Save & Repeat, Verify | Save & Repeat enriched (US-4.3 location sort) | ⚠️ "Save & Add Another" pre-fill is a UX design pattern — not modeled as a separate story; should be implemented within US-0.1/0.2 |
| JRN-01.2 | 5 stages | Locate, Open Record, Mark Consumed, Record Notes, Save & Return | — | ✅ Fully covered |
| JRN-01.3 | 5 stages | — | Navigate, Review Totals, Drill by Varietal, Drill by Region, Act on Insight | ⚠️ "Copy summary" export (Act on Insight) has no story — flagged as roadmap opportunity |
| JRN-02.1 | 4 stages | Trigger, Initiate, Enter & Save, Optional Add | — | ✅ Fully covered |
| JRN-02.2 | 5 stages | Trigger, Search, Read Result, Decide, Close | — | ⚠️ "Buy Again" intent tag and "Log a new one?" shortcut (Close stage) have no stories — roadmap |
| JRN-03.1 | 6 stages | Enter Cellar, Apply Filter 1, Apply Filter 2, Scan Results, Confirm Stock | Retrieve (location on card, location filter) | ⚠️ Location on list card (not just detail) is called out in JOURNEYS as a critical UX pattern; US-4.2 covers detail view; a separate story for list-card location display may be needed in R2 backlog |
| JRN-03.2 | 5 stages | Initiate (offline save — NFR), Rapid Repeat (Save & Add Another UX), Verify Count | First Entry (US-4.1 location), Return Upstairs | ⚠️ Offline-first write model is an NFR (PRD §6); no dedicated story — should be an implementation constraint on US-0.1 and US-0.2 |

### 5D. Gap Analysis

**Unmapped stories (orphans):** None. All 25 user stories are placed on the map.

**JTBD outcomes without NaC:** None. All 9 JTBD outcomes have at least one derived NaC.

**Journey stages without story coverage:**

| Stage | Journey | Gap Type | Recommendation |
|-------|---------|----------|----------------|
| Act on Insight — "Copy summary" export | JRN-01.3 | Missing story | Add to R2 backlog: US-5.4 Export cellar summary as plain text |
| Close — "Log a new one?" shortcut | JRN-02.2 | Missing story | Add to R3/roadmap: US-0.6 Quick-duplicate from consumed record |
| Scan Results — location on list card | JRN-03.1 | Partial coverage | US-4.2 covers detail; consider US-4.5 to surface location on inventory list card |
| Save & Repeat — "Save & Add Another" | JRN-01.1 / JRN-03.2 | UX pattern gap | Implement within US-0.1 acceptance criteria or add US-0.6 Save & Add Another with pre-fill |

**Cross-journey NaC patterns lacking explicit story coverage:**

| Pattern | Journeys | Status |
|---------|----------|--------|
| Offline-first write model | JRN-03.1, JRN-03.2 | NFR on PRD §6 — no story needed; must be acceptance criterion on US-0.1, US-0.2, and US-2.2 |
| Fuzzy / search-as-you-type | JRN-02.2 | US-2.1 AC covers case-insensitive substring; true fuzzy matching is a stretch goal |
| Consumed status as glanceable indicator | JRN-01.2, JRN-03.1 | US-1.3 covers this fully |
| Rating + note preview on result card | JRN-02.2: Read Result | US-2.1 AC does not explicitly require preview on card; should be added to US-2.1 or US-3.3 AC |

---

## 6. NaC-to-Acceptance-Criteria Alignment

This section verifies that each NaC aligns with — and is checkable against — the existing UserStory acceptance criteria.

| NaC-ID | Story | NaC Statement (condensed) | AC Alignment | Notes |
|--------|-------|--------------------------|--------------|-------|
| NaC-01 | US-0.1 | Complete record saved in under 60 seconds | ✅ AC: "A complete record can be submitted in under 60 seconds" | Exact match |
| NaC-02 | US-0.3 | Edited record reflects immediately in list + detail | ✅ AC: "Saving a valid edit updates the record and immediately reflects new values in both the detail view and the inventory list" | Exact match |
| NaC-03 | US-0.4 | Deleted bottle absent from inventory, search, statistics | ✅ AC: "After deletion the user is returned to the inventory list and the deleted bottle does not appear anywhere (list, search, statistics)" | Exact match |
| NaC-04 | US-1.1 | All bottles load in 200ms; new entries appear at top | ✅ AC: "The inventory screen loads within 200ms for collections up to 1,000 bottles"; default sort is most recently added | Exact match |
| NaC-05 | US-1.4 | Tapping bottle opens full detail with back control | ✅ AC: "The detail view loads the full record: all metadata fields, location, and tasting entry (if consumed)" | Exact match |
| NaC-06 | US-3.1 | Consume + note in single flow under 2 minutes | ✅ AC: "Triggering the action presents an optional tasting entry form before confirming"; no time SLA — NaC strengthens this | NaC adds 2-minute constraint; recommend adding to US-3.1 AC |
| NaC-07 | US-3.3 | Consumed/journal view shows date, rating, notes, most recent first | ✅ AC: "The consumed list shows each bottle ordered by consumption date (most recent first)" and "tasting date and rating (if recorded)" | Exact match |
| NaC-08 | US-3.4 | Edit tasting note from detail view without re-consuming | ✅ AC: "Saving valid edits updates the tasting entry and reflects changes immediately in the detail view" | Exact match |
| NaC-09 | US-3.5 | Restore in one confirmation; active count updates immediately | ✅ AC: "Confirming sets the bottle back to active status…bottle reappears in the active inventory count immediately" | Exact match |
| NaC-10 | US-2.3 | Vintage filter returns results in 200ms, ≤2 taps | ✅ AC: Vintage from/to inputs present; 200ms covered by NFR; ≤2 taps covered by US-2.2 filter panel | NaC adds 2-tap constraint; recommend adding to US-2.3 AC |
| NaC-11 | US-5.1 | Total/active/consumed counts in ≤2 taps; auto-updates | ✅ AC: "The screen is accessible from the main navigation in ≤2 taps"; "Counts update to reflect the current state" | Exact match |
| NaC-12 | US-5.2 | Varietal + region breakdown visible in one scroll; gap identifiable in 60 seconds | ✅ AC: "Both breakdowns are visible on the same screen (or within one scroll) without additional navigation" | 60-second criterion from JTBD-01.2 not in AC; recommend adding |
| NaC-13 | US-5.3 | Vintage and producer visible without extra navigation | ✅ AC: Visual representation used; no navigation beyond Statistics screen implied | Partial — AC doesn't explicitly forbid extra navigation; NaC clarifies intent |
| NaC-14 | US-4.3 | Sort by location groups same-rack bottles alphabetically; combinable with filters | ✅ AC: "Selecting this sort orders bottles alphabetically by their location string"; "The sort can be combined with active filters" | Exact match |
| NaC-15 | US-0.2 | New bottle in ≤60 seconds, name + vintage only | ✅ AC: "A minimal add (name + vintage only) completes in under 60 seconds on mobile" | Exact match |
| NaC-16 | US-0.5 | Inline error beside failing field; other values preserved | ✅ AC: "Each error message is displayed inline beside the failing field"; "All other previously entered field values are retained" | Exact match |
| NaC-17 | US-1.2 | Switch to grid in one tap; shows name, producer, vintage | ✅ AC: "Tapping the toggle switches between list and grid layouts"; "Grid cards show: bottle name, producer, and vintage at minimum" | Exact match |
| NaC-18 | US-2.1 | Partial name returns real-time results in 200ms; consumed records included | ✅ AC: "Results update in real time after a 250ms debounce"; "Search results return within 200ms" | NaC adds consumed-record inclusion — not explicit in AC; recommend adding |
| NaC-19 | US-3.2 | Star rating + note in ≤90 seconds | ✅ AC: "A complete tasting note and star rating can be captured in under 90 seconds on mobile" | Exact match |
| NaC-20 | US-1.3 | Consumed indicator visible on list + grid without tapping | ✅ AC: "The consumed indicator is visible in both list and grid views without tapping into the detail view" | Exact match |
| NaC-21 | US-2.2 | Multi-filter in ≤10 seconds; 2 taps; one-handed; offline | ✅ AC: "Filtered results update immediately"; "accessible in ≤2 taps"; "usable one-handed" | 10-second offline performance criterion from JTBD-03.1 not in AC; recommend adding |
| NaC-22 | US-2.4 | Clear all in one tap; hidden when no filters active | ✅ AC: "Tapping 'Clear all' removes all active filters and the search query simultaneously"; "'Clear all' control is hidden when no filters are active" | Exact match |
| NaC-23 | US-4.1 | Location field in minimal flow; saves offline immediately | ✅ AC: "The location field is present in the bottle add and edit forms (optional field, up to 200 characters)" | Offline save not in AC — must be added to US-4.1 (and US-0.1/0.2 as NFR) |
| NaC-24 | US-4.2 | Location near top of detail view; readable in low light | ✅ AC: "The location field is displayed near the top of the bottle detail view (not buried below the fold)"; "readable at standard mobile font sizes" | Low-light readability implied; exact match |
| NaC-25 | US-4.4 | Partial location string returns matches in 200ms; combinable | ✅ AC: "Entering a location string returns all bottles whose location contains that string"; "The location filter can be combined with other filters" | 200ms performance from NFR; combinable exact match |

**AC gaps requiring story updates (recommended):**

| Story | Gap | Recommended Addition |
|-------|-----|----------------------|
| US-3.1 | No time constraint on consume + note flow | Add: "The consume + note flow completes in a single continuous screen; no navigation between mark-consumed and note entry" |
| US-5.2 | No 60-second success criterion | Add: "Marcus can identify the most underrepresented region within 60 seconds of opening the Statistics view" |
| US-2.2 | No explicit 10-second offline performance criterion | Add: "Multi-filter results return in under 10 seconds on a device with no network connection and 300+ bottles" |
| US-2.1 | Consumed records not explicitly included in search | Add: "Search results include both active and consumed bottles, with consumed state visually differentiated in the result list" |
| US-4.1 | Offline save not explicit | Add: "The app saves the bottle record to local storage immediately; no network connection is required for the save to succeed" |

---

## 7. Story Map Summary

| Metric | Value |
|--------|-------|
| Total stories mapped | 25 / 25 |
| Orphan stories | 0 |
| Stories in R1 (MVP) | 18 |
| Stories in R2 (Post-MVP) | 7 |
| JTBD outcomes with NaC | 9 / 9 |
| Journey stages with story coverage | 28 / 32 |
| Uncovered journey stages (gap) | 4 (Act on Insight export, Close shortcut, location on list card, Save & Add Another) |
| NaC fully aligned to AC | 20 / 25 |
| NaC with recommended AC additions | 5 |
| Personas served in R1 | 3 / 3 |
| Personas served in R2 | 2 / 3 (Sophie secondary) |

---

*Generated: 2026-05-15 | Model: claude-sonnet-4-6*
