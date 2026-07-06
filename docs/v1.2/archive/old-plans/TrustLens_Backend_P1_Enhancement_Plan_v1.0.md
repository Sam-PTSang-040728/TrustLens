# TrustLens Backend P1 Enhancement Plan v1.0

> **Mục tiêu:** hoàn thiện các năng lực vận hành, quản trị, mở rộng và truy vết sau khi P0 đã bảo đảm pipeline end-to-end chạy thật.
>
> **Phạm vi P1:** retry nâng cao, batch processing, export DOCX/XLSX, audit log, scoring preset/versioning, metadata provider fallback, duplicate detection, confidence/explanation, soft delete/retention, observability cơ bản và test mở rộng.
>
> **Điều kiện tiên quyết:** P0 đã hoàn tất và đạt Definition of Done.
>
> **Đối tượng sử dụng:** Backend Developer, BA/AI-NLP Owner, Frontend Developer, QA, người phụ trách demo và vận hành.

---

# 1. Mục tiêu P1

P1 chuyển TrustLens từ một MVP chỉ chạy được thành một hệ thống có thể:

1. Xử lý nhiều bài nộp theo lô.
2. Retry có kiểm soát và truy vết được lịch sử.
3. Quản lý nhiều metadata provider có fallback.
4. Version hóa cấu hình Trust Score.
5. Xuất report ở PDF, DOCX và XLSX.
6. Lưu audit log cho các thao tác quan trọng.
7. Phát hiện citation trùng.
8. Giải thích rõ điểm số và mức confidence.
9. Quản lý vòng đời file, submission và report.
10. Hỗ trợ vận hành ổn định hơn trong staging/demo.

P1 không thay đổi mục tiêu học thuật cốt lõi của TrustLens. P1 tập trung vào khả năng vận hành, kiểm soát và mở rộng.

---

# 2. Kết quả bắt buộc sau khi hoàn tất P1

P1 chỉ được xem là hoàn tất khi đáp ứng đồng thời các điều kiện sau:

- [ ] Một assignment có thể xử lý nhiều submission theo batch.
- [ ] Mỗi submission có job riêng và không ảnh hưởng lẫn nhau.
- [ ] Retry tạo job mới nhưng giữ liên kết với job cũ.
- [ ] Có lịch sử retry.
- [ ] Có provider chính và provider fallback.
- [ ] Có cấu hình provider theo thứ tự ưu tiên.
- [ ] Có timeout, rate-limit handling và circuit breaker mức cơ bản.
- [ ] Có scoring preset được version hóa.
- [ ] Report cũ vẫn đọc đúng bằng version cũ.
- [ ] Export được PDF, DOCX và XLSX.
- [ ] Có audit log.
- [ ] Có soft delete.
- [ ] Có retention policy.
- [ ] Citation trùng được phát hiện và giải thích.
- [ ] Mỗi điểm thành phần có explanation.
- [ ] Confidence Score được tính và hiển thị độc lập.
- [ ] Có dashboard trạng thái batch.
- [ ] Có integration test cho batch, retry, export, provider fallback và soft delete.

---

# 3. Phạm vi chức năng P1

## 3.1. Trong phạm vi

| Nhóm | Nội dung |
|---|---|
| Batch processing | Xử lý nhiều submission trong assignment |
| Retry nâng cao | Retry theo job, retry metadata-only |
| Metadata provider management | Crossref, OpenAlex, Semantic Scholar hoặc nguồn mở tương đương |
| Provider fallback | Chuyển provider khi timeout, lỗi hoặc thiếu kết quả |
| Scoring preset | Tạo, version hóa và gắn preset với assignment |
| Audit log | Ghi nhận các thao tác quan trọng |
| Export mở rộng | PDF, DOCX, XLSX |
| Duplicate detection | DOI, title, URL, fuzzy match |
| Confidence | Citation confidence và report confidence |
| Explainability | Explanation và evidence chuẩn hóa |
| Soft delete | Submission, report, export |
| Retention | Quy tắc lưu/xóa file |
| Observability | Structured log, health, metrics cơ bản |
| QA mở rộng | Batch, retry, concurrency, provider fallback |

## 3.2. Ngoài phạm vi

- OCR nâng cao.
- Plagiarism detection.
- Citation-in-context alignment.
- Realtime WebSocket bắt buộc.
- Student portal hoàn chỉnh.
- Multi-tenant enterprise.
- Autoscaling production hoàn chỉnh.
- Predatory journal legal-grade classification.
- Full-text validation sau paywall.

---

# 4. Quan hệ với P0

P1 không được phá vỡ các contract đã khóa trong P0.

Các thành phần phải tiếp tục được giữ:

```text
POST /submissions/upload
POST /submissions/{id}/analyze
GET /jobs/{job_id}
GET /reports/{report_id}
POST /reports/{report_id}/exports
GET /report-exports/{id}/download
```

P1 chỉ mở rộng:

- Batch API.
- Retry API.
- Provider config API.
- Scoring preset API.
- Audit API.
- Soft delete API.
- Export format.
- Report explanation schema.
- Job history.

---

# 5. Batch processing

## 5.1. Mục tiêu

Cho phép giảng viên:

1. Upload nhiều file.
2. Tạo nhiều submission.
3. Khởi chạy phân tích theo batch.
4. Theo dõi trạng thái từng submission.
5. Không để lỗi một file làm dừng cả batch.
6. Xem tổng quan batch.

---

## 5.2. Data model `analysis_batches`

```text
id UUID PK
assignment_id UUID FK
created_by UUID FK users
status VARCHAR
total_items INT
queued_items INT
running_items INT
completed_items INT
failed_items INT
created_at TIMESTAMP
started_at TIMESTAMP NULL
completed_at TIMESTAMP NULL
```

