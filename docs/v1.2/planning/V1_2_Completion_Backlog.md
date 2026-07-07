# TrustLens v1.2 Completion Backlog

**Status:** canonical completion backlog
**Goal:** move TrustLens v1.2 from source-aligned controlled-pilot baseline to a
release candidate with executable evidence.

## 1. Status Vocabulary

| Status | Meaning |
|---|---|
| `Implemented` | Code exists and has a real call path. |
| `Verified` | Executed test evidence exists. |
| `Partial` | Implementation exists but coverage, hardening, or evidence is incomplete. |
| `Planned` | Not implemented. |
| `Blocked` | A decision, defect, or missing evidence prevents acceptance. |

## 2. P0 Backlog

| ID | Item | Status | Acceptance criteria |
|---|---|---|---|
| V12-P0-01 | Public registration role safety | Blocked | Public registration creates pending/non-active lecturer state; email/admin approval lifecycle is implemented or public registration is restricted; integration tests pass. |
| V12-P0-02 | PostgreSQL integration suite | Blocked | Migrations run on PostgreSQL; auth/upload/analyze/report/export/retry flows pass with isolated fixtures. |
| V12-P0-03 | Ownership negative tests | Blocked | Lecturer B cannot read, modify, analyze, export, retry, or infer resources owned by Lecturer A. |
| V12-P0-04 | Browser E2E smoke | Blocked | Backend-mode E2E covers login, course/class/assignment, upload, analyze, poll, report, export, and negative access. |
| V12-P0-05 | Error schema contract tests | Partial | All endpoint families return the official error schema with correlation ID. |
| V12-P0-06 | Worker heartbeat/lease evidence | Partial | Long stages update heartbeat; stale recovery and two-worker claim tests pass. |
| V12-P0-07 | Restore drill | Blocked | Restore into a separate environment with DB-file reconciliation and checksum evidence. |
| V12-P0-08 | CI submodule checkout evidence | Partial | Pull request workflows prove backend/frontend submodules are checked out at the expected commits. |

## 3. P1 Backlog

| ID | Item | Status | Acceptance criteria |
|---|---|---|---|
| V12-P1-01 | Production malware scanner | Planned | Approved scanner integrated; EICAR, unavailable, suspicious/infected, and cleanup tests pass. |
| V12-P1-02 | Shared rate limiting | Planned | Shared limiter works consistently across at least two API replicas. |
| V12-P1-03 | CSP, cookie, and CORS hardening | Planned | Exact CORS, CSP smoke, security headers, and token storage decision are approved and tested. |
| V12-P1-04 | Private/encrypted object storage | Planned | Storage is private, authorized, encrypted as approved, and included in restore drill. |
| V12-P1-05 | Accessibility and frontend tests | Planned | Component/service/accessibility/browser evidence is captured in CI. |
| V12-P1-06 | Performance benchmark | Planned | Benchmark report includes dataset, hardware, percentiles, provider latency, and concurrency. |
| V12-P1-07 | Academic benchmark/calibration | Planned | Dataset card, labeling guide, fixed vectors, benchmark report, calibration profile, and limitations are approved. |
| V12-P1-08 | Formal legal basis and retention approval | Planned | Provider/data processing and retention policy are approved for the deployment context. |

## 4. Implementation Priority

1. Fix public registration role safety.
2. Add PostgreSQL integration and migration gates.
3. Add ownership negative and error-contract tests.
4. Add browser E2E for the core lecturer flow.
5. Prove worker heartbeat, stale recovery, and concurrency behavior.
6. Complete restore drill and DB-file reconciliation.
7. Harden production security: scanner, shared limiter, CORS/CSP/token storage.
8. Complete accessibility, performance, and academic calibration evidence.
9. Run release readiness checklist and record GO/NO-GO decision.

## 5. Closure Conditions

v1.2 can be closed only when all P0 items are `Verified`, and any remaining P1 item
has one of:

- `Verified` evidence; or
- accepted-risk record with owner, severity, mitigation, user-facing limitation, and
  expiry date for a controlled pilot.

Accepted risk cannot be used for Critical/High security issues, IDOR, data loss,
production mock fallback, or Trust Score being interpreted as an automatic misconduct
judgment.
