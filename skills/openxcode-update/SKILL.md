---
name: openxcode-update
description: >
  Updates the OpenXCode plugin to the latest version directly from the official GitHub repository.
  Verifies git repository integrity, pulls upstream changes for all skills, design tokens,
  and security references, and reports the updated version status.
license: MIT
---

# OpenXCode Auto-Updater

You act as the self-updater companion for the **OpenXCode** Antigravity plugin. When this skill is invoked via `/openxcode-update` or `@openxcode-update`, you guide and facilitate updating the plugin to the latest upstream release from GitHub.

---

## Update Workflow

When the user runs `/openxcode-update`:

### 1. Locate Plugin Directory
Determine the active installation directory for OpenXCode. Typical locations:
- **Global configuration (Default)**:
  - Windows: `%USERPROFILE%\.gemini\config\plugins\openxcode`
  - macOS / Linux: `~/.gemini/config/plugins/openxcode`
- **Project-level / Submodule**:
  - `.agents/plugins/openxcode` inside the active workspace.

### 2. Execution Instructions
Provide the user with the exact, safe update command based on their operating system, or assist them in pulling the latest commits:

#### Windows (PowerShell):
```powershell
cd "$HOME\.gemini\config\plugins\openxcode"; git pull origin main
```

#### macOS / Linux (Terminal):
```bash
cd ~/.gemini/config/plugins/openxcode && git pull origin main
```

### 3. Verify Version & Report Status
After updating, inspect `plugin.json` to confirm the active version.
Deliver a clean, friendly status report in this exact format:

```text
🚀 OpenXCode Update Check
--------------------------------------------------
Status: Updated to latest release (or Already up to date)
Current Version: v1.1.0
Repository: https://github.com/amishsde/openxcode

Refreshed Components:
- Core Prompt Companion (/openxcode)
- UI/UX Design System & Tokens (/openxcode-design-principles)
- Enterprise AppSec Audit (/openxcode-security-audit)
--------------------------------------------------
All skills and security guidelines are now up to date.
```

### 4. Troubleshooting & Fallback
If the user encounters git errors (such as local modifications or detached HEAD):
- Suggest stash or clean pull: `git stash && git pull origin main`
- If not installed via git (e.g. manual ZIP extraction), provide the 1-line re-download command from the README.
