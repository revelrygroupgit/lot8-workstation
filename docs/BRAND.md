# Brand & Design System

Design reference for anyone editing `index.html` or building new sections.

---

## Colour palette

| Variable | Hex | Usage |
|---|---|---|
| `--orange` / `O` | `#FF5C00` | Primary brand, CTAs, product/build tracks |
| `--amber` / `A` | `#FFB800` | Secondary accent, brand/design tracks, Landing Page |
| `--green` / `G` | `#6BCB77` | Workstation tabs, success states, operational tracks |
| `--purple` / `P` | `#A78BFA` | AI/tech tracks, data |
| `--red` / `R` | `#F87171` | Warnings, flags, urgent |
| `--dark` | `#0A0A0A` | Page background |
| `--card` | `#111111` | Card backgrounds |
| `--border` | `#1E1E1E` | All borders |
| `--light` | `#F5F5F5` | Primary text |
| `--muted` | `#888888` | Secondary text |

In JavaScript render functions, use the short constants (`O`, `A`, `G`, `P`, `R`) — they are defined at the top of the script block:

```javascript
const O = '#FF5C00', A = '#FFB800', G = '#6BCB77', P = '#A78BFA', R = '#F87171', C = '#38BDF8';
```

---

## Typography

| Role | Font | Weight | Where |
|---|---|---|---|
| Display / headers | Bebas Neue | 400 (renders heavy) | Big headings, nav labels, metric values |
| Body | DM Sans | 400–700 | Everything else |

Both are loaded from Google Fonts in the `<head>`. No local font files.

---

## CSS component classes

Use these in render function HTML strings to stay consistent.

### Text

```html
<h2>Section heading</h2>          <!-- large section title -->
<h3>Sub-heading</h3>               <!-- card or block title -->
<p class="body">Body copy</p>      <!-- paragraph text -->
<p class="sub">Sub-caption</p>     <!-- small grey label -->
```

### Tags / badges

```html
<span class="tag orange">Label</span>
<span class="tag amber">Label</span>
<span class="tag green">Label</span>
<span class="tag purple">Label</span>
<span class="tag red">Label</span>
<span class="tag blue">Label</span>
```

### Cards

```html
<div class="card">Default card</div>
<div class="card orange">Orange-tinted card</div>
<div class="card green">Green-tinted card</div>
```

### Metrics

```html
<div class="metric-grid">
  <div class="metric">
    <div class="metric-value">$50K</div>
    <div class="metric-label">GMV Target</div>
  </div>
</div>
```

### Grids

```html
<div class="grid-2">...</div>   <!-- 2-column -->
<div class="grid-3">...</div>   <!-- 3-column -->
<div class="grid-4">...</div>   <!-- 4-column -->
```

All grids collapse to 1 column on mobile (`max-width: 768px`).

---

## Navigation colour coding

The three nav groups each have a fixed colour. Don't change these — the team uses them as wayfinding.

| Group | Colour |
|---|---|
| Action Center | Green (`#6BCB77`) |
| Lot 8 Strategy | Orange (`#FF5C00`) |
| Landing Page | Amber (`#FFB800`) |

---

## Brand voice

From the meeting notes (2 Jun 2026 + 7 Jun 2026 sessions):

- **Tagline:** Watch · Chat · Buy
- **Positioning:** "Entertainment is the future storefront"
- **Tone:** Punchy, direct, not luxury — premium through *taste*, not price-tag gloss
- **Avoid:** "Leaders of taste" (dropped), "exhibit" (replaced with "stream"), anything that sounds like Whatnot or live auction
- **Mascot:** **Otto** — the 8 turned sideways = infinity, two O's = eyes. In-chat persona for time warnings, item alerts, drop reminders. AI-powered. Young-audience voice.

---

## Logo

The header logo is pure CSS — no image file:

```html
<div class="logo-icon">8</div>
```

The `.logo-icon` class renders a rounded box with an orange gradient background and white Bebas Neue "8". Otto's face: the 8 = two eyes when turned sideways.
