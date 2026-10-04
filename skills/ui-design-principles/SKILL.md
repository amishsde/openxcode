---
name: ui-design-principles
description: >
  Enforces strict, professional UI/UX design standards for AI-generated code.
  Guarantees mobile-first responsiveness (320px to 4K), 4px spacing scale,
  fluid typography, Material Design 3 patterns, zero emojis, and WCAG AA accessibility.
license: MIT
---

# UI Design Principles for AI-Generated Code

Follow every rule below whenever you generate UI code (web, hybrid or app). If a rule conflicts with a request, follow the request but keep all other rules intact. Before delivering, run the checklist in section 14.

---

## 1. Core Philosophy

- Build mobile-first, then scale up to tablet, laptop and desktop. Never build desktop-only and shrink it.
- The UI must look professional, calm and consistent: same spacing, same sizes and same behaviour for the same kind of element everywhere.
- Look and feel on mobile must match a modern native Android app (Material Design 3).
- No emojis anywhere. Use icons only.
- No decorative or attention-grabbing animation. Only functional, short, subtle motion.
- Never hard-code random values. Use the tokens and scales defined in this document.

---

## 2. Device Coverage and Breakpoints

The layout must work without breaking on every common screen width.

| Class | Width range | Examples that must be checked |
|---|---|---|
| Small mobile | 320 - 359 px | 320, 340, 360 |
| Mobile | 360 - 599 px | 360, 375, 390, 393, 412, 414, 430, 480 |
| Tablet portrait | 600 - 839 px | 600, 768, 810, 820 |
| Tablet landscape / small laptop | 840 - 1279 px | 912, 1024, 1180 |
| Laptop | 1280 - 1599 px | 1280, 1366, 1440, 1536 |
| Desktop | 1600 - 2559 px | 1680, 1920, 2048 |
| Large desktop / 2K / 4K scaled | 2560 px and above | 2560, 3840 |

Breakpoints to use in CSS (min-width, mobile-first):

```css
--bp-sm: 360px;
--bp-md: 600px;
--bp-lg: 840px;
--bp-xl: 1280px;
--bp-2xl: 1600px;
```

Rules:
- Never allow horizontal page scroll at any width from 320 px upward.
- Use `min-height: 100dvh` (not `100vh`) for full-screen layouts.
- Respect safe areas on notched phones: `padding-bottom: env(safe-area-inset-bottom)` for bottom bars.
- Use fluid units (`rem`, `%`, `fr`, `clamp()`) for sizing. Use `px` only for borders, shadows and fixed touch targets.
- Test both portrait and landscape on mobile and tablet.

---

## 3. Layout and Grid

| Device | Columns | Page gutter (side padding) | Column gap |
|---|---|---|---|
| Mobile (< 600) | 4 | 16 px | 16 px |
| Tablet (600 - 839) | 8 | 24 px | 24 px |
| Laptop (840 - 1599) | 12 | 32 px | 24 px |
| Desktop (>= 1600) | 12 | 40 px | 32 px |

- Content container: `max-width: 1280px` for standard pages, `1440px` for dashboards. Center it with `margin-inline: auto`. On very large screens the content must not stretch edge to edge.
- Reading text width: max 65 - 75 characters per line (`max-width: 68ch`).
- Use CSS Grid for page structure and Flexbox for component rows. Do not position with fixed pixel offsets.
- Navigation pattern per device:
  - Mobile: top app bar + bottom navigation bar (max 5 items).
  - Tablet: navigation rail on the left (80 px wide).
  - Laptop / desktop: persistent sidebar (240 - 280 px) or top navbar.
- Cards grid: 1 column on mobile, 2 on tablet, 3 - 4 on laptop/desktop using `repeat(auto-fill, minmax(280px, 1fr))`.

---

## 4. Spacing System

Use a 4 px base unit. Only these values are allowed for padding, margin and gap:

```css
--space-1: 4px;
--space-2: 8px;
--space-3: 12px;
--space-4: 16px;
--space-5: 24px;
--space-6: 32px;
--space-7: 48px;
--space-8: 64px;
```

Usage guide:
- Icon to label gap: 8 px.
- Between related items (label and input, title and subtitle): 4 - 8 px.
- Between form fields: 16 px.
- Card inner padding: 16 px mobile, 20 - 24 px tablet and above.
- Between sections: 32 px mobile, 48 px tablet, 64 px laptop and above.
- Page top and bottom padding: 16 - 24 px mobile, 32 - 48 px desktop.
- Inner spacing must always be smaller than or equal to the outer spacing of its container.
- Same component = same padding on every screen of the same class. No one-off values.

