# OpenXCode

An Antigravity extension that keeps your AI agent grounded in clean, pragmatic software engineering and enterprise-grade security. Instead of introducing heavy libraries and over-complicated patterns for simple features, OpenXCode enforces standard-library-first implementations, anti-monolith architecture, and OWASP-grade security auditing.

<div align="center">

**Language / भाषा:**  
[English](#-english) &nbsp;•&nbsp; [Hinglish (हिंदी)](#-hinglish-हिंदी)

</div>

---

# 🌐 English

## Why OpenXCode?

Most modern coding models suffer from premature complexity:
- Adding unnecessary third-party dependencies when a single built-in function does the job.
- Creating multilayer abstractions (factories, interfaces, handlers) for logic that only has one consumer.
- Generating bloated boilerplate for edge cases that do not exist yet (violating YAGNI).
- Cramming thousands of lines into massive monolithic files, exhausting AI context tokens and causing regressions.

OpenXCode introduces a clear evaluation sequence that the agent must evaluate before writing any implementation.

---

## The Decision Order

When solving any implementation or bug-fixing task, the model follows this priority:

1. **Need Assessment (YAGNI)**: Does this code genuinely need to be written? If the requirement is speculative, skip it.
2. **Internal Reuse**: Check the existing codebase first. Reuse project utilities, models, and helper functions before creating new ones.
3. **Standard Library First**: Utilize language-native modules and standard APIs before writing custom routines.
4. **Platform Capabilities**: Prefer built-in platform capabilities (e.g., semantic HTML elements, browser APIs, or system utilities) over third-party packages.
5. **Leverage Current Dependencies**: Use packages already specified in dependency manifests rather than introducing new dependencies.
6. **Simplicity Over Abstraction**: Write straightforward, readable code. Avoid single-use abstractions, factories, and unnecessary interfaces.
7. **Deliver the Working Core**: Deliver clean, robust code that satisfies the requirements without extraneous scaffolding.

---

## Comparison

| Requirement | Unassisted Model Output | With OpenXCode Enabled |
| :--- | :--- | :--- |
| **Date Selection** | Installs external UI datepicker library | Uses native `<input type="date">` |
| **Modal / Dialog** | Custom backdrop listeners and portal management | Leverages semantic `<dialog>` tag |
| **Throttling/Debouncing** | Adds third-party utility package | Implements native `setTimeout` closure |
| **Deep Object Copy** | Pulls in serialization library | Uses native `structuredClone()` API |
| **Bug Fixing** | Wraps individual caller sites in patches | Identifies and fixes the shared root cause |
| **Code Organization** | Crams 1500+ lines into monolithic files | Modular, token-efficient files (<250 lines) |

---

## Installation

Clone the repository into your local Antigravity configuration directory:

### Windows (PowerShell)
```powershell
git clone https://github.com/amishsde/openxcode.git "$HOME\.gemini\config\plugins\openxcode"
```

### macOS / Linux
```bash
git clone https://github.com/amishsde/openxcode.git ~/.gemini/config/plugins/openxcode
```

---

## Updating

If you previously installed OpenXCode and want to pull the latest rules, skills, and security features:

### Windows (PowerShell)
```powershell
cd "$HOME\.gemini\config\plugins\openxcode"; git pull origin main
```

### macOS / Linux
```bash
cd ~/.gemini/config/plugins/openxcode && git pull origin main
```

---

## Usage

### The 2 Core Slash Commands

1. **`/openxcode <prompt>` (Primary Prompt Companion)**  
   Attach this command whenever you prompt the AI for features, refactoring, or bug fixes. The AI thoroughly analyzes your project context, rejects unnecessary dependencies (YAGNI), enforces modular architecture (<250 lines), and writes clean, standard-library-first code.
   ```text
   /openxcode Add user registration with validation
   ```

2. **`/openxcode-security-audit` (Full Codebase Security Audit)**  
   Executes an enterprise-grade Application Security (AppSec) audit through an **Adversarial Mindset (Threat Modeling / Attacker's Perspective)**. Scans across OWASP Top 10 and CWE Top 25, traces Source-to-Sink taint paths, inspects business logic & race conditions, and assigns CVSS v3.1 scores.
   ```text
   /openxcode-security-audit
   ```
   **Interactive Remediation:** Concludes with an actionable prompt allowing 1-click remediation:
   - `fix all`: Automatically resolves all detected vulnerabilities.
   - `fix recommended`: Fixes Critical and High severity risks immediately.
   - `fix #1`: Resolves a specific vulnerability finding.
   - `explain #1`: Shows in-depth exploit walkthrough and proof for a specific issue.


### Natural Interaction

You can also prompt the agent directly:
- *"Perform a security audit on this repository using @openxcode"*
- *"Check our authentication and database queries for OWASP vulnerabilities"*
- *"Review this module using @openxcode"*
- *"Refactor this service into modular, token-efficient components"*
- *"Identify redundant dependencies and security risks in this repository"*

---

## Security & Reliability Standards (OWASP Top 10 Aligned)

OpenXCode prioritizes simplicity without compromising security or system reliability:
- **Zero Secrets in Code (OWASP A07)**: Strict prohibition of hardcoded API keys, JWT secrets, passwords, or tokens. Enforces runtime environment configuration (`.env`).
- **Input Validation & Injection Prevention (OWASP A03)**: Comprehensive schema/type validation and parameterized queries to prevent SQLi, NoSQLi, XSS, SSRF, Path Traversal, and Command Injection.
- **Access Control & Least Privilege (OWASP A01)**: Verified authentication and authorization at all API and data layers; prevents IDOR.
- **Safe Error Handling & Data Leak Prevention (OWASP A05)**: Stack traces and internal database schemas are never leaked in client-facing responses or logs.
- **Secure Cryptography & Transport (OWASP A02)**: Standard hashing (Argon2/bcrypt/SHA-256) and secure web transport (HTTPS, `HttpOnly`, `SameSite` cookies).
- **Anti-Monolith Architecture**: Enforces single responsibility per file to minimize AI context token consumption and prevent regressions.

---
---

# 🌐 Hinglish (हिंदी)

## OpenXCode Kyu Zaroori Hai?

Aamtaur par AI models simple tasks ke liye bhi over-complicated code likhte hain:
- Chhoti-moti cheezon ke liye bhari 3rd party packages install karwana.
- Ek hi consumer ke liye multilayer abstractions (factories, interfaces, handlers) banana.
- Aise future use-cases ke liye boilerplate banana jinki abhi zaroorat hi nahi hai (YAGNI ka violation).
- 1500–2000 lines ka monolithic code ek hi file mein likhna, jisse AI ke tokens waste hote hain aur bugs aate hain.

OpenXCode AI agent ko ek Senior Software Engineer ki tarah **Pragmatic, Minimal, aur Standard-Library-First** code likhne par majboor karta hai.

---

## Decision Order (Sochne Ka Krama)

AI koi bhi code likhte ya bug theek karte waqt is sequence ko follow karega:

1. **Zaroorat ki Jaanch (YAGNI)**: Kya is code ki sach mein zaroorat hai? Agar requirement hypothetical hai, toh mat likho.
2. **Existing Code ka Reuse**: Naya helper banane se pehle project ke existing utilities aur models ko reuse karo.
3. **Standard Library Pehle**: Language runtime ke built-in features use karo.
4. **Platform Capabilities**: Browser/OS ke native HTML/CSS/APIs use karo external libraries ke bajaye.
5. **Installed Packages ka Use**: Naya package tabhi lao jab existing dependencies se kaam na chale.
6. **Simple & Readable Logic**: Single-use abstractions aur faltu layers se bacho.
7. **Production Minimum**: Clean, robust, aur maintainable code deliver karo.

---

## Installation (Install Kaise Karein)

Apne local Antigravity configuration directory mein clone karein:

### Windows (PowerShell)
```powershell
git clone https://github.com/amishsde/openxcode.git "$HOME\.gemini\config\plugins\openxcode"
```

### macOS / Linux
```bash
git clone https://github.com/amishsde/openxcode.git ~/.gemini/config/plugins/openxcode
```

---

## Updating (Update Kaise Karein)

Agar aapne pehle se install kiya hua hai aur latest security rules aur updates lena chahte hain:

### Windows (PowerShell)
```powershell
cd "$HOME\.gemini\config\plugins\openxcode"; git pull origin main
```

### macOS / Linux
```bash
cd ~/.gemini/config/plugins/openxcode && git pull origin main
```

---

## Usage (2 Core Slash Commands)

1. **`/openxcode <prompt>` (Primary Prompt Companion)**  
   Jab bhi aap AI ko koi prompt dete hain, is command ko attach karein:
   ```text
   /openxcode Add user registration with validation
   ```
   AI poore project context ko dekhega, faaltu libraries ko reject karega, modular files (<250 lines) banayega, aur standard library se clean code dega.

2. **`/openxcode-security-audit` (Full Codebase Security Audit)**  
   Poore project ka hacker perspective se deep security audit karta hai (OWASP Top 10, CWE Top 25, Taint tracking, Business logic & CVSS v3.1 scoring):
   ```text
   /openxcode-security-audit
   ```
   **Audit ke baad interactive fixes:**
   - `fix all`: Sabhi security vulnerabilities ko ek sath theek karega.
   - `fix recommended`: Sirf Critical aur High severity issues ko pehle fix karega.
   - `fix #1`: Kisi specific issue ko fix karega.
   - `explain #1`: Us specific issue ka detail exploit explanation aur proof dikhayega.


---

## License

This project is licensed under the [MIT License](LICENSE).
