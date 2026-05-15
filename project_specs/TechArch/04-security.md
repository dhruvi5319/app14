---

## 5. Security Architecture

### Authentication

**v1 decision: No authentication.**

The Wine Inventory App v1 is explicitly scoped to single-user personal use. Authentication has been deferred per PRD §4 ("Single-user personal app — authentication deferred or lightweight"). The application runs on the user's own device (local/Electron deployment) or as a private web app accessible only by the user.

**Post-v1 authentication path** (if web deployment with broader access is needed):
- Add a simple session token or JWT-based login with a single set of credentials
- Or integrate a lightweight OAuth provider (Google, GitHub) for convenience
- The REST API is already structured to add an `Authorization` header middleware with zero breaking changes

---

### Authorization

Since v1 has no multi-tenancy and no authentication, there is no access-control layer. All API endpoints are accessible to any caller on the same host.

**Threat model for v1 (local deployment):**
- Risk is limited to the local device; standard OS file-system permissions protect the SQLite `.db` file
- No secrets are stored in the application (no API keys, no user passwords)
- The app does not make outbound API calls to external services

**Threat model for v1 (web deployment):**
- Deploy behind a reverse proxy (nginx/Caddy) that restricts access by IP or requires HTTP Basic Auth at the proxy level
- Do not expose the API publicly without at least a shared secret or network restriction

---

### Data Protection

| Category | Practice |
|----------|---------|
| Data at rest | SQLite `.db` file stored in user's app data directory; protected by OS file permissions |
| Data in transit | HTTPS enforced for web deployments; localhost for local deployments |
| PII handling | No PII collected; data is wine metadata only; no user accounts or emails |
| Backups | User is responsible for backing up the SQLite file; app may expose an export/import feature post-v1 |
| Sensitive fields | `purchase_price` is the only potentially sensitive financial field; not encrypted at rest in v1 |

---

### Input Security

| Threat | Mitigation |
|--------|-----------|
| SQL injection | All queries use parameterized statements via knex.js; no string concatenation in SQL |
| XSS | React escapes all rendered content by default; no `dangerouslySetInnerHTML` usage |
| CSRF | Not applicable in v1 (no auth/session cookies); add CSRF token if sessions are introduced |
| Path traversal | No file path inputs; not applicable |
| Oversized requests | Express body parser limited to 1MB; field-level character limits enforced by Zod |
| Mass assignment | Zod schemas explicitly define allowed fields; extra keys are stripped before DB writes |

---

### API Security Headers

The Express server sets the following security headers via `helmet.js`:

```javascript
// server/app.ts
import helmet from 'helmet';
app.use(helmet());  // Sets: X-Content-Type-Options, X-Frame-Options,
                    // X-XSS-Protection, Referrer-Policy, HSTS (HTTPS only)
```

Additional headers for CORS (web deployment only):

```javascript
import cors from 'cors';
app.use(cors({
  origin: process.env.ALLOWED_ORIGIN || 'http://localhost:3000',
  methods: ['GET', 'POST', 'PATCH', 'DELETE'],
  allowedHeaders: ['Content-Type']
}));
```

---

### Rate Limiting

Not implemented in v1 (single-user, local deployment). If deployed as a web app, add `express-rate-limit`:

```javascript
import rateLimit from 'express-rate-limit';
const limiter = rateLimit({ windowMs: 60_000, max: 200 });
app.use('/api/', limiter);
```

---
