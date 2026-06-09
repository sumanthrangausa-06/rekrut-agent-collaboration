# Security Scanning Skill

Autonomous security scanning for continuous threat detection and vulnerability management.

## When to Use
- Every 6 hours (automated via cron)
- After every deployment to staging
- When new dependencies are added
- When SEC-001 or CISO requests a scan

## How to Use

```bash
# Run full security scan
openclaw skill run security-scanning --scope=full

# Quick dependency audit only
openclaw skill run security-scanning --scope=dependencies

# Code pattern scan
openclaw skill run security-scanning --scope=code
```

## Scan Types
| Type | Frequency | Agent |
|------|-----------|-------|
| Dependency audit | Every 6 hours | SEC-001 |
| Code pattern scan | Every 12 hours | SEC-003 |
| Configuration check | Every 24 hours | DO-006 |
| Penetration test | Weekly | SEC-002 |

## Output Format
```
[CRITICAL] npm audit: 2 vulnerabilities in express
[HIGH] routes/auth.js: Missing rate limiting on login
[MEDIUM] config: CORS too permissive for production
[LOW] docs: Missing security headers documentation
```

## Agent Assignment
- SEC-001: Vulnerability scanning lead
- SEC-002: Penetration testing
- CISO: Review and escalation
- DO-006: Security infrastructure
