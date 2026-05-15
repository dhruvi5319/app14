# User Journey Maps — Wine Inventory App

**Product:** Wine Inventory App  
**Project Acronym:** WineInventory  
**Date:** 2026-05-15  
**Status:** Draft  
**Version:** 1.0  
**Related Personas:** PERSONAS-WineInventory.md  
**Related JTBD:** JTBD-WineInventory.md  
**Related PRD:** PRD-WineInventory.md  
**Related Project:** .planning/PROJECT.md

---

## Journey Index

| JRN-ID | Persona | Scenario | Key JTBD | Stages |
|--------|---------|----------|----------|--------|
| JRN-01.1 | PER-01 Marcus Delacroix | Logging a new bottle purchase immediately on receipt | JTBD-01.1 | 5 |
| JRN-01.2 | PER-01 Marcus Delacroix | Opening a bottle, consuming it, and recording tasting notes | JTBD-01.3 | 5 |
| JRN-01.3 | PER-01 Marcus Delacroix | Weekly inventory review and cellar composition check | JTBD-01.2 | 5 |
| JRN-02.1 | PER-02 Sophie Nguyen | Quick-adding a gifted bottle on her phone in under a minute | JTBD-02.1 | 4 |
| JRN-02.2 | PER-02 Sophie Nguyen | Recalling a past tasting note while shopping at a wine store | JTBD-02.2 | 5 |
| JRN-03.1 | PER-03 Daniel Reyes | Cellar-side pairing decision for tonight's dinner guests | JTBD-03.1, JTBD-03.2 | 6 |
| JRN-03.2 | PER-03 Daniel Reyes | Logging a case delivery one-handed without leaving the cellar | JTBD-03.3 | 5 |

---

## PER-01: Marcus Delacroix — The Organized Collector

---

### JRN-01.1: Logging a New Bottle Purchase on Receipt

**Persona:** PER-01 (Marcus Delacroix)

**Scenario:** Marcus returns home from a wine auction with two cases of mixed Bordeaux and Burgundy. He's been burned before — bottles languished on a counter for three days while he "meant to update the spreadsheet," and details like bin assignments slipped from memory. This time he wants to log the bottles immediately, while he's still carrying them to the cellar.

**Related Jobs:** JTBD-01.1

---

#### Journey Stages

| Stage | Action | Touchpoint | Thinking | Feeling | Pain Point | Opportunity |
|-------|--------|------------|----------|---------|------------|-------------|
| **Arrive** | Sets bottles on cellar bench, opens app on tablet | App home screen / F1 (Inventory) | "Let me get these logged before I forget the bin assignments." | Purposeful, slightly rushed | Old habit: spreadsheet was always on the laptop upstairs — too far to bother | App opens to a visible "Add Bottle" action; zero navigation required |
| **Initiate** | Taps "Add Bottle" CTA from inventory screen | F0 — Add Bottle form | "I need to fill in name, producer, vintage, varietal, region, location — in the right order." | Focused | Form fields in wrong order force mental context-switching | Field order mirrors how Marcus thinks: Name → Producer → Vintage → Varietal → Region → Location |
| **Enter Details** | Types in all fields for first bottle; assigns rack/bin | F0 form + F4 location field | "Rack 2, Bin 7 — is that what I labeled it? Yes." | Confident, deliberate | No location shortcut — must type full string every time for 12 bottles | Recently-used location strings surfaced as quick-select chips to reduce retyping |
| **Save & Repeat** | Taps Save; sees bottle appear in list; taps Add Bottle again for next | F1 — Inventory list + F0 form | "Good, it saved. Now the Pomerol — same rack, next bin." | Satisfied, building momentum | No "save and add another" pattern; has to navigate back each time | "Save & Add Another" option pre-fills location field from previous entry |
| **Verify** | Scrolls inventory list to confirm all 12 new entries appear | F1 — Inventory list | "Twelve bottles, all there. I can trust this." | Relieved, accomplished | No batch confirmation — must visually count entries | Post-session summary: "12 bottles added today" with quick undo option for errors |

#### Key Moments

- **Decision Point:** Enter Details stage — if any required field is unclear or the form is cumbersome, Marcus defers logging to later (and it never happens)
- **Risk of Abandonment:** Save & Repeat stage — friction of navigating back to add each subsequent bottle from a multi-bottle session is the primary driver of deferred logging
- **Delight Opportunity:** Verify stage — a post-session confirmation summary rewards completion and builds trust in the system

