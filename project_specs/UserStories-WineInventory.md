# User Stories — Wine Inventory App

**Project:** WineInventory  
**Date:** 2026-05-15  
**Status:** Draft  
**Version:** 1.0  
**Source PRD:** PRD-WineInventory.md v1.0  
**Source FRD:** FRD-WineInventory.md v1.0  
**Source Personas:** PERSONAS-WineInventory.md v1.0  

---

## Personas

| ID | Name | Role |
|---|---|---|
| PER-01 | Marcus Delacroix | The Organized Collector |
| PER-02 | Sophie Nguyen | The Casual Enthusiast |
| PER-03 | Daniel Reyes | The Cellar-Side Decider |

---

## Priority Definitions

| Priority | Label | Description |
|---|---|---|
| P0 | MVP Critical | Must ship in v1; blocking for launch |
| P1 | High | Important; may ship post-MVP |
| P2 | Medium | Valuable but deferrable |
| P3 | Low | Nice to have; low urgency |

---

## Epic 0: Bottle Entry & Management (F0)

The foundational data-entry feature. Without bottle records, no other feature has data to operate on. Covers creating, editing, and deleting bottle records with validated metadata.

---

### US-0.1: Add a New Bottle to the Collection

**As a** Marcus Delacroix (Organized Collector), **I want to** add a new wine bottle with its name, producer, vintage, varietal, and region, **so that** it is permanently recorded in my inventory before I forget the details.

**Acceptance Criteria:**
- [ ] An "Add Bottle" action (button/FAB) is accessible from the main inventory screen in ≤2 taps
- [ ] The form presents fields: name (required), vintage (required), producer, varietal, region, notes, purchase date, purchase price, quantity, location
- [ ] Submitting with a valid name and vintage saves the record and navigates to the new bottle's detail view
- [ ] The new bottle immediately appears in the inventory list after creation
- [ ] A complete record (all fields filled) can be submitted in under 60 seconds

**Priority:** P0 | **Feature Ref:** F0

---

### US-0.2: Quick-Add a Bottle with Minimal Fields

**As a** Sophie Nguyen (Casual Enthusiast), **I want to** add a bottle by filling in only the wine name and vintage, **so that** I can log it immediately without being slowed down by optional fields.

**Acceptance Criteria:**
- [ ] Form submits successfully with only `name` and `vintage` populated
- [ ] All fields beyond name and vintage are clearly marked as optional
- [ ] A minimal add (name + vintage only) completes in under 60 seconds on mobile
- [ ] The saved bottle appears in the inventory list with the provided values and blanks for omitted fields

**Priority:** P0 | **Feature Ref:** F0

---

### US-0.3: Edit an Existing Bottle Record

**As a** Marcus Delacroix (Organized Collector), **I want to** edit any field on an existing bottle record at any time, **so that** I can correct mistakes or update details as my knowledge of a wine improves.

**Acceptance Criteria:**
- [ ] An "Edit" action is accessible from a bottle's detail view
- [ ] The edit form is pre-populated with all current field values
- [ ] Any field (including required fields) can be modified
- [ ] Saving a valid edit updates the record and immediately reflects new values in both the detail view and the inventory list
- [ ] Saving an invalid edit (e.g., blank name) shows inline field-level error messages and keeps the form open

**Priority:** P0 | **Feature Ref:** F0

---

### US-0.4: Delete a Bottle from the Inventory

**As a** Marcus Delacroix (Organized Collector), **I want to** permanently delete a bottle record, **so that** my inventory stays accurate and does not show bottles I no longer own or entered by mistake.

**Acceptance Criteria:**
- [ ] A "Delete" action is accessible from a bottle's detail view
- [ ] A confirmation dialog appears: "Delete [bottle name]? This cannot be undone."
- [ ] Confirming deletion permanently removes the bottle record and any associated tasting entry
- [ ] After deletion the user is returned to the inventory list and the deleted bottle does not appear anywhere (list, search, statistics)
- [ ] Cancelling the confirmation dialog leaves the bottle record unchanged

