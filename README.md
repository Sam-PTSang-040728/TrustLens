# TrustLens

**TrustLens** is an academic reference validation system designed to assess the reliability and relevance of reference lists in Information Technology reports.

## Project Title

**TrustLens - Hệ thống thẩm định độ tin cậy và phù hợp của danh mục tài liệu tham khảo trong báo cáo Công nghệ Thông tin**

## Project Description

TrustLens hỗ trợ giảng viên kiểm tra danh mục tài liệu tham khảo trong báo cáo của sinh viên Công nghệ Thông tin. Hệ thống tiếp nhận file báo cáo, trích xuất danh mục tài liệu tham khảo, kiểm tra độ tồn tại học thuật, đánh giá độ tin cậy, độ cập nhật và độ phù hợp của nguồn tài liệu, sau đó tạo điểm đánh giá tổng hợp gọi là **Trust Score**.

## Team Members

| STT | Họ và tên        | MSSV       | Lớp       | Vai trò                        |
| --- | ---------------- | ---------- | --------- | ------------------------------ |
| 1   | Phan Tấn Sang    | 2400008681 | 24DTH1D   | Backend Engineer               |
| 2   | Nguyễn Minh Trúc | 2400003987 | 24DTH2A   | BA / NLP / Academic Evaluation |
| 3   | Trần Quỳnh Như   | 2400008936 | 24DKTPM1A | Frontend Engineer              |

## Repositories

| Module        | Repository           |
| ------------- | -------------------- |
| Frontend      | `TrustLens-Frontend` |
| Backend       | `TrustLens-Backend`  |
| Documentation | `docs/`              |

## Core Features

1. Upload report files: PDF, DOCX
2. Extract reference list
3. Normalize citation format
4. Verify academic existence through metadata APIs
5. Evaluate source credibility
6. Evaluate recency
7. Evaluate relevance between report topic and reference content
8. Calculate Trust Score
9. Generate warnings and recommendations
10. Export evaluation results

## Technical Stack

| Layer           | Technology                                                |
| --------------- | --------------------------------------------------------- |
| Frontend        | React, Tailwind CSS                                       |
| Backend         | FastAPI, SQLAlchemy, Alembic, JWT Auth                    |
| Database        | PostgreSQL                                                |
| File Processing | PyMuPDF, python-docx, pdfplumber                          |
| Metadata APIs   | Crossref, OpenAlex, Semantic Scholar                      |
| NLP             | Sentence Transformers, Embedding Model, Cosine Similarity |
| Export          | PDF, DOCX, XLSX                                           |

## Repository Structure

```text
TrustLens/
├── docs/
├── frontend/
├── backend/
├── .github/
├── .gitmodules
└── README.md
```

## Development Branches

| Branch      | Purpose               |
| ----------- | --------------------- |
| `main`      | Stable version        |
| `develop`   | Development version   |
| `feature/*` | New features          |
| `fix/*`     | Bug fixes             |
| `docs/*`    | Documentation updates |