---

## 5. Typography

Font:
- Primary: `Roboto` (native Android feel). Fallback: `Inter, system-ui, -apple-system, "Segoe UI", sans-serif`.
- Use one family for the whole product. Monospace only for code.

Type scale (fluid, using clamp):

| Role | Mobile | Desktop | Weight | Line height |
|---|---|---|---|---|
| Display / hero | 28 px | 40 - 48 px | 600 | 1.2 |
| Heading 1 | 24 px | 32 px | 600 | 1.25 |
| Heading 2 | 20 px | 24 px | 600 | 1.3 |
| Heading 3 | 18 px | 20 px | 500 | 1.35 |
| Body large | 16 px | 18 px | 400 | 1.5 |
| Body (default) | 14 - 16 px | 16 px | 400 | 1.5 |
| Label / button | 14 px | 14 - 15 px | 500 | 1.25 |
| Caption / helper | 12 px | 13 px | 400 | 1.4 |

```css
:root {
  --fs-display: clamp(1.75rem, 1.2rem + 2.2vw, 3rem);
  --fs-h1: clamp(1.5rem, 1.2rem + 1.2vw, 2rem);
  --fs-h2: clamp(1.25rem, 1.1rem + 0.6vw, 1.5rem);
  --fs-body: clamp(0.875rem, 0.85rem + 0.15vw, 1rem);
}
```

Rules:
- Minimum text size is 12 px. Body text never below 14 px.
- Allowed weights: 400, 500, 600. Use 700 only for rare emphasis. Never use 300 or lighter for body text.
- Input text must be at least 16 px on mobile to avoid forced zoom.
- No ALL CAPS paragraphs. Uppercase is allowed only for very short labels with letter-spacing 0.5 px.
- Do not mix more than 3 font sizes inside a single card or component.
- Long text must wrap or truncate with ellipsis. Never overflow its container.

---

## 6. Color System

Define every color as a token (CSS variables). Never write raw hex inside components.

```css
:root {
  --color-primary: #1A73E8;
  --color-on-primary: #FFFFFF;
  --color-primary-container: #D3E3FD;
  --color-on-primary-container: #041E49;

  --color-surface: #FFFFFF;
  --color-surface-variant: #F1F3F4;
  --color-background: #F8F9FA;
  --color-outline: #747775;
  --color-outline-variant: #C4C7C5;

  --color-text-primary: #1F1F1F;
  --color-text-secondary: #444746;
  --color-text-disabled: #9AA0A6;

  --color-success: #1E8E3E;
  --color-warning: #B06000;
  --color-error: #D93025;
}
```

Rules:
- One primary color plus neutrals. Semantic colors (success, warning, error) only for status.
- Text contrast must meet WCAG AA: 4.5:1 for normal text, 3:1 for large text and UI borders.
- Text hierarchy: primary text for titles, secondary text for supporting info, disabled for inactive.
- Never use pure black (`#000`) text on pure white. Use the text tokens above.
- Never communicate state with color alone. Pair it with an icon or text.
- Provide a dark theme with `prefers-color-scheme: dark` using the same token names. Dark surfaces use dark grey (`#121212` - `#1E1E1E`), not pure black.
- Gradients are not allowed unless the user asks for them.

---

## 7. Buttons

| Property | Mobile | Tablet | Laptop / Desktop |
|---|---|---|---|
| Height | 48 px | 44 - 48 px | 40 px |
| Min touch target | 48 x 48 px | 44 x 44 px | 36 x 36 px |
| Horizontal padding | 24 px | 24 px | 20 - 24 px |
| Border radius | 24 px (pill) or 12 px | same | 8 - 12 px |
| Label | 14 px, weight 500 | same | 14 px, weight 500 |

