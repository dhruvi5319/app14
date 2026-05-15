# Jobs-to-be-Done — Wine Inventory App

**Product:** Wine Inventory App  
**Project Acronym:** WineInventory  
**Date:** 2026-05-15  
**Status:** Draft  
**Version:** 1.0  
**Related Personas:** PERSONAS-WineInventory.md  
**Related PRD:** PRD-WineInventory.md  

---

## JTBD Summary Table

| JTBD-ID | Persona | Job Statement (abbreviated) | Priority |
|---|---|---|---|
| JTBD-01.1 | PER-01 Marcus | Maintain a complete, current inventory so nothing is lost or forgotten | P0 |
| JTBD-01.2 | PER-01 Marcus | Understand cellar composition at a glance so I can identify gaps and make buying decisions | P1 |
| JTBD-01.3 | PER-01 Marcus | Build a tasting journal over time so I can learn my preferences and track a bottle's story | P0 |
| JTBD-02.1 | PER-02 Sophie | Log a bottle instantly on my phone so the purchase doesn't slip from memory | P0 |
| JTBD-02.2 | PER-02 Sophie | Recall what I've tasted so I can repeat purchases I loved and avoid ones I didn't | P0 |
| JTBD-02.3 | PER-02 Sophie | Answer "what do I have?" in seconds so I can confidently respond when someone asks | P0 |
| JTBD-03.1 | PER-03 Daniel | Find the right bottle for tonight's pairing in under 30 seconds while standing in the cellar | P0 |
| JTBD-03.2 | PER-03 Daniel | Confirm a bottle's location and stock status without walking to the rack first | P0 |
| JTBD-03.3 | PER-03 Daniel | Log new arrivals without leaving the cellar or interrupting the delivery workflow | P1 |

---

## PER-01: Marcus Delacroix — The Organized Collector

### JTBD-01.1: Complete, Current Inventory

**Job Statement:**  
When I receive a new bottle or reorganize my cellar, I want to record the full details of every bottle with minimal friction, so I can maintain a complete inventory that I can trust without double-checking against a spreadsheet.

**Current Alternatives:**
- Updates an Excel spreadsheet after purchases — often deferred for days and then skipped
- Relies on memory for bottles purchased at auctions or winery visits
- Reconciles consumed bottles manually and inconsistently, leaving "ghost" entries

**Hiring Criteria:**
- Adds a complete record (name, producer, vintage, varietal, region, location) in under 60 seconds
- No network required for add — can log immediately on receipt
- Edited records save instantly with no confirmation lag
- Consumed bottles are clearly removed from the active count without being permanently lost

**Success Measure:** Marcus can add a complete bottle record in under 60 seconds and reports zero "forgotten" bottles after 90 days of active use.

**Related Features:** F0, F1, F4  
**Priority:** P0

---

### JTBD-01.2: Cellar Composition Intelligence

**Job Statement:**  
When I'm planning upcoming purchases or hosting a dinner, I want to see a breakdown of my collection by varietal, region, and producer at a glance, so I can identify gaps and make informed buying decisions without manually counting spreadsheet rows.

**Current Alternatives:**
- Manually counts rows in Excel filtered by column values
- Browses the physical cellar to estimate composition by region rack
- Relies on memory of recent purchases without a current tally

**Hiring Criteria:**
- Shows total bottles, breakdown by varietal, and breakdown by region in a single dashboard view
- Reachable in no more than 2 taps from the home screen
- Statistics update automatically after any add, edit, or consume action

**Success Measure:** Marcus can state his exact bottle count by varietal and identify the most underrepresented region in his cellar within 60 seconds, without leaving the app.

**Related Features:** F5, F1  
**Priority:** P1

---

### JTBD-01.3: Personal Tasting Journal

**Job Statement:**  
When I open a bottle and drink it, I want to record my tasting notes and rating immediately after the experience, so I can build a personal record of preferences that grows more useful the longer I use the app.

**Current Alternatives:**
- Jots notes in email drafts or paper notebooks that are never referenced again
- Relies on memory — which fades within weeks and is unreliable across vintages
- Occasionally photographs a label but never connects it to a rating or impression

**Hiring Criteria:**
- Can log tasting notes and a star rating in under 2 minutes while still at the table
- Marking a bottle consumed and adding a note is a single continuous flow (not two separate tasks)
- Previous tasting entries are searchable by bottle name, varietal, or rating

**Success Measure:** Marcus records a tasting note for at least 80% of bottles he consumes within 30 days of first use, compared to near-0% today.

**Related Features:** F3  
**Priority:** P0

---

## PER-02: Sophie Nguyen — The Casual Enthusiast

