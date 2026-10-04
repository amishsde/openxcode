# Laptop Experience Design (1024px / 1280px – 1599px)

This reference defines enterprise standards for laptop user experiences: compact information density, persistent collapsible sidebars, keyboard-first workflows, hover states, and split-screen tiling resilience.

---

## 1. Persistent Collapsible Sidebar Navigation

On 13"–16" laptop displays, horizontal space allows a persistent navigation hierarchy while maximizing vertical work area:

### Standards
- **Expanded Sidebar State**:
  - Width: 240 px to 280 px.
  - Structure: Product branding at top, categorized group labels (12 px uppercase), active highlight pills with 20 px icons, nested expandable tree folders, and badge counters.
- **Collapsed Mini-Rail State**:
  - Width: 64 px to 72 px.
  - Displays icon-only destinations with instant tooltip preview on hover (150 ms delay).
- **Smooth Toggle Transition**:
  - Width transition: `transition: width 200ms cubic-bezier(0.2, 0, 0, 1);`.
  - Persist user's collapsed/expanded preference in `localStorage`.

```css
.laptop-sidebar {
  width: 260px;
  height: 100vh;
  display: flex;
  flex-direction: column;
  border-right: 1px solid var(--color-outline-variant);
  background: var(--color-surface);
  transition: width 200ms cubic-bezier(0.2, 0, 0, 1);
}

.laptop-sidebar.is-collapsed {
  width: 68px;
}
```

---

## 2. Keyboard-First Workflows & Command Palettes

Professional laptop users rely heavily on keyboards for speed and efficiency. Every primary workflow must be keyboard-accessible:

### Standards
- **Global Command Palette (`Cmd+K` / `Ctrl+K`)**:
  - Provide a modal command bar accessible via `Cmd+K` (macOS) / `Ctrl+K` (Windows/Linux).
  - Permits rapid fuzzy search across navigation routes, actions, entities, and settings.
- **Visible Focus Rings (`:focus-visible`)**:
  - Never suppress outline focus rings:
    ```css
    :focus-visible {
      outline: 2px solid var(--color-primary);
      outline-offset: 2px;
      border-radius: 4px;
    }
    ```
- **Modal Focus Trapping & Escape Key**:
  - When a modal, drawer, or dropdown is opened, trap `Tab` / `Shift+Tab` focus cycles entirely within the active layer.
  - Pressing `Escape` must immediately dismiss open overlays and return focus to the trigger button.
- **Data Table Keyboard Navigation**:
  - Support `ArrowUp` / `ArrowDown` navigation between rows in data tables, and `Enter` to open details.

---

## 3. High Information Density & Data Grids

Laptop interfaces require significantly higher density than touch devices to display complex workflows without excessive scrolling:

### Standards
- **Compact Row Heights**: Table row heights: 40 px (compact) to 48 px (comfortable).
- **Sticky Column Headers**:
  ```css
  .data-table thead th {
    position: sticky;
    top: 0;
    background: var(--color-surface);
    z-index: 2;
    border-bottom: 2px solid var(--color-outline-variant);
  }
  ```
- **Multi-Column Form Layouts**: Structure forms into clean 2-column or 3-column grids on laptop screens, grouping logically related fields (e.g. City, State, Postal Code in a 3-column row) with aligned baselines.
- **Density Toggles**: Allow users to toggle between "Comfortable" (48 px rows) and "Compact" (36–40 px rows) in data-heavy enterprise portals.

---

## 4. Mouse Pointer & Cursor Micro-Interactions

Unlike mobile and tablet, laptops feature high-precision mouse and trackpad pointers:

### Standards
- **Subtle Hover States**:
  - Interactive rows, buttons, and cards must display a clear hover response: background shift (+8% overlay tint) or subtle elevation lift.
  - Hover transitions: 100 ms to 150 ms (fast and crisp).
- **Tooltips with Delay**:
  - Show tooltips on icon-only buttons, truncated text cells, and status chips.
  - Enforce a 300 ms hover delay before opening to prevent visual noise while sweeping the mouse cursor across the screen.
- **Contextual Right-Click Menus**:
  - For power-user data tables, support custom right-click context menus (`contextmenu` event) with quick actions (Copy ID, Edit, Archive, Duplicate).

---

## 5. Window Snapping & Split-Screen Tiling Resilience

On 13"–15" laptop screens, users frequently snap windows side-by-side (Windows Snap Assist, macOS Split View / Stage Manager), effectively dividing the browser into a ~640 px to 720 px window:

### Standards
- **Fluid Degradation**:
  - When the browser window is snapped to half-screen, the UI must automatically collapse the 260 px persistent sidebar into a collapsible mini-rail or hamburger drawer.
  - Multi-column tables must retain horizontal scrolling containers (`overflow-x: auto`) without overflowing the page.
  - Top action bars must automatically compress actions into an overflow menu (`...`).
