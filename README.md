# OpenXCode

An Antigravity extension that keeps your AI agent grounded in clean, pragmatic software engineering. Instead of introducing heavy libraries and over-complicated patterns for simple features, OpenXCode forces the model to write clean, maintainable, and standard-library-first code.

---

## Why OpenXCode?

Most modern coding models suffer from premature complexity:
- Adding unnecessary dependencies when a single built-in function does the job.
- Creating multilayer abstractions (factories, interfaces, handlers) for logic that only has one consumer.
- Generating bloated boilerplate for edge cases that do not exist yet (violating YAGNI).

OpenXCode introduces a clear evaluation sequence that the agent must evaluate before writing any implementation.

---

## The Decision Order

When solving any implementation or bug-fixing task, the model follows this priority:

1. **Need Assessment**: Does this code genuinely need to be written? If the requirement is speculative, skip it.
2. **Internal Reuse**: Check the existing codebase first. Reuse project utilities, models, and helper functions before creating new ones.
3. **Standard Library**: Leverage the language runtime and standard library APIs.
4. **Platform-Native APIs**: Prefer standard browser/system capabilities (such as native HTML elements, CSS selectors, or system tools) over external packages.
5. **Existing Dependencies**: If a package is already installed, utilize it instead of adding another.
6. **Concise Logic**: Write simple, readable functions with minimal indirection.
7. **Production Minimum**: Deliver clean, robust code that satisfies the requirements without extraneous scaffolding.

---

## Comparison

| Requirement | Unassisted Model Output | With OpenXCode Enabled |
| :--- | :--- | :--- |
| **Date Selection** | Installs external UI datepicker library | Uses native `<input type="date">` |
| **Modal / Dialog** | Custom backdrop listeners and portal management | Leverages semantic `<dialog>` tag |
| **Throttling/Debouncing** | Adds third-party utility package | Implements native `setTimeout` closure |
| **Deep Object Copy** | Pulls in serialization library | Uses native `structuredClone()` API |
| **Bug Fixing** | Wraps individual caller sites in patches | Identifies and fixes the shared root cause |

---

## Installation

### Global (All Projects)

Clone the repository into your local Antigravity configuration directory:

**Windows (PowerShell):**
```powershell
git clone https://github.com/amishsde/openxcode.git "$HOME\.gemini\config\plugins\openxcode"
```

**macOS / Linux:**
```bash
git clone https://github.com/amishsde/openxcode.git ~/.gemini/config/plugins/openxcode
```

### Project-Specific Setup

If you prefer to include it directly within a specific repository:

```bash
git submodule add https://github.com/amishsde/openxcode.git .agents/plugins/openxcode
```

---

## Usage

### Slash Commands

- `/openxcode <prompt>`  
  Applies standard pragmatic engineering principles to the request.

- `/openxcode ultra <prompt>`  
  Enforces strict minimalism: refuses speculative features, favors single-function solutions, and eliminates boilerplate.

- `/openxcode-audit`  
  Reviews the codebase and highlights areas of over-engineering, unused code, and candidate areas for standard library replacement.

### Natural Interaction

You can also prompt the agent directly:
- *"Review this module using @openxcode"*
- *"Refactor this service with clean, minimal code"*
- *"Identify redundant dependencies in this repository"*

---

## Quality & Reliability Boundaries

OpenXCode prioritizes simplicity without compromising system reliability:
- **Input Validation**: Boundary checks and contract validations are never omitted.
- **Error Handling**: Graceful exceptions and defensive operations protecting user data are maintained.
- **Accessibility & Security**: Standard security practices and semantic accessibility compliance remain strictly enforced.
- **Context Tracing**: The model inspects the complete caller chain before applying any logic adjustments.

---

## License

This project is licensed under the [MIT License](LICENSE).
