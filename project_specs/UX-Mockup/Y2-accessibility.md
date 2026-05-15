---

## Accessibility Notes

**Standard:** WCAG 2.1 AA (per PRD Non-Functional Requirements)

---

### Color Contrast

| Element | Minimum Ratio | Application |
|---------|--------------|-------------|
| Body text on background | 4.5:1 | All list text, detail text, form labels |
| Large text (≥18px bold / ≥24px regular) | 3:1 | Bottle name heading, section headers |
| UI component borders (inputs, buttons) | 3:1 | Form field borders, button outlines |
| Consumed state (greyed text) | 4.5:1 | Greyed/dimmed consumed bottle names must still pass — use opacity carefully |
| Status badges | 4.5:1 | "Active ●" and "Consumed ✓" text against badge background |
| Error messages (red text) | 4.5:1 | Inline error messages must pass against white/light background |
| Star rating — filled stars | 3:1 | Gold/filled stars against card background |

**Note on consumed styling:** Name strikethrough + opacity reduction is visual only. The text must still meet minimum contrast ratios. Use a specific muted color value (e.g., `#767676` on white) rather than a percentage opacity on the original color.

---

### Keyboard Navigation

| Context | Behavior |
|---------|----------|
| Inventory List | Tab through bottle cards; Enter to open detail |
| Filter Panel | Tab through chips; Space to toggle chip; Enter to Apply; Esc to close |
| Add / Edit Form | Tab order follows visual field order (Name → Vintage → Producer → Varietal → Region → Location → Notes → Purchase Date → Price → Quantity → Save) |
| Star Rating | Arrow keys to change rating (← decrease, → increase); Tab to move away |
| Confirmation Dialog | Tab trapped within dialog; Enter = confirm; Esc = cancel |
| Bottom Sheet | Esc closes the sheet; Tab cycles through interactive elements within |
| FAB | Always reachable via Tab; Enter activates |

---

### Screen Reader Support

| Element | ARIA / Label |
|---------|-------------|
| FAB (+) | `aria-label="Add new bottle"` |
| Filter button | `aria-label="Filter (N active)"` — count announced dynamically |
| View toggle | `aria-label="Switch to grid view"` / `aria-label="Switch to list view"` |
| Sort dropdown | `aria-label="Sort inventory"` |
| Consumed badge | `aria-label="Consumed"` (not just a visual checkmark) |
| Active badge | `aria-label="Active"` |
| Star rating | `role="radiogroup"` with each star as `role="radio"` + `aria-label="N stars"` |
| Inline error | `aria-describedby` links input to its error message element |
| Required fields | `aria-required="true"` on Name and Vintage inputs |
| Loading state | `aria-busy="true"` on the list container during load |
| Result count | `aria-live="polite"` region — announced when filter count changes |
| Bottle name in dialog | Included in dialog description: `aria-describedby` |
| Toast notification | `role="status"` with `aria-live="polite"` |

---

### Focus Management

| Scenario | Focus Behavior |
|----------|---------------|
| Open Add Bottle Form | Focus moves to Bottle Name input |
| Open Filter Panel | Focus moves to first interactive element in panel |
| Close Filter Panel (Apply) | Focus returns to Filter button in Inventory List |
| Close Confirmation Dialog (Cancel) | Focus returns to the button that opened it |
| Confirm Delete | Focus returns to Inventory List (bottle is gone) |
| Form validation error | Focus moves to the first failing field |
| Save success (stay on page) | Focus stays on Save button |
| Navigate to new screen | Focus moves to the screen's heading (`<h1>`) |

---

### Motion & Animation

| Scenario | Behavior |
|----------|----------|
| Filter panel slides | Respect `prefers-reduced-motion` — disable slide animation; use instant show/hide |
| Toast notifications | Fade-in/out; reduce to instant on/off with `prefers-reduced-motion` |
| FAB pulse animation | Disabled with `prefers-reduced-motion` |
| Skeleton shimmer | Static grey block with `prefers-reduced-motion` |
| Bottle card hover effects | Minimal — no flicker or rapid color changes |

---

### Form Accessibility

| Requirement | Implementation |
|-------------|---------------|
| All inputs have visible labels | Labels above inputs (not placeholder-only) |
| Placeholder text is supplementary | Placeholders show examples only; labels carry semantic meaning |
| Error association | `aria-describedby` links each input to its error `<span>` |
| Required field indication | Both visual asterisk (*) and `aria-required="true"` |
| Character counter | Announced via `aria-live="polite"` on blur or when limit approached |
| Date picker | Native `<input type="date">` or accessible custom picker with ARIA |
| Numeric inputs | `inputmode="numeric"` for mobile keyboards; accepts keyboard number entry |

---

### Color-Blind Considerations

| Scenario | Solution |
|----------|----------|
| Active vs. Consumed status | Not distinguished by color alone — badge includes text label ("Active" / "Consumed") and icon (●/✓) |
| Star rating | Stars use shape (filled/outline) in addition to color |
| Error states | Error border + icon (⚠) + text message — not red border alone |
| Filter chip selected state | Filled chip + bold text + checkmark icon — not color alone |
| Chart bars (Statistics) | If colored bars are used, include text count values alongside |

---
