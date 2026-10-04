# OpenXCode

Pragmatic software engineering & enterprise security guidelines for Antigravity AI. Enforces standard-library-first implementations, anti-monolith modularity (<250 lines), zero-error discipline, and OWASP-grade security auditing.

---

## Installation

Clone the repository into your local Antigravity plugins directory:

### Windows (PowerShell)
```powershell
git clone --depth 1 https://github.com/amishsde/openxcode.git "$HOME\.gemini\config\plugins\openxcode"
```

### macOS / Linux
```bash
git clone --depth 1 https://github.com/amishsde/openxcode.git ~/.gemini/config/plugins/openxcode
```

---

## Updating

### Check Updates In-Chat (Recommended)
Inside any Antigravity chat session, run:
```text
/openxcode-update-check
```

### Apply Update (Terminal)
**Windows (PowerShell):**
```powershell
cd "$HOME\.gemini\config\plugins\openxcode"; git pull origin main
```

**macOS / Linux:**
```bash
cd ~/.gemini/config/plugins/openxcode && git pull origin main
```

---

## Usage

Use these slash commands directly in your Antigravity chat:

### 1. `/openxcode <prompt>`
Primary engineering companion. Rejects unnecessary dependencies (YAGNI), enforces modular architecture (<250 lines per file), eliminates syntax/hydration errors, and implements features using language-native standard libraries.
```text
/openxcode Add user authentication with schema validation
```

### 2. `/openxcode-security-audit`
Full codebase security audit from an adversarial attacker mindset. Scans OWASP Top 10 / CWE-25 vectors, traces source-to-sink taint flows, calculates CVSS v3.1 scores, and provides 1-click remediation commands (`fix all`, `fix recommended`, `fix #`).
```text
/openxcode-security-audit
```

### 3. `/openxcode-design-principles [prompt]`
Senior Multi-Platform UI/UX design system. Enforces cross-device excellence across Mobile (thumb zones, bottom sheets, `dvh`), Tablet (Master-Detail dual-pane, Navigation Rail), Laptop (collapsible sidebar, dense data tables, `Cmd+K`), and Desktop/4K Ultrawide (neck-strain container bounds, 3-column canvas, virtualized grids). Guarantees 4px spacing scale, Material Design 3, zero emojis (SVGs only), and WCAG AA accessibility.
```text
/openxcode-design-principles Build a responsive settings dashboard
```

### 4. `/openxcode-update-check`
Checks active version, update status, and displays the exact terminal command to sync.
```text
/openxcode-update-check
```

---

## License

Licensed under the [MIT License](LICENSE).
