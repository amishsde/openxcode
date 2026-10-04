---
name: openxcode-update-check
description: >
  Checks for OpenXCode plugin updates by comparing the local plugin.json version
  with the latest version on GitHub, reporting status, current version, latest version,
  and update command if available.
license: MIT
---

# OpenXCode Update Check

When invoked via `/openxcode-update-check` or `@openxcode-update-check`:

1. **Read Local Version**: Check the `version` field in the local `plugin.json` (inside the installed plugin directory, e.g. `~/.gemini/config/plugins/openxcode/plugin.json`).
2. **Fetch Upstream Version**: Read the `version` field from the remote repository manifest:
   `https://raw.githubusercontent.com/amishsde/openxcode/main/plugin.json`
3. **Compare & Report**:
   - Do NOT include any changes or changelog list.
   - If remote version is newer than local version -> Report **Status: Update Available**, show both **Current Version** and **Latest Version**, and provide the terminal update command.
   - If versions match -> Report **Status: Up to date**, and display both **Current Version** and **Latest Version**.

---

### Output Format

#### When an update is available:
```markdown
### OpenXCode Update Check

- **Status**: Update Available
- **Current Version**: `v1.0.0`
- **Latest Version**: `v1.0.1`

#### Command to Update:
**Windows (PowerShell):**
```powershell
cd "$HOME\.gemini\config\plugins\openxcode"; git pull origin main
```

**macOS / Linux:**
```bash
cd ~/.gemini/config/plugins/openxcode && git pull origin main
```
```

#### When already up to date:
```markdown
### OpenXCode Update Check

- **Status**: Up to date
- **Current Version**: `v1.2.1`
- **Latest Version**: `v1.2.1`
```
