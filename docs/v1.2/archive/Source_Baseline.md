# Source Baseline

**Snapshot date:** 2026-07-07
**Status:** code-inspection baseline for TrustLens v1.2 documentation
**Application version:** `1.2.0`
**API version:** `v1`
**Scoring version:** `trust-score-v1.2`

This document records the source snapshot used to align the v1.2 documentation.
It is not a production certification and does not replace executable release-gate
evidence.

## Repository Snapshot

| Component | Repository | Commit SHA | Version |
|---|---|---|---|
| Root | `PhanTanSang-netizen/trustlens` | `6a05af6a234b83b830fcb8faafa8175cb182d74a` | `1.2.0` |
| Backend | `PhanTanSang-netizen/TrustLens-Backend` | `b4ee9aa7f4ab7cef4b516f913185e2f63184bd7e` | `1.2.0` |
| Frontend | `tranquynhnhu102025-hash/Frontend-TrustLens` | `3517264c1ed8c8f35d72b15aef59f1b9498280e8` | `1.2.0` |

GitHub may show redirected owners if repositories were renamed or transferred. Use
the currently working URL for navigation, but do not rewrite project history without
a separate governance decision.

## Source-of-Truth Files

| Content | Source file |
|---|---|
| API routers | `apps/backend/app/api/v1/api_router.py` |
| Upload/analyze | `apps/backend/app/api/v1/endpoints/submissions.py` |
| Job/process/retry | `apps/backend/app/api/v1/endpoints/jobs.py` |
| Auth | `apps/backend/app/api/v1/endpoints/auth.py` |
| Default public register role | `apps/backend/app/services/auth_service.py` |
| Config/version | `apps/backend/app/core/config.py` |
| Trust Score definition | `apps/backend/app/core/trust_score_definition.py` |
| Scoring runtime | `apps/backend/app/services/scoring_service.py` |
| Analysis pipeline | `apps/backend/app/services/analysis_pipeline_service.py` |
| Queue service | `apps/backend/app/services/job_queue_service.py` |
| Metadata verification | `apps/backend/app/services/metadata_verification_service.py` |
| Metadata status vocabulary | `apps/backend/app/core/enums/metadata_status.py` |
| File storage and scan policy | `apps/backend/app/services/file_storage_service.py` |
| Frontend analyze client | `apps/frontend/src/services/uploadService.ts` |

## Evidence Priority

1. Executed integration, E2E, and security tests.
2. Mounted router plus service call path.
3. Model and migration.
4. Frontend client and route integration.
5. Unit tests.
6. Current documentation.
7. Roadmap or archived plans.

Plans and archived review notes must not be used alone to claim runtime behavior.

## Key Baseline Findings

| ID | Finding | Current documentation status |
|---|---|---|
| BASE-001 | Trust Score v1.2 uses C1-C7 and total weight 100. | Document as current baseline. |
| BASE-002 | Canonical analysis endpoint is `POST /api/v1/submissions/{submission_id}/analyze`. | Document as canonical. |
| BASE-003 | `POST /api/v1/jobs/submissions/{submission_id}/process` is a backward-compatible alias. | Document as alias for new clients to avoid. |
| BASE-004 | Jobs are persisted in `processing_jobs`; worker command is `python -m app.workers.tasks`. | Document as current queue architecture. |
| BASE-005 | Public registration creates `role="lecturer"` and `is_active=True`. | Mark public registration role safety `Blocked`. |
| BASE-006 | Crossref, OpenAlex, URL checker, and publication status evaluator have runtime evidence. | Document as implemented providers. |
| BASE-007 | Semantic Scholar client exists but is not part of the canonical metadata verification call path. | Do not list as implemented provider. |
| BASE-008 | Error normalization exists through exception handlers and correlation ID middleware, but endpoint coverage still needs contract tests. | Mark official error schema `Partial` until verified. |
| BASE-009 | Local scan policy and quarantine exist; enterprise malware scanning is not implemented. | Mark production malware scanner open. |
| BASE-010 | Full release sign-off still lacks PostgreSQL integration, ownership negative, browser E2E, restore, performance, accessibility, and academic calibration evidence. | Keep release status `Partial` or `Blocked` as appropriate. |
