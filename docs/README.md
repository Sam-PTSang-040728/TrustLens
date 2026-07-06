# TrustLens Documentation

**Trạng thái:** Baseline tài liệu viết lại theo hiện trạng mã nguồn  
**Ngày chốt nguồn:** 2026-07-01  
**Phạm vi:** Root repository và hai Git submodule frontend/backend

## 1. Mục đích

Bộ tài liệu này thay thế cấu trúc `docs/` cũ vốn trộn lẫn đặc tả yêu cầu, kế hoạch remediation, kiến trúc mục tiêu, API, kiểm thử và vận hành. Tài liệu mới phân tách rõ:

1. **As-is:** năng lực có bằng chứng trong mã nguồn.
2. **Partial:** có mã nguồn một phần nhưng chưa đủ điều kiện nghiệm thu.
3. **To-be:** định hướng hoặc kế hoạch, chưa được trình bày như chức năng đã triển khai.

## 2. Nguồn sự thật

| Thành phần | Repository | Snapshot dùng để biên soạn |
|---|---|---|
| Root | `Sam-PTSang-040728/TrustLens` | `c9ad1d3f7e46b7795e8aef86a5c526adfb34f013` |
| Backend | `Sam-PTSang-040728/TrustLens-Backend` | `bc0023ae37f28b3b1b833bc92c93c1bdb5f9c63a` |
| Frontend | `tranquynhnhu102025-hash/Frontend-TrustLens` | `0dded036f2632ece4401b6bc1b2c219050c56815` |

Khi tài liệu và mã nguồn khác nhau, mã nguồn tại snapshot trên được ưu tiên để mô tả hiện trạng. Kế hoạch P0/P1/P2 chỉ được dùng để xây dựng roadmap.

## 3. Danh mục tài liệu

| Tài liệu | Mục đích |
|---|---|
| [00-source-baseline.md](00-source-baseline.md) | Nguồn, phương pháp rà soát và giới hạn bằng chứng |
| [01-product-overview.md](01-product-overview.md) | Bài toán, giá trị, người dùng và phạm vi |
| [02-system-context-and-scope.md](02-system-context-and-scope.md) | Actor, ranh giới, tích hợp và luồng dữ liệu |
| [03-software-requirements-specification.md](03-software-requirements-specification.md) | Baseline FR/NFR |
| [04-use-cases-and-workflows.md](04-use-cases-and-workflows.md) | Use case và workflow |
| [05-trust-score-specification.md](05-trust-score-specification.md) | Trust Score v1.2 theo C1–C7 |
| [06-architecture.md](06-architecture.md) | Kiến trúc hiện tại và mục tiêu |
| [07-data-model.md](07-data-model.md) | Mô hình dữ liệu logic |
| [08-api-reference.md](08-api-reference.md) | API inventory theo router hiện hành |
| [09-security-and-privacy.md](09-security-and-privacy.md) | Bảo mật, quyền riêng tư và risk |
| [10-testing-and-quality.md](10-testing-and-quality.md) | Kiểm thử, quality gates và bằng chứng |
| [11-deployment-and-operations.md](11-deployment-and-operations.md) | Cài đặt, cấu hình và vận hành |
| [12-roadmap.md](12-roadmap.md) | Lộ trình phát triển |
| [13-known-gaps-and-decisions.md](13-known-gaps-and-decisions.md) | ADR, gap register và open questions |
| [14-contributing.md](14-contributing.md) | Quy ước đóng góp |
| [MIGRATION_NOTES.md](MIGRATION_NOTES.md) | Ánh xạ docs cũ sang docs mới |

## 4. Ký hiệu trạng thái

| Trạng thái | Ý nghĩa |
|---|---|
| `Implemented` | Có route/entry point, service/model và đường đi mã nguồn rõ ràng |
| `Partial` | Có một phần nhưng thiếu điều kiện nghiệm thu hoặc tồn tại luồng không nhất quán |
| `Planned` | Chỉ có trong kế hoạch |
| `Deferred` | Chủ động hoãn khỏi baseline |
| `Unknown` | Chưa đủ bằng chứng |

## 5. Baseline kỹ thuật

- Frontend: React 19, TypeScript, Vite 8, Tailwind CSS 4, React Router, Axios.
- Backend: Python 3.11+, FastAPI, SQLAlchemy 2, Alembic, PostgreSQL.
- File: PDF và DOCX có text layer.
- Metadata: Crossref, OpenAlex và kiểm tra DOI/URL.
- Relevance: embedding provider chính có thể là Gemini, fallback local.
- Scoring: `trust-score-v1.2`, bảy thành phần C1–C7, tổng trọng số 100.
- Background execution: FastAPI `BackgroundTasks`, chưa phải Celery/Redis.
- Export: PDF, DOCX, XLSX.
- Role: `ADMIN`, `LECTURER`, `STUDENT`.
- Data scope: ownership theo giảng viên/lớp học phần; chưa multi-tenant.

## 6. Quyết định đọc tài liệu quan trọng

1. `POST /api/v1/submissions/{submission_id}/analyze` là endpoint phân tích chính.
2. Trust Score thực tế là C1–C7, không phải C1–C8 như kế hoạch cũ.
3. Endpoint stage-specific chỉ dùng cho debug/kiểm thử nội bộ.
4. OCR, batch, realtime, LMS và multi-tenancy là nội dung tương lai.
5. Fallback sang mock khi backend lỗi không hợp lệ ở staging/production.
6. Không tuyên bố có integration test hoặc production readiness khi chưa có bằng chứng.

## 7. Quy tắc cập nhật

Mọi thay đổi ảnh hưởng API, schema, quyền, pipeline hoặc Trust Score phải cập nhật tài liệu trong cùng pull request. Một hạng mục chỉ được ghi `Implemented` khi có mã nguồn, migration nếu cần, kiểm thử phù hợp và bằng chứng chạy được.
