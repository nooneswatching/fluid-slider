# Fluid Slider Prototype

## Overview
Full-width carousel component for ACOM. Each slide is a 16:9 card with a full-bleed image, overlaid quote text, attribution, and CTA button. Slides are sized to match the shared grid system and support infinite looping, parallax, mouse drag, and touch swipe.

## Dependencies
- `../global/styles/reset.css`
- `../global/styles/grid.css` — defines breakpoints, columns, margins, and gutter
- `../assets/Fonts/AdobeCleanDisplay-{Regular,Bold,Black}.otf`

## Grid Alignment
Slide width is computed from the `.grid` container's content-box (viewport width minus grid margins, capped at 1920px). A hidden `.grid` reference element is measured via JS on every resize — no debounce, so the slide tracks the grid 1:1 in real time. Toggle the grid overlay with the **G** key to verify alignment.

### Breakpoints
| Name | Range | Columns | Margin |
|------|-------|---------|--------|
| XS | < 768px | 6 | 24px |
| S | 768–1023px | 6 | 24px |
| M | 1024–1279px | 12 | 8.333% |
| L | 1280–1440px | 12 | 8.333% |
| XL | > 1440px | 12 | 8.333% (max 1920px) |

## Architecture

### Infinite Loop
The first and last slides are cloned and appended/prepended to the track. After a transition lands on a clone, the track instantly jumps to the corresponding real slide with all transitions disabled (`.dragging` class on the track suppresses both track transform and slide opacity transitions to prevent flash).

### Parallax
Image containers are 120% wide with `overflow: hidden`. During slide transitions, `requestAnimationFrame` polls the track's computed `transform` and applies an inverse offset to each image (factor: 0.18). No CSS transition on images — parallax is driven purely by JS to avoid settling glitches.

### Navigation
- **Arrow buttons**: Centered on the grid edge. Glassy default state (semi-transparent white + backdrop blur). On hover: solid black background with a duplicate-arrow slide animation (snappy cubic-bezier ease).
- **Dot indicators**: Centered horizontally on the active slide, 24px from the bottom edge. Semi-transparent white inactive, solid white active.
- **Mouse drag**: Click and drag to scrub. Velocity and distance thresholds determine whether to advance or snap back.
- **Touch swipe**: Same mechanics as mouse drag with horizontal/vertical scroll lock.

### Typography
- Quote: Adobe Clean Display Black, `clamp(28px, 3.5vw, 52px)`, hanging open quote via `text-indent: -0.55em`
- Attribution name: Adobe Clean Display Bold, 18px
- Attribution title: Adobe Clean Display Regular, 16px, 70% white
- CTA: Adobe Clean Display Bold, 15px, solid white pill button, hover inverts to black

## Slide Content
| # | Quote | Name | Title | CTA |
|---|-------|------|-------|-----|
| 1 | "Firefly helped me unlock the true speed of my creativity." | Noah Spencer | Digital Creator, Studio Games | Create with Firefly |
| 2 | "I can go from concept to final design in half the time." | Maria Chen | Senior Designer, Brightworks | Explore Photoshop |
| 3 | "The tools just disappear and I can focus on the art." | James Okafor | Illustrator & Art Director | Try Illustrator |

## Assets
- `assets/images/image_01.jpg` — Slide 1
- `assets/images/image_02.jpg` — Slide 2
- `assets/images/image_03.jpg` — Slide 3
- `assets/icons/arrow-left.svg` — Left arrow (11x11)
- `assets/icons/arrow-right.svg` — Right arrow (11x11)
