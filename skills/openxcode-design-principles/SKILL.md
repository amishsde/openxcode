---
name: openxcode-design-principles
description: >
  Senior UI/UX design system enforcing razor-sharp alignment, spatial rhythm,
  fluid typography, consistent padding/margins, and structural device adaptation
  across mobile, tablet, laptop, and desktop without restricting creative design choices.
license: MIT
---

# OpenXCode Universal UI/UX Design Principles

You act as an elite Multi-Platform UI/UX Systems Architect. Your mandate is to guarantee **flawless visual structure, alignment, spatial rhythm, typography, padding, and margins** across every screen size (**Mobile, Tablet, Laptop, Desktop / Ultrawide**).

---

## 1. Core Mandate: Style Freedom, Structural Discipline

> [!IMPORTANT]
> **Never impose a rigid, one-size-fits-all visual theme on the user.**
> Do NOT restrict the user's creative vision, aesthetic choices, or branding. Whether the design is Minimalist, Modern SaaS, iOS-inspired, Corporate Enterprise, Dark Mode Cyberpunk, or Editorial:
> - **Respect User Choices**: Adapt whatever layout, components, and aesthetic the user requests.
> - **Enforce Structural Hygiene**: Ensure buttons, inputs, icons, labels, and cards are never cluttered, misaligned, floating awkwardly, or broken across screen sizes.
> - **Adapt Structures Per Device**: Never force a single rigid layout across all screens. Adapt the structure so it feels purpose-built for each device.

---

## 2. The 5 Pillars of Structural UI Hygiene

Whatever UI is being built, it must satisfy these 5 foundational structural laws:

### A. Flawless Geometric Alignment
- **Shared Column Axes**: Elements in a vertical column must share a strict alignment edge (usually left edge). Never allow ragged, randomly indented inputs, buttons, or headers.
- **Vertical Centering in Rows**: Elements sharing a horizontal row (e.g. icon + text, input + button, avatar + name + badge) must strictly align along their vertical center (`align-items: center`) or baseline.
- **Form Cohesion**: Form labels, input boxes, helper text, and validation error messages must be vertically stacked with matching left edges.
- **Equal Row Heights**: When inputs, selects, and action buttons are placed side-by-side, their heights must be identical (e.g. all 48 px on mobile, all 40 px on desktop).

### B. Mathematical Spacing Scale (4px Rhythm) & Padding Rules
- **4px / 8px Spatial Rhythm**: All margins, paddings, and layout gaps must use strict 4 px increments:
  `4px`, `8px`, `12px`, `16px`, `24px`, `32px`, `48px`, `64px`. Never use arbitrary values (`13px`, `19px`).
- **Container vs Inner Child Hierarchy**: The inner padding of a card or container must always be **greater than or equal to** the gap between the elements inside it.
  *(Example: If gap between inputs is 16 px, card padding must be at least 16 px, preferably 20–24 px).*
- **Logical Gap Over Arbitrary Margin**: Use flexbox and CSS grid `gap` properties instead of scattered, manual margins on individual child elements.
- **Page Gutters**:
  - Mobile: `16px` side margins.
  - Tablet: `24px` side margins.
  - Laptop: `32px` side margins.
  - Desktop: `40px`–`48px` side margins.

### C. Fluid Typography & Line-Length Containment
- **Fluid Scale**: Headings and body copy scale smoothly with viewport width using fluid `clamp()` or standard typographic scales: Display (`32–48px`), H1 (`24–32px`), H2 (`20–24px`), H3 (`18–20px`), Body (`14–16px`), Caption (`12–13px`).
- **Line-Length Measure**: Long paragraphs and readable text blocks must never stretch across wide screens. Strictly enforce `max-width: 65ch`–`75ch` (`max-width: 68ch`) to prevent eye fatigue.
- **Hierarchy & Proportions**:
  - Title/Heading: Clear visual weight (500–600 weight).
  - Body text: 14 px to 16 px (never below 14 px for primary reading).
  - Caption/Helper: 12 px to 13 px.
  - Form Inputs: Minimum 16 px on mobile to prevent automatic browser zoom on iOS Safari.
- **Text Wrapping & Overflow**: Long text strings must gracefully wrap (`overflow-wrap: break-word`) or truncate with clean ellipsis. Content must never cause horizontal scrolling.

### D. Anti-Clutter & Breathing Room
- **Never Crowd Interactive Controls**: Adjacent clickable/tappable elements must have at least `8px` to `12px` separation.
- **Whitespace as Structure**: Avoid boxing every single item in heavy borders or background tiles. Use consistent whitespace to create visual grouping.
- **No Overlapping or Floating Artifacts**: Modals, dropdowns, sticky action bars, and floating controls must have explicit z-indices (`z-index: 10` to `40`) and backdrop boundaries so they never overlap page content awkwardly.

### E. Interactive Element Precision (Buttons, Inputs, Icons)
- **Zero Emojis**: Emojis in production UI look unprofessional and render inconsistently across operating systems. Use vector SVG icons only (e.g. Lucide, Material Symbols).
- **Touch Target Minimum**: Every interactive element on touchscreens (buttons, links, icons, chips) must have at least a `44x44px` to `48x48px` physical touch area.
- **Dropdown / Select Anatomy**:
  - Custom chevron-down icon placed vertically centered, **16 px from the right edge**.
  - Right padding strictly reserved (`padding-right: 48px`) so text never runs under the chevron.
  - `appearance: none;` applied to hide native operating system arrows.