- Keep one radius style across the whole product (all pill or all rounded-rectangle).
- Variants: Filled (primary action, one per screen section), Tonal (secondary), Outlined (tertiary), Text (low emphasis).
- On mobile, primary buttons in forms and dialogs are full width. On tablet and above they are auto width, aligned right in dialogs and forms.
- Button label color must contrast with its background (see section 6). Label on filled button uses `--color-on-primary`.
- Icon inside button: 20 px, gap 8 px from label, vertically centered.
- States are mandatory: default, hover (desktop only, +8% overlay), focus-visible (2 px outline, 2 px offset), pressed (+12% overlay), disabled (38% opacity, no pointer), loading (spinner replaces icon, width stays the same).
- Buttons in a row: gap 8 - 12 px. Equal heights. Primary on the right (desktop) or on top (mobile stack).
- Never shrink a button below its label width. Use `white-space: nowrap`.

---

## 8. Form Fields, Select and Dropdown

Text fields:
- Height 48 px (mobile) / 40 - 44 px (desktop), or 56 px for Material filled/outlined style on mobile.
- Horizontal padding 16 px. Radius 8 - 12 px. Border 1 px `--color-outline`, 2 px `--color-primary` on focus.
- Label above the field (12 - 14 px, weight 500) with 4 - 8 px gap. Helper or error text below with 4 px gap, 12 px size.
- Placeholder uses `--color-text-secondary` and must never replace a label.

Select / dropdown (strict):
- Hide the native arrow with `appearance: none`.
- Draw a chevron-down icon inside the box, vertically centered, with **16 px gap from the right edge** (12 px minimum on very small fields).
- Reserve space for the icon with `padding-right: 48px` so text never runs under the icon.
- The icon must not capture clicks (`pointer-events: none`). Clicking anywhere on the box opens the list.
- The icon rotates 180 degrees when the list is open (150 ms). No other motion.
- The dropdown list: same width as the field, radius 8 - 12 px, max-height 280 px with scroll, item height 48 px (mobile) / 40 px (desktop), item padding 16 px, selected item uses `--color-primary-container`.
- On mobile, prefer a bottom sheet for long lists.

```css
.select-wrap { position: relative; }

.select {
  width: 100%;
  height: 48px;
  padding: 0 48px 0 16px;      /* right padding leaves room for icon */
  border: 1px solid var(--color-outline);
  border-radius: 12px;
  background: var(--color-surface);
  color: var(--color-text-primary);
  font: 400 1rem/1.25 inherit;
  appearance: none;
  -webkit-appearance: none;
  text-overflow: ellipsis;
}

.select-wrap .select-icon {
  position: absolute;
  right: 16px;                 /* proper gap from right edge */
  top: 50%;
  width: 20px;
  height: 20px;
  transform: translateY(-50%);
  pointer-events: none;
  color: var(--color-text-secondary);
}
```

Other form rules:
- Same height for every input, select and button placed on the same row.
- Error state: red border, error icon inside the field with the same 16 px right gap, message below.
- Checkbox and radio: 20 px visual size inside a 48 px touch area (24 px on desktop).
- Switches follow the Material 3 size (52 x 32 px).

---

## 9. Icons (No Emojis)

- **Emojis are forbidden** in UI, labels, buttons, placeholders, toasts, alt text, console logs, comments and sample data.
- Use one icon set only: Material Symbols (Rounded or Outlined) or Lucide. Never mix sets.
- Sizes: 20 px inside buttons and fields, 24 px default, 32 px for empty states, 48 px maximum for illustrations.
- Same stroke weight and style for every icon.
- Icon color follows text color tokens. Active nav icon uses the primary color.
- Icon-only buttons must have `aria-label` and a 48 x 48 px touch target (36 px on desktop).
- Use SVG (inline or component). Do not use image files for icons.

---

## 10. Native Android Look and Feel (Mobile)

- Follow Material Design 3 patterns.
- Top app bar: 56 px height, title 20 px weight 500, left-aligned, back arrow on the left, max 2 action icons on the right.
- Bottom navigation: 80 px height (including label), 3 - 5 items, icon 24 px + label 12 px, active indicator pill 64 x 32 px in `--color-primary-container`.
- FAB: 56 px, 16 px from the right and bottom edges (above bottom nav).
- Cards: radius 12 - 16 px, 16 px padding, tonal surface or very light elevation. No heavy shadows.
- Lists: 56 - 72 px row height, 16 px side padding, 1 px divider only when needed.
- Dialogs: radius 28 px, 24 px padding, max-width 560 px, actions aligned right.
- Bottom sheets for pickers and menus on mobile. Snackbar (not alert) for feedback: bottom, 48 px high, auto-hide in 4 s.
- Touch feedback: subtle ripple or opacity overlay on press. No hover-only interactions on touch devices.
- Scrolling is native-like: `-webkit-overflow-scrolling: touch`, `overscroll-behavior: contain` inside sheets and dialogs.
- Use `-webkit-tap-highlight-color: transparent` and provide custom pressed feedback.