**Priority:** P0 | **Feature Ref:** F0

---

### US-0.5: Receive Validation Feedback on Invalid Input

**As a** Sophie Nguyen (Casual Enthusiast), **I want to** see clear error messages when I submit an invalid bottle form, **so that** I know exactly which field to fix without losing my other entered data.

**Acceptance Criteria:**
- [ ] Submitting with a blank name shows: "Bottle name is required."
- [ ] Submitting a vintage outside 1800–current year shows: "Vintage must be between 1800 and [current year]."
- [ ] Submitting a future purchase date shows: "Purchase date cannot be in the future."
- [ ] Each error message is displayed inline beside the failing field
- [ ] All other previously entered field values are retained when validation fails

**Priority:** P0 | **Feature Ref:** F0

---

## Epic 1: Inventory Browsing (F1)

The primary everyday interface — the view users open when they want to see their cellar at a glance. Supports both list and grid layouts with at-a-glance bottle summaries and consumed-status indicators.

---

### US-1.1: View All Bottles in a List

**As a** Marcus Delacroix (Organized Collector), **I want to** see all my bottles in a scrollable list sorted by most recently added, **so that** I can review my full collection and quickly spot new additions.

**Acceptance Criteria:**
- [ ] The inventory screen loads within 200ms for collections of up to 1,000 bottles
- [ ] Each list row shows: bottle name, producer (if present), vintage year, and varietal (if present)
- [ ] The default sort order is most recently added first (descending `created_at`)
- [ ] When the collection is empty, the screen shows: "No bottles in your cellar yet. Tap + to add your first bottle."
- [ ] Collections exceeding 50 bottles paginate or virtual-scroll, with additional records loading as the user scrolls

**Priority:** P0 | **Feature Ref:** F1

---

### US-1.2: Toggle Between List and Grid View

**As a** Sophie Nguyen (Casual Enthusiast), **I want to** switch between a list and a card grid layout, **so that** I can browse my collection visually when I want a more relaxed view of what I own.

**Acceptance Criteria:**
- [ ] A view mode toggle (list icon / grid icon) is visible on the inventory screen
- [ ] Tapping the toggle switches between list and grid layouts without re-fetching data
- [ ] Grid cards show: bottle name, producer, and vintage at minimum
- [ ] The selected view mode persists for the current session

**Priority:** P0 | **Feature Ref:** F1

---

### US-1.3: Distinguish Consumed Bottles at a Glance

**As a** Daniel Reyes (Cellar-Side Decider), **I want to** see immediately which bottles have been consumed directly in the list, **so that** I do not walk to a rack to retrieve a bottle I have already opened.

**Acceptance Criteria:**
- [ ] Consumed bottles are visually differentiated from active bottles (e.g., greyed out, strikethrough, or "consumed" tag)
- [ ] The consumed indicator is visible in both list and grid views without tapping into the detail view
- [ ] Active and consumed bottles appear together by default; the user can optionally filter to show only active or only consumed bottles

**Priority:** P0 | **Feature Ref:** F1

---

### US-1.4: Navigate to a Bottle's Detail View

**As a** Marcus Delacroix (Organized Collector), **I want to** tap on any bottle in the list to open its full detail record, **so that** I can see all metadata, notes, and location information for that bottle.

**Acceptance Criteria:**
- [ ] Tapping any bottle row or grid card navigates to the bottle detail view
- [ ] The detail view loads the full record: all metadata fields, location, and tasting entry (if consumed)
- [ ] A back navigation control returns the user to the inventory list

**Priority:** P0 | **Feature Ref:** F1

---

## Epic 2: Search & Filter (F2)

The fast-lookup system that lets users narrow the collection by any combination of attributes. Critical for all three personas, especially for cellar-side pairing decisions.

---

### US-2.1: Search Bottles by Text

