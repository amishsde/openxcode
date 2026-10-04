# Mobile Experience Design (320px – 599px)

This reference defines enterprise standards for mobile-first user experience, thumb-zone reachability, touch target ergonomics, virtual keyboard adaptability, and native Material Design 3 / iOS HIG parity.

---

## 1. Thumb-Zone Ergonomics & Reachability

Smartphones are held predominantly with one hand, relying on the thumb for navigation and primary inputs. Interfaces must be architected around natural thumb reach arcs:

### The 3 Thumb Zones
1. **Natural Reach Zone (Bottom 1/3 of the screen)**:
   - **Elements**: Primary CTAs, bottom navigation, floating action buttons (FAB), bottom sheet triggers, confirmation actions.
   - **Rule**: High-frequency actions MUST live here. Never force one-handed users to stretch to the top corners for primary tasks.
2. **Stretch Zone (Middle 1/3 of the screen)**:
   - **Elements**: Scrollable content feeds, cards, form input fields, selection chips.
   - **Rule**: Easily reachable with minor thumb extension or regular vertical scrolling.
3. **Hard-to-Reach Zone (Top 1/3 of the screen)**:
   - **Elements**: Back navigation arrow, screen titles, view-only headers, secondary overflow menus (`...`).
   - **Rule**: Reserved strictly for passive information or infrequent low-risk actions.

### Sticky Bottom Action Bars
Forms and checkout funnels must anchor primary action buttons to the bottom of the viewport:
```css
.mobile-bottom-bar {
  position: sticky;
  bottom: 0;
  left: 0;
  right: 0;
  width: 100%;
  padding: 12px 16px;
  padding-bottom: max(16px, env(safe-area-inset-bottom));
  background: var(--color-surface);
  border-top: 1px solid var(--color-outline-variant);
  box-shadow: 0 -4px 12px rgba(0, 0, 0, 0.05);
  z-index: 10;
}
```

---

## 2. Touch Target Hit-Box Physics

Fingers have high contact variability and zero mouse-pointer precision. Small buttons result in rage clicks and accidental submissions.

### Standards
- **Minimum Physical Target Size**: 48 x 48 px (Android MD3 minimum 48x48 dp; iOS HIG minimum 44x44 pt).
- **Minimum Touch Separation**: At least 8 px spacing between adjacent interactive elements to eliminate false taps.
- **Pseudo-Element Hit Box Expansion**: If an icon or indicator is visually small (e.g. 20 px close icon), expand its touch target using pseudo-elements:
  ```css
  .icon-button-touch {
    position: relative;
    width: 24px;
    height: 24px;
    display: inline-flex;
    align-items: center;
    justify-content: center;
  }
  .icon-button-touch::before {
    content: '';
    position: absolute;
    top: 50%;
    left: 50%;
    width: 48px;
    height: 48px;
    transform: translate(-50%, -50%);
  }
  ```

---

## 3. Modal Bottom Sheets vs Centered Popups

Never display centered desktop-style modals on mobile devices. They obscure context, make the top-right close icon unreachable, and produce awkward scrolling bugs.

### Standards
- **Use Bottom Sheets**: Anchor all contextual menus, pickers, filters, and quick confirmation dialogs to the bottom of the screen.
- **Visual Styling**:
  - Border radius: `16px 16px 0 0` (or `28px 28px 0 0` for MD3).
  - Drag handle indicator: 32 x 4 px rounded pill centered 8 px from the top.
  - Backdrop overlay: 40% black (`rgba(0, 0, 0, 0.4)`).
- **Scroll & Overscroll Containment**:
  ```css
  .bottom-sheet {
    position: fixed;
    bottom: 0;
    left: 0;
    right: 0;
    max-height: 85dvh;
    border-radius: 20px 20px 0 0;
    background: var(--color-surface);
    overflow-y: auto;
    overscroll-behavior: contain;
    -webkit-overflow-scrolling: touch;
    touch-action: pan-y;
    z-index: 30;
  }
  ```

---

## 4. Virtual Keyboard & Viewport Dynamics

When the mobile on-screen keyboard opens, the visible viewport contracts drastically (often down to 250–320 px of vertical space).

### Standards
- **Use `dvh` Units**: Always use `min-height: 100dvh` (Dynamic Viewport Height) rather than `100vh`. Standard `vh` ignores the dynamic appearance/disappearance of mobile browser navigation bars.
- **Prevent Forced iOS Zoom**: All form inputs, selects, and textareas must have `font-size: 1rem` (16 px) or higher. Font sizes below 16 px trigger automatic browser zooming on focus in iOS Safari, ruining page alignment.
- **Input Types & Specialized Keypads**: Explicitly invoke native keypads using `inputmode` and `enterkeyhint`:
  - Phone: `<input type="tel" inputmode="tel" />`
  - Currency / OTP / PIN: `<input type="text" inputmode="numeric" pattern="[0-9]*" />`
  - Search: `<input type="search" enterkeyhint="search" />`
  - Next Field: `enterkeyhint="next"`
- **Viewport Keyboard Resize Meta**: Ensure HTML `<meta name="viewport" content="width=device-width, initial-scale=1.0, interactive-widget=resizes-content">` is configured so sticky action buttons rest above the open keyboard.

---

## 5. Mobile Navigation & Safe Area Insets

### Mobile Navigation Patterns (Bottom Nav, Header Bar, or Drawer)
Whatever navigation style is chosen for the application, it must adapt cleanly to small mobile widths:
- **Bottom Navigation Bar**: Ideal for core destinations (3 to 5 items maximum). Keep labels concise (11–12 px) and active state clearly highlighted with high contrast or a subtle container pill.
- **Top App Header**: Keep height compact (48 px to 56 px). Position primary navigation triggers (back button or menu drawer) on the left, with title left-aligned or centered, and a maximum of 1–2 action icons on the right.
- **Safe Area Insets (Notches & Home Indicators)**:
  Mobile screens feature rounded display corners, camera notches, dynamic islands, and home swipe bars:
  ```css
  /* Always respect safe areas on mobile devices */
  .mobile-app-header {
    padding-top: max(12px, env(safe-area-inset-top));
  }

  .mobile-bottom-nav {
    padding-bottom: max(8px, env(safe-area-inset-bottom));
  }
  ```

---

## 6. Zero-Hover Architecture & Native Touch States

Hover states do not exist on touchscreen mobile devices. Coding hover-reliant behavior breaks mobile usability.

### Standards
- **Never Rely on Hover**: Tooltips, hover reveals, and action icons shown on hover must never be used. All interactive elements must be visible by default or toggled via explicit tap.
- **Instant Touch Feedback**:
  - Remove sticky gray mobile web tap highlight: `-webkit-tap-highlight-color: transparent;`.
  - Provide immediate active feedback:
    ```css
    .touch-interactive:active {
      opacity: 0.7;
      transform: scale(0.98);
      transition: transform 50ms ease, opacity 50ms ease;
    }
    ```
- **Single-Column Mobile Forms**: Form fields must strictly stack in a single vertical column on mobile screens (<600px). Never place First Name and Last Name inputs side-by-side on 360–400px screens.
