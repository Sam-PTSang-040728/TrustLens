# 03. Software Requirements Specification

## 0. Kiểm soát tài liệu

| Thuộc tính | Giá trị |
|---|---|
| Document ID | `TL-SRS-2026-07` |
| Phiên bản | `2.0-rewrite` |
| Trạng thái | Baseline theo mã nguồn |
| Hệ thống | TrustLens |
| Quy ước | SHALL-style, có ưu tiên và trạng thái |
| Tham chiếu | ISO/IEC/IEEE 29148 theo nguyên tắc information item; không tuyên bố chứng nhận |

## 1. Quy ước

- **Must:** bắt buộc cho baseline.
- **Should:** quan trọng nhưng có thể hoãn.
- **Could:** mở rộng.
- **Implemented:** có bằng chứng mã nguồn.
- **Partial:** chưa đồng nhất hoặc thiếu điều kiện nghiệm thu.
- **Planned:** chưa triển khai.

## 2. Yêu cầu chức năng

### 2.1. Xác thực và hồ sơ

| ID | Yêu cầu | Ưu tiên | Trạng thái |
|---|---|---:|---|
| FR-AUTH-001 | Hệ thống phải cho phép đăng ký bằng họ tên, email và mật khẩu hợp lệ. | Must | Implemented |
| FR-AUTH-002 | Hệ thống phải từ chối email đã tồn tại. | Must | Implemented |
| FR-AUTH-003 | Hệ thống phải cho phép đăng nhập và trả access/refresh token. | Must | Implemented |
| FR-AUTH-004 | Hệ thống phải cho phép refresh access token. | Must | Implemented |
| FR-AUTH-005 | Hệ thống phải từ chối user không hoạt động. | Must | Implemented |
| FR-AUTH-006 | Người dùng phải xem và cập nhật các trường hồ sơ được phép. | Must | Implemented |
| FR-AUTH-007 | Người dùng không được tự sửa role/permission. | Must | Implemented |
| FR-AUTH-008 | Hệ thống nên có logout/revocation phía server. | Should | Planned |
| FR-AUTH-009 | Hệ thống phải rate-limit đăng nhập thất bại. | Must | Planned |

### 2.2. Course, class và assignment

| ID | Yêu cầu | Ưu tiên | Trạng thái |
|---|---|---:|---|
| FR-COURSE-001 | Người có quyền phải tạo học phần với mã duy nhất. | Must | Implemented |
| FR-COURSE-002 | Người có quyền phải liệt kê học phần. | Must | Implemented |
| FR-CLASS-001 | Lecturer/admin phải tạo lớp thuộc học phần hợp lệ. | Must | Implemented |
| FR-CLASS-002 | Mã lớp phải duy nhất trong phạm vi hiện tại. | Must | Implemented |
| FR-CLASS-003 | Lecturer chỉ xem/sửa lớp mình sở hữu; admin xem toàn bộ. | Must | Implemented |
| FR-CLASS-004 | Người có quyền phải cập nhật lớp. | Must | Implemented |
| FR-CLASS-005 | Xóa lớp phải xử lý dữ liệu liên quan có kiểm soát. | Must | Implemented/Partial |
| FR-CLASS-006 | Người có quyền phải xem danh sách submission của lớp. | Must | Implemented |
| FR-ASG-001 | Lecturer/admin phải tạo assignment trong lớp thuộc quyền. | Must | Implemented |
| FR-ASG-002 | Tên assignment không được trùng trong cùng lớp. | Must | Implemented |
| FR-ASG-003 | Người có quyền phải liệt kê assignment theo lớp. | Must | Implemented |
| FR-ASG-004 | Người có quyền phải cập nhật assignment. | Must | Implemented |
| FR-ASG-005 | Assignment phải có citation style và trạng thái nhận bài. | Must | Implemented |

### 2.3. Upload và submission

| ID | Yêu cầu | Ưu tiên | Trạng thái |
|---|---|---:|---|
| FR-SUB-001 | Hệ thống phải nhận PDF/DOCX qua multipart. | Must | Implemented |
| FR-SUB-002 | Hệ thống phải kiểm tra extension, MIME, size và file rỗng. | Must | Implemented |
| FR-SUB-003 | Hệ thống phải từ chối upload nếu assignment không mở. | Must | Implemented |
| FR-SUB-004 | Hệ thống phải kiểm tra quyền trên assignment. | Must | Implemented |
| FR-SUB-005 | Hệ thống phải tạo File, Submission và Job nhất quán. | Must | Implemented |
| FR-SUB-006 | Hệ thống phải lưu checksum. | Must | Implemented |
| FR-SUB-007 | Tên lưu vật lý không được phụ thuộc trực tiếp tên gốc. | Must | Implemented |
| FR-SUB-008 | Người có quyền phải xóa mềm submission và ghi audit. | Must | Implemented/Partial |
| FR-SUB-009 | Hệ thống nên malware-scan file. | Should | Planned |
| FR-SUB-010 | Hệ thống có thể hỗ trợ PDF scan qua OCR. | Could | Planned |

