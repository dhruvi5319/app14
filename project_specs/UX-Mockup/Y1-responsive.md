---

## Responsive Considerations

### Breakpoints

| Breakpoint | Label | Range | Primary Use Context |
|------------|-------|-------|-------------------|
| Mobile | Small | < 768px | Cellar-side (Daniel), party quick-add (Sophie) |
| Tablet | Medium | 768px – 1024px | Desk entry after auction (Marcus on tablet) |
| Desktop | Large | > 1024px | Optional; not primary use case for v1 |

---

### Mobile (< 768px) — Primary Design Target

**Inventory List:**
- Single-column list view
- Grid view: 2 columns
- FAB pinned bottom-right, always visible
- Bottom navigation bar with 3 tabs + FAB
- Search bar full-width below header
- Filter panel as bottom sheet (slides up from bottom, max 90vh, scrollable)

**Bottle Cards:**
- Full-width cards with all summary fields visible
- Location string on third line — no truncation beyond 2 lines (ellipsis)
- Status badge always visible (right-aligned)
- Minimum tap target: 44px height for any interactive element

**Add / Edit Form:**
- Full-screen form (not modal)
- Sticky footer with Save + Save & Add Another
- Vintage field uses `inputmode="numeric"` for numeric keyboard
- Date fields use native date picker
- Auto-scroll to first error field

**Consume + Tasting:**
- Full-screen form
- Star rating: evenly spaced 48px tap targets
- Textarea: min 120px height; grows with content

**Statistics:**
- Single-column layout
- Horizontal bar charts full-width
- Summary cards: 3 across in a row (compact card size)

---

### Tablet (768px – 1024px) — Secondary Design Target

**Inventory List:**
- Two-column layout: sidebar (filters) + main content area
- Filter panel is persistent left sidebar (not bottom sheet)
- Grid view: 3 columns
- FAB remains bottom-right; can also have a header "Add Bottle" button

**Bottle Cards:**
- Wider cards; location on same line as status badge (more horizontal space)

**Add / Edit Form:**
- Center-column form (max 600px wide), not full-bleed
- Left/right padding to prevent overly wide input fields
- "Save & Add Another" has more visual breathing room

**Bottle Detail:**
- Two-column: left = bottle metadata, right = tasting entry / actions
- Location prominently displayed top-left

**Statistics:**
- Two-column grid for chart sections (Varietal | Region side-by-side)
- Summary cards: larger with more detail

---

### Desktop (> 1024px) — Tertiary (Best Effort in v1)

**Layout approach:**
- Max content width: 1200px, centered
- Left sidebar navigation (persistent) replaces bottom nav
- Inventory list + filter panel in two-column split layout
- Add / Edit form in a modal dialog (centered overlay) rather than full page

**Key adaptations:**
- Hover states on all interactive elements (buttons, cards, chips)
- Keyboard navigation for form fields (Tab order follows visual order)
- Right-click / context menu for bottle cards (Edit, Delete shortcuts)
- Filter panel always visible as left sidebar (no bottom sheet)

---

### Touch Target Compliance (All Breakpoints)

| Element | Minimum Size |
|---------|-------------|
| FAB | 56px × 56px |
| Filter chips | 44px height minimum |
| Star rating stars | 48px × 48px each |
| List row / card | 64px height minimum |
| Navigation tab | 48px height minimum |
| Action buttons | 44px height minimum |
| Text inputs | 48px height minimum |

---

### One-Handed Mobile Usability

Priority elements in the **bottom 60% of the screen** (thumb-reachable zone on a standard phone):

- FAB (Add Bottle) — bottom-right corner
- Bottom navigation tabs
- Filter panel chips (bottom sheet)
- "Save" / "Save & Add Another" buttons (sticky footer)
- "Confirm & Save" / "Cancel" (sticky footer on consume form)

Elements that require two-handed interaction are acceptable in the top area:
- Header navigation (Back, title)
- Overflow menu (⋮)

---
