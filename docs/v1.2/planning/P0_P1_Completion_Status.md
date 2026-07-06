# P0/P1 Completion Status

**Last updated:** 2026-07-06  
**Scope:** implementation and documentation baseline after v1.2 pilot-readiness work.

## Status Legend

| Status | Meaning |
|---|---|
| Completed | Code/docs/config exist and local verification evidence is recorded. |
| Partial | Implemented enough for pilot use, but production-grade evidence or infrastructure is still missing. |
| Open | Not implemented or intentionally deferred. |

## P0 Release-Gate Remediation

| Area | Status | Evidence |
|---|---|---|
| Canonical analyze/process/retry pipeline | Completed | `POST /submissions/{id}/analyze`, process alias, and retry all use `run_analysis_pipeline`. |
| Retry lineage | Completed | Retry jobs store `retry_of_job_id` and require a terminal source job. |
| One active job per submission | Completed | Service checks plus PostgreSQL partial unique index. |
| Completed job must have report | Completed | Pipeline sets `report_id`; job response exposes report id. |
| Explicit-only frontend mock mode | Completed | Backend-mode failures are not converted to mock success; frontend mock is gated by `VITE_USE_MOCK=true`. |
| Secret fail-fast | Completed | `SECRET_KEY` is required, length-checked, and rejects placeholder/sample values. |
| Error contract normalization | Completed | Backend exception handlers return structured errors with `correlation_id`. |
| Trust Score v1.2 C1-C7 alignment | Completed | Backend/frontend/docs use C1-C7 and `trust-score-v1.2`. |
| Frontend report navigation by report id | Completed | Report route uses `report_id`, not submission fallback. |
| Local verification | Completed | Backend compile/import and frontend lint/build pass. |
| PostgreSQL integration and browser E2E | Partial | CI scaffolding exists; concrete integration/E2E suites remain to be added. |

## P1 Pilot-Readiness Items

| P1 ID | Item | Status | Evidence / caveat |
|---|---|---|---|
| P1-01 | CI quality gate | Partial | Backend, frontend, docs workflows and Dependabot added. Full branch protection and E2E artifacts require repository settings and test suites. |
| P1-02 | Dependency/toolchain standardization | Completed | Backend `requirements-dev.txt`; frontend lockfile and CI `npm ci`; root `CONTRIBUTING.md`. |
| P1-03 | Auth hardening | Partial | Password policy, rate limits, refresh rotation, replay rejection, logout revocation implemented. Shared rate limiter/CSP/cookie migration remain production hardening. |
| P1-04 | File quarantine and scan policy | Partial | Quarantine, signature checks, local scan policy, accepted-storage gate implemented. Production malware scanner remains open. |
| P1-05 | Durable worker queue | Completed for pilot | Database-backed queue on `processing_jobs`, worker command, claim metadata, queue metrics. External broker remains optional scale work. |
| P1-06 | Retention, purge, privacy controls | Partial | Configurable retention defaults and admin purge dry-run/apply implemented. Formal owner-approved retention policy and restore evidence remain open. |
| P1-07 | Observability and readiness | Partial | `x-correlation-id`, `/health/ready`, `/health/metrics`, queue counts/age implemented. Alerts/dashboard/tracing remain environment work. |
| P1-08 | Backup, restore, incident runbook | Partial | Operations docs updated with runbook entries. Backup automation and restore drill evidence remain open. |
| P1-09 | Pagination, timeout, performance baseline | Partial | Primary list APIs now use pagination contract. Performance benchmark and query-plan evidence remain open. |
| P1-10 | Benchmark and calibration Trust Score | Open | No labeled benchmark corpus/calibration report yet. Trust Score remains review-support only. |
| P1-11 | Usability, accessibility, frontend quality | Partial | Frontend builds/lints and handles paginated responses. Accessibility audit/component/E2E coverage remains open. |
| P1-12 | Operations docs and pilot package | Partial | v1.2 docs updated for API, architecture, data, security, testing, operations, roadmap, and gaps. Formal pilot approval checklist remains open. |

## Pilot-Ready Decision

TrustLens v1.2 is **pilot-readiness implemented with caveats**, not fully production-ready.

Completed enough for controlled pilot:

- P0 critical remediation items are closed.
- Durable job execution exists through the database-backed worker.
- Auth, upload, pagination, retention, and observability controls have pilot implementations.
- CI and documentation scaffolding are present.

Still required before declaring full `Pilot Ready` without qualification:

- PostgreSQL integration/security tests and browser E2E.
- Repository branch protection requiring CI.
- Production malware scanner or approved scanner policy.
- Backup automation plus restore drill evidence.
- Academic benchmark/calibration package.
- Accessibility audit and frontend component/service test coverage.
