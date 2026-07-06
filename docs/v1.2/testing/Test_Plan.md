# 10. Kiểm thử và Chất lượng

## P0 remediation evidence - 2026-07-06

Executed in this workspace:

| Gate | Command | Result |
|---|---|---|
| Backend syntax/import compile | `python -m compileall app` | Pass |
| Backend app startup import | `.\.venv\Scripts\python.exe -c "from app.main import fastapi_app; print(fastapi_app.version)"` | Pass, printed `1.2.0` |
| Alembic head check | `.\.venv\Scripts\python.exe -m alembic heads` | Pass, head `f6a7b8c9d0e1` |
| Frontend lint | `npm run lint` | Pass |
| Frontend production build | `npm run build` | Pass; Vite chunk-size warning only |

Not executed:

- Backend unit tests: `pytest` is not installed in the backend `.venv` or system Python, and it is not listed in `requirements.txt`.
- Backend PostgreSQL integration/security tests: suite not present in the repository yet.
- Frontend unit/E2E tests: `package.json` has no `test` or `e2e` script.

Release evidence status: P0 remediation code gates above pass, but full release-gate acceptance still requires adding backend dev test dependencies, integration/security suites, and browser E2E.
## 1. Bằng chứng hiện có

Backend có unit test được nhận diện cho metadata status, relevance, weight invariance, scoring components, publication status, duplicate detection và DOI/metadata conflict.

Chưa đủ bằng chứng để khẳng định:

- Toàn bộ test pass.
- Có PostgreSQL integration test.
- Có frontend-backend E2E.
- Có security/performance test.
- Có CI gate.

`pytest` không xuất hiện trong `requirements.txt` được rà soát; dev dependencies cần chuẩn hóa.

## 2. Test pyramid

### Unit

Parser, normalization, metadata status, candidate matching, C1–C7, label boundaries, duplicate, penalty, permission mapping và state transition.

### Integration

API + PostgreSQL, migration, storage, background pipeline, provider adapter với mock server, export, ownership và audit.

### E2E

Login → course → class → assignment → upload → analyze → poll → report → export → retry → cross-user negative case.

## 3. Fixture tối thiểu

| Fixture | Mục tiêu |
|---|---|
| PDF text layer | Happy path |
| DOCX | Happy path |
| PDF scan | Lỗi OCR chưa hỗ trợ |
| PDF encrypted/malformed | Validation |
| Không references | Detection failure |
| APA/IEEE | Parser/style |
| Thiếu năm | C1/C5 |
| DOI đúng | C2 |
| DOI conflict | Conflict |
| URL sống phi học thuật | C2/C3 |
| Provider timeout | Degrade |
| Duplicate DOI | C7 |
| Fuzzy duplicate | C7 |
| Tiếng Việt/Anh | Relevance |
| File >20 MB | Limit |
| MIME spoof | Security |

## 4. Acceptance flow P0

```text
Register/Login
→ Course
→ Class
→ Assignment
→ Upload PDF/DOCX
→ Analyze
→ Poll real progress
→ Real C1-C7 report
→ Export PDF/DOCX/XLSX
```

Flow chỉ pass khi không mock, dữ liệu lưu DB, có scoring version, ownership/audit đúng, export mở được và retry dùng pipeline thật.

## 5. Regression quan trọng

### Score invariance

- Tổng weight 100.
- Config invalid fallback an toàn.
- Boundary 49.99/50/69.99/70/84.99/85.
- Confidence không làm thay score ngoài thiết kế.
- Provider unavailable không thành “fake”.

### Ownership

Lecturer B không được truy cập class, assignment, submission, job, report và export của lecturer A.

### Retry

Job thất bại → sửa nguyên nhân → retry tạo job mới → pipeline thật → report → giữ lineage.

### Provider degradation

Primary timeout → fallback → evidence ghi rõ → confidence giảm → không kết luận tuyệt đối.

## 6. Frontend gate

Lệnh hiện có:

```bash
npm run lint
npm run build
```

Cần bổ sung component/service tests, route guard test, contract test, accessibility và browser E2E. Không fallback mock ngoài mock mode.

## 7. Backend gate đề xuất

Tách `requirements.txt` và `requirements-dev.txt`; bổ sung pytest, async/cov, API test client, lint/format/type-check. Tool cụ thể là quyết định developer guide, không phải SRS.

## 8. CI gate

1. Secret scan.
2. Backend lint/type/unit.
3. Migration up.
4. Integration.
5. Frontend install/lint/build.
6. E2E smoke.
7. Dependency audit.
8. Docs link/markdown check.
9. Artifact report.

## 9. Performance

Đo theo file size, pages, citations, provider latency, embedding provider, concurrent jobs và export format. Không đặt SLA trước benchmark; báo cáo phải nêu hardware, dataset và percentile.

## 10. Risk

| Risk | Mức | Kiểm soát |
|---|---|---|
| Placeholder retry | Critical | Unified pipeline + E2E |
| Mock fallback | High | Fail loudly |
| No durable queue | High | Worker queue |
| Parser variability | High | Fixture corpus |
| Provider instability | High | Stub/fallback/circuit breaker |
| Score bias | High | Human benchmark |
| Missing integration | High | CI gate |
| Default secret | Critical | Fail-fast |
| PDF scan unsupported | Medium | UX/OCR roadmap |

## 11. Definition of Done

Requirement ID, code review, unit/integration/negative authorization tests, migration, API docs, loading/error UI, không mock production, audit/observability phù hợp và không còn high/critical security warning chưa xử lý.
