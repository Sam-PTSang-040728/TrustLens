# Release Readiness Checklist v1.2

**Status:** v1.2.1 release-gate checklist
**Rule:** a checked item without evidence is treated as unchecked.

## 1. Scope and Version

- [ ] Release commit and submodule pointers are locked.
- [ ] Application/backend/frontend versions are `1.2.1`.
- [ ] API remains `v1` under `/api/v1`.
- [ ] Trust Score remains `trust-score-v1.2`.
- [ ] No unsupported feature is shown as supported.
- [ ] Known limitations are updated and approved.

## 2. Required Gates

| Gate | Required evidence | Status |
|---|---|---|
| Ruff | Command output or CI run | Open |
| Pytest | Command output or CI run | Open |
| Alembic upgrade head | PostgreSQL upgrade log | Blocked until DB evidence |
| PostgreSQL integration | Integration report | Blocked |
| Ownership negative tests | Security/API test report | Blocked |
| Frontend lint/build | Command output or CI run | Open |
| Browser E2E | E2E report | Blocked |
| Secret/dependency scan | CI/security report | Open |
| Restore evidence | Restore drill report | Blocked |

## 3. Backend Quality

- [ ] `ruff check app tests` passes.
- [ ] `pytest` passes.
- [ ] `alembic upgrade head` passes on PostgreSQL.
- [ ] Migration from previous release snapshot passes.
- [ ] Ownership negative tests pass.
- [ ] Error schema contract tests pass.
- [ ] Worker retry/concurrency/stale recovery tests pass.
- [ ] Export files open and match authorization rules.

## 4. Frontend Quality

- [ ] `npm ci` passes.
- [ ] `npm run lint` passes.
- [ ] `npm run build` passes.
- [ ] Component/service tests pass.
- [ ] Browser E2E smoke passes in backend mode.
- [ ] Production build does not enable mock mode.
- [ ] Accessibility smoke/audit passes.
- [ ] Error/loading/empty states are checked.

## 5. Core Flow

- [ ] Register/login.
- [ ] Course/class/assignment creation.
- [ ] PDF text-layer upload.
- [ ] DOCX upload.
- [ ] Analyze returns persisted job.
- [ ] Worker processes job.
- [ ] Progress reflects backend state.
- [ ] Completed job has `report_id`.
- [ ] Report renders C1-C7 evidence.
- [ ] PDF/DOCX/XLSX export works.
- [ ] Failed terminal job retry preserves lineage.
- [ ] Cross-user access is denied.

## 6. Security

- [ ] Public registration no longer creates active lecturers directly.
- [ ] Production secret policy passes.
- [ ] No secrets in repository/history scan scope.
- [ ] Exact CORS allow-list configured.
- [ ] HTTPS/HSTS configured for deployment.
- [ ] CSP/security headers validated.
- [ ] Shared rate limiter configured if multiple API processes exist.
- [ ] Refresh rotation/replay/logout tests pass.
- [ ] Approved malware scanner is tested.
- [ ] Private storage and authorized download are configured.
- [ ] Error/log redaction is verified.
- [ ] No unresolved Critical/High issue remains.

## 7. Queue and Operations

- [ ] Worker process is deployed separately from API.
- [ ] Queue mode is `database`.
- [ ] Heartbeat updates during long jobs.
- [ ] Stale-job recovery test passes.
- [ ] Queue metrics are available.
- [ ] Queue-stuck alert is configured.
- [ ] Provider timeout/fallback policy is configured.
- [ ] Disk/storage alert is configured.

## 8. Data Lifecycle

- [ ] Retention values are approved.
- [ ] Purge dry-run is reviewed.
- [ ] Purge apply is tested in non-production.
- [ ] Rejected quarantine files are cleaned by policy.
- [ ] Export retention works.
- [ ] Backup is encrypted.
- [ ] Restore drill is completed.
- [ ] DB-file reconciliation is completed after restore.

## 9. Trust Score Governance

- [ ] Weights sum to 100.
- [ ] Fixed test vectors pass.
- [ ] Report stores scoring version.
- [ ] Confidence is separate from score.
- [ ] Penalty is visible/explainable.
- [ ] Disclaimer appears in UI/report.
- [ ] Benchmark/calibration status is disclosed.
- [ ] Scoring changes have changelog and compatibility note.

## 10. Evidence Register

| Evidence | Required for | Link/location | Owner | Date |
|---|---|---|---|---|
| Backend CI run | Backend gate | | | |
| Frontend CI run | Frontend gate | | | |
| Docs CI run | Documentation gate | | | |
| PostgreSQL integration report | P0 backend acceptance | | | |
| Ownership negative report | Security/IDOR gate | | | |
| Browser E2E report | User-flow acceptance | | | |
| Worker concurrency/crash report | Queue reliability | | | |
| Export parser verification | Report/export gate | | | |
| Migration head and upgrade log | Data/schema gate | | | |
| Malware scanner evidence | Upload security | | | |
| CORS/CSP/rate-limit evidence | Production security | | | |
| Backup restore report | Operations gate | | | |
| Benchmark/calibration report | Trust Score governance | | | |
| Known limitations approval | Product/legal/academic scope | | | |

## 11. Decision Record

| Field | Value |
|---|---|
| Release version | `1.2.1` |
| Commit SHA | |
| Backend submodule SHA | |
| Frontend submodule SHA | |
| Database migration head | |
| Scoring version | |
| Environment | |
| Decision | `GO` / `NO-GO` / `GO WITH ACCEPTED RISKS` |
| Accepted risks | |
| Approvers | |
| Date | |

`GO WITH ACCEPTED RISKS` is only for controlled pilots. It cannot be used for
Critical/High security issues, IDOR, data loss risk, production mock fallback, or
Trust Score being presented as an automatic misconduct judgment.