Trạng thái:

```python
class BatchStatus(str, Enum):
    CREATED = "created"
    QUEUED = "queued"
    RUNNING = "running"
    PARTIAL_SUCCESS = "partial_success"
    COMPLETED = "completed"
    FAILED = "failed"
    CANCELLED = "cancelled"
```

---

## 5.3. Data model `analysis_batch_items`

```text
id UUID PK
batch_id UUID FK
submission_id UUID FK
job_id UUID FK NULL
status VARCHAR
ordinal INT
error_code VARCHAR NULL
created_at TIMESTAMP
updated_at TIMESTAMP
```

Constraint:

```text
UNIQUE(batch_id, submission_id)
```

---

## 5.4. Batch API

### Tạo batch

```http
POST /api/v1/analysis-batches
```

Request:

```json
{
  "assignment_id": "uuid",
  "submission_ids": [
    "uuid-1",
    "uuid-2",
    "uuid-3"
  ]
}
```

Response:

```json
{
  "batch_id": "uuid",
  "status": "created",
  "total_items": 3,
  "items": [
    {
      "submission_id": "uuid-1",
      "status": "created"
    },
    {
      "submission_id": "uuid-2",
      "status": "created"
    },
    {
      "submission_id": "uuid-3",
      "status": "created"
    }
  ]
}
```

---

### Khởi chạy batch

```http
POST /api/v1/analysis-batches/{batch_id}/start
```

Response:

```json
{
  "batch_id": "uuid",
  "status": "queued",
  "total_items": 3,
  "queued_items": 3
}
```

---

### Xem trạng thái batch

```http
GET /api/v1/analysis-batches/{batch_id}
```

Response:

```json
{
  "batch_id": "uuid",
  "assignment_id": "uuid",
  "status": "running",
  "summary": {
    "total": 20,
    "queued": 5,
    "running": 3,
    "completed": 10,
    "failed": 2
  },
  "items": [
    {
      "submission_id": "uuid",
      "job_id": "uuid",
      "status": "completed",
      "progress": 100,
      "report_id": "uuid",
      "error": null
    }
  ]
}
```

---

## 5.5. Quy tắc batch

- Không tạo hai job active cho cùng submission.
- Một item lỗi không làm batch dừng.
- Batch `PARTIAL_SUCCESS` khi có cả completed và failed.
- Batch chỉ `FAILED` khi toàn bộ item thất bại.
- Giới hạn batch mặc định: `20` file.
- Có thể cấu hình giới hạn qua `.env`.
- Không enqueue toàn bộ không giới hạn.
- Có concurrency limit cho worker.
- Mỗi item phải lưu job riêng.
- Có thể retry item riêng.

---

# 6. Retry nâng cao

## 6.1. Các loại retry

```python
class RetryMode(str, Enum):
    FULL = "full"
    METADATA_ONLY = "metadata_only"
    SCORING_ONLY = "scoring_only"
    REPORT_ONLY = "report_only"
```

### FULL

Chạy lại toàn bộ pipeline.

### METADATA_ONLY

Dùng extraction và citation đã lưu, chỉ chạy lại metadata verification trở đi.

### SCORING_ONLY

Dùng citation và metadata đã lưu, chạy lại scoring theo preset/version mới.

### REPORT_ONLY

Chỉ tái sinh report/export.

---

## 6.2. API retry

```http
POST /api/v1/jobs/{job_id}/retry
```

Request:

```json
{
  "mode": "metadata_only",
  "reason": "Provider Crossref timeout trong lần chạy trước."
}
```

Response:

```json
{
  "job_id": "new-job-id",
  "retry_of_job_id": "old-job-id",
  "retry_mode": "metadata_only",
  "status": "queued"
}
```

---

## 6.3. Data model bổ sung cho job

```text
retry_of_job_id UUID NULL
retry_mode VARCHAR NULL
retry_reason TEXT NULL
attempt_number INT DEFAULT 1
```

---

## 6.4. Retry policy

| Lỗi | Retry mode đề xuất |
|---|---|
| Provider timeout | METADATA_ONLY |
| Provider rate limit | METADATA_ONLY |
| Temporary network failure | METADATA_ONLY |
| Worker crash | FULL |
| Scoring config mới | SCORING_ONLY |
| Template export lỗi | REPORT_ONLY |
| File corrupted | Không retry |
| PDF encrypted | Không retry |
| No text layer | Không retry nếu chưa OCR |
| No reference section | Không retry tự động |

---

## 6.5. Giới hạn retry

- Tối đa 3 lần tự động.
- Tối đa 5 lần thủ công.
- Phải có backoff.
- Không retry loop vô hạn.
- Ghi audit log.
- Giữ toàn bộ lịch sử.
- Không overwrite job cũ.

---

# 7. Metadata provider management

## 7.1. Provider interface

```python
class MetadataProvider(Protocol):
    name: str

    async def lookup_by_doi(self, doi: str) -> ProviderResult:
        ...

    async def search_by_title(
        self,
        title: str,
        authors: list[str] | None,
        year: int | None,
    ) -> list[ProviderCandidate]:
        ...

    async def health_check(self) -> ProviderHealth:
        ...
```

---

## 7.2. Provider được hỗ trợ

Mức tối thiểu:

1. Crossref.
2. OpenAlex.

Có thể bổ sung:

3. Semantic Scholar.
4. DataCite.
5. URL chính thức.

Không nên phụ thuộc một provider duy nhất.

---

## 7.3. Data model `metadata_providers`

```text
id UUID PK
code VARCHAR UNIQUE
name VARCHAR
base_url VARCHAR
enabled BOOLEAN
priority INT
timeout_seconds INT
max_retries INT
rate_limit_per_minute INT NULL
api_key_secret_name VARCHAR NULL
created_at TIMESTAMP
updated_at TIMESTAMP
```

