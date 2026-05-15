# Personas — Wine Inventory App

**Product:** Wine Inventory App  
**Project Acronym:** WineInventory  
**Date:** 2026-05-15  
**Status:** Draft  
**Version:** 1.0  
**Related PRD:** PRD-WineInventory.md  
**Related Project:** .planning/PROJECT.md

---

## Persona Summary

| ID | Name | Role | Primary Goal |
|---|---|---|---|
| PER-01 | Marcus Delacroix | The Organized Collector | Maintain a complete, searchable record of 150+ bottles so nothing is lost or forgotten |
| PER-02 | Sophie Nguyen | The Casual Enthusiast | Quickly log bottles and capture tasting impressions without friction or complexity |
| PER-03 | Daniel Reyes | The Cellar-Side Decider | Find the right bottle to open in under 30 seconds while standing in front of the rack |

---

## PER-01: Marcus Delacroix

**The Organized Collector**

### Role & Context

Marcus is a 48-year-old financial analyst who has been collecting wine seriously for twelve years. His cellar currently holds around 180 bottles, accumulated from winery visits, wine club memberships, and auction purchases. He has deep domain knowledge — he can discuss Bordeaux vintages and Burgundy producers fluently — but his current tracking system is a sprawling Excel spreadsheet that has grown unwieldy. Marcus updates it inconsistently because the manual effort is high, and he frequently discovers he owns bottles he'd forgotten about or, worse, realizes he'd already consumed them. He works primarily from a home office and accesses the app on both a laptop and a tablet. He is the archetypal user this app is designed around.

### Goals

- Replace his spreadsheet with a structured, searchable inventory that stays current without friction (F0, F1)
- Know exactly how many bottles he owns by varietal, region, and producer at a glance (F5)
- Track physical cellar locations so he can retrieve any bottle without a full search (F4)
- Build a personal tasting journal that captures ratings over time so he can learn his preferences (F3)

### Pain Points

- Loses context frequently — forgets he owns specific bottles or where they are in the cellar (PRD §2)
- Spreadsheet has no search or filter capability beyond basic column sorts
- Tasting notes from past bottles are either unrecorded or scattered in email drafts and paper notebooks
- Manually reconciling "active" bottles after consumption is tedious and often skipped

### Technical Expertise

**High** — Comfortable with web apps and tablets; uses productivity software daily; has no tolerance for clunky UX but does not need simplistic design.

### Top Tasks

1. **Add a new bottle** — Logging a purchase immediately after receiving it so it enters the system before he forgets details (frequent, P0)
2. **Browse full inventory** — Reviewing the collection to plan what to open over the coming weeks (weekly, P0)
3. **View cellar statistics** — Checking composition breakdowns to identify gaps (e.g., "I need more white Burgundy") (monthly, P1)
4. **Record a tasting entry** — Logging notes and a rating immediately after opening a bottle (as-needed, P0)
5. **Update a bottle location** — Reorganizing the cellar and moving bottles between racks (occasional, P1)

### Success Criteria

- Can add a complete bottle record (name, producer, vintage, varietal, region, location) in under 60 seconds
- Can view varietal and region breakdowns without navigating more than 2 screens
- Zero bottles lost from the inventory due to sync or data loss events over 90 days
- Tasting note for an opened bottle captured in under 2 minutes while still at the table

---

## PER-02: Sophie Nguyen

**The Casual Enthusiast**

### Role & Context

Sophie is a 34-year-old UX designer who enjoys wine socially but is building her collection more deliberately over the past two years. She currently owns around 35 bottles, mostly gifts, wine-trip purchases, and a few deliberate buys based on recommendations. She has no formal wine training but is developing preferences and wants to start remembering what she's tried. Her current "system" is nothing — she relies on memory and occasionally photos of bottle labels. She primarily uses her phone and expects any tool she adopts to feel as natural as a notes app. Her biggest frustration is that she opens a bottle, enjoys it, and then can't recall the producer or vintage six months later when she wants to buy it again.

### Goals

- Log a bottle in under a minute using only a phone, ideally with minimal required fields (F0)
- Capture a quick tasting note immediately after opening so she can reference it later (F3)
- Search her collection by varietal or region when a friend asks "what whites do you have?" (F2)
- Understand what she generally owns without having to count manually (F1, F5)

### Pain Points

- Manual entry feels like homework — she needs the minimum viable form to feel fast (PRD §2)
- No tasting record means she can't learn from past bottles or repeat purchases she loved
- Poor lookup experience: when someone asks what she has, she mentally blanks on her own collection
- Paper and photos are fragile — she's already lost track of several bottles she enjoyed

### Technical Expertise

**High (domain: apps)** — As a UX designer she is very comfortable with digital tools, but she has a low tolerance for friction; she'll abandon a flow that takes more than 3 taps to complete. Wine knowledge is intermediate-low.

### Top Tasks

1. **Quick-add a bottle** — Logging a new purchase or gift immediately so it doesn't slip from memory (frequent, P0)
2. **Mark a bottle consumed + add tasting note** — Capturing impressions right after opening, including a star rating (frequent, P0)
3. **Search by varietal** — Finding what she has when choosing what to open or answering a friend's question (as-needed, P0)
4. **Browse inventory** — Casually scrolling through her collection when she's curious what she owns (occasional, P0)

### Success Criteria

- New bottle added in under 60 seconds on mobile with only required fields filled
- Tasting note + star rating recorded in under 90 seconds after a bottle is opened
- Can locate a specific varietal within 10 seconds using search or filter
- Returns to app at least twice per week after first 30 days (engagement proxy)

---

