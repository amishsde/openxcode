# Broken Access Control, Multi-Tenancy & Authentication

This reference defines rules for evaluating authorization models, multi-tenant isolation, direct object references, session integrity, password recovery, and OAuth security.

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

## 4. Password Reset & Account Recovery Security

- **Cryptographically Secure Random Tokens**: Reset tokens must be generated using cryptographically strong randomness (e.g. `crypto.randomBytes(32).toString('hex')`). Never use sequential IDs, timestamps, or guessable math.
- **Short Lifetime & Single Use**: Enforce strict expiration (maximum 10–15 minutes). Invalidate the reset token immediately upon successful password change.
- **Timing Attack Defense**: Compare reset tokens using constant-time string comparison (`crypto.timingSafeEqual`) to prevent side-channel timing discovery.
- **Session Revocation on Password Change**: Changing or resetting a password must immediately invalidate all existing active sessions and refresh tokens across all devices.

---

## 5. OAuth 2.0, OpenID Connect & Step-Up Auth (MFA)

- **PKCE Enforcement**: Mandatory Proof Key for Code Exchange (PKCE) with `S256` code challenge for all Single Page Applications (SPAs) and public mobile clients to prevent authorization code interception.
- **State Parameter CSRF Guard**: Generate a cryptographically random, unguessable `state` parameter bound to the user session before redirecting to identity providers; verify `state` matches identically on the callback.
- **Exact Redirect URI Matching**: Strict, exact string matching of callback URLs against an authorized allowlist. Prohibit wildcards or open subdomain patterns in OAuth redirect configs.
- **Step-Up Authentication (MFA)**: Require re-authentication or multi-factor verification before executing high-impact security actions (changing primary email, disabling 2FA, updating payout banking details).

---

## 6. Business Logic Abuse & Race Conditions (TOCTOU)

- **Atomic State Updates**: Prevent Time-of-Check to Time-of-Use (TOCTOU) race conditions in balance debits, coupon redemptions, and inventory holds. Use database transactions with row-level locks (`SELECT ... FOR UPDATE`) or atomic decrement operations.
- **Parameter & Price Tampering**: Never trust client-supplied prices, discount percentages, or quantities. Always calculate order totals server-side and validate that quantities are positive integers (`quantity > 0`).
