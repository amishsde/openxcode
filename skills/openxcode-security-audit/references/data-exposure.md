# Production Data Exposure, KMS Cryptography & Observability Hygiene

This reference defines enterprise standards for preventing credential leakage, sensitive data exposure, key management, and compliant audit logging across frontend, backend, and build artifacts.

---

## 1. Presigned URLs & Sensitive Media Handling

### The Threat
Cloud storage providers (AWS S3, Google Cloud Storage, CloudFront, Azure Blob) generate presigned URLs with temporary authorization credentials embedded in query parameters (e.g. `AWSAccessKeyId`, `Signature`, `Expires`, `token`). 
Logging or exposing these complete URLs leaks temporary bucket credentials and private document/image access grants.

### Standards
- **Treat Signed URLs as Sensitive Credentials**: Any URL containing temporary signatures, access tokens, or auth headers is a secret.
- **Query Parameter Redaction**: Never write full signed URLs or sensitive query parameters into server logs, error trackers, or client responses. Redact them to origin and pathname:
  ```typescript
  // SECURE: Redact query parameters before logging
  function sanitizeUrl(rawUrl: string): string {
    try {
      const parsed = new URL(rawUrl);
      return `${parsed.origin}${parsed.pathname}?signature=[REDACTED]`;
    } catch {
      return "[MALFORMED_URL]";
    }
  }
  ```
- **Public Image URLs**: Public static assets containing no sensitive tokens or query parameters may be used normally.

---

## 2. Production Logging Hygiene & Smart Sanitization

### The Threat
Developers often use `console.log`, `print`, or verbose loggers during development. In production, these statements leak user records, tokens, OTPs, and private paths to stdout and APM collectors. Conversely, blindly removing all logs destroys observability and incident diagnosis.

### Standards
- **Disable Verbose Diagnostics**: Strip or suppress `console.debug`, `console.log`, and development-only traces in production builds (via build tool configurations like `terser` / `@babel/plugin-transform-react-jsx` / logging levels).
- **Sanitize, Do Not Blindly Remove**: Retain safe operational diagnostics. Sanitize sensitive payloads while preserving:
  - Unique Request / Correlation IDs (`x-request-id`, `traceId`)
  - Deterministic safe error codes (`AUTH_INVALID_CREDENTIALS`)
  - HTTP method, route, and status code
- **Prohibited Log Items**: Never print passwords, OTPs, JWT tokens, cookies, authorization headers, credit card / payment data (PCI-DSS), database credentials, or full user records into logs.

---

## 3. Cryptographic Storage & Key Lifecycle (Secret Zero & KMS)

- **Secret Zero Principle**: No master encryption keys in source control, Docker images, or build logs. Use AWS KMS, GCP KMS, Vault, or sealed secret operators.
- **Envelope Encryption**: High-scale databases should use envelope encryption: Data Encryption Keys (DEKs) encrypt the data rows locally, while a Key Encryption Key (KEK) managed in KMS protects the DEKs.

---

## 4. Immutable Audit Logging & Non-Repudiation (SOC 2 / ISO 27001)

- **Mutation Audit Trail**: Every mutation on sensitive entities (permissions, billing, user access, data export) must emit an immutable audit log entry.
- **PII Scrubbing**: Audit logs must automatically sanitize passwords, tokens, API keys, card numbers, and SSNs before writing to disk or streaming to Datadog/CloudWatch:
```json
{
  "timestamp": "2026-10-04T07:30:00.000Z",
  "actorId": "usr_948271",
  "tenantId": "ten_00281",
  "action": "role.update",
  "targetId": "usr_10284",
  "ipHash": "sha256_salted_hash",
  "status": "SUCCESS"
}
```

---

## 5. Client Bundle, Source Map & SSR Hydration Leaks

### The Threat
Modern frameworks (Next.js, Nuxt, Remix) frequently leak server-side data through automatic serialization into client-accessible bundles and HTML pages.

### Standards
- **`NEXT_PUBLIC_` & Environment Boundary**: Never prefix server-only secrets (database URLs, Stripe secret keys, JWT private keys) with client-exposed prefixes like `NEXT_PUBLIC_`, `VITE_`, or `REACT_APP_`.
- **SSR Hydration Payloads**: Audit SSR initial state containers (such as `__NEXT_DATA__` or `window.__INITIAL_STATE__`). Ensure `getServerSideProps` or server components return only the minimum data required by the UI—never return raw database models containing password hashes, email verification tokens, or internal flags.
- **Production Source Maps**: Prohibit deployment of `.map` source maps to public production CDNs. Public source maps allow attackers to decompile the entire frontend and shared backend codebase.
- **Client DevTools / Network Tab**: Verify that API responses do not send bloated database objects where the client only needed 2 fields.

---

## 6. Third-Party SDK & Telemetry Safeguards

- **Before-Send Scrubbing**: Configure APM and error reporting filters (e.g., Sentry `beforeSend` callback) to scrub:
  - `Authorization` and `Cookie` headers
  - Credit card numbers, CVVs, and SSNs
  - Passwords and secret query parameters
- **User Privacy Compliance**: Restrict analytics SDKs from capturing keystrokes in password, payment, or private input fields.

---

## 7. Verification Discipline

- **Never Assume Safety**: Code is not safe merely because a logger call was omitted.
- **Inspect Real Exposure Paths**: Always verify actual exposure vectors:
  1. Browser Network Tab (response JSON payloads)
  2. Rendered HTML Source (hydration state and hidden inputs)
  3. Client JavaScript Bundles & public build directories
  4. Server-side log aggregators
- **Never claim security verification passed unless it was actually tested.**
