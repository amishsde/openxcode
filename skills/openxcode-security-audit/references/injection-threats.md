# Injection Threats & Source-to-Sink Taint Analysis

This reference covers untrusted input tracking, injection prevention, server-side request forgery (SSRF), open redirects, prototype pollution, and XXE defenses.

---

## 1. Source-to-Sink Taint Tracking

### Untrusted Sources
Any data entering the system from external boundaries is tainted:
- Route parameters (`req.params`, `params.id`)
- Query parameters (`req.query`)
- Request body payloads (`req.body`, JSON, XML, multipart form-data)
- Request headers (`req.headers`, `Authorization`, `Referer`, `User-Agent`)
- Cookies (`req.cookies`)
- Webhooks from external third parties

### Critical Execution Sinks
Tainted input must never reach sinks without validation or parameterized boundaries:
- **Database Sinks**: Raw SQL (`db.query()`, `prisma.$executeRawUnsafe()`, `sequelize.query()`), NoSQL evaluation (`collection.find({ $where: ... })`).
- **Command Sinks**: System shells (`exec()`, `spawn()`, `child_process`, `os.system()`).
- **Filesystem Sinks**: Path resolution (`fs.readFile()`, `path.join()`, upload destinations).
- **Client DOM Sinks**: Raw HTML injection (`dangerouslySetInnerHTML`, `innerHTML`, template interpolation).
- **Network Request Sinks**: Dynamic HTTP calls (`fetch()`, `axios()`, `http.get()` with user-supplied URLs).

---

## 2. SQL & NoSQL Injection Defenses

- **Strict Parameterization**: Always use parameterized queries or ORM safe methods. Never concatenate strings into query buffers:
  ```typescript
  // BAD: Vulnerable to SQLi
  await db.query(`SELECT * FROM users WHERE email = '${email}'`);

  // SECURE: Parameterized query
  await db.query('SELECT * FROM users WHERE email = $1', [email]);
  ```
- **ORM Raw Query Ban**: Flag any usage of raw SQL methods like `$queryRawUnsafe` or `sequelize.literal` unless accompanied by strict compile-time typed parameters.
- **NoSQL Operator Injection**: In MongoDB/Mongoose, sanitize request bodies to prevent object operator injection (e.g. `{ username: { $gt: "" } }`).

---

## 3. Server-Side Request Forgery (SSRF)

### The Threat
When the server fetches content from a user-supplied URL (e.g. webhooks, link previews, file imports), an attacker can specify internal network IPs to query cloud metadata services or internal microservices.

### Defenses
- **URL Parsing & Protocol Restriction**: Restrict protocols strictly to `https:`. Reject `file:`, `gopher:`, `ftp:`.
- **Private & Cloud Metadata IP Blocking**: Verify that resolved IP addresses do NOT match:
  - Localhost: `127.0.0.0/8`, `::1`
  - Cloud Metadata Service: `169.254.169.254` (AWS, GCP, Azure metadata)
  - Private networks (RFC 1918): `10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`
- **DNS Rebinding Prevention**: Resolve the hostname via DNS first, validate the resulting IP against the blocklist, and perform the HTTP request directly to the validated IP with the original `Host` header.

---

## 4. Cross-Site Scripting (XSS) & DOM Injection

- **Context-Aware Escaping**: Ensure templating engines auto-escape output. Prohibit raw HTML setters (`dangerouslySetInnerHTML`) unless cleansed by an established sanitizer like DOMPurify.
- **Dynamic URL Protocols**: Guard dynamic links (`<a href={userUrl}>`) to prevent `javascript:` pseudoprotocols. Enforce `http:` or `https:` allowlists.
- **Content Security Policy (CSP)**: Ensure CSP headers prohibit `unsafe-inline` and `unsafe-eval`.

---

## 5. Open Redirect Prevention

- **Validate Redirect Targets**: Never redirect callers to unvalidated user-supplied parameters (`res.redirect(req.query.returnUrl)`).
- **Strict Relative Path Constraint**: If redirecting locally, enforce that the path begins with a single forward slash `/` and does NOT start with `//` or `/\` (which modern browsers treat as external protocol-relative URLs).
- **Domain Allowlisting**: If external redirects are necessary, strictly validate the hostname against an explicit server-side allowlist.

---

## 6. Clickjacking & UI Redressing

- **Frame Framing Restrictions**: Configure HTTP response headers to prevent malicious sites from embedding the application inside hidden `<iframe>` overlays:
  - `X-Frame-Options: DENY` (or `SAMEORIGIN`)
  - CSP: `frame-ancestors 'none'` (or `frame-ancestors 'self'`)

---

## 7. Prototype Pollution Prevention (JavaScript / Node.js)

- **Unsafe Object Merging**: Prevent recursive merges, `Object.assign`, or JSON clones from modifying `__proto__`, `constructor`, or `prototype` keys on root objects.
- **Safe Object Dictionaries**: Use `Object.create(null)` for key-value maps to ensure no prototype inheritance exists, or freeze prototypes via `Object.freeze(Object.prototype)`.

---

## 8. Path Traversal & XML External Entity (XXE)

- **Path Traversal Defense**: Never concatenate user input directly into file paths (`path.join(__dirname, userInput)`). Resolve absolute paths using `path.resolve()` and verify that the target path begins strictly with the allowed base directory:
  ```typescript
  const safePath = path.resolve(BASE_DIR, userInput);
  if (!safePath.startsWith(BASE_DIR)) {
    throw new SecurityError("Path traversal detected");
  }
  ```
- **XXE Prevention**: When parsing XML, disable external entity resolution (`disallow-doctype-decl: true`, `resolveExternals: false`).
