# 12. Roadmap

## 1. Nguyên tắc

Roadmap phân biệt **đã có**, **cần ổn định**, **mở rộng** và **production**. Không dùng roadmap để che technical debt.

## 2. Baseline hiện tại

### Đã có

React/FastAPI, auth, role/permission, course/class/assignment, PDF/DOCX upload, canonical analyze/process/retry pipeline, metadata verification, Trust Score v1.2, report, PDF/DOCX/XLSX export, dashboard, admin user/provider/audit/AI diagnostics and existing unit tests.

### Partial

Scoring config management, report history, provider fallback, soft delete/retention, observability, production hardening and test coverage.

### Chưa có

Durable queue, batch, OCR, realtime, student portal, LMS, multi-tenant, MLOps và production SLO/DR.

## 3. Phase 0 — Baseline stabilization

### P0.1. Unified pipeline

Status 2026-07-06: implemented in code.

- Analyze/process/retry call the same canonical orchestration.
- The state machine is persisted on processing jobs.
- One active job per submission is enforced by service checks and a PostgreSQL partial unique index.
- Retry lineage is stored with `retry_of_job_id`.
### P0.2. Contract alignment

- Khóa C1–C7 v1.2.
- Đồng bộ frontend/backend.
- Global error schema.
- OpenAPI review.
- Xóa docs stale.
- Internalize stage endpoints.

### P0.3. Remove false success

- Không fallback mock ở backend mode.
- UI báo lỗi thật.
- Mock chỉ khi `VITE_USE_MOCK=true`.
- Banner rõ cho mock environment.

### P0.4. Test baseline

- Dev test dependencies.
- PostgreSQL integration tests.
- PDF/DOCX E2E.
- Ownership negative tests.
- Retry/export/migration tests.

### P0.5. Security minimum

- Fail-fast default secret.
- Password/rate-limit.
- CORS allow-list.
- Error sanitization.
- Upload hardening.
- Secret scan.

**Exit:** end-to-end không mock, retry tạo report thật, CI pass, C1–C7 versioned, không critical gap.

## 4. Phase 1 — Operational maturity

### Durable jobs

Queue/broker, worker, retry/backoff, dead-letter, idempotency và metrics.

### Batch

Batch entity/items, per-item isolation, dashboard, cancel/retry selected.

### Provider resilience

Adapter, priority, timeout, rate-limit handling, circuit breaker, fallback và health metrics.

### Scoring governance

Backend config API, preset versioning, assignment binding, immutable report version, test vector và audit.

### Data lifecycle

Soft delete standard, retention, purge, restore, storage reconciliation và export lifecycle.

### Observability

Structured logs, correlation, metrics, tracing, alerting và job SLI.

**Exit:** worker restart không mất job; batch/fallback/retention/restore pass; có SLO baseline.

## 5. Phase 2 — Intelligence expansion

- OCR per page, confidence, bbox, hybrid merge và manual review.
- Citation-in-context mapping.
- Missing/unused references.
- Context relevance.
- Plagiarism/similarity signal không mang tính phán quyết.
- Labeled dataset, human agreement, precision/recall, calibration, bias/drift và model card.

**Exit:** benchmark được phê duyệt, confidence calibrated, OCR đạt threshold, model có rollback.

## 6. Phase 3 — Platform expansion

### Student portal

Student ownership, self-upload, feedback visibility, correction/appeal và privacy.

### LMS

LTI/REST/webhook, roster/class/assignment/submission sync; grade passback chỉ khi được phê duyệt.

### Multi-tenancy

Tenant entity, tenant-scoped role, isolation, quota, config và audit. Không chỉ thêm `tenant_id` mà thiếu query guard/isolation test.

## 7. Phase 4 — Production scale

Containerization, CI/CD, autoscaling, object storage, WAF, SSO, secret rotation, backup/DR, cost accounting, SLO/SLA, incident governance và data residency.

## 8. Ưu tiên theo rủi ro

| Ưu tiên | Hạng mục | Lý do |
|---:|---|---|
| 1 | Unified pipeline/retry | Completed giả |
| 2 | Disable mock fallback | Thành công giả |
| 3 | Integration/E2E | Thiếu bằng chứng |
| 4 | Secret/CORS/rate limit | Security |
| 5 | Durable queue | Mất job |
| 6 | Scoring governance | Không tái lập |
| 7 | Provider resilience | Phụ thuộc ngoài |
| 8 | Retention/backup | Dữ liệu |
| 9 | OCR/batch | Mở rộng giá trị |
| 10 | LMS/multi-tenant | Sau khi lõi ổn định |

## 9. Không nên làm sớm

- Tách microservices trước khi thống nhất pipeline.
- Thêm LLM mọi stage.
- Tuyên bố AI xác minh “nguồn giả”.
- Mở student portal khi ownership chưa ổn.
- Multi-tenant khi chưa có isolation test.
- Autoscaling khi job chưa durable.
- Đổi weight không version/test.
# P1 pilot-readiness update - 2026-07-06

Moved from roadmap to implemented pilot baseline:

- Database-backed durable worker queue using `processing_jobs`.
- Auth hardening: stronger password policy, auth rate limits, refresh rotation, logout revocation.
- File quarantine, magic-byte validation, and local scan policy.
- Readiness/metrics endpoints and correlation ID middleware.
- Primary list API pagination.
- Retention purge service with safe dry-run default.
- CI workflow scaffolding for backend, frontend, docs, and dependency updates.

Still roadmap after this update:

- Shared distributed rate limiter for multi-replica deployments.
- Production malware scanner integration.
- Browser E2E suite and PostgreSQL integration/security tests.
- Backup/restore automation and completed restore drill evidence.
- Academic benchmark corpus, calibration report, and scoring changelog governance.
- Accessibility audit and component tests.
# Current milestone status - 2026-07-06

For the authoritative P0/P1 completion matrix, see [P0/P1 completion status](P0_P1_Completion_Status.md).

Summary:

- P0 critical remediation has implementation evidence, but is not closed unconditionally
  until PostgreSQL integration, ownership-negative, browser E2E and CI evidence are
  attached to the release record.
- P1 database-backed durable queue, auth hardening, upload quarantine/scan gate, primary API pagination, readiness/metrics, retention purge scaffolding, CI scaffolding, and docs updates are implemented.
- Items still open for full pilot sign-off are integration/security/E2E test suites, branch protection evidence, production malware scanner, restore drill evidence, academic benchmark/calibration, and accessibility/component test coverage.

Any older line in this roadmap that says durable queue, auth hardening, upload scan, retention purge, or pagination is not implemented is superseded by this milestone status.
