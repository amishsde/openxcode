# Tablet Experience Design (600px – 1023px / 840px)

This reference defines enterprise standards for adaptive tablet interfaces, Master-Detail dual-pane layouts, Navigation Rails, orientation transitions, and hybrid input ergonomics (finger touch + stylus + trackpad).

---

## 1. Master-Detail Dual-Pane Architecture

Tablets offer substantially more horizontal canvas than mobile phones. Presenting a single full-screen list on a 768px–1024px tablet results in awkward, wasted whitespace. The defining UI pattern for tablet is **Master-Detail**:

### Split-Pane Dynamics
- **Left Pane (Master List)**: 320 px to 360 px fixed width (or 35% of container). Houses search, filters, and list items.
- **Right Pane (Detail View)**: Fluid remaining width (65%). Houses the selected record, full metadata, tabs, and editing controls.
- **Independent Scroll Containers**: The Master list and Detail canvas must have independent vertical scroll containers (`overflow-y: auto`), preventing the list from scrolling away when reading a long document.

```css
.tablet-master-detail {
  display: grid;
  grid-template-columns: 340px 1fr;
  height: 100dvh;
  overflow: hidden;
}

.tablet-master-pane {
  border-right: 1px solid var(--color-outline-variant);
  overflow-y: auto;
  background: var(--color-surface);
}

.tablet-detail-pane {
  overflow-y: auto;
  background: var(--color-background);
  padding: 24px 32px;
}
```

---

## 2. Tablet Navigation Adaptation (Rails, Tabs, or Collapsible Drawers)

Bottom navigation bars stretch uncomfortably across wide tablet screens. On tablet viewports (>= 600 px), adapt navigation to prevent excessive horizontal stretching:
- **Navigation Rail**: Ideal for multi-view apps. A 72 px to 80 px vertical column docked to the left screen edge.
- **Top Header with Tabs**: Clean alternative for content-first or document apps.
- **Collapsible Drawer / Slide-Over**: Perfect for rich menus or hierarchical navigation.

### Standards
- **Width**: 72 px to 80 px fixed vertical column docked to the left screen edge.
- **Top Section**: Product logo or primary Floating Action Button (FAB).
- **Middle Section**: 3 to 7 primary navigation destinations aligned vertically. Each item consists of an icon (24 px) with a 12 px label directly below.
- **Bottom Section**: Settings icon or user profile avatar.
- **Z-Index**: Pinned alongside content (`z-index: 10`).

```css
.tablet-nav-rail {
  width: 80px;
  height: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 16px 0;
  border-right: 1px solid var(--color-outline-variant);
  background: var(--color-surface);
}

.nav-rail-item {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 4px;
  width: 56px;
  padding: 8px 0;
  border-radius: 12px;
  text-decoration: none;
  font-size: 0.75rem;
}
```

---

## 3. Orientation Lifecycle (Portrait vs Landscape Transitions)

Tablets are routinely rotated between portrait (768 px or 810 px) and landscape (1024 px or 1080 px). The UI must adapt seamlessly:

### Responsive Behavior Matrix
- **Portrait Mode (600 px – 839 px)**:
  - Display Master view in single-pane with a compact Navigation Rail.
  - Tapping an item slides in the Detail view or opens a collapsible drawer.
  - Detail view contains a visible back button returning to the list.
- **Landscape Mode (840 px – 1023 px)**:
  - Automatically un-collapses into the persistent dual-pane Master-Detail layout.
  - Back button in Detail view is automatically hidden because both panes are simultaneously visible.
- **State Preservation**: Rotating between portrait and landscape must preserve the active item selection, text input drafts, and scroll positions.

---

## 4. Adaptive Modality & Contextual Popovers

Full-screen bottom sheets look absurd on a 10" tablet display. The interface must graduate to desktop-like adaptive modality:

### Standards
- **Centered Dialogs**: Replace full-height bottom sheets with centered modals (`max-width: 560px`, `border-radius: 28px`, centered with `margin: auto`).
- **Anchored Popovers**: Contextual menus and pickers must anchor directly beneath or adjacent to the triggering button via popovers/dropdowns, rather than taking over the entire screen.
- **Action Alignment**: Modal action buttons on tablet are aligned to the right side of the dialog footer (Primary CTA on the right, Cancel/Secondary on the left with 8 px gap).

---

## 5. Hybrid Input Ergonomics (Touch + Stylus + Trackpad)

Modern tablets (iPad, Galaxy Tab, Surface) are hybrid devices supporting touch, high-precision stylus (Apple Pencil, S-Pen), and external keyboards/trackpads:

### Standards
- **Target Sizing**: Minimum 44 x 44 px hit target (comfortably balances touch targets while allowing tablet information density).
- **Pointer / Cursor Support**: When external mouse/trackpad pointers hover over interactive elements, show subtle background highlights (`--color-surface-variant`) without requiring hover for core functionality.
- **Stylus Drag & Note Targets**: Provide ample margin buffers around editable canvases to prevent palm rejection misfires.
- **Multi-Window Tiling (Split View)**: Support narrow viewport widths (e.g. 1/3 iPad split screen = ~320–375 px) where the layout must automatically downscale to mobile-phone UI rules.