## PER-03: Daniel Reyes

**The Cellar-Side Decider**

### Role & Context

Daniel is a 55-year-old restaurateur who maintains a personal home cellar of 300+ bottles alongside his professional knowledge of wine. Unlike Marcus, he already has good cellar organization habits — his bottles are physically labeled and racked by region — but his lookup system doesn't match his physical organization. When guests arrive for dinner, he goes to the cellar with a specific occasion in mind ("I need something to pair with the lamb") and currently spends 5–10 minutes manually searching through racks because he has no app that maps his physical location scheme to a searchable inventory. He carries only his phone into the cellar and needs one-handed operation. His need is almost entirely about speed of lookup at the point of decision, not data entry richness.

### Goals

- Locate any bottle by rack/bin location in under 30 seconds while standing in the cellar (F4, F2)
- Filter by varietal and region simultaneously to narrow options for a pairing decision (F2)
- Confirm at a glance whether a bottle has already been consumed (F1, F3)
- Occasionally add new arrivals without a complex form (F0)

### Pain Points

- No quick way to filter by varietal AND region simultaneously when deciding what to open (PRD §2)
- Has to physically walk through all racks when a bottle's exact location slips his mind
- Consumed bottles still appear in his mental model because he has no system to remove them
- Existing tools he's tried are too desktop-focused to be practical on a phone in a dim cellar

### Technical Expertise

**Intermediate** — Comfortable with smartphones and apps but not looking to learn a new complex tool. Prioritizes speed and readability over features. Expects the app to work offline since his cellar has poor Wi-Fi.

### Top Tasks

1. **Filter by varietal + region** — Narrowing to 3–5 candidates for tonight's pairing decision (frequent, P0, critical)
2. **Look up a bottle's location** — Finding which rack/bin holds the specific bottle he's chosen (frequent, P4)
3. **Confirm consumed status** — Verifying a bottle is still in stock before walking to its rack (frequent, P0)
4. **Add a new arrival** — Quickly logging a case delivery without leaving the cellar (occasional, P0)
5. **Filter by location** — Grouping all bottles in a specific rack to see what's available in one area (as-needed, P1)

### Success Criteria

- Can filter to a specific varietal + region result set in under 10 seconds on a phone
- Bottle location is visible in the result list without tapping into detail view
- App functions fully offline (no network required for browse, search, and filter)
- Core actions (filter, tap bottle, see location) completable with one hand

---

## Persona Relationships

| Interaction | PER-01 Marcus | PER-02 Sophie | PER-03 Daniel |
|---|---|---|---|
| **PER-01 Marcus** | — | Share similar add/log flows; Marcus is more detail-oriented | Share cellar-side lookup need; Daniel prioritizes speed over detail |
| **PER-02 Sophie** | Less concern with location tracking; more mobile-native | — | Both need fast mobile flows; Sophie cares more about tasting journal |
| **PER-03 Daniel** | Shares statistics interest; Daniel focuses on lookup not journaling | Both casual about data entry; Daniel's scale is much larger | — |

All three personas use the app independently (v1 is single-user). No persona interacts with another's data. The primary axis of differentiation is **collection scale and primary intent**: Marcus optimizes for completeness, Sophie for ease of capture, Daniel for speed of retrieval.

---

## Feature-Persona Matrix

| Feature | ID | PER-01 Marcus | PER-02 Sophie | PER-03 Daniel |
|---|---|---|---|---|
| Bottle Entry & Management | F0 | **Primary** | **Primary** | Secondary |
| Inventory Browsing | F1 | **Primary** | Secondary | **Primary** |
| Search & Filter | F2 | **Primary** | **Primary** | **Primary** |
| Consumption Tracking & Tasting Notes | F3 | **Primary** | **Primary** | Secondary |
| Bottle Location Tracking | F4 | **Primary** | None | **Primary** |
| Inventory Statistics & Dashboard | F5 | **Primary** | Secondary | None |

**Key:** Primary = core need for this persona; Secondary = useful but not primary driver; None = low/no relevance for this persona's use case.

### Matrix Notes

- **F0 (Bottle Entry)** — Marcus and Sophie are the primary drivers; Daniel uses it but with lower frequency and simpler requirements.
- **F2 (Search & Filter)** — All three personas depend on this, but for different reasons: Marcus for completeness audits, Sophie for quick "what do I have?" questions, Daniel for cellar-side pairing decisions.
- **F3 (Tasting Notes)** — Marcus and Sophie are primary; Daniel occasionally rates bottles but his primary workflow does not depend on the journal feature.
- **F4 (Location Tracking)** — Marcus and Daniel are primary; Sophie's smaller collection does not yet demand physical location management.
- **F5 (Statistics)** — Marcus is the primary beneficiary; Sophie finds it mildly useful; Daniel does not need statistical views.

---

## Persona Design Implications

| Design Consideration | Evidence |
|---|---|
| Mobile-first is mandatory | All three personas use or prefer phone-based access; Daniel requires one-handed cellar-side operation |
| Minimize required fields on add | Sophie will abandon lengthy forms; Daniel wants quick-add for arrivals |
| Location should be visible in list view | Daniel needs location without tapping into detail; F4 must surface to the list/card |
| Consumed status must be glanceable | Daniel and Marcus both need at-a-glance consumed indicators in F1 list view |
| Offline capability is critical | Daniel's cellar has poor Wi-Fi; offline browse and search are non-negotiable |
| Fast filter UX is the highest-leverage design decision | F2 is Primary for all three personas; filter UI must be accessible in ≤2 taps |

---

*Generated: 2026-05-15 | Model: claude-sonnet-4-6*