Không lưu API key plain text trong database.

---

## 7.4. Provider status

```python
class ProviderStatus(str, Enum):
    HEALTHY = "healthy"
    DEGRADED = "degraded"
    UNAVAILABLE = "unavailable"
    RATE_LIMITED = "rate_limited"
    DISABLED = "disabled"
```

---

## 7.5. Fallback algorithm

```text
For each citation:

1. Nếu có DOI:
   - thử provider ưu tiên cao nhất hỗ trợ DOI
   - nếu verified → dừng
   - nếu not_found → thử provider tiếp theo
   - nếu timeout/rate-limited → đánh dấu provider error, thử provider tiếp theo

2. Nếu không có DOI hoặc DOI lookup thất bại:
   - search theo title + author + year
   - ranking candidate
   - chọn candidate nếu confidence >= threshold
   - nếu nhiều candidate gần nhau → ambiguous

3. Nếu tất cả provider lỗi:
   - match_status = unknown
   - provider_status = unavailable
   - confidence giảm
   - không kết luận citation giả
```

---

## 7.6. Circuit breaker mức cơ bản

Mỗi provider có:

```text
failure_count
last_failure_at
circuit_state
cooldown_until
```

State:

```python
class CircuitState(str, Enum):
    CLOSED = "closed"
    OPEN = "open"
    HALF_OPEN = "half_open"
```

Quy tắc mẫu:

- 5 lỗi liên tiếp → OPEN.
- Cooldown 60 giây.
- Sau cooldown → HALF_OPEN.
- Request thử thành công → CLOSED.
- Request thử thất bại → OPEN lại.

---

## 7.7. Provider metrics

Theo dõi:

- Request count.
- Success count.
- Timeout count.
- Rate-limit count.
- Average latency.
- Match success rate.
- Fallback count.

---

# 8. Scoring preset và versioning

## 8.1. Mục tiêu

Cho phép:

- Admin tạo scoring preset.
- Gắn preset vào assignment.
- Report lưu version đã dùng.
- Không làm thay đổi report cũ.
- Có thể chạy lại scoring với version mới.

---

## 8.2. Data model `scoring_presets`

```text
id UUID PK
code VARCHAR
name VARCHAR
description TEXT
version INT
status VARCHAR
weights JSONB
thresholds JSONB
rules JSONB
created_by UUID FK users
created_at TIMESTAMP
activated_at TIMESTAMP NULL
retired_at TIMESTAMP NULL
```

Constraint:

```text
UNIQUE(code, version)
```

---

## 8.3. Trạng thái preset

```python
class ScoringPresetStatus(str, Enum):
    DRAFT = "draft"
    ACTIVE = "active"
    RETIRED = "retired"
```

---

## 8.4. Ví dụ preset

```json
{
  "code": "IT_GENERAL",
  "name": "CNTT tổng quát",
  "version": 1,
  "weights": {
    "c1": 10,
    "c2": 20,
    "c3": 20,
    "c4": 20,
    "c5": 10,
    "c6": 10,
    "c7": 5,
    "c8": 5
  },
  "thresholds": {
    "verified_min_confidence": 0.85,
    "partial_min_confidence": 0.65,
    "relevance_high": 0.75,
    "relevance_medium": 0.55,
    "outdated_years": 5
  },
  "rules": {
    "hard_flag_penalty": 10,
    "duplicate_penalty": 2,
    "unknown_metadata_penalty": 0
  }
}
```

---

## 8.5. API preset

### Tạo preset

```http
POST /api/v1/admin/scoring-presets
```

### Kích hoạt version

```http
POST /api/v1/admin/scoring-presets/{preset_id}/activate
```

### Gán preset cho assignment

```http
PUT /api/v1/assignments/{assignment_id}/scoring-preset
```

Request:

```json
{
  "scoring_preset_id": "uuid"
}
```

---

## 8.6. Quy tắc version

- Preset ACTIVE không chỉnh trực tiếp.
- Muốn thay đổi phải tạo version mới.
- Report lưu `preset_id`, `preset_code`, `preset_version`.
- Scoring-only retry có thể chọn version mới.
- Không overwrite điểm cũ nếu re-score.
- Có thể tạo report revision.

---

# 9. Report revision

## 9.1. Data model

```text
reports
id UUID PK
submission_id UUID FK
revision_number INT
job_id UUID FK
scoring_preset_id UUID FK
scoring_preset_version INT
report_trust_score NUMERIC
confidence_score NUMERIC
status VARCHAR
supersedes_report_id UUID NULL
created_at TIMESTAMP
```

Constraint:

```text
UNIQUE(submission_id, revision_number)
```

---

## 9.2. Quy tắc

- Report đầu tiên: revision 1.
- Re-score: tạo revision 2.
- Report cũ không xóa.
- Frontend mặc định hiển thị revision mới nhất.
- Người dùng có thể xem lịch sử.

---

# 10. Duplicate citation detection

## 10.1. Các cấp độ duplicate

```python
class DuplicateType(str, Enum):
    EXACT_DOI = "exact_doi"
    EXACT_URL = "exact_url"
    EXACT_NORMALIZED_TITLE = "exact_normalized_title"
    FUZZY_TITLE = "fuzzy_title"
    POSSIBLE_DUPLICATE = "possible_duplicate"
```

---

## 10.2. Thuật toán

Ưu tiên:

```text
1. DOI normalized giống nhau
2. URL canonical giống nhau
3. Normalized title giống nhau
4. Title similarity >= threshold
5. Kết hợp title + year + first author
```

---

## 10.3. Data model

Bổ sung cho `citations`:

```text
duplicate_of UUID NULL
duplicate_type VARCHAR NULL
duplicate_confidence NUMERIC NULL
```

---

## 10.4. Quy tắc chấm điểm

- Exact DOI duplicate: cảnh báo chắc chắn.
- Fuzzy title duplicate: chỉ cảnh báo cần kiểm tra.
- Không tự xóa citation.
- Không phạt hai lần nếu nhiều rule cùng phát hiện.
- Report phải giải thích cặp duplicate.

---

## 10.5. Response

```json
{
  "duplicate": {
    "is_duplicate": true,
    "duplicate_of": "citation-uuid",
    "type": "exact_doi",
    "confidence": 1.0,
    "explanation": "Hai citation có cùng DOI sau khi chuẩn hóa."
  }
}
```

---

# 11. Confidence Score

## 11.1. Nguyên tắc

Confidence Score không phản ánh chất lượng nguồn. Nó phản ánh độ chắc chắn của hệ thống đối với kết quả phân tích.

Ví dụ:

- Nguồn có Trust Score thấp nhưng confidence cao.
- Nguồn có Trust Score cao nhưng confidence thấp.
- Không gộp hai giá trị thành một.

---

## 11.2. Citation Confidence

Đầu vào:

| Thành phần | Ví dụ trọng số |
|---|---:|
| Parse confidence | 20% |
| Metadata match confidence | 35% |
| Provider reliability | 15% |
| Relevance evidence coverage | 15% |
| Citation style detection confidence | 10% |
| Completeness coverage | 5% |

Công thức:

```text
Citation_Confidence =
    weighted_average(
        parse_confidence,
        metadata_confidence,
        provider_reliability,
        relevance_coverage,
        style_confidence,
        completeness_coverage
    )
```

---

## 11.3. Report Confidence

```text
Report_Confidence =
    weighted_average(Citation_Confidence_i)
    - uncertainty_penalty
```

Uncertainty penalty dựa trên:

- Tỷ lệ unknown.
- Tỷ lệ ambiguous.
- Provider outage.
- Citation parse confidence thấp.
- Reference section detection confidence thấp.

---

## 11.4. Nhãn confidence

| Khoảng | Nhãn |
|---:|---|
| 0.85–1.00 | high |
| 0.65–0.84 | medium |
| 0.00–0.64 | low |

---

# 12. Explainability schema

Mỗi điểm phải có:

```json
{
  "score": 16.0,
  "max_score": 20.0,
  "reason_code": "METADATA_VERIFIED_PARTIAL",
  "explanation": "Tiêu đề và tác giả khớp, nhưng năm xuất bản lệch 1 năm.",
  "evidence": {
    "title_similarity": 0.96,
    "author_match": true,
    "year_match": false
  },
  "recommendation": "Kiểm tra lại năm xuất bản trong citation."
}
```

---

## 12.1. Chuẩn reason code

Ví dụ:

```text
METADATA_COMPLETE
METADATA_MISSING_AUTHOR
METADATA_VERIFIED_EXACT_DOI
METADATA_VERIFIED_PARTIAL
SOURCE_JOURNAL_RECOGNIZED
SOURCE_PERSONAL_BLOG
RELEVANCE_HIGH
RELEVANCE_LOW
RECENCY_ACCEPTABLE
RECENCY_OUTDATED
STYLE_MATCH
STYLE_MISMATCH
DUPLICATE_EXACT_DOI
ACADEMIC_RISK_HIGH
```

---

## 12.2. Nguyên tắc ngôn ngữ

Không dùng:

```text
"Nguồn giả"
"Nguồn sai hoàn toàn"
"Tài liệu không hợp lệ tuyệt đối"
```

Ưu tiên:

```text
"Không xác minh được qua provider hiện tại"
"Cần kiểm tra thủ công"
"Confidence thấp"
"Có dấu hiệu không phù hợp"
```

---

# 13. Export DOCX

## 13.1. Nội dung

DOCX gồm:

1. Trang tiêu đề.
2. Thông tin assignment.
3. Tóm tắt Trust Score.
4. Confidence.
5. C1–C8.
6. Bảng citation.
7. Warning.
8. Recommendation.
9. Ghi chú giảng viên.
10. Disclaimer.
11. Scoring version.

---

## 13.2. Công nghệ

```text
python-docx
```

---

## 13.3. API

Dùng endpoint đã có:

```http
POST /api/v1/reports/{report_id}/exports
```

Request:

```json
{
  "format": "docx",
  "include_teacher_notes": true
}
```

---

## 13.4. Tiêu chí nghiệm thu

- Mở được bằng Microsoft Word.
- Không lỗi font.
- Bảng không tràn trang nghiêm trọng.
- Unicode tiếng Việt đúng.
- Dữ liệu khớp dashboard.
- Có revision và version.

---

# 14. Export XLSX

## 14.1. Workbook structure

```text
Sheet 1: Report Summary
Sheet 2: Citation Details
Sheet 3: Warnings
Sheet 4: Score Components
Sheet 5: Metadata Evidence
```

---

## 14.2. Công nghệ

```text
openpyxl
```

---

## 14.3. Cột đề xuất

### Citation Details

```text
Citation ID
Raw Citation
Title
Authors
Year
DOI
URL
Venue
Provider
Match Status
Match Confidence
C1
C2
C3
C4
C5
C6
C7
C8
Reference Trust Score
Confidence Score
Warnings
Recommendations
```

---

## 14.4. Tiêu chí nghiệm thu

- Có header rõ ràng.
- Không mất tiếng Việt.
- Không formula injection.
- Dữ liệu nhất quán với API.
- Có freeze panes.
- Có filter.
- Không dùng merged cell quá mức.
- Có sheet metadata/config.

---

# 15. Audit log

## 15.1. Sự kiện bắt buộc