#### Success Outcome

Marcus logs a complete bottle record (name, producer, vintage, varietal, region, rack/bin location) in under 60 seconds per bottle, with zero deferred entries (JTBD-01.1 success measure).

#### Feature Touchpoints

| Stage | Features |
|-------|----------|
| Arrive | F1 (Inventory Browsing) |
| Initiate | F0 (Bottle Entry) |
| Enter Details | F0 (Bottle Entry), F4 (Location Tracking) |
| Save & Repeat | F0 (Bottle Entry), F1 (Inventory Browsing) |
| Verify | F1 (Inventory Browsing) |

---

### JRN-01.2: Opening a Bottle and Recording Tasting Notes

**Persona:** PER-01 (Marcus Delacroix)

**Scenario:** Marcus is hosting a small dinner and opens a 2017 Chambolle-Musigny he's been holding for four years. After the meal, still at the table with guests, he wants to record his tasting impressions and mark the bottle consumed before the memory fades — exactly the moment he used to let slip by.

**Related Jobs:** JTBD-01.3

---

#### Journey Stages

| Stage | Action | Touchpoint | Thinking | Feeling | Pain Point | Opportunity |
|-------|--------|------------|----------|---------|------------|-------------|
| **Locate** | Opens app on tablet; searches bottle name | F2 — Search | "I know it's in here — 2017 Chambolle." | Calm, comfortable | If search doesn't return exact match quickly, he may give up and note it on paper instead | Search result appears within one keystroke of a distinctive word; consumed toggle visible |
| **Open Record** | Taps the bottle card to open detail view | F1 → Bottle Detail | "There it is — purchased 2022, Rack 4 Bin 3. Now let me consume it." | Satisfied | Detail view is a separate navigation step from list | Bottle detail shows "Mark as Consumed + Add Note" as a prominent paired CTA |
| **Mark Consumed** | Taps "Mark as Consumed" — sees date auto-filled | F3 — Consumption flow | "Good, today's date is pre-filled. I just need the notes." | Relieved (no manual date entry) | Two-step process (mark consumed, then navigate to notes) creates friction at the table | Single continuous flow: marking consumed immediately presents the note and rating fields |
| **Record Notes** | Types tasting impressions; selects star rating | F3 — Tasting note form | "Dried cherry, forest floor, silky tannins — 4.5 stars. This one aged beautifully." | Engaged, reflective | No prompt or structure — blank text field offers no scaffolding | Optional guided prompts (aroma / palate / finish / overall) that can be ignored but help beginners |
| **Save & Return** | Taps Save; sees bottle moved to "Consumed" view | F3 → F1 Consumed view | "Done. It's recorded. I'll look back at this in a year." | Accomplished, at peace | No feedback that journal is building value over time | Milestone message: "Your 23rd tasting note — your journal is growing." |

#### Key Moments

- **Decision Point:** Locate stage — if search is slow or imprecise, Marcus abandons and reverts to writing on paper; this is the highest-risk failure point
- **Risk of Abandonment:** Mark Consumed stage — a two-step flow with navigation between screens at a dinner table is enough friction to cause deferral
- **Delight Opportunity:** Save & Return stage — acknowledgment that a journal entry was saved and that the journal is accumulating value turns a task into a ritual

#### Success Outcome

Marcus records a tasting note and rating in under 2 minutes while still at the table, within a single continuous consume-and-note flow (JTBD-01.3 success measure).

#### Feature Touchpoints

| Stage | Features |
|-------|----------|
| Locate | F2 (Search & Filter) |
| Open Record | F1 (Inventory Browsing), F4 (Location Tracking) |
| Mark Consumed | F3 (Consumption Tracking) |
| Record Notes | F3 (Tasting Notes) |
| Save & Return | F3 (Tasting Notes), F1 (Inventory Browsing) |

---

### JRN-01.3: Weekly Inventory Review and Composition Check

**Persona:** PER-01 (Marcus Delacroix)

**Scenario:** On Sunday afternoon Marcus sits down to plan what he'll drink and potentially buy over the next month. He wants a clear picture of his cellar's composition — how many bottles by varietal, which regions are underrepresented — so he can decide what to order from his wine club before Tuesday's cutoff.

