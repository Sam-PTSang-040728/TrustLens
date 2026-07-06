# 13. Known Gaps và Quyết định Kiến trúc

## P0 remediation status - 2026-07-06

Remediated in implementation:

- GAP-001: analyze, process alias and retry now use the canonical analysis pipeline.
- GAP-002: frontend mock fallback is explicit-only through `VITE_USE_MOCK=true`; admin user mutations no longer fake success after backend failure.
- GAP-003: backend `SECRET_KEY` has no source default and validates length/sample values with environment-aware fail-fast rules.
- GAP-007: backend exception handlers normalize validation, HTTP and internal failures to a top-level error schema with `correlation_id`.

Still open after P0 code remediation:

- GAP-004: `BackgroundTasks` is still not a durable distributed queue.
- GAP-005: PostgreSQL integration/security suites and browser E2E are not present yet.
- GAP-008 to GAP-020 remain as previously listed unless separately closed.
## 1. ADR đã khóa

### ADR-001 — C1–C7 v1.2 là baseline

Frontend/backend cùng định nghĩa C1–C7, tổng 100. Docs C1–C8 phải archive hoặc cập nhật.

### ADR-002 — Analyze là canonical

```text
POST /api/v1/submissions/{submission_id}/analyze
```

Process/retry phải gọi cùng pipeline trước production.

### ADR-003 — Modular monolith trước distributed services

Giữ FastAPI modular monolith; tách worker theo workload sau khi ổn định. Rủi ro hiện tại là consistency/test/ownership, không phải thiếu microservices.

### ADR-004 — Mock chỉ explicit

Mock chỉ hoạt động khi `VITE_USE_MOCK=true`; backend error phải fail thật.

### ADR-005 — Evidence và confidence bắt buộc

Mỗi component score có reason/evidence/confidence.

### ADR-006 — Provider không phải oracle

Not-found/provider failure không đồng nghĩa fake.

### ADR-007 — Scoring change phải version

Weight, threshold, model hoặc logic có tác động phải tăng version.

## 2. Gap register

| ID | Mức | Gap | Tác động | Hành động |
|---|---|---|---|---|
| GAP-001 | Critical | Retry/process placeholder | Fake completion | Remediated: unified canonical pipeline |
| GAP-002 | Critical | Mock fallback | Fake success | Remediated: explicit mock mode only |
| GAP-003 | Critical | Default secret | Token compromise | Remediated: startup fail-fast validator |
| GAP-004 | High | Không durable queue | Mất job | Worker queue |
| GAP-005 | High | Chưa xác nhận integration/E2E | Không đủ nghiệm thu | CI integration |
| GAP-006 | High | C1–C8 docs vs C1–C7 code | Báo cáo sai | Migrate |
| GAP-007 | High | Client/server error schema drift | UI may parse errors incorrectly | Remediated: global error handler |
| GAP-008 | High | CORS rộng | Origin ngoài ý muốn | Allow-list |
| GAP-009 | High | Password/rate-limit yếu | Account attack | Auth hardening |
| GAP-010 | High | localStorage token | XSS impact | CSP/cookie strategy |
| GAP-011 | High | Không malware scan | Upload risk | Quarantine/scan |
| GAP-012 | High | Retention chưa khóa | Privacy/storage | Policy + purge |
| GAP-013 | Medium | OCR chưa có | PDF scan fail | UX/OCR |
| GAP-014 | Medium | List API không pagination | Scale | Pagination |
| GAP-015 | Medium | Stage endpoints rộng | Contract surface | Internalize |
| GAP-016 | Medium | Model skeleton chưa chắc dùng | Complexity | Entity audit |
| GAP-017 | Medium | Preset frontend chưa chắc backend áp dụng | Config drift | Backend source |
| GAP-018 | Medium | Chưa benchmark | Validity unknown | Evaluation corpus |
| GAP-019 | Medium | Disclaimer report chưa đủ | Misinterpretation | UI/report warning |
| GAP-020 | Low | Một số lỗi encoding/không dấu | UX | Normalize i18n |

## 3. Sai lệch docs cũ

- `archive/reviews/SRS_Standardization_Review.md` là review SRS, không phải product docs; đã phân tách vào SRS/architecture/test.
- P0 cũ dùng C1–C8 và Celery/Redis như baseline; không còn đúng hiện trạng.
- P1 có một số nội dung đã partial như export, audit, duplicate/confidence; batch/retention/observability vẫn thiếu.
- P2 giữ làm roadmap; OCR/LMS/multi-tenant/MLOps không được mô tả implemented.

## 4. Open questions

| ID | Câu hỏi | Owner đề xuất |
|---|---|---|
| OQ-001 | Aggregate/penalty chính thức? | BA/NLP |
| OQ-002 | C1–C7 có thay vĩnh viễn C1–C8? | Product + BA |
| OQ-003 | Student xem evidence đến mức nào? | Product |
| OQ-004 | Retention bao lâu? | Product + Privacy |
| OQ-005 | Provider nào được nhận text? | Security/Privacy |
| OQ-006 | Queue production chọn gì? | Backend/DevOps |
| OQ-007 | Multi-tenant là yêu cầu thật hay xa? | Product |
| OQ-008 | Weight tùy chỉnh theo assignment đến đâu? | BA/NLP |
| OQ-009 | Report history immutable hay mutable? | Backend/BA |
| OQ-010 | Ai gán nhãn benchmark? | Academic owner |

## 5. Architectural consistency

Hệ thống nhất quán khi có một API contract, một state machine, một pipeline, một scoring definition versioned, một permission vocabulary, một error schema, một mock policy, một ownership model và docs khớp code.
