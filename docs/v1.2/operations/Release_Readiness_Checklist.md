# Checklist Release Readiness TrustLens v1.2

**Cách dùng:** mỗi mục phải có owner, evidence link và ngày xác nhận. Checkbox không có evidence không được tính là hoàn thành.

## 1. Scope và version

- [ ] Release commit và submodule pointers đã khóa.
- [ ] Backend version, frontend version và Trust Score version được ghi nhận.
- [ ] Không có feature ngoài scope được hiển thị như đã hỗ trợ.
- [ ] Known limitations đã cập nhật.
- [ ] Changelog đã cập nhật.

## 2. Repository và CI

- [ ] GitHub Actions checkout `submodules: recursive`.
- [ ] Backend CI pass.
- [ ] Frontend CI pass.
- [ ] Docs lint pass.
- [ ] Dependency audit pass hoặc có risk acceptance.
- [ ] Required checks được bật trong branch protection.
- [ ] Không merge trực tiếp bypass required checks ngoài break-glass procedure.

## 3. Backend quality

- [ ] Ruff/lint pass.
- [ ] Unit tests pass.
- [ ] PostgreSQL integration tests pass.
- [ ] Alembic upgrade từ empty DB pass.
- [ ] Migration từ previous release snapshot pass.
- [ ] Ownership negative tests pass.
- [ ] Retry/queue concurrency tests pass.
- [ ] Export files mở được.

## 4. Frontend quality

- [ ] Lint pass.
- [ ] Production build pass.
- [ ] Component/service tests pass.
- [ ] Browser E2E smoke pass.
- [ ] Backend mode không fallback mock.
- [ ] Không gọi endpoint không tồn tại.
- [ ] Accessibility smoke pass.
- [ ] Error/loading/empty states đã kiểm tra.

## 5. Core acceptance flow

- [ ] Register/login.
- [ ] Course/class/assignment creation.
- [ ] PDF text-layer upload.
- [ ] DOCX upload.
- [ ] Analyze returns persisted job.
- [ ] Worker processes job.
- [ ] Progress reflects backend state.
- [ ] Completed job has report ID.
- [ ] Report renders C1–C7 evidence.
- [ ] PDF/DOCX/XLSX export.
- [ ] Failed job retry preserves lineage.
- [ ] Cross-user access denied.

## 6. Security

- [ ] Production secret length/policy pass.
- [ ] No secret in repository/history scan scope.
- [ ] Exact CORS allow-list.
- [ ] HTTPS/HSTS.
- [ ] CSP/security headers.
- [ ] Rate limiter suitable for deployment topology.
- [ ] Refresh rotation/replay/logout tests pass.
- [ ] Malware scanner approved and tested.
- [ ] Private storage and authorized download.
- [ ] Error/log redaction verified.
- [ ] No unresolved Critical/High issue.

## 7. Queue và operations

- [ ] Worker process deployed separately from API.
- [ ] Queue mode is `database`.
- [ ] Heartbeat updates during long jobs.
- [ ] Stale-job recovery test pass.
- [ ] Queue metrics available.
- [ ] Queue-stuck alert configured.
- [ ] Provider timeout/fallback configured.
- [ ] Disk/storage alert configured.

## 8. Data lifecycle

- [ ] Retention values approved.
- [ ] Purge dry-run reviewed.
- [ ] Purge apply tested in non-production.
- [ ] Rejected quarantine files cleaned.
- [ ] Export retention works.
- [ ] Backup encrypted.
- [ ] Restore drill completed.
- [ ] DB-file reconciliation completed after restore.

## 9. Trust Score governance

- [ ] Weights sum to 100.
- [ ] Fixed test vectors pass.
- [ ] Report stores scoring version.
- [ ] Confidence is separate from score.
- [ ] Penalty is visible/explainable.
- [ ] Disclaimer appears in UI/report.
- [ ] Benchmark/calibration status disclosed.
- [ ] Scoring changes have changelog and compatibility note.

## 10. Documentation

- [ ] SRS matches implemented scope.
- [ ] API reference matches mounted router.
- [ ] FE–BE contract matrix reviewed.
- [ ] Deployment guide tested from clean environment.
- [ ] Worker runbook tested.
- [ ] Security hardening checklist reviewed.
- [ ] Known limitations published.
- [ ] Demo/pilot guide does not overclaim accuracy.

## 11. Go/No-Go record

| Field | Value |
|---|---|
| Release version | |
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

`GO WITH ACCEPTED RISKS` chỉ phù hợp controlled pilot; không được dùng để bỏ qua lỗi Critical hoặc nguy cơ lộ dữ liệu.

## 12. Evidence Register

Before the decision meeting, attach or link the following evidence. A checked box
without evidence is treated as unchecked.

| Evidence | Required for | Link/location | Owner | Date |
|---|---|---|---|---|
| Backend CI run | Repository/backend gate | | | |
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

## 13. Decision Rules

Decision must be one of:

- `GO`: all P0 and release-critical P1 gates completed with evidence.
- `NO-GO`: any P0 gate missing/failing, any unresolved Critical/High security issue,
  any IDOR/data-loss risk, or production mock/batch contract mismatch.
- `GO WITH ACCEPTED RISKS`: controlled pilot only; each remaining Partial/Open/Blocked
  item has owner, severity, expiry date, mitigation and user-facing limitation.

`GO WITH ACCEPTED RISKS` cannot be used for:

- missing ownership enforcement;
- missing production malware control for real uploads;
- untested restore when real data is in scope;
- frontend calling nonexistent backend endpoints in production;
- Trust Score presented as fraud/academic-truth judgment;
- secrets, tokens or provider payloads leaking in logs/errors.

## 14. Accepted Risk Template

| Field | Value |
|---|---|
| Risk ID | |
| Backlog item | |
| Severity | |
| Scope | |
| Mitigation | |
| User-facing limitation | |
| Expiry date | |
| Owner | |
| Approver | |

Accepted risks must be revisited before the expiry date and cannot silently roll into
the next release.