### JTBD-02.1: Frictionless Mobile Capture

**Job Statement:**  
When I receive a bottle as a gift or buy one on impulse, I want to log it on my phone using only the minimum required fields, so I can capture it before the moment passes without the entry feeling like homework.

**Current Alternatives:**
- Takes a photo of the label and never transcribes it into any system
- Relies on memory — which fails when she's shopping for the same wine months later
- Occasionally texts herself the name — loses it in the conversation thread

**Hiring Criteria:**
- New bottle added in under 60 seconds on mobile with only name and vintage required
- Optional fields are clearly optional and don't block save
- Single-handed, 3-tap maximum path to a saved record from the home screen

**Success Measure:** Sophie successfully logs a new bottle in under 60 seconds on a first attempt with no tutorial, measured during a usability test on a real device.

**Related Features:** F0  
**Priority:** P0

---

### JTBD-02.2: Taste Memory & Repeat-Buy Confidence

**Job Statement:**  
When I'm at a wine shop or browsing online, I want to look up a bottle I previously tasted and see my rating and notes, so I can confidently decide whether to buy it again without guessing based on vague memory.

**Current Alternatives:**
- Tries to recall the producer and vintage from memory — frequently fails after 3+ months
- Screenshots labels or emails herself names but can't find the files later
- Asks the shop staff for a recommendation instead of repeating a known favourite

**Hiring Criteria:**
- Can search past tasting entries by wine name, varietal, or producer in under 10 seconds
- Rating and notes are visible without tapping into a detail screen
- Consumed and active bottles are separately accessible but searchable together

**Success Measure:** Sophie can retrieve her rating for a previously consumed bottle in under 10 seconds using search, without recalling the exact bottle name.

**Related Features:** F3, F2  
**Priority:** P0

---

### JTBD-02.3: Instant Collection Awareness

**Job Statement:**  
When a friend asks "what whites do you have?" or I'm choosing what to open, I want to filter my collection by varietal or region in one gesture, so I can answer confidently within seconds without mentally rummaging through my memory.

**Current Alternatives:**
- Mentally runs through what she thinks she remembers — often blanks or gives a wrong answer
- Walks to wherever the bottles are stored and looks at them physically
- Shrugs and opens whatever is nearest

**Hiring Criteria:**
- Filter by varietal returns results in under 10 seconds from any screen in the app
- Filter is accessible in 2 taps or fewer
- Result list shows enough detail (name, vintage, varietal) to answer the question without opening each record

**Success Measure:** Sophie can answer "what Chardonnays do I have?" correctly in under 10 seconds from app open, including filter interaction, on a cold start.

**Related Features:** F2, F1  
**Priority:** P0

---

## PER-03: Daniel Reyes — The Cellar-Side Decider

### JTBD-03.1: Pairing Decision Under 30 Seconds

**Job Statement:**  
When I'm in the cellar choosing a bottle for tonight's dinner and have a specific pairing in mind, I want to filter by varietal and region simultaneously so I can narrow to 3–5 candidate bottles within 30 seconds without network access.

**Current Alternatives:**
- Walks the racks visually, reading labels — takes 5–10 minutes per visit
- Relies on memory of what's in each area — unreliable at scale (300+ bottles)
- Brings a paper list that is never current

**Hiring Criteria:**
- Multi-filter (varietal AND region simultaneously) returns results in under 10 seconds
- Filter controls accessible in 2 taps from the inventory list
- App functions fully offline — no Wi-Fi required in the cellar
- Result list shows bottle name, vintage, and rack/bin location without tapping into detail

**Success Measure:** Daniel can narrow 300+ bottles to a 3–5 candidate shortlist for a specific pairing in under 30 seconds, measured cellar-side without network access.

**Related Features:** F2, F4, F1  
**Priority:** P0

---

### JTBD-03.2: Location & Stock Confirmation Before Walking

**Job Statement:**  
When I've chosen a bottle I want to open, I want to confirm its exact rack/bin location and verify it hasn't already been consumed, so I can walk directly to the right spot without a manual search through the racks.

**Current Alternatives:**
- Walks to the area he thinks the bottle is and searches visually — often wastes a trip
- Has no system to track consumed status — bottles "disappear" without a record
- Relies on physical labels on racks that don't sync with actual stock

**Hiring Criteria:**
- Rack/bin location is visible in the list result without tapping into detail view
- Consumed status is clearly indicated at a glance (distinct icon or visual state)
- Location field supports Daniel's custom labeling scheme (free-text, e.g., "Rack 3 / Bin 12")
- One-handed interaction — all critical actions reachable with a thumb

**Success Measure:** Daniel can confirm a specific bottle's location and consumed status in under 5 seconds from a search result list, one-handed, without opening the detail view.

