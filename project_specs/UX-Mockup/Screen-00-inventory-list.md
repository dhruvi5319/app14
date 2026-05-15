---

## Screen 00: Inventory List

**Purpose:** Primary everyday interface — the home screen. Shows the full collection with at-a-glance bottle summaries, search, and filter access. Entry point for all other major flows.
**User Stories:** US-1.1, US-1.2, US-1.3, US-1.4, US-2.1, US-2.4

### Layout — List View (Mobile)

```
┌─────────────────────────────────────────────┐
│ ≡  My Cellar              [⊞ grid] [sort ▾] │  ← Header
├─────────────────────────────────────────────┤
│ 🔍 Search by name, producer, notes…  [⧩ 2] │  ← Search bar + filter badge
│ ─────────────────────────────────────────── │
│ 142 bottles  ·  Clear all                   │  ← Result count + clear (when active)
├─────────────────────────────────────────────┤
│                                             │
│ ┌─────────────────────────────────────────┐ │
│ │ Château Margaux                     2015│ │  ← Bottle name + vintage (right)
│ │ Château Margaux  ·  Cab. Sauvignon      │ │  ← Producer · Varietal
│ │ Rack 3 / Bin 12             [Active ●] │ │  ← Location + status badge
│ └─────────────────────────────────────────┘ │
│                                             │
│ ┌─────────────────────────────────────────┐ │
│ │ Domaine Leflaive Puligny          2018  │ │
│ │ Domaine Leflaive  ·  Chardonnay         │ │
│ │ Rack 1 / Bin 4              [Active ●] │ │
│ └─────────────────────────────────────────┘ │
│                                             │
│ ┌─────────────────────────────────────────┐ │
│ │ ~~Pichon Baron~~                  2010  │ │  ← Consumed: name greyed + strikethrough
│ │ Pichon Baron  ·  Cab. Sauvignon         │ │  ← Producer/varietal dimmed
│ │ Rack 5 / Bin 3          [Consumed ✓]   │ │  ← Consumed badge
│ └─────────────────────────────────────────┘ │
│                                             │
│           · · · (scroll) · · ·             │
│                                             │
├─────────────────────────────────────────────┤
│  [Cellar]       [Journal]      [Stats]      │  ← Bottom nav
│                                     [  +  ] │  ← FAB
└─────────────────────────────────────────────┘
```

### Layout — Grid View (Mobile)

```
┌─────────────────────────────────────────────┐
│ ≡  My Cellar              [☰ list] [sort ▾] │
├─────────────────────────────────────────────┤
│ 🔍 Search…                            [⧩ 2]│
├─────────────────────────────────────────────┤
│ ┌──────────────┐  ┌──────────────┐          │
│ │  Château     │  │  Domaine     │          │
│ │  Margaux     │  │  Leflaive    │          │
│ │  2015        │  │  2018        │          │
│ │  [Active ●]  │  │  [Active ●]  │          │
│ └──────────────┘  └──────────────┘          │
│ ┌──────────────┐  ┌──────────────┐          │
│ │ ~~Pichon~~   │  │  ...         │          │
│ │ ~~Baron~~    │  │              │          │
│ │  2010        │  │              │          │
│ │ [Consumed ✓] │  │              │          │
│ └──────────────┘  └──────────────┘          │
├─────────────────────────────────────────────┤
│  [Cellar]       [Journal]      [Stats]      │
│                                     [  +  ] │
└─────────────────────────────────────────────┘
```

### Information Hierarchy

| Priority | Content | Placement |
|----------|---------|-----------|
| Primary | Bottle name + vintage | Top of card, large text |
| Secondary | Producer · Varietal | Second line, muted text |
| Secondary | Location string | Third line, muted text with pin icon |
| Indicator | Consumed/Active status badge | Right side of third line |
| Action | FAB (+) | Bottom-right, always visible |
| Action | Search bar | Below header, always visible |
| Action | Filter button + badge | Right of search bar |

### States

| State | Appearance | User Feedback |
|-------|------------|---------------|
| Default (list) | Rows with all summary fields | Standard layout |
| Default (grid) | 2-column cards with name, producer, vintage, status | Toggle persists for session |
| Loading | Skeleton rows (shimmer) in list shape | No text — skeleton implies loading |
| Empty collection | Illustration + "No bottles in your cellar yet. Tap + to add your first bottle." | Empty state with FAB callout |
| Filter active | "N bottles found · Clear all" bar; filter badge on button | Count + clear action visible |
| No search results | "No bottles match your search. Try adjusting the filters." | Empty state with clear suggestion |
| Consumed bottle | Greyed card + strikethrough name + "Consumed ✓" badge | Visually distinct at a glance |
| Active bottle | Full-color card + "Active ●" badge | Standard appearance |

### Interactive Elements

| Element | Type | Behavior |
|---------|------|----------|
| FAB (+) | Primary CTA | Opens Add Bottle Form |
| Bottle row / card | Tap target | Navigates to Bottle Detail View |
| Search bar | Text input | Activates search; 250ms debounce on type |
| Filter button (⧩) | Button + badge | Opens Filter Panel bottom sheet |
| Sort dropdown (▾) | Select | Changes sort field (name / vintage / created / location) |
| View toggle (⊞/☰) | Toggle icon | Switches list ↔ grid without re-fetch |
| Clear all | Text link | Clears all active search + filters |

### Sort Options

| Sort Label | Field | Default Order |
|------------|-------|--------------|
| Recently Added (default) | `created_at` | desc |
| Name A–Z | `name` | asc |
| Vintage (oldest first) | `vintage` | asc |
| Vintage (newest first) | `vintage` | desc |
| Producer A–Z | `producer` | asc |
| Location A–Z | `location` | asc (Post-MVP, US-4.3) |

---