```text
AUTH_LOGIN_SUCCESS
AUTH_LOGIN_FAILED
AUTH_LOGOUT
USER_CREATED
CLASS_CREATED
CLASS_UPDATED
ASSIGNMENT_CREATED
SUBMISSION_UPLOADED
ANALYSIS_STARTED
ANALYSIS_RETRIED
ANALYSIS_COMPLETED
ANALYSIS_FAILED
REPORT_VIEWED
REPORT_EXPORTED
SCORING_PRESET_CREATED
SCORING_PRESET_ACTIVATED
PROVIDER_CONFIG_CHANGED
SUBMISSION_DELETED
SUBMISSION_RESTORED
FILE_PURGED
FORBIDDEN_ACCESS_ATTEMPT
```

---

## 15.2. Data model `audit_logs`

```text
id UUID PK
actor_user_id UUID NULL
action VARCHAR
resource_type VARCHAR
resource_id UUID NULL
request_id UUID
ip_address VARCHAR NULL
user_agent TEXT NULL
metadata JSONB
created_at TIMESTAMP
```

---

## 15.3. Dữ liệu không được log

- Password.
- Access token.
- Refresh token.
- API key.
- Full file content.
- Raw binary.
- Secret environment value.

---

## 15.4. API audit

Admin only:

```http
GET /api/v1/admin/audit-logs
```

Filter:

```text
actor_user_id
action
resource_type
resource_id
from
to
page
page_size
```

---

# 16. Soft delete

## 16.1. Entity áp dụng

- Submission.
- File metadata.
- Report.
- Export.
- Assignment nếu không còn ràng buộc active.
- Class nếu không còn dữ liệu active.

---

## 16.2. Trường

```text
deleted_at TIMESTAMP NULL
deleted_by UUID NULL
delete_reason TEXT NULL
```

---

## 16.3. Quy tắc

- Query mặc định không trả bản ghi đã xóa.
- Admin có thể xem trash.
- Lecturer chỉ restore dữ liệu thuộc quyền.
- File vật lý chưa xóa ngay.
- Purge theo retention policy.
- Audit mọi thao tác delete/restore/purge.

---

## 16.4. API

```http
DELETE /api/v1/submissions/{submission_id}
```

Response:

```json
{
  "submission_id": "uuid",
  "status": "deleted",
  "deleted_at": "2026-06-19T10:00:00Z"
}
```

Restore:

```http
POST /api/v1/submissions/{submission_id}/restore
```

---

# 17. Retention policy

## 17.1. Mục tiêu

Quản lý file và dữ liệu theo thời gian, tránh giữ vô hạn.

---

## 17.2. Policy mẫu

| Dữ liệu | Thời gian |
|---|---:|
| File active | Theo assignment |
| File soft-deleted | 30 ngày |
| Export file | 30 ngày |
| Audit log | 180 ngày |
| Job logs chi tiết | 90 ngày |
| Provider response raw rút gọn | 90 ngày |
| Report metadata | Lâu dài hoặc theo chính sách khoa |

---

## 17.3. Purge task

Celery beat hoặc cron:

```text
purge_expired_exports
purge_soft_deleted_files
archive_old_audit_logs
cleanup_orphan_temp_files
```

---

## 17.4. Quy tắc purge

- Không purge report active.
- Không purge file đang có job active.
- Không purge nếu legal hold/manual lock.
- Ghi audit log.
- Xóa file vật lý và cập nhật database.
- Kiểm tra orphan file.

---

# 18. Observability cơ bản

## 18.1. Structured logging

Log JSON:

```json
{
  "timestamp": "2026-06-19T10:00:00Z",
  "level": "INFO",
  "request_id": "uuid",
  "job_id": "uuid",
  "submission_id": "uuid",
  "event": "metadata_lookup_completed",
  "provider": "crossref",
  "latency_ms": 820,
  "status": "success"
}
```

---

## 18.2. Health endpoints

```http
GET /health/live
GET /health/ready
GET /health/providers
```

### Live

Ứng dụng còn chạy.

### Ready

Database, Redis và worker khả dụng.

### Providers

Tình trạng metadata provider.

---

## 18.3. Metrics tối thiểu

```text
http_request_total
http_request_duration_seconds
analysis_job_total
analysis_job_duration_seconds
analysis_job_failed_total
metadata_provider_request_total
metadata_provider_latency_seconds
metadata_provider_failure_total
batch_total
batch_failed_items_total
export_total
```

Có thể dùng Prometheus-compatible endpoint:

```http
GET /metrics
```

---

# 19. Frontend thay đổi P1

## 19.1. Batch UI

- Chọn nhiều submission.
- Tạo batch.
- Xem progress tổng.
- Xem trạng thái từng item.
- Retry item lỗi.

---

## 19.2. Report revision UI

- Hiển thị revision hiện tại.
- Cho phép xem revision cũ.
- Hiển thị scoring preset/version.
- Cho phép re-score nếu có quyền.

---

## 19.3. Export UI

- PDF.
- DOCX.
- XLSX.
- Tùy chọn ghi chú giảng viên.

---

## 19.4. Explanation UI

Mỗi score component có:

- Score.
- Max score.
- Explanation.
- Evidence.
- Recommendation.
- Confidence.

---

## 19.5. Provider status UI

Admin:

- Provider enabled/disabled.
- Priority.
- Health.
- Last failure.
- Latency.
- Fallback count.

---

# 20. API contract bổ sung P1

## 20.1. Batch

```text
POST   /analysis-batches
POST   /analysis-batches/{id}/start
GET    /analysis-batches/{id}
POST   /analysis-batches/{id}/cancel
POST   /analysis-batches/{id}/retry-failed
```

## 20.2. Retry