**As a** Sophie Nguyen (Casual Enthusiast), **I want to** type a word into a search bar and instantly see matching bottles, **so that** I can find a specific wine by name or producer without scrolling the entire list.

**Acceptance Criteria:**
- [ ] A search input is accessible from the inventory screen in ≤2 taps
- [ ] Results update in real time after a 250ms debounce as the user types
- [ ] Search matches against bottle name, producer, and notes fields (case-insensitive substring)
- [ ] A matching bottle count is displayed (e.g., "12 bottles found") while any filter is active
- [ ] When no bottles match, the screen shows: "No bottles match your search. Try adjusting the filters."
- [ ] Search results return within 200ms for collections up to 1,000 bottles

**Priority:** P0 | **Feature Ref:** F2

---

### US-2.2: Filter by Varietal and Region for a Pairing Decision

**As a** Daniel Reyes (Cellar-Side Decider), **I want to** apply varietal and region filters simultaneously, **so that** I can narrow to 3–5 candidates for a pairing decision in under 10 seconds while standing in the cellar.

**Acceptance Criteria:**
- [ ] Varietal and region filter controls are accessible in ≤2 taps from the inventory screen
- [ ] Multiple values within the same filter dimension are combined with OR logic (e.g., Cabernet OR Merlot)
- [ ] Filters across different dimensions are combined with AND logic (e.g., varietal = Cabernet AND region = Bordeaux)
- [ ] Filtered results update immediately when a filter value is selected
- [ ] The filter UI is usable one-handed on a phone screen

**Priority:** P0 | **Feature Ref:** F2

---

### US-2.3: Filter by Vintage Year or Year Range

**As a** Marcus Delacroix (Organized Collector), **I want to** filter my collection by a single vintage year or a year range, **so that** I can find all bottles from a specific harvest or era I am interested in.

**Acceptance Criteria:**
- [ ] The filter panel includes `vintage from` and `vintage to` year inputs
- [ ] Entering only `vintage from` returns bottles with vintage ≥ that year
- [ ] Entering only `vintage to` returns bottles with vintage ≤ that year
- [ ] Entering both returns bottles with vintage within the inclusive range
- [ ] Submitting a `vintage from` greater than `vintage to` shows: "vintage_from must not exceed vintage_to."
- [ ] Vintage inputs only accept values between 1800 and the current year

**Priority:** P0 | **Feature Ref:** F2

---

### US-2.4: Clear All Active Filters

**As a** Daniel Reyes (Cellar-Side Decider), **I want to** clear all active search and filter selections with a single tap, **so that** I can reset to the full collection quickly when my pairing decision changes.

**Acceptance Criteria:**
- [ ] A "Clear all" / "Reset" control is visible whenever any filter or search query is active
- [ ] Tapping "Clear all" removes all active filters and the search query simultaneously
- [ ] After clearing, the full unfiltered collection is displayed with the default sort order
- [ ] The "Clear all" control is hidden when no filters are active

**Priority:** P0 | **Feature Ref:** F2

---

## Epic 3: Consumption Tracking & Tasting Notes (F3)

Transforms the app from a static inventory into a personal tasting journal. Users mark bottles as consumed, log tasting entries (date, notes, star rating), and revisit their history.

---

### US-3.1: Mark a Bottle as Consumed

**As a** Marcus Delacroix (Organized Collector), **I want to** mark a bottle as consumed when I open it, **so that** it is removed from my active inventory count and I always know exactly how many bottles I still have.

**Acceptance Criteria:**
- [ ] A "Mark as Consumed" action is accessible from an active bottle's detail view
- [ ] Triggering the action presents an optional tasting entry form (date, notes, rating) before confirming
- [ ] Confirming sets the bottle as consumed and removes it from the active inventory count
- [ ] The bottle remains visible in the collection with a consumed indicator
- [ ] Attempting to consume an already-consumed bottle returns a conflict error and does not duplicate the entry

**Priority:** P0 | **Feature Ref:** F3

---

### US-3.2: Log a Tasting Note and Star Rating

