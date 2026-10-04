# API Abuse, Distributed Rate Limiting & Resource Exhaustion (DoS / ReDoS)

This reference covers defenses against denial-of-service, automated form spam, rate limiting gaps, distributed traffic exhaustion, webhook tampering, and GraphQL/WebSocket vulnerabilities.

---

## 1. Native Zero-Bloat Form Spam Defense (Honeypot + Timing)

Third-party CAPTCHA widgets load massive JavaScript tracking scripts, degrade privacy, and hurt performance. OpenXCode prioritizes lightweight, standard-library-first bot defenses:

### Honeypot Trap
Add a hidden form field invisible to human users via CSS (e.g. `position: absolute; left: -9999px; opacity: 0;`). Legitimate users leave it empty; automated bot scrapers blindly populate all input fields.
```html
<!-- Client: Hidden Honeypot Field -->
<div style="display:none;" aria-hidden="true">
  <input type="text" name="_hp_website" tabindex="-1" autocomplete="off" />
</div>
```
```typescript
// Server: Reject any submission where honeypot is populated
if (req.body._hp_website) {
  // Silent reject or generic 200 response to prevent bot adaptation
  return res.status(200).json({ success: true });
}
```

### Form Submission Timing Check
Record when the form was initially rendered (e.g., an encrypted or signed timestamp in the form state). Reject submissions completed faster than 1.5 seconds. Humans take several seconds to fill out forms; automated bots submit forms within milliseconds.

---

## 2. Distributed Rate Limiting & Denial of Wallet (DoW)

In high-scale or serverless systems, in-memory rate limits fail across distributed instances.

### Threat Vectors
- Attackers flood CPU-intensive endpoints (password hashing, PDF generation, LLM completion, SMS triggers) or distribute requests across serverless instances to bypass single-node memory counters.
- **Denial of Wallet (DoW)**: Attackers trigger metered third-party APIs (OpenAI, Stripe, Twilio) to drive up infrastructure bills.

### Standards
- **Redis Sliding Window**: Utilize a distributed token bucket or sliding window algorithm with atomic Redis pipelines or Upstash Ratelimit.
- **Queue Backpressure**: Protect downstream databases from connection pool exhaustion using queue backpressure (BullMQ/SQS) or graceful degradation.
- **Cost Quota Guardrails**: Impose hard per-user and per-organization daily billing caps on metered third-party endpoints.
- **Sensitive Route Protection**: Enforce rate limits on high-risk endpoints (login, OTP, password reset, search).

---

## 3. Webhook Signature Verification (HMAC)

### The Threat
Without cryptographic signature verification, an attacker can directly post forged webhook payloads (e.g. `checkout.session.completed`) to credit accounts or fulfill orders without payment.

### Standards
- **Cryptographic HMAC Validation**: Always verify the webhook signature header (e.g. `Stripe-Signature`, `X-Hub-Signature-256`) using the configured webhook signing secret and `crypto.timingSafeEqual`.
- **Raw Body Preserved**: Compute the HMAC digest over the **raw, unparsed request buffer**, never over re-serialized JSON (which mutates key ordering and spacing).
- **Replay Protection**: Validate timestamp headers to reject replayed webhook requests older than 5 minutes (300 seconds).

---

## 4. GraphQL & WebSocket Security

### GraphQL Defenses
- **Query Depth Limiting**: Enforce a strict maximum query depth (typically <= 6-7 levels) to prevent circular relation queries from exhausting database resources.
- **Query Complexity Analysis**: Calculate complexity scores per query to block expensive nested pagination attacks before database execution.
- **Disable Production Introspection**: Disable schema introspection and GraphiQL IDE in production environments (`introspection: false`).

### WebSocket Defenses
- **Origin Header Validation**: Verify the `Origin` header during the HTTP connection upgrade handshake to eliminate Cross-Site WebSocket Hijacking (CSWSH).
- **Authentication on Upgrade**: Authenticate the user during the initial HTTP upgrade handshake using secure session cookies or tokens before opening the WebSocket channel.
- **Frame Rate Limiting**: Apply incoming message rate limits on active socket connections.

---

## 5. Regular Expression Denial of Service (ReDoS)

- **Evil Regex Detection**: Scan regular expressions for nested quantifiers that cause exponential backtracking on crafted inputs:
  - `(a+)+$`
  - `([a-zA-Z]+)*$`
  - `(a|aa)+$`
- **Remediation**: Use non-backtracking regular expressions, linear regex engines, or input length restrictions before evaluation.

---

## 6. Unbounded Payloads & Pagination

- **Body Size Restrictions**: Reject unbounded JSON payloads. Configure server body parsers with explicit limits (e.g., `express.json({ limit: '1mb' })`).
- **Strict Database Query Pagination**: Never permit unlimited queries (`db.users.findMany()`). Always enforce mandatory `take` / `limit` caps (e.g. maximum 100 items per request).

---

## 7. Enterprise File Upload Safety

### The Threat
Allowing users to upload files without rigorous validation introduces Remote Code Execution (RCE) via web shell uploads, stored XSS via SVGs/HTML, path traversal via malformed filenames, and denial-of-service via huge archives (zip bombs).

### Standards
- **Binary Magic Byte Inspection**: Never trust client-controlled `Content-Type` headers or file extensions (`.jpg`). Always verify the actual binary header/magic bytes using trusted inspectors (e.g. `file-type` in Node.js, `python-magic` in Python):
  ```typescript
  // SECURE: Verify binary magic bytes
  import { fileTypeFromBuffer } from 'file-type';
  const type = await fileTypeFromBuffer(fileBuffer);
  const ALLOWED_MIME = ['image/jpeg', 'image/png', 'application/pdf'];
  if (!type || !ALLOWED_MIME.includes(type.mime)) {
    throw new SecurityError('Invalid or spoofed file type');
  }
  ```
- **Random UUID Renaming**: Completely discard user-supplied original filenames upon receipt. Generate a random UUID/nanoid for storage (`${crypto.randomUUID()}.${safeExt}`). This completely neutralizes directory traversal (`../../etc/passwd`) and file overwrite attacks.
- **Non-Executable Storage Isolation**: Never store uploaded files inside the web server's publicly executed document root. Store uploads in dedicated private cloud storage (AWS S3, GCP Cloud Storage) or outside the application directory where script execution permissions are disabled (`noexec`).
- **SVG & HTML Media Sanitization**: SVG files can contain embedded `<script>` or onload handlers that execute XSS when viewed in browsers. Either disallow SVG uploads, strictly sanitize them with a dedicated XML sanitizer, or serve them with `Content-Disposition: attachment` and `Content-Security-Policy: sandbox`.
- **Pre-Buffer Size Guardrails**: Enforce stream-level file size caps (e.g., maximum 5MB) before buffering into memory to prevent server memory exhaustion.
