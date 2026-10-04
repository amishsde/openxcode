# Broken Access Control, Multi-Tenancy & Authentication

This reference defines rules for evaluating authorization models, multi-tenant isolation, direct object references, session integrity, and password security.

---

## 1. Multi-Tenant Data Isolation & Insecure Direct Object References (IDOR)

### The Threat
An authenticated user requests a resource by supplying an identifier (`/api/invoices/9928`). In enterprise SaaS systems, simple user verification is insufficient: if the query does not constrain organizational tenancy (`tenantId`), data leaks across tenant boundaries.

### Standards
- **Enforce Tenant Boundaries & Caller Ownership**: Never fetch or mutate objects by resource ID alone. Always pair resource IDs with the caller's session identifier and organization tenant ID:
  ```typescript
  // BAD: Vulnerable to cross-tenant IDOR
  const invoice = await db.invoice.findUnique({ where: { id: req.params.id } });

  // SECURE: Enforced tenant and user boundary in WHERE clause
  const invoice = await db.invoice.findFirst({
    where: {
      id: req.params.id,
      tenantId: session.tenantId, // Non-negotiable multi-tenant constraint
      userId: session.userId,     // Caller ownership check
    }
  });
  if (!invoice) {
    throw new NotFoundError("Resource not found or unauthorized");
  }
  ```
- **Horizontal & Vertical Privilege Separation**: Verify role checks (e.g. `role === 'admin'`) occur strictly on the server for all protected actions. Never rely on hiding buttons in client UI.

---

## 2. Session Management & Cookie Security Flags

### Transport Standards
All cookies conveying session tokens or authentication state must enforce:
- **`HttpOnly`**: Blocks client-side JavaScript access via `document.cookie`, neutralizing session theft through XSS.
- **`Secure`**: Mandates transmission exclusively over encrypted HTTPS connections.
- **`SameSite=Lax` or `SameSite=Strict`**: Defends against Cross-Site Request Forgery (CSRF).

### Token Handling & Storage
- **Local Storage Ban for Sensitive Auth Tokens**: Storing sensitive access tokens or session secrets in `localStorage` leaves them permanently vulnerable to DOM-level script injection (XSS). Prefer `HttpOnly` secure cookies.
- **JWT Cryptographic Verification**: Always verify JWT algorithms explicitly on the server (`algorithms: ['RS256']` or `HS256`). Block algorithm confusion (`alg: 'none'`).

---

## 3. Password Hashing & Credential Security

- **Safe Argon2id Configuration**: Passwords must be hashed using Argon2id with memory cost >= 64MB (65536 KiB), time cost >= 3 iterations, and parallelism >= 1.
- **Prohibit Weak / Deprecated Hashing**: Never use MD5, SHA-1, plain SHA-256, or unsalted crypto for passwords.

---

## 4. Business Logic Abuse & Race Conditions (TOCTOU)

- **Atomic State Updates**: Prevent Time-of-Check to Time-of-Use (TOCTOU) race conditions in balance debits, coupon redemptions, and inventory holds. Use database transactions with row-level locks (`SELECT ... FOR UPDATE`) or atomic decrement operations.
- **Parameter & Price Tampering**: Never trust client-supplied prices, discount percentages, or quantities. Always calculate order totals server-side and validate that quantities are positive integers (`quantity > 0`).