**As a** Sophie Nguyen (Casual Enthusiast), **I want to** record a quick tasting note and a 1–5 star rating immediately after opening a bottle, **so that** I can remember what I thought of it when I consider buying it again later.

**Acceptance Criteria:**
- [ ] The tasting entry form includes: tasting date (defaults to today), free-text notes field (up to 5,000 characters), and a 1–5 star rating control
- [ ] All tasting fields are optional — consuming a bottle is possible without entering any tasting data
- [ ] A complete tasting note and star rating can be captured in under 90 seconds on mobile
- [ ] Submitting a future tasting date shows: "Tasting date cannot be in the future."
- [ ] Submitting a rating outside 1–5 shows: "Rating must be between 1 and 5."

**Priority:** P0 | **Feature Ref:** F3

---

### US-3.3: View Consumed Bottles and Tasting History

**As a** Marcus Delacroix (Organized Collector), **I want to** view all the bottles I have consumed with their tasting dates and ratings, **so that** I can review my tasting history and learn my preferences over time.

**Acceptance Criteria:**
- [ ] A "Consumed" / "Journal" view or filter is accessible from the inventory screen
- [ ] The consumed list shows each bottle ordered by consumption date (most recent first)
- [ ] Each row shows: bottle name, vintage, producer, tasting date, and rating (if recorded)
- [ ] Tapping a consumed bottle opens its full detail view including tasting notes
- [ ] The consumed count is displayed separately from the active bottle count

**Priority:** P0 | **Feature Ref:** F3

---

### US-3.4: Edit Tasting Notes After Initial Entry

**As a** Marcus Delacroix (Organized Collector), **I want to** edit the tasting notes and rating on a consumed bottle after the initial entry, **so that** I can refine my impressions as I reflect on the wine over the following days.

**Acceptance Criteria:**
- [ ] An "Edit Tasting Notes" action is accessible from a consumed bottle's detail view
- [ ] The edit form is pre-populated with the existing tasting date, notes, and rating
- [ ] Saving valid edits updates the tasting entry and reflects changes immediately in the detail view
- [ ] The same validation rules apply as on initial entry (date not in future, rating 1–5, notes ≤ 5,000 chars)

**Priority:** P0 | **Feature Ref:** F3

---

### US-3.5: Restore a Consumed Bottle to Active Status

**As a** Marcus Delacroix (Organized Collector), **I want to** restore a bottle I mistakenly marked as consumed back to active status, **so that** my inventory remains accurate when I make an error.

**Acceptance Criteria:**
- [ ] A "Restore to Cellar" / "Mark as Active" action is accessible from a consumed bottle's detail view
- [ ] A confirmation dialog appears: "Restore [bottle name] to your active cellar?"
- [ ] Confirming sets the bottle back to active status, clears the consumed date, and deletes the associated tasting entry
- [ ] The bottle reappears in the active inventory count immediately
- [ ] Attempting to restore an already-active bottle returns a conflict error

**Priority:** P0 | **Feature Ref:** F3

---

## Epic 4: Bottle Location Tracking (F4)

Allows users to record a physical storage location for each bottle (rack, bin, shelf). Makes physical retrieval fast without searching the entire cellar. Post-MVP.

---

### US-4.1: Assign a Location to a Bottle

**As a** Daniel Reyes (Cellar-Side Decider), **I want to** assign a free-text location label to each bottle (e.g., "Rack 3 / Bin 12"), **so that** I know exactly where to go in the cellar once I have decided which bottle to open.

**Acceptance Criteria:**
- [ ] The `location` field is present in the bottle add and edit forms (optional field, up to 200 characters)
- [ ] Any UTF-8 text is accepted including slashes, dashes, and spaces
- [ ] A saved location is immediately reflected in the bottle's detail view
- [ ] Exceeding 200 characters shows: "Location must not exceed 200 characters."
- [ ] Leaving the location blank saves the bottle without a location (no error)

**Priority:** P1 | **Feature Ref:** F4