**Related Jobs:** JTBD-01.2

---

#### Journey Stages

| Stage | Action | Touchpoint | Thinking | Feeling | Pain Point | Opportunity |
|-------|--------|------------|----------|---------|------------|-------------|
| **Navigate** | Opens app on tablet; taps Statistics or Dashboard link | F5 — Statistics Dashboard | "I want to see the full breakdown — varietals, regions, the counts." | Curious, planning mode | Statistics buried behind navigation; unclear which tab to hit | Statistics accessible in 2 taps from the home screen; bottom nav item labeled clearly |
| **Review Totals** | Reads total active bottle count and top-line breakdown | F5 — Summary cards | "180 active bottles. 12 consumed this quarter. Down from last year." | Engaged | No trend data — just a static count with no change over time indicator | "Compared to last month" delta shown alongside totals for at-a-glance change |
| **Drill by Varietal** | Reviews varietal breakdown chart | F5 — Varietal chart | "Heavy on Pinot Noir and Cab. Very light on whites — only 14 bottles." | Slightly concerned | Chart doesn't allow tapping a bar to see the actual bottles in that varietal | Tap a chart bar to jump directly to a filtered inventory view for that varietal |
| **Drill by Region** | Reviews region breakdown | F5 — Region chart | "No Alsace at all. I keep meaning to buy some Riesling." | Motivated, planning | Region list truncated — small collections under 3 bottles not shown | Show all regions including small counts; allow user to set a "target" count per region |
| **Act on Insight** | Notes gaps; closes app to place wine club order | External (email / wine club site) | "I need 6 whites: 3 Burgundy, 3 Alsace. And one more Champagne." | Satisfied with plan | No way to export or copy the insight for reference outside the app | "Copy summary" button exports a plain-text breakdown for pasting into an email or note |

#### Key Moments

- **Decision Point:** Drill by Varietal stage — if the chart doesn't clearly highlight gaps, Marcus must manually calculate what's missing; failure here returns him to the spreadsheet
- **Risk of Abandonment:** Navigate stage — if Statistics is hard to find (buried in menus), he skips the review entirely
- **Delight Opportunity:** Act on Insight stage — an export or share feature bridges the app to his buying workflow, creating a sticky use case

#### Success Outcome

Marcus identifies his cellar's most underrepresented region and plans targeted purchases within 60 seconds of opening the statistics view, without leaving the app (JTBD-01.2 success measure).

#### Feature Touchpoints

| Stage | Features |
|-------|----------|
| Navigate | F5 (Statistics Dashboard), F1 (Inventory Browsing) |
| Review Totals | F5 (Statistics Dashboard) |
| Drill by Varietal | F5 (Statistics Dashboard), F2 (Search & Filter) |
| Drill by Region | F5 (Statistics Dashboard) |
| Act on Insight | F5 (Statistics Dashboard) — export opportunity |

---

## PER-02: Sophie Nguyen — The Casual Enthusiast

---

### JRN-02.1: Quick-Adding a Gifted Bottle on Mobile

**Persona:** PER-02 (Sophie Nguyen)

**Scenario:** Sophie receives a bottle of Sancerre as a birthday gift from a friend who says "you have to try this producer." She's at the party, holding the bottle, phone in one hand. She wants to log it right now before the name disappears into the noise of the evening — but she'll abandon the attempt if it takes more than three taps.

**Related Jobs:** JTBD-02.1

---

#### Journey Stages

| Stage | Action | Touchpoint | Thinking | Feeling | Pain Point | Opportunity |
|-------|--------|------------|----------|---------|------------|-------------|
| **Trigger** | Reads the bottle label; opens app on phone | App home screen | "I need to log this right now. The producer's name is going to disappear from my brain." | Motivated but time-pressured | App takes too long to load from a cold start, or the Add action isn't immediately visible | Add Bottle is a primary FAB on the home screen; app opens ready to add in under 2 seconds |
| **Initiate** | Taps "Add Bottle" FAB; form appears | F0 — Add Bottle form (mobile) | "Okay, just the basics — name, vintage. I don't even know the varietal off-hand." | Slightly anxious (afraid of a long form) | Long form with many required fields creates dread before she's typed a character | Only Name and Vintage are required; all other fields clearly optional with visual hierarchy |
| **Enter & Save** | Types bottle name and vintage year; taps Save | F0 — Minimal entry form | "Done — two fields. That's it? It saved?" | Surprised and pleased | Uncertainty about whether the save worked — no clear confirmation feedback | Instant success state: bottle card animates into the list with a "Added!" micro-confirmation |
| **Optional Add** | Adds varietal from label while still reading it | F0 — Bottle detail (edit) | "Oh, I can add the varietal too. Let me do that while I have the label." | Comfortable, in control | Optional fields hidden behind a separate edit screen; extra tap required | Optional fields accessible inline after save; progressively reveal without requiring navigation |