### 2.4. Job và pipeline

| ID | Yêu cầu | Ưu tiên | Trạng thái |
|---|---|---:|---|
| FR-JOB-001 | Hệ thống phải có một endpoint orchestration phân tích. | Must | Implemented |
| FR-JOB-002 | Analyze phải trả `202 Accepted` cùng `job_id`. | Must | Implemented |
| FR-JOB-003 | Pipeline phải chạy ngoài response chính. | Must | Implemented bằng `BackgroundTasks` |
| FR-JOB-004 | Trạng thái/progress phải lưu thật vào DB. | Must | Implemented |
| FR-JOB-005 | User phải đọc được job thuộc phạm vi quyền. | Must | Implemented |
| FR-JOB-006 | Pipeline phải validate, extract, detect, parse, verify, score, build report. | Must | Implemented |
| FR-JOB-007 | Lỗi phải có code/message/details có kiểm soát. | Must | Implemented |
| FR-JOB-008 | Retry phải dùng pipeline chính và giữ lineage. | Must | Partial |
| FR-JOB-009 | Hệ thống phải ngăn active job trùng hoặc bảo đảm idempotency. | Must | Partial/Unknown |
| FR-JOB-010 | Production nên dùng durable queue/worker. | Should | Planned |
| FR-JOB-011 | Hệ thống có thể phát realtime progress. | Could | Planned |

### 2.5. Extraction và citation

| ID | Yêu cầu | Ưu tiên | Trạng thái |
|---|---|---:|---|
| FR-EXT-001 | Hệ thống phải trích xuất DOCX. | Must | Implemented |
| FR-EXT-002 | Hệ thống phải trích xuất PDF có text layer. | Must | Implemented |
| FR-EXT-003 | PDF không có text layer phải trả lỗi rõ trong baseline chưa OCR. | Must | Implemented |
| FR-REF-001 | Hệ thống phải phát hiện reference section. | Must | Implemented |
| FR-REF-002 | Hệ thống phải lưu heading, vị trí và raw text section. | Must | Implemented |
| FR-CIT-001 | Hệ thống phải tách citation theo thứ tự. | Must | Implemented |
| FR-CIT-002 | Hệ thống phải lưu author/title/year/DOI/URL/style khi nhận diện được. | Must | Implemented |
| FR-CIT-003 | Dữ liệu thiếu phải dẫn đến warning/confidence phù hợp, không kết luận tuyệt đối. | Must | Partial |

### 2.6. Metadata verification

| ID | Yêu cầu | Ưu tiên | Trạng thái |
|---|---|---:|---|
| FR-META-001 | Hệ thống phải query provider theo DOI hoặc metadata phù hợp. | Must | Implemented |
| FR-META-002 | Hệ thống phải lưu provider, query, candidate, status, confidence, evidence. | Must | Implemented |
| FR-META-003 | Hệ thống phải phân biệt verified/partial/ambiguous/not-found/url-only/provider-unavailable. | Must | Implemented |
| FR-META-004 | `NOT_FOUND` không được gắn tự động là fake. | Must | Implemented |
| FR-META-005 | URL sống không đủ chứng minh tính học thuật. | Must | Implemented |
| FR-META-006 | Hệ thống nên fallback provider có kiểm soát. | Should | Partial |
| FR-META-007 | Admin phải xem/cập nhật provider. | Should | Implemented |

### 2.7. Trust Score

| ID | Yêu cầu | Ưu tiên | Trạng thái |
|---|---|---:|---|
| FR-SCORE-001 | Baseline phải dùng Trust Score v1.2 C1–C7. | Must | Implemented |
| FR-SCORE-002 | Tổng trọng số phải bằng 100; config sai phải fallback/từ chối an toàn. | Must | Implemented |
| FR-SCORE-003 | Mỗi component phải có score, max, reason, evidence, confidence. | Must | Implemented |
| FR-SCORE-004 | Label phải theo threshold versioned. | Must | Implemented |
| FR-SCORE-005 | Confidence phải độc lập với Trust Score. | Must | Implemented |
| FR-SCORE-006 | Report phải lưu `scoring_config_version`. | Must | Implemented |
| FR-SCORE-007 | Phải phát hiện duplicate DOI/URL và fuzzy title-author-year. | Must | Implemented |
| FR-SCORE-008 | Report penalty phải tách khỏi component weight. | Must | Implemented/Partial |
| FR-SCORE-009 | Production phải có benchmark/calibration trước khi dùng cho quyết định chính thức. | Must | Planned |

### 2.8. Report và export

