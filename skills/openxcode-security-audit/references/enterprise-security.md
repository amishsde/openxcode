# Enterprise Security Vectors & Compliance Reference

This document provides deep threat matrices and remediation formulas for Tier 2 and Tier 3 enterprise systems.

---

## 1. Multi-Tenant Data Isolation & IDOR Protection

In enterprise SaaS systems, simple user verification is insufficient. Strict tenant-level isolation must be guaranteed at the data layer.

### Threat Vector
An authenticated user in `Tenant A` queries `/api/invoices/9928`. Even with a valid session, if the query does not constrain `tenantId`, data leaks across organization boundaries.

### Enterprise Remediation Pattern
```typescript
// BAD: Vulnerable to cross-tenant IDOR
const invoice = await db.invoice.findUnique({ where: { id: req.params.id } });

// SECURE: Enforced tenant boundary in WHERE clause
const invoice = await db.invoice.findFirst({
  where: {
    id: req.params.id,
    tenantId: session.tenantId, // Non-negotiable tenant constraint
  }
});
if (!invoice) {
  throw new NotFoundError("Resource not found or unauthorized");
}
```

---

## 2. Distributed Rate Limiting & Denial of Wallet (DoW)

In high-scale systems, memory-based rate limits fail across distributed instances.

### Threat Vector
Attackers flood CPU-intensive endpoints (password hashing, PDF generation, LLM completion, SMS triggers) or distribute requests across serverless instances to bypass single-node rate limits.

### Enterprise Remediation Pattern
- **Redis Sliding Window**: Utilize a distributed token bucket or sliding window algorithm with atomic Redis pipelines or Upstash Ratelimit.
- **Fail-Closed or Graceful Degradation**: Protect downstream databases from connection pool exhaustion using queue backpressure (BullMQ/SQS).
- **Cost Quota Guardrails**: Impose hard per-user billing quotas on external API calls (e.g. OpenAI/Stripe) to prevent bill bombing.

---

## 3. Cryptographic Storage & Key Lifecycle

- **Secret Zero Principle**: No encryption keys in source control, Docker images, or build logs. Use AWS KMS, GCP KMS, Vault, or sealed secrets.
- **Envelope Encryption**: High-scale databases should use envelope encryption: Data Encryption Keys (DEKs) encrypt the data rows, while a Key Encryption Key (KEK) in KMS protects the DEKs.
- **Safe Argon2id Config**: Passwords must be hashed using Argon2id with memory cost >= 64MB (65536 KiB), time cost >= 3 iterations, and parallelism >= 1.

---

## 4. Audit Logging & Non-Repudiation (SOC 2 / ISO 27001)

- Every mutation on sensitive entities (permissions, billing, user access, data export) must emit an immutable audit log entry.
- **PII Scrubbing**: Audit logs must automatically sanitize passwords, tokens, API keys, card numbers, and SSNs before writing to disk or streaming to Datadog/CloudWatch.
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
