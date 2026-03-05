# Fluid Slider

Full-width carousel for ACOM. Each slide is a 16:9 card with a full-bleed image, overlaid quote text, attribution, and CTA button. Supports infinite looping, parallax, fluid drag masking, mouse drag, and touch swipe.

## File structure

```
index.html          — everything (HTML, CSS, JS)
styles/
  reset.css         — browser reset
  grid.css          — ACOM grid system (breakpoints, columns, margins, overlay)
assets/
  Fonts/            — Adobe Clean Display (Regular, Bold, Black)
  images/           — slide images (desktop + mobile @sm variants)
vendor/
  gsap.min.js       — GSAP 3 (animation engine)
```

## Dependencies

- **GSAP 3** (`vendor/gsap.min.js`) — tweening and `gsap.set` / `gsap.getProperty`
- **Adobe Clean Display** — Regular (400), Bold (700), Black (900)
- No build step, no npm, no bundler — open `index.html` directly

## Grid alignment

Slide width is computed from the `.grid` container's content-box (viewport minus margins, capped at 1920px). A hidden `.grid-ref` element is measured via JS on every resize. Toggle the grid overlay with **G** to verify alignment.

| Breakpoint | Range | Columns | Margin |
|---|---|---|---|
| XS | < 768px | 6 | 24px fixed |
| S | 768–1023px | 6 | 24px fixed |
| M | 1024–1279px | 12 | 8.333% |
| L | 1280–1440px | 12 | 8.333% |
| XL | > 1440px | 12 | 8.333% (max 1920px) |

## Architecture

### Infinite loop

`CLONE_COUNT = 3` clones are prepended and appended to the track. The outermost clone on each side is an unreachable safety buffer — drag and snap are hard-clamped one step inside it. When a throw settles on any clone, `boundaryJump()` instantly teleports to the corresponding real slide.

### Drag & snap

Vanilla pointer/touch events feed a rolling 80ms velocity window. On release, velocity is projected `280ms` forward to determine the snap target, then `gsap.to()` with `power4.out` animates to it. `power4.out` is strictly monotonic — zero overshoot, guaranteed to stay within rendered content.

### Fluid mask

On drag start, `.dragging` is added to the track, triggering `clip-path: inset(4% round 16px)` on all slide images (snappy 150ms ease-in). On release `.dragging` is removed and the mask returns to `inset(0%)` with a 600ms fluid ease-out. Adjust `DRAG_MASK_INSET` in JS to change the inset percentage.

### Parallax

Image containers are 120% wide with `overflow: hidden`. During transitions, `onUpdate` applies an inverse translateX offset to each image (`PARALLAX_FACTOR = 0.18`). Driven entirely by JS — no CSS transition on images.

### Text mask tracking

During drag, `.slide-text` translates right by `--drag-mask-px` (= `slideWidth × DRAG_MASK_INSET%`), keeping the text visually anchored to the left edge of the clip-path mask. Entry transition: 150ms ease-in (matches mask). Exit: 600ms fluid ease-out (matches mask release).

### Velocity-driven slide trail

During drag, all non-active slides receive a per-slide `translateX` offset proportional to their distance from `currentIndex` and the current pointer velocity: `offset = velocity × TRAIL_INERTIA × distance`. Slides only ever trail *away* from the active slide (directional clamp via `Math.max/min(0, …)`) to prevent overlap. On release, trail offsets animate to 0 using the same `dur` + `power4.out` as the snap tween — no separate stagger.

### Navigation

- **Arrows** — centered on the grid edge. Duplicate-arrow slide animation on hover.
- **Dots** — centered on the active slide, 24px from the bottom edge.
- **Mouse drag** — velocity + distance threshold determines snap target.
- **Touch swipe** — same mechanics with horizontal/vertical scroll lock.

## Tunable constants

```js
const REAL_COUNT      = 3;    // number of real slides
const CLONE_COUNT     = 3;    // clones per side (outermost is safety buffer)
const GAP             = 8;    // px — matches grid gutter
const PARALLAX_FACTOR = 0.18; // image parallax intensity
const TRANSITION_DUR  = 0.6;  // arrow/dot nav duration (s)
const DRAG_MASK_INSET = 4;    // % — clip-path inset depth while dragging
const TRAIL_INERTIA   = 40;   // ms — neighbor trail offset = velocity (px/ms) × this value
```

## Slide content

| # | Quote | Name | Title | CTA |
|---|---|---|---|---|
| 1 | "Firefly helped me unlock the true speed of my creativity." | Noah Spencer | Digital Creator, Studio Games | Create with Firefly |
| 2 | "I can go from concept to final design in half the time." | Maria Chen | Senior Designer, Brightworks | Explore Photoshop |
| 3 | "The tools just disappear and I can focus on the art." | James Okafor | Illustrator & Art Director | Try Illustrator |