| ID | Yêu cầu | Ưu tiên | Trạng thái |
|---|---|---:|---|
| FR-REP-001 | Report phải liên kết submission và job. | Must | Implemented |
| FR-REP-002 | Report phải có score, label, confidence, component summary, citation payload và warning. | Must | Implemented |
| FR-REP-003 | User chỉ xem report thuộc phạm vi quyền. | Must | Implemented |
| FR-REP-004 | Hệ thống phải export PDF, DOCX, XLSX. | Must | Implemented |
| FR-REP-005 | Download export phải kiểm tra quyền. | Must | Implemented |
| FR-REP-006 | Hệ thống nên duy trì report history/revision. | Should | Implemented/Partial |
| FR-REP-007 | Report phải hiển thị giới hạn diễn giải. | Must | Partial |

### 2.9. Dashboard và admin

| ID | Yêu cầu | Ưu tiên | Trạng thái |
|---|---|---:|---|
| FR-DASH-001 | Lecturer/admin phải xem summary theo scope. | Should | Implemented |
| FR-DASH-002 | Hệ thống phải cung cấp recent activities. | Should | Implemented |
| FR-DASH-003 | Hệ thống phải cung cấp weekly trend. | Should | Implemented |
| FR-ADM-001 | Admin phải xem/tạo/cập nhật/vô hiệu hóa user. | Must | Implemented |
| FR-ADM-002 | Admin phải xem audit log. | Must | Implemented |
| FR-ADM-003 | Admin phải xem AI health và diagnose relevance. | Should | Implemented |
| FR-ADM-004 | Admin phải quản lý scoring config qua backend. | Should | Partial |

## 3. Yêu cầu phi chức năng

### 3.1. Bảo mật

- `NFR-SEC-001`: mọi endpoint nghiệp vụ phải xác thực.
- `NFR-SEC-002`: authorization và ownership phải kiểm tra ở backend.
- `NFR-SEC-003`: mật khẩu phải băm; không log/return dạng rõ.
- `NFR-SEC-004`: secret production không được dùng default hoặc commit.
- `NFR-SEC-005`: upload phải chống path traversal, MIME spoofing và oversize.
- `NFR-SEC-006`: auth/API nhạy cảm phải rate-limit.
- `NFR-SEC-007`: CORS production phải dùng allow-list cụ thể.
- `NFR-SEC-008`: audit phải ghi login, upload, analyze, delete và admin changes.

### 3.2. Hiệu năng

- `NFR-PERF-001`: endpoint tạo job không được chờ toàn bộ pipeline.
- `NFR-PERF-002`: list lớn phải pagination.
- `NFR-PERF-003`: progress phải cập nhật theo stage.
- `NFR-PERF-004`: provider call phải timeout/retry giới hạn.
- `NFR-PERF-005`: production phải đo duration theo trang/citation/provider.

Không đặt SLA khi chưa có benchmark.

### 3.3. Độ tin cậy

- `NFR-REL-001`: provider lỗi không được tự động làm mất submission nếu có thể degrade.
- `NFR-REL-002`: job phải có trạng thái cuối xác định.
- `NFR-REL-003`: retry tạo job mới và giữ lịch sử.
- `NFR-REL-004`: production phải khôi phục job sau worker restart.
- `NFR-REL-005`: backup/restore file và report phải được test.

### 3.4. Khả dụng

- `NFR-USA-001`: UI phải có loading/progress/success/error.
- `NFR-USA-002`: lỗi phải hành động được.
- `NFR-USA-003`: report tách score/confidence/evidence/warning.
- `NFR-USA-004`: accessibility ở mức WCAG phù hợp.
- `NFR-USA-005`: không mock fallback ở staging/production.

### 3.5. Bảo trì và quan sát

- `NFR-MNT-001`: API prefix `/api/v1`.
- `NFR-MNT-002`: Alembic migration.
- `NFR-MNT-003`: frontend/backend dùng cùng permission và scoring contract.
- `NFR-MNT-004`: log nên có correlation ID.
- `NFR-MNT-005`: health endpoint bắt buộc.
- `NFR-MNT-006`: thay đổi API/schema/scoring phải cập nhật docs.

### 3.6. Privacy và AI

- `NFR-PRI-001`: chỉ gửi dữ liệu tối thiểu tới provider.
- `NFR-PRI-002`: không log raw report text mặc định.
- `NFR-PRI-003`: phải truy vết provider/model/prompt version.
- `NFR-PRI-004`: evidence provenance phải khả dụng.
- `NFR-PRI-005`: production phải có retention policy.

## 4. Ràng buộc

- FastAPI, SQLAlchemy, Alembic, PostgreSQL.
- React/TypeScript/Vite.
- PDF/DOCX baseline.
- API `/api/v1`.
- Scoring `trust-score-v1.2`.
- Lecturer-scoped, chưa tenant-scoped.
- `BackgroundTasks` là hiện trạng, không phải kiến trúc production cam kết.

## 5. Release gate

Một release chỉ được ghi `Ready` khi migration chạy được, frontend build/lint pass, unit/integration/E2E cần thiết pass, ownership negative tests pass, không mock fallback, không default secret và docs khớp implementation.
