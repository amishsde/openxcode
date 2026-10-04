# API Abuse, Distributed Rate Limiting & Resource Exhaustion (DoS / ReDoS)

This reference covers defenses against denial-of-service, automated form spam, rate limiting gaps, distributed traffic exhaustion, and Denial of Wallet (DoW).

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

## 3. Regular Expression Denial of Service (ReDoS)

- **Evil Regex Detection**: Scan regular expressions for nested quantifiers that cause exponential backtracking on crafted inputs:
  - `(a+)+$`
  - `([a-zA-Z]+)*$`
  - `(a|aa)+$`
- **Remediation**: Use non-backtracking regular expressions, linear regex engines, or input length restrictions before evaluation.

---

## 4. Unbounded Payloads & Pagination

- **Body Size Restrictions**: Reject unbounded JSON payloads. Configure server body parsers with explicit limits (e.g., `express.json({ limit: '1mb' })`).
- **Strict Database Query Pagination**: Never permit unlimited queries (`db.users.findMany()`). Always enforce mandatory `take` / `limit` caps (e.g. maximum 100 items per request).
- **File Upload Guardrails**: Enforce strict file size limits and MIME-type allowlists on file upload endpoints.
