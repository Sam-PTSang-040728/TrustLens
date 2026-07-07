# Security and Privacy

**Status:** source-aligned v1.2 baseline

This document describes implemented controls, blockers, and remaining hardening work.
It intentionally avoids unconditional production claims where release evidence is
missing.

## 1. Critical Blocker

### Public Self-Registration Creates Active Lecturers

Current source code creates public registrations with:

```python
role="lecturer"
is_active=True
```

**Status:** `Blocked`

Public registration must not directly create an active lecturer account. A safer
future lifecycle is:

```text
PENDING_LECTURER
-> EMAIL_VERIFIED
-> ADMIN_APPROVED
-> LECTURER
```

Until this is implemented and tested, public registration role safety must remain a
release blocker.

## 2. Assets and Risk Areas

| Asset | Main risks |
|---|---|
| Accounts and tokens | Credential stuffing, refresh replay, role escalation, token theft. |
| Submission files | Malware, path traversal, unauthorized access, retention failure. |
| Extracted text | Privacy leakage, excessive provider sharing, raw logging. |
| Classes/submissions/reports | IDOR, cross-lecturer access, stale deleted resources. |
| Metadata/provider secrets | Key leakage, provider payload leakage, rate-limit failures. |
| Trust Score reports | Misinterpretation as misconduct proof or scientific truth. |

## 3. Implemented Controls

| Control | Status | Notes |
|---|---|---|
| Password policy | Implemented | Minimum length, common password and trivial repeat rejection. |
| JWT access tokens | Implemented | `HS256` access tokens. |
| Refresh rotation/revocation | Implemented | Server-side hashed refresh token records, rotation, logout revocation. |
| Replay/revoked refresh rejection | Implemented | Audited on rejected reuse. |
| Process-local auth rate limit | Implemented | Login/register/refresh limits. |
| Secret validation | Implemented | Required `SECRET_KEY`, length checks, placeholder rejection. |
| Permission dependencies | Implemented | Backend permission dependencies. |
| Ownership checks | Implemented | Class, assignment, submission, job, report/export scope checks. |
| Quarantine storage | Implemented | Uploads start in quarantine. |
| Signature validation | Implemented | Extension/MIME/signature checks. |
| Local scan policy | Implemented | Pilot policy scanner; not enterprise malware scanning. |
| Accepted-storage gate | Implemented | Analysis requires accepted and clean file state. |
| Audit log | Implemented | Auth, upload, analyze, delete, and admin actions. |
| Retention purge | Implemented | Configurable dry-run/apply endpoint. |
| AI raw input logging disabled | Implemented | Raw AI input persistence/logging disabled by default. |
| Correlation ID | Implemented | `x-correlation-id` middleware. |
| Security headers | Partial | Baseline headers exist; production CSP validation still needed. |

## 4. Controls Not Yet Sufficient

| Area | Status | Required before full sign-off |
|---|---|---|
| Shared rate limiter | Planned | Redis or equivalent shared store across replicas. |
| Enterprise malware scanning | Planned | Approved scanner, EICAR test, unavailable policy, quarantine cleanup. |
| Encrypted object storage | Planned | Private storage, encryption, authorized download, restore evidence. |
| Formal legal basis | Planned | Policy approval for processing, retention, providers, and data subject handling. |
| Full security integration suite | Blocked | Ownership negative, refresh replay, upload security, CORS/CSP, error redaction tests. |
| Cookie/CSP hardening | Planned | Evaluate HttpOnly Secure SameSite cookies and strict CSP. |
| Restore evidence | Blocked | Restore drill with DB-file reconciliation. |

## 5. Authorization Matrix

| Action | Admin | Lecturer | Student |
|---|---:|---:|---:|
| Login | Yes | Yes | Yes |
| Course management | Yes | Yes | No |
| Class management | Yes | Owned scope | No |
| Assignment management | Yes | Owned scope | No |
| Upload/analyze | Yes | Owned scope | No |
| Report/export | Yes | Owned scope | No |
| User admin | Yes | No | No |
| Audit/provider admin | Yes | No | No |

Executable ownership negative tests are still required before marking this matrix
`Verified`.

## 6. Privacy Rules

- Send only the minimum necessary citation/reference context to providers.
- Do not send the full report to metadata providers.
- Do not log passwords, tokens, secrets, full report text, raw AI input, or sensitive
  provider payloads.
- Preserve provider/model/prompt/version evidence where AI or metadata providers are
  used.
- Publish limitations for Trust Score interpretation and provider coverage.

## 7. Required Security Tests

1. Cross-lecturer IDOR for class, assignment, submission, job, report, and export.
2. Role escalation and self-edit role/permission attempts.
3. Expired, malformed, replayed, and revoked refresh tokens.
4. MIME spoofing, path traversal, oversize, malformed PDF/DOCX, and scan rejection.
5. Stored file guessing and unauthorized download.
6. Invalid CORS origin and CSP smoke.
7. Brute force/rate-limit behavior.
8. Error/log redaction.
9. Mock mode disabled outside explicit mock configuration.
10. Provider secret redaction.
11. Access to deleted resources.

## 8. Policy Boundary

Trust Score is a review-support signal based on available metadata and evidence. It
is not an automatic conclusion about fraud, academic misconduct, scientific truth, or
grading outcome.