#### Key Moments

- **Decision Point:** Initiate stage — the form length and required-field pressure determine whether Sophie logs the bottle or puts her phone away
- **Risk of Abandonment:** Trigger stage — a slow cold start or non-obvious Add action kills the attempt before it begins; Sophie is at a party, not at her desk
- **Delight Opportunity:** Enter & Save stage — an unexpectedly fast, delightful save flow creates a "that was easy" moment that builds the habit

#### Success Outcome

Sophie adds a new bottle from cold app start in under 60 seconds, entering only name and vintage, with no tutorial or instruction (JTBD-02.1 success measure).

#### Feature Touchpoints

| Stage | Features |
|-------|----------|
| Trigger | F1 (Inventory Browsing — home screen) |
| Initiate | F0 (Bottle Entry) |
| Enter & Save | F0 (Bottle Entry) |
| Optional Add | F0 (Bottle Entry — edit) |

---

### JRN-02.2: Recalling a Past Tasting Note at a Wine Shop

**Persona:** PER-02 (Sophie Nguyen)

**Scenario:** Sophie is browsing a wine shop and spots a bottle that looks familiar — she thinks she tried this producer last year and loved it. She pulls out her phone, hoping the app can confirm her memory and tell her how she rated it, so she can decide confidently whether to buy it again rather than guessing.

**Related Jobs:** JTBD-02.2

---

#### Journey Stages

| Stage | Action | Touchpoint | Thinking | Feeling | Pain Point | Opportunity |
|-------|--------|------------|----------|---------|------------|-------------|
| **Trigger** | Recognizes producer name on a shelf bottle; opens app | App home screen | "Did I try this? I think I did. Let me check." | Curious, uncertain | App not positioned as a "lookup tool" — she might not think to reach for it | A prominent search icon on the home screen signals "this is a lookup app too" |
| **Search** | Types producer name or partial wine name into search | F2 — Search bar | "Domaine… something. I remember it started with 'D'." | Slightly frustrated (incomplete recall) | Search requires exact match; partial producer name returns nothing | Fuzzy search or "search as you type" across name, producer, and notes fields |
| **Read Result** | Scans result list; finds the consumed entry with rating | F2 → Search results (F3 tasting data) | "There it is — 4 stars. 'Crisp, mineral, long finish.' That was the one." | Delighted, vindicated | Rating and note preview hidden behind a tap into detail view | Rating stars and first line of tasting notes visible on the result card without tapping |
| **Decide** | Puts the bottle in her basket; opens the detail for the note | F3 — Bottle detail / tasting entry | "I loved it. I'm buying two this time." | Confident, satisfied | No easy way to compare two similar bottles side-by-side | "Buy Again" intent tag or a way to mark "want to repeat" directly from the consumed record |
| **Close** | Puts phone away; proceeds to checkout | — (external) | "That's exactly what the app should do." | Happy, feels the app is earning its place | App doesn't prompt to log the new purchase — missed capture moment | After viewing a consumed bottle, offer "Log a new one?" shortcut to create a sibling entry |

#### Key Moments

- **Decision Point:** Search stage — if fuzzy search fails to surface the bottle from a partial name, Sophie loses confidence and the app has failed its core promise
- **Risk of Abandonment:** Read Result stage — if rating and notes require an additional tap, Sophie may not bother in a shop aisle; the result card must be self-sufficient
- **Delight Opportunity:** Close stage — a "Log a new one?" shortcut turns a lookup into a capture moment, keeping the inventory current

#### Success Outcome

Sophie retrieves her rating and tasting note for a past bottle in under 10 seconds using search, without recalling the exact bottle name (JTBD-02.2 success measure).

#### Feature Touchpoints