---

### US-4.2: See Location Prominently in the Bottle Detail View

**As a** Daniel Reyes (Cellar-Side Decider), **I want to** see the bottle's location displayed prominently in the detail view, **so that** I can read it at a glance on my phone in a dim cellar without hunting for the information.

**Acceptance Criteria:**
- [ ] The `location` field is displayed near the top of the bottle detail view (not buried below the fold)
- [ ] Location is shown for both active and consumed bottles
- [ ] If no location has been recorded, a placeholder reads: "No location recorded."
- [ ] The location text is readable at standard mobile font sizes without zooming

**Priority:** P1 | **Feature Ref:** F4

---

### US-4.3: Sort the Inventory by Location

**As a** Marcus Delacroix (Organized Collector), **I want to** sort my inventory list alphabetically by location, **so that** all bottles in the same rack or area are grouped together for easy cellar reorganization.

**Acceptance Criteria:**
- [ ] "Sort by location" is available as a sort option in the inventory browsing view
- [ ] Selecting this sort orders bottles alphabetically by their location string (ascending)
- [ ] Bottles with no location recorded are sorted to the end of the list
- [ ] The sort can be combined with active filters

**Priority:** P1 | **Feature Ref:** F4

---

### US-4.4: Filter the Inventory by Location

**As a** Daniel Reyes (Cellar-Side Decider), **I want to** filter my collection by a location keyword (e.g., "Rack 3"), **so that** I can see all bottles in a specific area of the cellar when I am reorganizing or deciding what to open from that section.

**Acceptance Criteria:**
- [ ] A location filter input is available in the filter panel
- [ ] Entering a location string returns all bottles whose location contains that string (case-insensitive substring match)
- [ ] The location filter can be combined with other filters (varietal, region, etc.)
- [ ] Clearing the location filter field restores the full (unfiltered by location) result set

**Priority:** P1 | **Feature Ref:** F4

---

## Epic 5: Inventory Statistics & Dashboard (F5)

A summary view giving collectors a high-level picture of their cellar composition: total counts and breakdowns by varietal, region, vintage, and producer. Post-MVP.

---

### US-5.1: View Cellar Summary Counts

**As a** Marcus Delacroix (Organized Collector), **I want to** see a summary showing my total bottle count, active count, and consumed count, **so that** I have an immediate sense of my cellar's size without counting manually.

**Acceptance Criteria:**
- [ ] The Statistics / Dashboard screen displays three counts: Total, Active, and Consumed
- [ ] Counts update to reflect the current state of the collection (no stale data)
- [ ] The screen is accessible from the main navigation in ≤2 taps
- [ ] When the collection is empty, the screen shows: "Add bottles to your collection to see statistics."

**Priority:** P1 | **Feature Ref:** F5

---

### US-5.2: View Breakdown by Varietal and Region

**As a** Marcus Delacroix (Organized Collector), **I want to** see how many bottles I have per varietal and per region, **so that** I can identify gaps in my collection (e.g., "I need more white Burgundy") without navigating more than 2 screens.

**Acceptance Criteria:**
- [ ] The dashboard shows a varietal breakdown: a sorted list or chart of `(varietal, count)` pairs, ordered by count descending
- [ ] The dashboard shows a region breakdown: a sorted list or chart of `(region, count)` pairs, ordered by count descending
- [ ] Bottles with no varietal or region recorded are grouped under "Unknown" or "Not specified"
- [ ] Each breakdown can show up to 20 entries by default with a "Show more" option for longer lists
- [ ] Both breakdowns are visible on the same screen (or within one scroll) without additional navigation

**Priority:** P1 | **Feature Ref:** F5

---

### US-5.3: View Vintage Distribution and Producer Breakdown

**As a** Marcus Delacroix (Organized Collector), **I want to** see how my active bottles are distributed by vintage year and by producer, **so that** I can plan my drinking window and know which producers I am most invested in.