```text
POST   /jobs/{id}/retry
GET    /jobs/{id}/history
```

## 20.3. Scoring preset

```text
GET    /admin/scoring-presets
POST   /admin/scoring-presets
GET    /admin/scoring-presets/{id}
POST   /admin/scoring-presets/{id}/activate
POST   /admin/scoring-presets/{id}/retire
PUT    /assignments/{id}/scoring-preset
```

## 20.4. Metadata provider

```text
GET    /admin/metadata-providers
POST   /admin/metadata-providers
PUT    /admin/metadata-providers/{id}
POST   /admin/metadata-providers/{id}/enable
POST   /admin/metadata-providers/{id}/disable
GET    /admin/metadata-providers/{id}/health
```

## 20.5. Audit

```text
GET    /admin/audit-logs
```

## 20.6. Soft delete

```text
DELETE /submissions/{id}
POST   /submissions/{id}/restore
GET    /admin/trash/submissions
```

---

# 21. Security bổ sung P1

## 21.1. Rate limiting

Áp dụng:

| Endpoint | Giới hạn gợi ý |
|---|---:|
| Login | 5 request/phút/IP |
| Upload | 20 request/phút/user |
| Analyze | 20 request/phút/user |
| Export | 30 request/phút/user |
| Provider admin API | 10 request/phút/admin |

---

## 21.2. Formula injection trong XLSX

Nếu cell bắt đầu bằng:

```text
=
+
-
@
```

Thì phải escape hoặc lưu dạng text.

---

## 21.3. SSRF

Nếu backend xử lý URL citation:

- Chặn localhost.
- Chặn private IP.
- Chặn file scheme.
- Chặn redirect không kiểm soát.
- Chỉ cho HTTP/HTTPS.
- Có timeout.

---

## 21.4. Audit integrity

- Không cho lecturer sửa audit log.
- Không cho API xóa audit log tùy ý.
- Có retention policy rõ ràng.
- Có request_id.

---

# 22. Test strategy P1

## 22.1. Batch tests

- Batch 5 file.
- Batch 20 file.
- Một file lỗi.
- Nhiều file lỗi.
- Cancel batch.
- Retry failed items.
- Không tạo duplicate job.

---

## 22.2. Retry tests

- Metadata-only retry.
- Scoring-only retry.
- Report-only retry.
- Retry vượt giới hạn.
- Retry job non-retryable.
- Retry giữ lịch sử.

---

## 22.3. Provider tests

- Provider chính success.
- Provider chính timeout, fallback success.
- Tất cả provider fail.
- Rate limit.
- Circuit breaker open.
- Circuit breaker half-open.
- Candidate ambiguous.

---

## 22.4. Preset tests

- Tạo preset draft.
- Activate preset.
- Không sửa preset active trực tiếp.
- Tạo version mới.
- Assignment gắn preset.
- Report lưu đúng version.
- Re-score tạo revision mới.

---

## 22.5. Export tests

- PDF.
- DOCX.
- XLSX.
- Unicode tiếng Việt.
- Formula injection.
- Ownership download.
- Expired export.

---

## 22.6. Soft delete tests

- Delete.
- Query không thấy record.
- Restore.
- Purge sau retention.
- Không purge job active.
- Audit log được tạo.

---

# 23. Acceptance test matrix P1

| ID | Kiểm thử | Kết quả |
|---|---|---|
| P1-AT-01 | Tạo batch 10 file | Batch được tạo |
| P1-AT-02 | Start batch | Có job riêng cho từng item |
| P1-AT-03 | Một item lỗi | Batch partial success |
| P1-AT-04 | Retry item lỗi | Tạo job mới |
| P1-AT-05 | Retry metadata-only | Không extraction lại |
| P1-AT-06 | Provider chính timeout | Fallback provider hoạt động |
| P1-AT-07 | Tất cả provider lỗi | Match status unknown |
| P1-AT-08 | Tạo preset mới | Preset draft |
| P1-AT-09 | Activate preset | Version active |
| P1-AT-10 | Re-score | Tạo report revision mới |
| P1-AT-11 | Citation duplicate | Có duplicate type và confidence |
| P1-AT-12 | Explanation | Mỗi component có reason/evidence |
| P1-AT-13 | Export DOCX | Mở được, dữ liệu đúng |
| P1-AT-14 | Export XLSX | Sheet đúng, không formula injection |
| P1-AT-15 | Audit log | Ghi đúng action |
| P1-AT-16 | Soft delete | Record ẩn khỏi query |
| P1-AT-17 | Restore | Record hoạt động lại |
| P1-AT-18 | Purge | File hết hạn bị xóa |
| P1-AT-19 | Health ready | Kiểm tra DB/Redis/worker |
| P1-AT-20 | Batch 20 file | Không timeout HTTP |

---

# 24. Cấu trúc service đề xuất

```text
app/
├── services/
│   ├── batch_service.py
│   ├── retry_service.py
│   ├── scoring/
│   │   ├── config.py
│   │   ├── preset_service.py
│   │   ├── confidence.py
│   │   └── explanation.py
│   ├── metadata/
│   │   ├── provider_registry.py
│   │   ├── fallback_resolver.py
│   │   ├── circuit_breaker.py
│   │   └── metrics.py
│   ├── duplicate_detection.py
│   ├── audit_service.py
│   ├── retention_service.py
│   └── export/
│       ├── pdf_exporter.py
│       ├── docx_exporter.py
│       └── xlsx_exporter.py
├── tasks/
│   ├── analysis.py
│   ├── batch.py
│   ├── export.py
│   └── retention.py
```

---

# 25. Skeleton batch implementation