| Stage | Features |
|-------|----------|
| Trigger | F1 (Inventory Browsing — home screen) |
| Search | F2 (Search & Filter) |
| Read Result | F2 (Search results), F3 (Tasting data on card) |
| Decide | F3 (Bottle detail, tasting entry) |
| Close | F0 (Bottle Entry — shortcut opportunity) |

---

## PER-03: Daniel Reyes — The Cellar-Side Decider

---

### JRN-03.1: Cellar-Side Pairing Decision for Dinner Guests

**Persona:** PER-03 (Daniel Reyes)

**Scenario:** Daniel's dinner guests arrive in 45 minutes. He's serving rack of lamb and wants a serious red — Rhône or Bordeaux, ideally 10+ years old. He walks into his cellar, phone in one hand, and needs to identify the best 3–5 candidates in under 30 seconds without any Wi-Fi. He can't afford to waste time hunting racks manually.

**Related Jobs:** JTBD-03.1, JTBD-03.2

---

#### Journey Stages

| Stage | Action | Touchpoint | Thinking | Feeling | Pain Point | Opportunity |
|-------|--------|------------|----------|---------|------------|-------------|
| **Enter Cellar** | Walks in carrying phone; opens app one-handed | App home screen / F1 (Inventory) | "I need a red Rhône or Bordeaux, 2012 or older. Let me filter." | Focused, slightly pressured | App requires two hands or doesn't load fast enough in low-light cellar | App opens to inventory list immediately; filter button is large, thumb-reachable, high-contrast |
| **Apply Filter 1** | Taps Filter; selects "Red" and "Rhône" | F2 — Filter panel | "Rhône first — I've got more of those." | Efficient | Filter panel uses small checkboxes or requires scrolling to find regions | Filter chips are large, easy to tap with a thumb; region list is alphabetical with a jump index |
| **Apply Filter 2** | Adds "Bordeaux" as second region; applies | F2 — Multi-filter | "Good — and Bordeaux too. Show me both." | Confident | Filters are mutually exclusive — can't select two regions simultaneously | Multi-select filter allows OR logic across regions; single "Apply" button confirms both |
| **Scan Results** | Reads result list — 8 bottles shown | F1 — Filtered list | "Eight candidates. I can see name, vintage, and location right here." | Relieved | Location and vintage only visible inside detail view — must tap each card | Rack/bin location and vintage displayed on the list card; no tap required |
| **Confirm Stock** | Checks consumed status of his first choice | F1 — List card (F3 consumed indicator) | "The 2010 Pichon — is that still in stock? Yes, not consumed." | Tense, then relieved | Consumed bottles not clearly distinguished in list — could grab for a bottle that's gone | Consumed indicator is a distinct visual state (greyed card + strikethrough + icon) |
| **Retrieve** | Reads rack/bin from card; walks directly to Rack 5 / Bin 3 | F4 — Location on list card | "Rack 5, Bin 3. Got it." | Decisive, satisfied | Location string requires opening detail view | Full location string prominently placed on the list card, above the fold |

#### Key Moments

- **Decision Point:** Apply Filter 2 stage — if multi-region OR filtering isn't possible, Daniel can only filter by one region at a time, doubling his lookup effort
- **Critical Risk of Abandonment:** Scan Results stage — if location and vintage aren't visible without tapping, Daniel abandons the app for his physical walk-through; this is the single highest-stakes UX decision for this persona
- **Delight Opportunity:** Confirm Stock stage — instant at-a-glance consumed status turns a 5-minute guessing walk into a 30-second decision

#### Success Outcome

Daniel narrows 300+ bottles to 3–5 pairing candidates in under 30 seconds, one-handed, without network access, and walks directly to the correct rack/bin (JTBD-03.1 + JTBD-03.2 success measure).

#### Feature Touchpoints

| Stage | Features |
|-------|----------|
| Enter Cellar | F1 (Inventory Browsing) |
| Apply Filter 1 | F2 (Search & Filter) |
| Apply Filter 2 | F2 (Search & Filter — multi-select) |
| Scan Results | F1 (Inventory Browsing), F4 (Location on card) |
| Confirm Stock | F1 (List), F3 (Consumed indicator) |
| Retrieve | F4 (Location Tracking) |

---

### JRN-03.2: Logging a Case Delivery Without Leaving the Cellar

**Persona:** PER-03 (Daniel Reyes)