**Related Features:** F4, F1, F3  
**Priority:** P0

---

### JTBD-03.3: Quick Arrival Logging Without Leaving the Cellar

**Job Statement:**  
When a case delivery arrives and I'm physically in the cellar putting bottles away, I want to log each new bottle quickly using only the essentials, so I can keep the inventory accurate in real time without interrupting the stacking workflow.

**Current Alternatives:**
- Stacks the bottles first, intends to log them later — and often never does
- Writes labels on masking tape on the rack but never enters them digitally
- Logs them the next day from memory, losing accuracy on bin assignments

**Hiring Criteria:**
- New bottle can be logged in under 45 seconds on a phone with one hand
- Location field (rack/bin) is included in the minimal entry flow
- App saves locally without requiring a network connection
- No mandatory fields beyond name and vintage prevent rapid-fire logging of a case

**Success Measure:** Daniel logs a 6-bottle case delivery in under 5 minutes total, cellar-side, without network access, with location recorded for each bottle.

**Related Features:** F0, F4  
**Priority:** P1

---

## Outcome-to-Feature Traceability

| JTBD-ID | Related Features | Expected Outcome |
|---|---|---|
| JTBD-01.1 | F0, F1, F4 | Collector's active inventory is always current; zero "ghost" bottles from forgotten consumption |
| JTBD-01.2 | F5, F1 | Marcus can identify cellar gaps by varietal/region in under 60 seconds without manual counting |
| JTBD-01.3 | F3 | 80% of consumed bottles have a tasting note within 30 days of first use |
| JTBD-02.1 | F0 | Sophie logs a new bottle in under 60 seconds on mobile with no tutorial required |
| JTBD-02.2 | F3, F2 | Sophie retrieves a past tasting note and rating in under 10 seconds by search |
| JTBD-02.3 | F2, F1 | Sophie answers a "what do you have?" question in under 10 seconds from app open |
| JTBD-03.1 | F2, F4, F1 | Daniel narrows 300+ bottles to 3–5 pairing candidates in under 30 seconds, offline |
| JTBD-03.2 | F4, F1, F3 | Daniel confirms bottle location and stock status in under 5 seconds from list view |
| JTBD-03.3 | F0, F4 | Daniel logs a 6-bottle delivery in under 5 minutes, one-handed, without network |

---

## NaC Preview

*Candidate Natural Acceptance Criteria — to be refined in STORY-MAP phase.*

| JTBD-ID | Outcome | Candidate Natural Acceptance Criterion |
|---|---|---|
| JTBD-01.1 | Complete bottle record in under 60 seconds | Given a new bottle to log, when Marcus fills in name, producer, vintage, varietal, region, and location, the record is saved and visible in the inventory list in under 60 seconds from first tap |
| JTBD-01.2 | Cellar composition visible in 2 taps | Given 10+ bottles in inventory, when Marcus opens the statistics view, he sees total count, top 5 varietals, and top 5 regions, reachable in no more than 2 taps from the home screen |
| JTBD-01.3 | Tasting note logged in under 2 minutes | Given an open bottle, when Marcus marks it consumed and adds tasting notes and a rating, the entry is saved in under 2 minutes without leaving the bottle detail flow |
| JTBD-02.1 | New bottle added in under 60 seconds on mobile | Given Sophie opens the app from cold start, when she taps "Add Bottle" and fills only the required fields, the bottle appears in her inventory in under 60 seconds |
| JTBD-02.2 | Past tasting note retrieved in under 10 seconds | Given at least one consumed bottle with a tasting note, when Sophie searches by wine name or varietal, the result shows rating and note preview within 10 seconds without opening detail view |
| JTBD-02.3 | Varietal filter returns results in under 10 seconds | Given a collection with multiple varietals, when Sophie applies a varietal filter from the inventory screen, matching bottles are displayed in under 10 seconds, accessible in 2 taps or fewer |
| JTBD-03.1 | Multi-filter returns results in under 10 seconds offline | Given the app in offline mode with 300+ bottles, when Daniel applies both a varietal filter and a region filter, results are returned in under 10 seconds |
| JTBD-03.2 | Location visible in list without opening detail | Given a filtered result list, when Daniel views the result cards, each card displays the rack/bin location and consumed status without requiring a tap to open the detail view |
| JTBD-03.3 | 6-bottle case logged in under 5 minutes offline | Given an offline device in the cellar, when Daniel logs 6 bottles sequentially with name, vintage, and location, all 6 records are saved and visible in under 5 minutes total |

---

*Generated: 2026-05-15 | Model: claude-sonnet-4-6*
