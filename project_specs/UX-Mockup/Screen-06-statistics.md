---

## Screen 06: Statistics Dashboard (Post-MVP — F5)

**Purpose:** High-level cellar composition summary with counts and breakdowns by varietal, region, vintage, and producer. Helps collectors identify gaps and plan purchases.
**User Stories:** US-5.1, US-5.2, US-5.3
**Priority:** P1 (Post-MVP)

### Layout — Mobile

```
┌─────────────────────────────────────────────┐
│  My Cellar Stats                            │  ← Header
├─────────────────────────────────────────────┤
│                                             │
│  ┌───────────┐ ┌──────────┐ ┌────────────┐ │
│  │   Total   │ │  Active  │ │  Consumed  │ │  ← Summary cards
│  │    142    │ │   118    │ │     24     │ │
│  │  bottles  │ │ bottles  │ │  bottles   │ │
│  └───────────┘ └──────────┘ └────────────┘ │
│                                             │
│  ─────────── BY VARIETAL ─────────────────  │
│                                             │
│  Cabernet Sauvignon  ████████████████  34   │  ← Horizontal bar
│  Chardonnay          ████████████     28   │
│  Pinot Noir          ██████████       22   │
│  Merlot              ██████           14   │
│  Unknown             ██                5   │
│                          + Show more (3)    │
│                                             │
│  ─────────── BY REGION ────────────────────  │
│                                             │
│  Bordeaux            ████████████     22   │
│  Napa Valley         ██████████       19   │
│  Burgundy            ████████         16   │
│  Rhône Valley        ██████           12   │
│  Unknown             ██                4   │
│                          + Show more (5)    │
│                                             │
│  ─── VINTAGE DISTRIBUTION (active) ───────  │
│                                             │
│  2015  ██████████████                  12   │
│  2016  ████████████                    10   │
│  2017  ████████████████████████        20   │
│  2018  ██████████████████              16   │
│  2019  ███████████                      9   │
│                                             │
│  ─────────── BY PRODUCER ──────────────────  │
│                                             │
│  1. Château Margaux             8 bottles   │  ← Count list with mini bar
│  2. Domaine Leflaive            6 bottles   │
│  3. Pichon Baron                5 bottles   │
│  4. Rossignol-Trapet            4 bottles   │
│  5. Unknown                     3 bottles   │
│                          + Show more (12)   │
│                                             │
├─────────────────────────────────────────────┤
│  [Cellar]       [Journal]      [Stats ●]   │  ← Bottom nav; Stats active
└─────────────────────────────────────────────┘
```

### Information Hierarchy

| Priority | Content | Placement |
|----------|---------|-----------|
| Primary | Summary cards (Total / Active / Consumed) | Top of screen |
| Primary | Varietal breakdown chart | First section |
| Primary | Region breakdown chart | Second section |
| Secondary | Vintage distribution | Third section |
| Secondary | Producer breakdown | Fourth section |
| Utility | "Show more" for long lists | End of each section |

### States

| State | Appearance | User Feedback |
|-------|------------|---------------|
| Default (populated) | All sections rendered with data | Charts and counts |
| Empty collection | "Add bottles to your collection to see statistics." centered | Illustration + message |
| Loading | Skeleton bars in chart shape | Shimmer effect |
| Error | "Unable to load statistics. Please try again." with retry button | 503 / 504 error state |
| Partial data | "Unknown" entry shown at bottom of each breakdown | Always visible |

### Interactive Elements

| Element | Type | Behavior |
|---------|------|----------|
| Varietal bar | Tappable bar | Navigates to Inventory List pre-filtered by that varietal |
| Region bar | Tappable bar | Navigates to Inventory List pre-filtered by that region |
| Summary cards | Static | Non-interactive (counts only) |
| Show more | Text link | Expands section to show all entries |
| Vintage bars | Static | Non-interactive (informational) |

### Key UX Decisions

- Summary cards (Total / Active / Consumed) are always at the very top — Marcus's primary question is "how many do I have?" (US-5.1)
- Tappable chart bars link to filtered inventory view — creates the insight → browse → add loop (JRN-01.3 delight opportunity)
- Vintage distribution shows active bottles only by default — "what am I currently holding?" is the primary question (US-5.3)
- Statistics tab is in the primary navigation (bottom nav) — accessible in ≤2 taps (JRN-01.3 Navigate stage risk mitigation)
- "Unknown" always shown in breakdowns — doesn't suppress incomplete data

---
