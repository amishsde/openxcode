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
  Reviews the codebase and highlights areas of over-engineering, redundant dependencies, dead code, and security vulnerabilities.

- `/openxcode-security`  
  Executes an in-depth, industry-standard Application Security (AppSec) audit aligned with OWASP Top 10 and CWE standards.

### Natural Interaction

You can also prompt the agent directly:
- *"Perform a security audit on this repository using @openxcode"*
- *"Check our authentication and database queries for OWASP vulnerabilities"*
- *"Review this module using @openxcode"*
- *"Refactor this service with clean, minimal code"*
- *"Identify redundant dependencies and security risks in this repository"*

---

## Security & Reliability Standards (OWASP Top 10 Aligned)

OpenXCode prioritizes simplicity without compromising security or system reliability:
- **Zero Secrets in Code (OWASP A07)**: Strict prohibition of hardcoded API keys, JWT secrets, passwords, or tokens. Enforces runtime environment configuration (`.env`).
- **Input Validation & Injection Prevention (OWASP A03)**: Comprehensive schema/type validation and parameterized queries to prevent SQLi, NoSQLi, XSS, SSRF, Path Traversal, and Command Injection.
- **Access Control & Least Privilege (OWASP A01)**: Verified authentication and authorization at all API and data layers; prevents IDOR.
- **Safe Error Handling & Data Leak Prevention (OWASP A05)**: Stack traces and internal database schemas are never leaked in client-facing responses or logs.
- **Secure Cryptography & Transport (OWASP A02)**: Standard hashing (Argon2/bcrypt/SHA-256) and secure web transport (HTTPS, `HttpOnly`, `SameSite` cookies).
- **Context & Blast Radius**: The model inspects the complete caller chain and impact radius before making modifications.

---

## License

This project is licensed under the [MIT License](LICENSE).