```python
class BatchService:
    def create_batch(
        self,
        *,
        assignment_id: UUID,
        submission_ids: list[UUID],
        current_user: User,
    ) -> AnalysisBatch:
        self._validate_assignment_access(
            assignment_id=assignment_id,
            current_user=current_user,
        )

        if len(submission_ids) == 0:
            raise ValidationError("BATCH_EMPTY")

        if len(submission_ids) > settings.MAX_BATCH_SIZE:
            raise ValidationError("BATCH_TOO_LARGE")

        submissions = self._load_submissions(submission_ids)

        for submission in submissions:
            if submission.assignment_id != assignment_id:
                raise ValidationError("BATCH_ASSIGNMENT_MISMATCH")

        batch = self.batch_repository.create(
            assignment_id=assignment_id,
            created_by=current_user.id,
            total_items=len(submissions),
        )

        self.batch_item_repository.create_many(
            batch_id=batch.id,
            submissions=submissions,
        )

        self.audit.log(
            actor=current_user,
            action="ANALYSIS_BATCH_CREATED",
            resource_type="analysis_batch",
            resource_id=batch.id,
        )

        return batch
```

---

# 26. Skeleton fallback resolver

```python
class MetadataFallbackResolver:
    def __init__(
        self,
        providers: list[MetadataProvider],
        circuit_breaker: CircuitBreaker,
    ):
        self.providers = sorted(
            providers,
            key=lambda provider: provider.priority,
        )
        self.circuit_breaker = circuit_breaker

    async def verify(
        self,
        citation: NormalizedCitation,
    ) -> VerificationResult:
        attempts: list[ProviderAttempt] = []

        for provider in self.providers:
            if not provider.enabled:
                continue

            if self.circuit_breaker.is_open(provider.name):
                attempts.append(
                    ProviderAttempt(
                        provider=provider.name,
                        status="circuit_open",
                    )
                )
                continue

            try:
                result = await self._query_provider(
                    provider=provider,
                    citation=citation,
                )

                attempts.append(
                    ProviderAttempt(
                        provider=provider.name,
                        status="success",
                    )
                )

                if result.match_status == "verified":
                    return VerificationResult(
                        result=result,
                        attempts=attempts,
                    )

                if result.match_status in {"partial", "ambiguous"}:
                    return VerificationResult(
                        result=result,
                        attempts=attempts,
                    )

            except ProviderRateLimitError:
                self.circuit_breaker.record_failure(provider.name)
                attempts.append(
                    ProviderAttempt(
                        provider=provider.name,
                        status="rate_limited",
                    )
                )

            except ProviderTimeoutError:
                self.circuit_breaker.record_failure(provider.name)
                attempts.append(
                    ProviderAttempt(
                        provider=provider.name,
                        status="timeout",
                    )
                )

        return VerificationResult.unknown(attempts=attempts)
```

---

# 27. Skeleton scoring preset validation

```python
def validate_scoring_preset(payload: ScoringPresetPayload) -> None:
    weights = payload.weights

    expected = {
        "c1": 10,
        "c2": 20,
        "c3": 20,
        "c4": 20,
        "c5": 10,
        "c6": 10,
        "c7": 5,
        "c8": 5,
    }

    if set(weights.keys()) != set(expected.keys()):
        raise ValidationError("INVALID_SCORING_COMPONENTS")

    if sum(weights.values()) != 100:
        raise ValidationError("INVALID_TOTAL_WEIGHT")

    for key, maximum in expected.items():
        if weights[key] < 0:
            raise ValidationError("NEGATIVE_WEIGHT")
```

---

# 28. Migration plan

## Migration 1

```text
analysis_batches
analysis_batch_items
```

## Migration 2

```text
metadata_providers
provider_health_snapshots
```

## Migration 3

```text
scoring_presets
assignment.scoring_preset_id
report revision columns
```

## Migration 4

```text
audit_logs
```

## Migration 5

```text
soft delete columns
retention columns
```

## Migration 6

```text
duplicate detection columns
confidence explanation columns
```

---

# 29. Commit plan

### Commit 1

```text
docs: define p1 batch retry provider and preset contracts
```

### Commit 2

```text
feat(batch): add analysis batch orchestration
```

### Commit 3

```text
feat(retry): support staged retry and job history
```

### Commit 4

```text
feat(metadata): add provider registry fallback and circuit breaker
```

### Commit 5

```text
feat(scoring): add versioned scoring presets and report revisions
```

### Commit 6

```text
feat(citation): detect duplicates with evidence
```

### Commit 7

```text
feat(explainability): add confidence and component explanations
```

### Commit 8

```text
feat(export): add docx and xlsx report exports
```

### Commit 9

```text
feat(audit): add immutable audit logging
```

### Commit 10

```text
feat(retention): add soft delete restore and purge tasks
```

### Commit 11

```text
feat(observability): add structured logs health and metrics
```

### Commit 12

```text
test(p1): cover batch retry provider fallback and retention
```

---

# 30. Phân công đề xuất

| Hạng mục | Owner | Reviewer |
|---|---|---|
| Batch API | BE | FE |
| Retry flow | BE | QA |
| Provider registry | BE + AI/NLP | BA |
| Scoring preset | AI/NLP + BE | BA |
| Duplicate detection | AI/NLP | BE |
| Confidence | AI/NLP | BA |
| Explainability | BA + AI/NLP | FE |
| DOCX/XLSX export | BE | QA |
| Audit log | BE | QA |
| Soft delete | BE | BA |
| Retention | BE | QA |
| Batch UI | FE | BE |
| Integration test | BE + QA | BA |

---

# 31. Rủi ro P1

## 31.1. Provider rate limit

**Rủi ro:** batch lớn gây rate limit.

**Giảm thiểu:**

- Queue theo provider.
- Cache metadata.
- Exponential backoff.
- Rate-limit aware scheduling.
- Fallback provider.

