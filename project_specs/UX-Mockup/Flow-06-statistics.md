---

## Flow 06: View Statistics Dashboard

**Trigger:** User taps "Stats" in the bottom navigation bar
**User Stories:** US-5.1, US-5.2, US-5.3
**Journey:** JRN-01.3 (Marcus — weekly composition review)

```
[Any Screen]
        │
        ▼  tap "Stats" in bottom nav (≤2 taps from anywhere)
[Statistics Dashboard Screen]
        │
        ├── [Summary Cards] — Total / Active / Consumed counts
        │
        ├── [Varietal Breakdown] — horizontal bar chart, sorted by count desc
        │       │
        │       └── tap a bar → [Inventory List pre-filtered by that varietal]
        │
        ├── [Region Breakdown] — horizontal bar chart, sorted by count desc
        │       │
        │       └── tap a bar → [Inventory List pre-filtered by that region]
        │
        ├── [Vintage Distribution] — horizontal bar chart, chronological, active only
        │
        └── [Producer Breakdown] — count list, sorted by count desc
                │
                └── "Show more" if > 20 producers
```

**Steps:**

1. **Entry** — "Stats" is a tab in the bottom navigation, accessible in 1 tap from any screen.

2. **Summary Cards** — Three stat cards at the top: Total Bottles, Active, Consumed. Always fresh (no stale data). Empty collection shows: "Add bottles to your collection to see statistics." (US-5.1)

3. **Varietal & Region Charts** — Horizontal bar charts. Bars are tappable — tapping navigates to the Inventory List pre-filtered by that varietal or region (JRN-01.3 Drill by Varietal opportunity). "Unknown" grouped at the bottom. Up to 20 entries shown; "Show more" for longer lists. (US-5.2)

4. **Vintage Distribution** — Shows only active bottles by default (what the user currently holds). Sorted oldest → newest. (US-5.3)

5. **Producer Breakdown** — Count list with horizontal bars. Top 20 by default; "Show more" expands the list. (US-5.3)

**Key UX Decisions:**

- Tappable chart bars create a direct link from insight → filtered inventory view (JRN-01.3 delight opportunity)
- Vintage distribution defaults to active-only because the user's primary question is "what am I holding?"
- Statistics tab in bottom nav = ≤2 taps from anywhere, not buried in a hamburger menu (JRN-01.3 Navigate stage risk)

---