**Acceptance Criteria:**
- [ ] The dashboard shows a vintage distribution for active bottles: `(vintage year, count)` pairs sorted chronologically (oldest first)
- [ ] The dashboard shows a producer breakdown: `(producer, count)` pairs sorted by count descending
- [ ] The vintage distribution excludes consumed bottles by default (shows what the user currently holds)
- [ ] Producers with no name recorded are grouped under "Unknown"
- [ ] Visual representation (horizontal bar chart or numbered count list) is used for each breakdown

**Priority:** P1 | **Feature Ref:** F5

---

## Story Index

| Story ID | Title | Persona(s) | Priority | Feature Ref |
|---|---|---|---|---|
| US-0.1 | Add a New Bottle to the Collection | Marcus, Sophie | P0 | F0 |
| US-0.2 | Quick-Add a Bottle with Minimal Fields | Sophie | P0 | F0 |
| US-0.3 | Edit an Existing Bottle Record | Marcus | P0 | F0 |
| US-0.4 | Delete a Bottle from the Inventory | Marcus | P0 | F0 |
| US-0.5 | Receive Validation Feedback on Invalid Input | Sophie | P0 | F0 |
| US-1.1 | View All Bottles in a List | Marcus | P0 | F1 |
| US-1.2 | Toggle Between List and Grid View | Sophie | P0 | F1 |
| US-1.3 | Distinguish Consumed Bottles at a Glance | Daniel | P0 | F1 |
| US-1.4 | Navigate to a Bottle's Detail View | Marcus | P0 | F1 |
| US-2.1 | Search Bottles by Text | Sophie | P0 | F2 |
| US-2.2 | Filter by Varietal and Region for a Pairing Decision | Daniel | P0 | F2 |
| US-2.3 | Filter by Vintage Year or Year Range | Marcus | P0 | F2 |
| US-2.4 | Clear All Active Filters | Daniel | P0 | F2 |
| US-3.1 | Mark a Bottle as Consumed | Marcus | P0 | F3 |
| US-3.2 | Log a Tasting Note and Star Rating | Sophie | P0 | F3 |
| US-3.3 | View Consumed Bottles and Tasting History | Marcus | P0 | F3 |
| US-3.4 | Edit Tasting Notes After Initial Entry | Marcus | P0 | F3 |
| US-3.5 | Restore a Consumed Bottle to Active Status | Marcus | P0 | F3 |
| US-4.1 | Assign a Location to a Bottle | Daniel | P1 | F4 |
| US-4.2 | See Location Prominently in the Bottle Detail View | Daniel | P1 | F4 |
| US-4.3 | Sort the Inventory by Location | Marcus | P1 | F4 |
| US-4.4 | Filter the Inventory by Location | Daniel | P1 | F4 |
| US-5.1 | View Cellar Summary Counts | Marcus | P1 | F5 |
| US-5.2 | View Breakdown by Varietal and Region | Marcus | P1 | F5 |
| US-5.3 | View Vintage Distribution and Producer Breakdown | Marcus | P1 | F5 |

**Total stories: 25** — 18 P0 (MVP), 7 P1 (Post-MVP)

---

## PRD Feature Coverage

| Feature | ID | Stories | Priority | MVP? |
|---|---|---|---|---|
| Bottle Entry & Management | F0 | US-0.1 – US-0.5 | P0 | ✅ Yes |
| Inventory Browsing | F1 | US-1.1 – US-1.4 | P0 | ✅ Yes |
| Search & Filter | F2 | US-2.1 – US-2.4 | P0 | ✅ Yes |
| Consumption Tracking & Tasting Notes | F3 | US-3.1 – US-3.5 | P0 | ✅ Yes |
| Bottle Location Tracking | F4 | US-4.1 – US-4.4 | P1 | ⬜ Post-MVP |
| Inventory Statistics & Dashboard | F5 | US-5.1 – US-5.3 | P1 | ⬜ Post-MVP |

---

*Generated: 2026-05-15 | Model: claude-sonnet-4-6*
