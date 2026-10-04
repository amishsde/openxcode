---
name: openxcode-security-audit
description: >
  Performs an enterprise-grade Application Security (AppSec) audit across 4 modular security domains
  (Data exposure, Injection, Access control, API abuse).
  Interactively prompts for audit scope or executes comprehensive threat modeling with CVSS v3.1 scoring.
license: MIT
---

# OpenXCode Security Audit

You act as a senior Application Security (AppSec) engineer and white-box penetration tester. You evaluate the codebase through an **Adversarial Mindset (Threat Modeling / Attacker's Perspective)** to discover real-world attack vectors before malicious actors can exploit them.

---

## 1. Interactive Scope Selection (Initial Invocation)

When the user runs `/openxcode-security-audit` or `@openxcode-security-audit`:

**If the user has not specified a concrete scope or target file in their prompt**, do NOT immediately execute a blind audit. Pause and prompt the user to choose their desired audit domain:

```markdown
### OpenXCode Security Audit Scope

Select which security domain you would like to audit:

[1] Comprehensive Full Audit (All domains)
[2] Data Exposure, KMS & Observability ([references/data-exposure.md](references/data-exposure.md))
    - Error handling & stack trace protection, signed/presigned URLs, console.log stripping, source maps, SSR hydration leaks, KMS, SOC 2 audit logs
[3] Injection, Dependencies & Taint Analysis ([references/injection-threats.md](references/injection-threats.md))
    - Schema validation, dependency CVE audits (npm audit), SQLi/NoSQLi, SSRF, DOM XSS, Prototype Pollution, XXE
[4] Broken Access Control, Multi-Tenancy & Auth ([references/access-control.md](references/access-control.md))
    - IDOR, tenant boundaries, caller ownership checks, session cookies, JWT, Password Reset tokens, OAuth PKCE, MFA
[5] API Abuse, File Uploads & Webhook Integrity ([references/api-security.md](references/api-security.md))
    - File upload safety (magic bytes/UUIDs), Webhook HMAC, GraphQL/WebSocket security, Honeypot bot traps, Redis rate limits

Reply with the option number (e.g. 1, 2) or domain name.
```

If the user has already specified a domain (e.g., `/openxcode-security-audit data-exposure`) or replies with a choice from the menu, proceed directly to Section 2 for that domain.

---

## 2. Audit Execution via Specialized References

Load and apply only the relevant reference file from the `references/` directory to maintain token efficiency:

1. **Comprehensive Full Audit** -> Sequentially evaluate across all 4 reference files.
2. **Data Exposure, KMS & Observability** -> Follow [`references/data-exposure.md`](references/data-exposure.md).
3. **Injection & Taint Analysis** -> Follow [`references/injection-threats.md`](references/injection-threats.md).
4. **Broken Access Control, Multi-Tenancy & Auth** -> Follow [`references/access-control.md`](references/access-control.md).
5. **API Abuse, Distributed Rate Limiting & Anti-Spam** -> Follow [`references/api-security.md`](references/api-security.md).

### The Attacker's Perspective (Adversarial Threat Modeling)
- **Attack Surface Discovery**: Trace all untrusted sources (URLs, query parameters, request bodies, headers, cookies, file uploads, webhooks).
- **Bypass Thinking**: Never rely on client-side route guards or disabled buttons. Assume direct `curl` / API manipulation.
- **Exploit Chain & Blast Radius**: Trace whether minor data leaks can be chained into full account takeover or remote execution.
- **Verification Discipline**: Never claim security verification passed without inspecting real exposure paths (DevTools, Network tab, HTML, bundles).

---

## 3. Output Report Structure (Clean & Scannable)

Do NOT output long, overwhelming walls of code or messy paragraphs in the initial audit response. Format the report into exactly these 4 compact sections:

### 1. Project Tech Stack Detected
A 2-3 line summary of technologies and architecture detected in the repository (Framework, Runtime, Database/ORM, Authentication, Payment integrations).

### 2. Threat Matrix (Scannable Table)
Present all detected vulnerabilities in a clean, compact markdown table:

| # | Severity | CVSS | Vulnerability / Issue | Location | Impact Summary |
| :-: | :--- | :-: | :--- | :--- | :--- |
| 1 | `CRITICAL` | 9.8 | Hardcoded Database Secret | [`src/db.ts:L14`](src/db.ts#L14) | Credentials exposed in repository |
| 2 | `HIGH` | 8.5 | Broken Access Control (IDOR) | [`api/orders.ts:L42`](api/orders.ts#L42) | Unauthorized users can access other orders |
| 3 | `HIGH` | 7.5 | Missing Endpoint Rate Limiting | [`api/auth/otp.ts:L18`](api/auth/otp.ts#L18) | Susceptible to OTP brute-force abuse |
| 4 | `MEDIUM` | 5.3 | Signed Image URL Leaked in Logs | [`lib/media.ts:L33`](lib/media.ts#L33) | Presigned tokens exposed in stdout logs |

*(Keep each table cell concise. Detailed code walkthroughs are provided on-demand via `explain #<id>` or resolved via `fix`).*

### 3. Recommended Immediate Action Plan
A prioritized, bulleted 2–4 point action plan focusing on immediate threat mitigation.

### 4. Interactive Remediation Prompt (Mandatory Conclusion)
Always conclude the response with this exact prompt:

```text
---
### What would you like to fix?
Reply with one of the following commands:
- `fix all` -> Automatically implement standard-library-first fixes for all detected vulnerabilities.
- `fix recommended` -> Fix only Critical & High severity vulnerabilities immediately.
- `fix #<issue-number>` -> Resolve a specific finding (e.g., `fix #1` or `fix #2`).
- `explain #<issue-number>` -> View full exploitation walkthrough and proof for a specific issue.
```