---

## 11. Animation and Motion

Principle: motion explains a change. If it does not help the user understand something, remove it.

Allowed (only when needed):
- Page / screen transition: fade or short slide, 200 - 300 ms.
- Dialog, bottom sheet and menu open/close: fade + small translate or scale from 0.95, 150 - 250 ms.
- Dropdown chevron rotation: 150 ms.
- Button, chip and list-item state change (hover, press, focus): 100 - 150 ms color or opacity.
- Expand / collapse of accordions: 200 ms height or opacity.
- Loading: one skeleton shimmer (slow, subtle) or one small spinner. Only while loading, never as decoration.
- Toast / snackbar: fade and slide in, 200 ms.

Timing and easing:

```css
:root {
  --motion-fast: 120ms;
  --motion-base: 200ms;
  --motion-slow: 300ms;
  --ease-standard: cubic-bezier(0.2, 0, 0, 1);
  --ease-exit: cubic-bezier(0.4, 0, 1, 1);
}
```

Forbidden:
- Infinite or looping animations (except the loading spinner / skeleton while data loads).
- Bounce, shake, wobble, pulse, glow, flashing, floating, rotating logos or banners.
- Auto-playing carousels, marquees, typewriter effects and parallax.
- Animations longer than 400 ms. Scroll-jacking. Animations that delay the user from acting.
- Animating layout properties (`width`, `height`, `top`, `left`, `margin`) when `transform` and `opacity` can do the job.

Always include:

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
  }
}
```

---

## 12. Alignment and Structure

- Align everything to the grid. Elements in a column share the same left edge. Elements in a row share the same vertical center or baseline.
- Page structure order: app bar / header, page title, content sections, footer. Same order on every page.
- Each section has one clear heading and consistent spacing from the previous section (see section 4).
- Use consistent component heights in a row (buttons, inputs, selects, chips = same height).
- Text alignment: left for text (right for RTL languages), numbers in tables right-aligned, headings never centered inside long forms.
- Center alignment is allowed only for short content: empty states, hero blocks, dialogs titles on mobile.
- Tables on mobile: convert to cards or allow controlled horizontal scroll inside the table container only (never the page).
- Images: always set `aspect-ratio`, `object-fit: cover`, and `max-width: 100%`. No layout shift on load.
- Use `gap` for spacing between siblings instead of margins on each child.
- Z-index scale: content 0, sticky 10, dropdown 20, modal 30, toast 40. No random large values.

---

## 13. States, Accessibility and Quality

- Every screen must define: loading, empty, error and success states. Empty states use an icon, one line of text and one action.
- Visible `:focus-visible` outline on every interactive element. Keyboard navigation must work on desktop.
- Use semantic HTML (`button`, `nav`, `main`, `label`, `table`). Do not make a `div` act like a button.
- Disabled elements must look disabled and not respond to clicks.
- Support text zoom up to 200% without breaking layout.
- Do not use `!important` except for the reduced-motion rule.
- Keep CSS organized with tokens at `:root`, then base styles, then components. Do not repeat the same values in many places.

---

## 14. Pre-Delivery Checklist

Confirm every item before delivering code:

- [ ] Works at 320, 360, 390, 412, 768, 1024, 1366, 1440, 1920 and 2560 px with no horizontal scroll and no overlapping.
- [ ] Spacing uses only the 4 px scale. Page gutters match section 3.
- [ ] Font family, sizes and weights follow the type scale. Body text is at least 14 px.
- [ ] All colors come from tokens and pass contrast checks. Dark theme works.
- [ ] Buttons meet height and touch-target sizes with all states defined.
- [ ] Every select and dropdown has a chevron icon with 16 px right gap and 48 px right padding.
- [ ] Inputs, selects and buttons in the same row have equal heights.
- [ ] Only one icon set is used. Zero emojis anywhere.
- [ ] Mobile has native Android feel (app bar, bottom nav, bottom sheets, ripple/press feedback).
- [ ] Animations are only the allowed ones, 120 - 300 ms, no infinite or decorative motion, reduced-motion supported.
- [ ] Loading, empty and error states exist for every data screen.
- [ ] Alignment, grid and section order are consistent on every page.
