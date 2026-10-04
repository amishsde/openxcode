---
name: openxcode-update-check
description: >
  Checks for the latest updates of the OpenXCode plugin from GitHub,
  reports active version, status, recent changes, and provides the update command.
license: MIT
---

# OpenXCode Update Check

When invoked via `/openxcode-update-check` or `@openxcode-update-check`, output only the status, version, changes, and the exact update command.

---

### Output Format (Strictly Minimal)

```markdown
### OpenXCode Update Check

- **Status**: Up to date (or Update available)
- **Version**: `v1.2.0`
- **Changes**:
  - Added `/openxcode-update-check` command for quick version and update inspection.
  - Added `--depth 1` shallow clone support to eliminate historical commit bloat.
  - Cleaned and streamlined documentation.

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