---

## 31.2. Re-score làm mất tính truy vết

**Rủi ro:** điểm thay đổi nhưng report cũ bị overwrite.

**Giảm thiểu:**

- Report revision.
- Preset version.
- Không update trực tiếp report cũ.

---

## 31.3. Batch overload

**Rủi ro:** enqueue quá nhiều task.

**Giảm thiểu:**

- Max batch size.
- Worker concurrency limit.
- Queue priority.
- Backpressure.

---

## 31.4. Export lớn

**Rủi ro:** XLSX hoặc DOCX lớn.

**Giảm thiểu:**

- Export chạy async.
- Giới hạn số citation.
- Streaming download.
- Retention file export.

---

## 31.5. Audit log tăng nhanh

**Rủi ro:** database phình.

**Giảm thiểu:**

- Index hợp lý.
- Retention.
- Archive.
- Không log payload lớn.

---

# 32. Performance targets P1

| Tác vụ | Mục tiêu |
|---|---:|
| Tạo batch 20 item | <= 3 giây |
| Analyze API | <= 5 giây |
| Job status API | <= 500 ms |
| Report API | <= 1 giây |
| Audit query | <= 2 giây |
| Export DOCX 40 citation | <= 30 giây |
| Export XLSX 40 citation | <= 20 giây |
| Provider fallback | Không tăng quá 2 lần timeout tổng |
| Batch 20 file | Không block HTTP |

---

# 33. Definition of Done P1

## Batch

- [ ] Có batch entity.
- [ ] Có batch item.
- [ ] Có start batch.
- [ ] Có status batch.
- [ ] Có retry failed.
- [ ] Có partial success.
- [ ] Có giới hạn batch.

## Retry

- [ ] FULL.
- [ ] METADATA_ONLY.
- [ ] SCORING_ONLY.
- [ ] REPORT_ONLY.
- [ ] Có history.
- [ ] Có attempt limit.

## Provider

- [ ] Ít nhất 2 provider.
- [ ] Có fallback.
- [ ] Có timeout.
- [ ] Có rate-limit handling.
- [ ] Có health.
- [ ] Có circuit breaker cơ bản.

## Scoring preset

- [ ] Có draft.
- [ ] Có active.
- [ ] Có retired.
- [ ] Có version.
- [ ] Assignment gắn preset.
- [ ] Report lưu preset version.
- [ ] Re-score tạo revision.

## Duplicate

- [ ] DOI.
- [ ] URL.
- [ ] Title.
- [ ] Fuzzy.
- [ ] Có evidence.

## Confidence

- [ ] Citation confidence.
- [ ] Report confidence.
- [ ] Không trộn với Trust Score.
- [ ] Có label high/medium/low.

## Explainability

- [ ] Reason code.
- [ ] Explanation.
- [ ] Evidence.
- [ ] Recommendation.
- [ ] Không dùng kết luận tuyệt đối.

## Export

- [ ] PDF.
- [ ] DOCX.
- [ ] XLSX.
- [ ] Ownership check.
- [ ] Retention.
- [ ] Unicode đúng.
- [ ] Chống formula injection.

## Audit

- [ ] Ghi login.
- [ ] Ghi upload.
- [ ] Ghi analyze.
- [ ] Ghi retry.
- [ ] Ghi export.
- [ ] Ghi config change.
- [ ] Ghi delete/restore.
- [ ] Không log secret.

## Soft delete và retention

- [ ] Delete.
- [ ] Restore.
- [ ] Purge.
- [ ] Audit.
- [ ] Không purge job active.

## Observability

- [ ] Structured log.
- [ ] Request ID.
- [ ] Job ID.
- [ ] Health live.
- [ ] Health ready.
- [ ] Provider health.
- [ ] Metrics.

## QA

- [ ] Batch test.
- [ ] Retry test.
- [ ] Provider fallback test.
- [ ] Preset version test.
- [ ] Duplicate test.
- [ ] DOCX export test.
- [ ] XLSX export test.
- [ ] Soft delete test.
- [ ] Retention test.
- [ ] Audit test.

---

# 34. Thứ tự triển khai ưu tiên

Nếu thời gian giới hạn:

1. Provider fallback.
2. Scoring preset version.
3. Batch processing.
4. Retry staged.
5. Report revision.
6. Duplicate detection.
7. Confidence và explanation.
8. DOCX export.
9. XLSX export.
10. Audit log.
11. Soft delete.
12. Retention.
13. Metrics.

---

# 35. Tiêu chí chuyển sang P2

Chỉ chuyển P2 khi:

- P0 ổn định.
- P1 integration test pass.
- Batch 20 file chạy thành công.
- Provider fallback hoạt động.
- Không mất report cũ khi re-score.
- Export DOCX/XLSX hoạt động.
- Audit và retention chạy đúng.
- Không còn mock trong staging.
- Không có lỗi ownership.
- Demo nội bộ chạy lặp lại thành công.

---

# 36. Kết luận

P1 không nhằm thêm nhiều màn hình. P1 nhằm biến TrustLens thành hệ thống có:

```text
Khả năng xử lý theo lô
+ Khả năng retry
+ Khả năng fallback
+ Khả năng version hóa
+ Khả năng giải thích
+ Khả năng truy vết
+ Khả năng quản lý vòng đời dữ liệu
```

Sau P1, backend phải không chỉ trả được kết quả, mà còn trả lời được:

- Kết quả được tính theo preset nào?
- Provider nào đã được dùng?
- Vì sao provider fallback?
- Vì sao citation có điểm này?
- Confidence là bao nhiêu?
- Job đã retry bao nhiêu lần?
- Report nào là revision mới nhất?
- Ai đã upload, analyze, export hoặc delete?
- File và report được giữ đến khi nào?