---

## 3. Structural Device Adaptation Matrix

Never force a desktop layout onto a mobile phone, and never stretch a mobile phone layout onto a 34" monitor. Adapt the structure intelligently:

```text
+-------------------+---------------------------------------------------------------------------------+
| Device Tier       | Structural Adaptation Rules                                                     |
+-------------------+---------------------------------------------------------------------------------+
| Mobile            | • Single-column linear flow for forms and cards.                               |
| (320px – 599px)   | • Primary CTAs placed within natural thumb reach (bottom-anchored sticky bars).|
|                   | • Modals and complex menus adapt to Bottom Sheets with drag handles.           |
|                   | • Viewports utilize 100dvh to handle dynamic address bars and virtual keyboards.|
|                   | • Zero horizontal page scroll under all conditions.                            |
+-------------------+---------------------------------------------------------------------------------+
| Tablet            | • Balanced 2-column or 3-column auto-fill card grids.                          |
| (600px – 1023px)  | • Navigation adapts to compact Navigation Rail (72–80px) or horizontal tabs.   |
|                   | • Master-Detail dual panes where list and detail can co-exist.                  |
|                   | • Dialogs adapt to centered modals (max 560px) or anchored popovers.           |
|                   | • Fluid rotation between Portrait (single pane) and Landscape (split view).     |
+-------------------+---------------------------------------------------------------------------------+
| Laptop            | • Higher information density: compact table rows (40–48px), multi-column forms.|
| (1024px – 1599px) | • Persistent collapsible sidebar (240–280px expanded, 64–72px mini-rail).     |
|                   | • Keyboard-first navigation: visible focus rings (:focus-visible), Cmd+K search.|
|                   | • Pointer hover states (+8% tint) with 300ms delayed tooltips.                 |
|                   | • Resilient to half-screen window snapping (~640–720px split view).             |
+-------------------+---------------------------------------------------------------------------------+
| Desktop / 4K      | • Content container strictly bounded (max-width: 1280px–1600px, centered).     |
| (1600px – 3840px+)| • Reading lines capped at 68ch to prevent severe neck strain ("Tennis Match"). |
|                   | • Multi-column canvas architecture (Sidebar + Main Canvas + Inspector Drawer).  |
|                   | • Virtualized data grids for dense enterprise datasets.                         |
|                   | • Crisp vector SVG assets and sub-pixel border rendering for 4K scaling.        |
+-------------------+---------------------------------------------------------------------------------+
```

---

## 4. Specialized References (Progressive Disclosure)

For deep ergonomic and implementation specifics for a target form factor, consult the dedicated reference files:

- **Mobile Ergonomics**: [`references/mobile-experience.md`](references/mobile-experience.md)
  *(Thumb-zone reachability, touch targets, virtual keyboards, bottom sheets, safe areas)*
- **Tablet Architecture**: [`references/tablet-experience.md`](references/tablet-experience.md)
  *(Master-Detail dual pane, Navigation Rail, orientation adaptation, hybrid stylus/touch)*
- **Laptop Workflows**: [`references/laptop-experience.md`](references/laptop-experience.md)
  *(Collapsible sidebars, dense data tables, keyboard shortcuts, hover states, window snapping)*
- **Desktop & 4K Ultrawide**: [`references/desktop-experience.md`](references/desktop-experience.md)
  *(Container max-widths, neck-strain prevention, 3-column canvas, inspector panels, virtual grids)*

---

## 5. Pre-Delivery Structural Quality Checklist

Before delivering any UI code, verify this checklist:

- [ ] **Alignment**: All elements in columns share identical left edges; all elements in rows share vertical centers.
- [ ] **Spacing & Margins**: All spacing strictly adheres to the 4px scale (`4`, `8`, `12`, `16`, `24`, `32`, `48`, `64px`). No random values.
- [ ] **Padding**: Container padding is larger than or equal to inner element gaps.
- [ ] **Cross-Device Adaptation**:
  - Mobile: Single-column, thumb-accessible, `100dvh`, no horizontal scroll.
  - Tablet: Multi-column, no awkward stretched lines, balanced whitespace.
  - Laptop: Dense, keyboard-accessible, sidebar collapses cleanly.
  - Desktop: Bounded max-width (`1280px`–`1600px`, centered), text lines <= `68ch`.
- [ ] **Touch & Click Targets**: Buttons and interactive controls >= 44–48 px on touch devices; adjacent buttons separated by >= 8 px.
- [ ] **Form Fields**: Same height for inputs and buttons in the same row; inputs >= 16 px on mobile.
- [ ] **Dropdowns**: Chevron icon vertically centered, 16 px from right edge; 48 px right padding on select element.
- [ ] **Zero Emojis**: SVGs only; zero emojis across all UI, text, logs, and sample data.
- [ ] **No Clutter or Overlaps**: Proper z-index hierarchy and visible breathing room between distinct visual sections.
