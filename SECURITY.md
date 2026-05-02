# Security Policy

## Supported Versions

| Version | Supported          |
| ------- | ------------------ |
| 0.1.x   | :white_check_mark: |

## Reporting a Vulnerability

**Do not report security vulnerabilities through public GitHub issues.**

If you discover a security vulnerability in SMN, please report it responsibly:

1. **Email:** Send a detailed report to **freddie.ley@icloud.com**
2. **Subject line:** `[SECURITY] Brief description of the issue`
3. **Include:**
   - Description of the vulnerability
   - Steps to reproduce
   - Potential impact assessment
   - Suggested fix (if any)

### Response Timeline

| Stage | Target |
|-------|--------|
| Acknowledgment | Within 48 hours |
| Initial assessment | Within 5 business days |
| Fix development | Within 30 days for critical/high severity |
| Public disclosure | Coordinated after fix is released |

### Severity Classification

We follow CVSS v3.1 for severity scoring:

| Severity | CVSS Score | Response |
|----------|-----------|----------|
| Critical | 9.0 - 10.0 | Emergency patch, immediate notification to affected users |
| High | 7.0 - 8.9 | Priority fix within 14 days |
| Medium | 4.0 - 6.9 | Fix within 30 days |
| Low | 0.1 - 3.9 | Fix in next regular release |

## Security Architecture

SMN is designed for enterprise and government customers. The following security controls are built into the platform:

### Authentication & Authorization
- API key authentication with SHA-256 hashed storage (raw keys shown once at creation)
- Per-tenant data isolation at the database level
- Scoped permissions following `resource:action` convention with wildcard support
- ABAC (Attribute-Based Access Control) policy conditions

### Data Protection
- All sensitive keys are hashed before storage (never stored in plaintext)
- SSRF protection on HTTP connectors (blocks private ranges, metadata endpoints)
- Output guardrails with PII detection and redaction
- Content policy enforcement blocking secrets and private keys in outputs

### Audit & Compliance
- Hash-chained immutable audit trail (tamper-evident, EU AI Act Art. 12 compliant)
- 7-year default audit retention (configurable via `SMN_AUDIT_RETENTION_DAYS`)
- Full input/output logging for high-risk agents
- Compliance checking against EU AI Act and NIST AI RMF frameworks

### Runtime Security
- Non-root Docker containers
- Per-task cost budgets and kill switch
- Per-tenant rate limiting (Redis-backed with in-memory fallback)
- Circuit breaker patterns to prevent cascading LLM failures
- Policy-as-code with deny-by-default option

### Supply Chain
- Dependency scanning via Safety in CI
- Static analysis via Bandit in CI
- Pinned minimum versions for all dependencies

## Security Best Practices for Operators

1. **Change `SMN_SECRET_KEY`** — The default value is insecure. Generate a cryptographically random key:
   ```bash
   python -c "import secrets; print(secrets.token_urlsafe(64))"
   ```

2. **Use PostgreSQL in production** — SQLite is for development only.

3. **Enable TLS** — Deploy behind a reverse proxy (nginx, Caddy, ALB) with TLS termination.

4. **Restrict network access** — The API server should not be directly exposed to the internet without a load balancer and firewall rules.

5. **Rotate API keys** — Use key expiration (`expires_at`) and rotate keys regularly.

6. **Set cost limits** — Configure `SMN_MAX_COST_PER_TASK_USD` and `SMN_REQUIRE_HUMAN_APPROVAL_ABOVE_USD` to prevent runaway costs.

7. **Review audit logs** — Regularly verify audit chain integrity with `smn audit verify`.

8. **Keep dependencies updated** — Run `safety check` regularly and update pinned versions.

## Acknowledgments

We appreciate the security research community's efforts in responsibly disclosing vulnerabilities. Reporters will be acknowledged in the CHANGELOG (unless they prefer to remain anonymous).
