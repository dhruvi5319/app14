---

## Screen 05: Journal / Consumed List

**Purpose:** Dedicated view of all consumed bottles ordered by consumption date (most recent first), with tasting data visible on each card. Entry point for reviewing tasting history and editing notes.
**User Stories:** US-3.3, US-1.3

### Layout — Mobile

```
┌─────────────────────────────────────────────┐
│  Journal                           [sort ▾] │  ← Header
├─────────────────────────────────────────────┤
│ 🔍 Search journal…                          │  ← Search (same engine as inventory)
│ 24 bottles consumed                         │  ← Total consumed count
├─────────────────────────────────────────────┤
│                                             │
│ ┌─────────────────────────────────────────┐ │
│ │ Chambolle-Musigny                  2017  │ │  ← Name + vintage
│ │ Rossignol-Trapet  ·  Pinot Noir         │ │  ← Producer · Varietal
│ │ Consumed: 2026-05-10   ★★★★☆            │ │  ← Date + star rating
│ │ "Dried cherry, forest floor, silky…"    │ │  ← First line of tasting notes
│ └─────────────────────────────────────────┘ │
│                                             │
│ ┌─────────────────────────────────────────┐ │
│ │ Pichon Baron                       2010  │ │
│ │ Pichon Baron  ·  Cab. Sauvignon          │ │
│ │ Consumed: 2026-03-22   ★★★★★             │ │
│ │ "Exceptional age — perfect drinking now" │ │
│ └─────────────────────────────────────────┘ │
│                                             │
│ ┌─────────────────────────────────────────┐ │
│ │ Sancerre                           2023  │ │
│ │ Henri Bourgeois  ·  Sauvignon Blanc      │ │
│ │ Consumed: 2026-01-14   (no rating)       │ │  ← No rating placeholder
│ │ (No tasting notes)                      │ │  ← No notes placeholder
│ └─────────────────────────────────────────┘ │
│                                             │
│           · · · (scroll) · · ·             │
│                                             │
├─────────────────────────────────────────────┤
│  [Cellar]       [Journal ●]    [Stats]      │  ← Bottom nav; Journal active
└─────────────────────────────────────────────┘
```

### Information Hierarchy

| Priority | Content | Placement |
|----------|---------|-----------|
| Primary | Bottle name + vintage | Top of card |
| Primary | Consumption date | Third line, left |
| Primary | Star rating | Third line, right of date |
| Secondary | Producer · Varietal | Second line |
| Secondary | First line of tasting notes | Fourth line (truncated) |
| Tertiary | No rating / No notes placeholders | Muted italic text |
| Utility | Total consumed count | Below search bar |

### States

| State | Appearance | User Feedback |
|-------|------------|---------------|
| Default | Consumed bottles sorted by consumed_at desc | Most recent at top |
| Empty journal | Illustration + "You haven't consumed any bottles yet. Start by marking a bottle as consumed." | Gentle prompt |
| No tasting notes | Card shows "(No tasting notes)" in muted text | Not hidden — visible gap |
| No rating | Card shows "(no rating)" | Not hidden |
| Loading | Skeleton cards (shimmer) | Non-blocking |
| Search active | Results filtered by journal search; count updates | "N bottles found" |

### Interactive Elements

| Element | Type | Behavior |
|---------|------|----------|
| Bottle card | Tap target | Navigates to Bottle Detail View (consumed state) |
| Search bar | Text input | Filters journal entries by name, producer, notes |
| Sort ▾ | Select | Consumed date (default), Name, Vintage, Rating |
| Total count | Static label | Shows total consumed bottle count |

### Card Design — Tasting Preview

The journal card intentionally shows:
1. Rating stars inline (no tap needed) — Sophie can see her rating at a glance in a wine shop (JRN-02.2 Read Result)
2. First ~60 chars of tasting notes — enough context to confirm memory without opening the detail
3. Consumed date — chronological context

This eliminates the need to tap into the detail view for the most common lookup scenario (JRN-02.2).

### Key UX Decisions

- Journal is a dedicated bottom nav tab, not a filter on the Inventory List — matches the "tasting history review" mental model (Marcus's JRN-01.3, US-3.3)
- Rating and note preview on the card surface — critical for Sophie's wine-shop recall scenario (JRN-02.2)
- Consumed count shown prominently — reinforces the "journal is growing" feedback (JRN-01.2 milestone)
- Consumed bottles are also visible in the Inventory List with consumed styling — the Journal tab is additive, not the only way to see them

---
