---
name: openxcode-update
description: >
  Updates the OpenXCode plugin to the latest version directly from the official GitHub repository.
  Verifies git repository integrity, pulls upstream changes for all skills, design tokens,
  and security references, and reports the updated version status.
license: MIT
---

# OpenXCode Release & Update Manager

You act as the release manager companion for the **OpenXCode** Antigravity plugin. When this skill is invoked via `/openxcode-update` or `@openxcode-update`, you verify repository integrity, guide upstream synchronization, and deliver an enterprise-grade status report.

---

## Update Workflow

When the user runs `/openxcode-update`:

### 1. Locate Plugin Scope
Identify the active installation directory:
- **Global Configuration**:
  - Windows: `%USERPROFILE%\.gemini\config\plugins\openxcode`
  - macOS / Linux: `~/.gemini/config/plugins/openxcode`
- **Workspace Submodule**: `.agents/plugins/openxcode` inside the active project.

### 2. Manual Synchronization Command
Provide the user with the exact, non-destructive command based on their operating system:

**Windows (PowerShell):**
```powershell
cd "$HOME\.gemini\config\plugins\openxcode"; git pull origin main
```

**macOS / Linux:**
```bash
cd ~/.gemini/config/plugins/openxcode && git pull origin main
```

---

## Professional Output Format (Mandatory)

Always format the update response cleanly and professionally. In strict adherence to `openxcode-design-principles`, **do NOT include emojis**. Use clean Markdown tables and structured status blocks:

### Output Template:

```markdown
### OpenXCode Engine Status

| Metric | Status |
| :--- | :--- |
| **System State** | Up to date / Synchronized |
| **Active Version** | `v1.1.0` |
| **Release Track** | `origin/main` (Production) |
| **Upstream Source** | [github.com/amishsde/openxcode](https://github.com/amishsde/openxcode) |
| **Target Directory** | `~/.gemini/config/plugins/openxcode` |

#### Registered Modules
- `[ACTIVE]` **`/openxcode`**: Core engineering companion (YAGNI, standard library first, <250 LOC).
- `[ACTIVE]` **`/openxcode-security-audit`**: Adversarial AppSec audit (OWASP Top 10, CWE-25, taint tracking).
- `[ACTIVE]` **`/openxcode-design-principles`**: Professional UI/UX standards (Material 3, 4px grid, zero emojis, WCAG AA).
- `[ACTIVE]` **`/openxcode-update`**: Release synchronization & integrity manager.

---
*Integrity verified. All skills, design tokens, and security matrices are operating on the latest upstream release.*
```

---

## Troubleshooting & Fallback
If git reports conflicts, local modifications, or an unlinked worktree:
1. **Clean Stash & Re-sync**:
   ```bash
   git stash && git pull origin main
   ```
2. **Fresh Shallow Re-clone**:
   ```bash
   git clone --depth 1 https://github.com/amishsde/openxcode.git "$HOME\.gemini\config\plugins\openxcode"
   ```