**Scenario:** A 6-bottle case of new-release Burgundy arrives. Daniel intercepts the delivery and carries it directly to the cellar himself. He wants to log each bottle while placing it on the rack — phone in one hand, bottle in the other — before he goes back upstairs. He's done this before with masking tape labels, which he never digitized. Not this time.

**Related Jobs:** JTBD-03.3

---

#### Journey Stages

| Stage | Action | Touchpoint | Thinking | Feeling | Pain Point | Opportunity |
|-------|--------|------------|----------|---------|------------|-------------|
| **Initiate** | Opens app in cellar; taps "Add Bottle" with thumb | F0 — Add Bottle (mobile, offline) | "Same producer, six bottles, different bins. Let me bang these out quickly." | Determined, slightly impatient | App requires network to save — fails offline; all input lost | App saves to local storage immediately; syncs when network available; no data loss |
| **First Entry** | Types name and vintage; types rack/bin location | F0 form + F4 location field | "Rossignol-Trapet 2022, Rack 6 Bin 1. Next." | Efficient | Form too long for one-handed use; Save button at bottom out of thumb reach | Required fields are name, vintage, location only; Save button is sticky at bottom thumb zone |
| **Rapid Repeat** | Taps "Save & Add Another"; location pre-fills; changes bin number only | F0 — Save & repeat flow | "Same rack, just change the bin. 1, 2, 3, 4, 5, 6." | Building rhythm | No "save and add another"; each save requires navigating back to home and re-tapping Add | "Save & Add Another" prefills name, producer, vintage from previous entry; only bin changes |
| **Verify Count** | After sixth bottle, checks inventory for all 6 entries | F1 — Inventory list | "Six entries. All Rack 6. Good." | Satisfied, relieved | No session summary showing how many bottles were just added | "Session complete" badge or recent additions view groups the last N entries |
| **Return Upstairs** | Puts phone in pocket; task done | — | "That took maybe 3 minutes. Old me would have labeled them with tape and never digitized it." | Accomplished, converted | — | — |

#### Key Moments

- **Decision Point:** Initiate stage — if the app requires a network connection to save, Daniel loses all entries if Wi-Fi drops; this is a hard blocker for this persona
- **Risk of Abandonment:** First Entry stage — a form that requires two hands or has Save at the top of the screen makes one-handed logging physically difficult
- **Delight Opportunity:** Rapid Repeat stage — a "Save & Add Another" with smart pre-fill collapses a 6-bottle session from 6 minutes to under 3 minutes, making Daniel a committed user

#### Success Outcome

Daniel logs a 6-bottle case delivery in under 5 minutes total, one-handed, without network access, with location recorded for each bottle (JTBD-03.3 success measure).

#### Feature Touchpoints

| Stage | Features |
|-------|----------|
| Initiate | F0 (Bottle Entry — offline), F1 (Inventory Browsing) |
| First Entry | F0 (Bottle Entry), F4 (Location Tracking) |
| Rapid Repeat | F0 (Save & Add Another flow) |
| Verify Count | F1 (Inventory Browsing) |

---

## Cross-Journey Patterns

### Pain Points Appearing Across Multiple Journeys

| Pattern | Journeys Affected | Common Root Cause |
|---------|-------------------|-------------------|
| **No "Save & Add Another" flow** | JRN-01.1, JRN-03.2 | Single-entry forms assume one bottle per session; multi-bottle scenarios are common for both Marcus and Daniel | Add a persistent "Save & Add Another" action that pre-fills shared fields (producer, location rack) from the previous entry |
| **Location not visible in list view** | JRN-01.1, JRN-03.1, JRN-03.2 | Location is treated as a detail-only field; list cards don't surface it | Promote location to a visible field on all list cards; make it configurable if users prefer not to show it |
| **Offline support gaps** | JRN-03.1, JRN-03.2 | Cellar has poor Wi-Fi; any network dependency blocks Daniel's entire use case | All write and read operations must use local storage first; network sync is background, never blocking |
| **Tasting note friction at the table** | JRN-01.2, JRN-02.2 | Marking consumed and adding notes are split into separate navigable steps | Consume + note as a single continuous flow; no navigation required between marking consumed and writing the note |
| **Search requires exact match** | JRN-02.2 | Fuzzy search not implemented; partial producer/name recall fails | Implement search-as-you-type with fuzzy matching across name, producer, and notes |

### Shared Opportunities

