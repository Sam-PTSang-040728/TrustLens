# 07. Mô hình Dữ liệu Logic

## 1. Mục tiêu

Mô hình phải truy vết được:

```text
Ai → lớp/assignment nào → file/submission/job nào
→ citation/metadata nào → scoring version nào
→ report/export nào
```

## 2. Nhóm thực thể

### Identity

- `User`
- `UserProfile`
- `RolePermission`
- `AuditLog`

### Academic

- `Course`
- `ClassModel`
- `Assignment`
- `Term`
- `Student`

`Student` và `Term` có model nhưng mức sử dụng trong endpoint baseline cần xác nhận thêm.

### Submission

- `File`
- `Submission`
- `ProcessingJob`
- `ExtractedDocument`
- `ReferenceSection`

### Citation/metadata

- `Citation`
- `CitationField`
- `MetadataProvider`
- `MetadataRecord`

### Scoring/report

- `ScoreComponent`
- `TrustScore`
- `CitationScore`
- `Warning`
- `ScoringConfig`
- `Report`
- `ReportExport`

## 3. Quan hệ logic

```mermaid
erDiagram
    USER ||--o| USER_PROFILE : has
    USER ||--o{ CLASS : owns
    USER ||--o{ FILE : uploads
    USER ||--o{ AUDIT_LOG : performs
    COURSE ||--o{ CLASS : contains
    CLASS ||--o{ ASSIGNMENT : contains
    ASSIGNMENT ||--o{ SUBMISSION : receives
    ASSIGNMENT }o--o| SCORING_CONFIG : uses
    FILE ||--|| SUBMISSION : backs
    SUBMISSION ||--o{ PROCESSING_JOB : processes
    SUBMISSION ||--o| EXTRACTED_DOCUMENT : produces
    SUBMISSION ||--o| REFERENCE_SECTION : contains
    SUBMISSION ||--o{ CITATION : contains
    SUBMISSION ||--o{ METADATA_RECORD : verifies
    SUBMISSION ||--o| REPORT : produces
    REFERENCE_SECTION ||--o{ CITATION : groups
    CITATION ||--o{ CITATION_FIELD : parses
    CITATION ||--o{ METADATA_RECORD : matches
    CITATION ||--o| CITATION_SCORE : scores
    CITATION ||--o{ WARNING : raises
    REPORT ||--o{ REPORT_EXPORT : exports
    REPORT }o--|| PROCESSING_JOB : generated_by
```

## 4. Quy tắc toàn vẹn

### Identity

- Email duy nhất sau chuẩn hóa.
- Role thuộc tập hỗ trợ.
- User inactive không lấy token mới.
- Permission backend là nguồn kiểm soát.

### Ownership học thuật

- Class có một `lecturer_id`.
- Assignment thuộc một class.
- Lecturer chỉ truy cập scope mình sở hữu.
- Admin có phạm vi toàn hệ thống hiện tại.
- Khi bổ sung tenant, mọi thực thể nghiệp vụ phải tenant-scoped và được test isolation.

### File

File phải có original name, stored name/path, MIME, size, checksum, uploader và timestamp. Path không nhận trực tiếp từ client; tên lưu không đoán được; phải đối soát record với file vật lý.

### Submission

- Thuộc assignment hợp lệ.
- File tồn tại.
- Xóa không để report/export mồ côi.
- Status phải đồng bộ job/report.

### ProcessingJob

- Thuộc một submission.
- Retry liên kết job cũ.
- Progress 0–100.
- Chỉ chuyển trạng thái hợp lệ.
- Completed phải có report hoặc lý do rõ nếu loại job không tạo report.
- Cần idempotency/unique rule cho active jobs.

### Citation

- `sequence_no` giữ thứ tự.
- Raw text được giữ để audit parser.
- DOI/URL chuẩn hóa nhưng không làm mất bản gốc.
- Reprocessing phải có replace/version strategy.

### Metadata

- Ghi provider, query, status, confidence, evidence.
- `PROVIDER_UNAVAILABLE` khác `NOT_FOUND`.
- Raw response cần size/retention policy.
- Không trộn record giữa submission.

### Scoring

- Config versioned.
- Tổng weight bằng 100.
- Report lưu scoring version.
- Warning có code/severity/recommendation.
- Không sửa ngược report lịch sử khi đổi thuật toán.

### Report/export

- Report thuộc submission và liên kết job.
- Export thuộc report.
- Download kiểm tra quyền ngược tới class owner.
- File export mất phải trả lỗi rõ và có thể tái tạo theo policy.

## 5. State machine đề xuất

| State | Ý nghĩa |
|---|---|
| `QUEUED` | Đã tạo job |
| `VALIDATING` | Kiểm tra file/context |
| `EXTRACTING` | Trích xuất text |
| `DETECTING_REFERENCES` | Xác định danh mục |
| `PARSING_CITATIONS` | Tách citation |
| `NORMALIZING` | Chuẩn hóa |
| `VERIFYING_METADATA` | Đối chiếu provider |
| `SCORING` | Tính điểm |
| `BUILDING_REPORT` | Tạo report |
| `COMPLETED` | Hoàn tất |
| `FAILED_*` | Lỗi theo stage |
| `CANCELLED` | Bị hủy |

Không nên duy trì đồng thời state rút gọn và chi tiết nếu không có mapping chuẩn.

## 6. Soft delete và retention

Một số entity có dấu hiệu soft delete. Cần chuẩn hóa:

- `deleted_at`, `deleted_by`.
- Query mặc định loại record đã xóa.
- Retention window.
- Restore policy.
- Purge file vật lý.
- Audit tối thiểu được giữ.

## 7. Dữ liệu nhạy cảm

| Dữ liệu | Mức | Kiểm soát |
|---|---|---|
| Password hash | Cao | Không return/log |
| Token | Cao | Redact, expiry, revoke |
| File report | Cao | Ownership, encryption, retention |
| Extracted text | Cao | Data minimization |
| Public metadata | Trung bình | Provenance/cache policy |
| Audit | Cao | Restricted access |
| AI request/evidence | Cao | Không raw log |
| Score/report | Trung bình-Cao | Ownership + explainability |

## 8. Khoảng trống

- Chưa tenant.
- Chưa batch entities trong implementation.
- Chưa durable queue metadata.
- Retention chưa khóa.
- Chưa xác nhận object storage abstraction.
- Chưa có consent/legal basis model.
- Chưa benchmark dataset/model registry.
- Một số model skeleton có thể chưa được endpoint sử dụng.
# P1 pilot-readiness update - 2026-07-06

New or expanded runtime fields:

- `refresh_tokens`: stores hashed refresh tokens, `jti`, expiry, revocation time/reason, and rotation lineage.
- `files.storage_state`: `quarantine`, `accepted`, or rejected states; analysis requires `accepted`.
- `files.scan_status`: `pending`, `clean`, `suspicious`, `infected`, or `failed`; analysis requires `clean`.
- `files.scan_provider`, `scan_signature_version`, `scanned_at`, `scan_details`: scan provenance and audit evidence.
- `processing_jobs.attempt_count`, `claimed_by`, `heartbeat_at`, `next_run_at`: database-backed queue claim/retry/recovery metadata.

Retention defaults for pilot configuration:

| Data | Default |
|---|---:|
| Source submission/file data | 180 days after soft delete |
| Export files | 14 days |
| Audit records | 365 days target; purge keeps audit separate from business data deletion |

The purge service removes expired export files and soft-deleted submission source files from physical storage when `dry_run=false`.
