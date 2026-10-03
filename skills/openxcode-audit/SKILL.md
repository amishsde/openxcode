---
name: openxcode-audit
description: >
  Codebase analysis skill for identifying over-engineering, redundant dependencies,
  dead code, and security risks aligned with industry standards (OWASP Top 10, CWE).
---

# OpenXCode Audit

Perform a systematic codebase review to identify unnecessary complexity, optimization opportunities, and security risks.

When assessing security, adopt an **Adversarial Mindset**: analyze the code as a penetration tester or malicious actor looking for entry points, trust boundary violations, parameter tampering, and auth bypasses.


## Findings Categories

- `security`: Security vulnerabilities (OWASP Top 10, CWE), hardcoded secrets/tokens, unvalidated input vectors, injection risks, broken access control, and insecure configurations.
- `remove`: Dead functions, obsolete configurations, or unused code branches.
- `stdlib`: Custom utility routines that can be replaced with native standard library functions.
- `platform`: Third-party packages whose functionality is natively available in the platform or runtime.
- `reuse`: Redundant helper implementations that duplicate existing project modules.
- `simplify`: Overly complex abstractions, single-consumer interfaces, or deep inheritance hierarchies that can be flattened.

## Review Focus Areas

### 1. Security & Compliance (Industry Standards: OWASP Top 10 / CWE)
- **Source-to-Sink Taint Tracking**: Trace untrusted user input directly to dangerous execution sinks (`db.query`, `exec`, `fs`, `eval`) lacking strict sanitization.
- **Secret & Credential Leaks**: Scan for hardcoded API keys, JWT secrets, passwords, private keys, database URLs, and `.env` leaks in repository/client bundles.
- **Business Logic Flaws & Race Conditions**: Concurrency flaws (TOCTOU), parameter tampering (negative prices/quantities), and workflow/state machine bypasses.
- **Broken Access Control & Auth**: Missing middleware guards, unauthenticated API endpoints, IDOR (Insecure Direct Object Reference), or client-only security enforcement.
- **Native Anti-Spam (Zero-Bloat)**: Verify forms utilize lightweight Honeypots and timing checks against bots instead of heavy third-party CAPTCHAs.
- **Reconnaissance & Info Disclosure**: Audit `robots.txt` / `sitemap.xml` for leaked admin/staging routes, and verify clean custom 404/500 error pages.
- **Cross-Site Scripting (XSS) & SSRF**: Unescaped DOM rendering (`dangerouslySetInnerHTML`, `innerHTML`, `v-html`) and unvalidated server-side HTTP request destinations.
- **Rate Limiting & Resource Exhaustion (DoS)**: Missing rate limits on OTP/auth routes, ReDoS, and unbounded payload/memory risks.
- **Cookie Security & Privacy**: Audit cookie flags (`HttpOnly`, `Secure`, `SameSite`) and ensure tracking scripts wait for cookie consent.
- **Insecure Dependencies & Scripts**: Vulnerable/outdated third-party packages, untrusted external scripts, or compromised package scripts.



### 2. Architecture & Simplification
- Third-party packages that duplicate runtime/standard library features.
- Unnecessary abstraction layers created around single call sites.
- Premature architectural patterns where standard procedures suffice.
- Over-engineered fallback logic for unreachable internal states.

## Finding Format

Report each finding with:
- **Category & Severity**: e.g., `[security] [HIGH]`, `[stdlib] [LOW]`
- **Location**: Clickable file path and line numbers (`[filename.ts:L12-L18](file:///path/to/filename.ts#L12-L18)`)
- **Risk / Problem**: Why this is an issue or security risk.
- **Remediation**: Concise, minimal, standard-library-first fix.