| Opportunity | Journeys Benefited | Value |
|-------------|-------------------|-------|
| **Location string on list card** | JRN-01.1, JRN-03.1, JRN-03.2 | Eliminates the "tap to see where it is" step for both Marcus and Daniel — highest-leverage single card design decision |
| **Consumed status as glanceable indicator** | JRN-01.2, JRN-03.1 | Both Marcus and Daniel need to see at-a-glance whether a bottle is still in stock; a grey/strikethrough state is sufficient |
| **Rating + note preview on result card** | JRN-02.2 | Sophie retrieves tasting data without opening detail view; same card improvement benefits Marcus browsing his journal |
| **Offline-first write model** | JRN-03.1, JRN-03.2 | Solving offline for Daniel implicitly solves data-loss risk for Marcus and reliability concerns for Sophie |
| **Statistics as a buying-decision tool** | JRN-01.3 | Linking chart bars to filtered inventory views creates a virtuous loop: insight → browse → add → statistics update |

### Convergence Points (Stages Where Multiple Personas Share the Same Need)

- **Inventory List (F1):** All three personas arrive here — Marcus for auditing, Sophie for awareness, Daniel for pairing decisions. The list card design must serve all three simultaneously.
- **Search & Filter (F2):** All three use filter for different reasons but with identical UX expectations: fast, ≤2 taps, results visible without navigating away.
- **Add Bottle (F0):** Marcus and Daniel both face multi-bottle sessions; Sophie's one-at-a-time scenario benefits from the same quick-save affordance.

---

## Journey-to-JTBD Traceability

| JRN-ID | Stage | JTBD-ID | Expected Outcome |
|--------|-------|---------|-----------------|
| JRN-01.1 | Enter Details | JTBD-01.1 | Complete record (name, producer, vintage, varietal, region, location) added in under 60 seconds |
| JRN-01.1 | Save & Repeat | JTBD-01.1 | Multi-bottle session completed without navigational friction; no entries deferred |
| JRN-01.1 | Verify | JTBD-01.1 | Marcus confirms all new entries are in inventory; zero "ghost" or missing bottles |
| JRN-01.2 | Locate | JTBD-01.3 | Bottle found via search in under 5 seconds; consume CTA immediately visible |
| JRN-01.2 | Mark Consumed + Record Notes | JTBD-01.3 | Tasting note and rating saved in under 2 minutes; single continuous flow without navigation |
| JRN-01.3 | Review Totals + Drill by Varietal | JTBD-01.2 | Cellar breakdown by varietal and region visible in 2 taps; gaps identifiable at a glance |
| JRN-01.3 | Act on Insight | JTBD-01.2 | Marcus can state the most underrepresented region within 60 seconds of opening statistics |
| JRN-02.1 | Initiate + Enter & Save | JTBD-02.1 | New bottle added in under 60 seconds from cold start; only name and vintage required |
| JRN-02.1 | Optional Add | JTBD-02.1 | Optional fields accessible inline without navigating away; no friction if skipped |
| JRN-02.2 | Search + Read Result | JTBD-02.2 | Past tasting note and rating retrieved in under 10 seconds from partial name; visible on result card |
| JRN-02.2 | Decide | JTBD-02.2 | Sophie can confidently repeat a purchase based on her own record without additional research |
| JRN-03.1 | Apply Filter 1 + Apply Filter 2 | JTBD-03.1 | Multi-filter (varietal AND region) returns result in under 10 seconds; accessible in 2 taps; offline |
| JRN-03.1 | Scan Results + Retrieve | JTBD-03.1 | Rack/bin location and vintage visible on list card without tapping; narrows to 3–5 candidates in 30 seconds |
| JRN-03.1 | Confirm Stock | JTBD-03.2 | Consumed status glanceable on list card; Daniel confirms stock without opening detail view |
| JRN-03.2 | Initiate | JTBD-03.3 | App saves offline; no network required; no data loss if Wi-Fi absent in cellar |
| JRN-03.2 | First Entry + Rapid Repeat | JTBD-03.3 | 6-bottle session completed in under 5 minutes; Save & Add Another with location pre-fill |
| JRN-03.2 | Verify Count | JTBD-03.3 | All 6 entries visible in inventory list with correct rack/bin assignments immediately after session |

---

*Generated: 2026-05-15 | Model: claude-sonnet-4-6*
