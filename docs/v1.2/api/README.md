# 08. API Reference

## P0 remediation update - 2026-07-06

The current v1.2 implementation supersedes older notes in this file where they conflict:

- `POST /api/v1/submissions/{submission_id}/analyze` is the canonical orchestration endpoint.
- `POST /api/v1/jobs/submissions/{submission_id}/process` is a backward-compatible alias that runs the same canonical analysis pipeline.
- `POST /api/v1/jobs/{job_id}/retry` creates a new lineage-linked job only when the target job is terminal, then runs the same canonical analysis pipeline.
- At most one active job may exist per submission. Active statuses are `QUEUED`, `VALIDATING`, `EXTRACTING`, `DETECTING_REFERENCES`, `PARSING_CITATIONS`, `NORMALIZING`, `VERIFYING_METADATA`, `SCORING`, and `BUILDING_REPORT`.
- Latest job lookup uses `created_at desc limit 1`; it must not raise `MultipleResultsFound`.
- Job status values are normalized to lower-case at the API boundary. A completed job must include `report_id`.
- Frontend report navigation uses `/report/{report_id}`. It must not fall back from `report_id` to `submission_id`.
- Reports can be read by `GET /api/v1/reports/{report_id}` or by the legacy submission route `GET /api/v1/reports/submissions/{submission_id}`.
- Error responses are top-level objects: `{ "error_code": "...", "message": "...", "details": {}, "retryable": false, "correlation_id": "..." }`.
- Mock data is available only when `VITE_USE_MOCK=true`; production builds fail if that flag is true.

## 1. Base URL

```text
http://localhost:8000/api/v1
```

Swagger development: `http://localhost:8000/docs`.

## 2. Auth

```http
Authorization: Bearer <access_token>
```

Public endpoints:

- `GET /health`
- `POST /auth/register`
- `POST /auth/login`
- `POST /auth/refresh`

## 3. Role và permission

| Role | Phạm vi |
|---|---|
| `ADMIN` | Toàn bộ lecturer permission + admin |
| `LECTURER` | Course/class/assignment/upload/analyze/report/export |
| `STUDENT` | Hiện chỉ `auth.login` |

Permission:

```text
auth.login
course.manage
assignment.manage
submission.upload
job.analyze
report.view_own_scope
report.export
admin.user_manage
admin.scoring_config
admin.audit_log
admin.metadata_provider
```

## 4. Error schema

Backend dùng object:

```json
{
  "error_code": "ERROR_CODE",
  "message": "Human-readable message",
  "details": {}
}
```

Do FastAPI `HTTPException(detail={...})`, payload có thể nằm dưới `detail`. Client và backend cần chuẩn hóa global error contract.

## 5. Endpoint inventory

### Health/Auth/User

| Method | Path | Auth/permission | Mô tả |
|---|---|---|---|
| GET | `/health` | Public | Health |
| POST | `/auth/register` | Public | Register |
| POST | `/auth/login` | Public | Login |
| POST | `/auth/refresh` | Refresh token | Access token mới |
| GET | `/users/me` | Authenticated | Current profile |
| PATCH | `/users/me` | Authenticated | Update profile |

### Courses

| Method | Path | Permission | Mô tả |
|---|---|---|---|
| POST | `/courses` | `course.manage` | Create |
| GET | `/courses` | `course.manage` | List |

### Classes

| Method | Path | Permission | Mô tả |
|---|---|---|---|
| POST | `/classes` | `course.manage` | Create |
| GET | `/classes` | `course.manage` | List by scope |
| PUT | `/classes/{class_identifier}` | `course.manage` | Update |
| DELETE | `/classes/{class_identifier}` | `course.manage` | Delete |
| GET | `/classes/{class_identifier}/submissions` | `course.manage` | Submission list |

### Assignments

| Method | Path | Permission | Mô tả |
|---|---|---|---|
| POST | `/assignments` | `assignment.manage` | Create |
| GET | `/assignments` | `assignment.manage` | List/filter class |
| PATCH | `/assignments/{assignment_id}` | `assignment.manage` | Update |

### Submissions

| Method | Path | Permission | Mô tả | Ghi chú |
|---|---|---|---|---|
| POST | `/submissions/upload` | `submission.upload` | Upload | Canonical |
| POST | `/submissions/{id}/analyze` | `job.analyze` | Real pipeline | Canonical |
| DELETE | `/submissions/{id}` | `submission.upload` | Soft delete | Audit |
| POST | `/submissions/{id}/detect-references` | `job.analyze` | Detect stage | Internal/debug |
| POST | `/submissions/{id}/parse-citations` | `job.analyze` | Parse stage | Internal/debug |
| POST | `/submissions/{id}/verify-metadata` | `job.analyze` | Verify stage | Internal/debug |

### Jobs

| Method | Path | Permission | Mô tả | Trạng thái |
|---|---|---|---|---|
| GET | `/jobs/{job_id}` | `report.view_own_scope` | Status | Implemented |
| GET | `/jobs/submissions/{submission_id}/latest` | `report.view_own_scope` | Latest job | Implemented |
| POST | `/jobs/{job_id}/retry` | `job.analyze` | Retry terminal job | Canonical pipeline |
| POST | `/jobs/submissions/{submission_id}/process` | `job.analyze` | Backward-compatible process alias | Canonical pipeline |

### Reports/exports

| Method | Path | Permission | Mô tả |
|---|---|---|---|
| GET | `/reports/{report_id}` | `report.view_own_scope` | Read report by report id |
| GET | `/reports/submissions/{submission_id}` | `report.view_own_scope` | Read report |
| POST | `/reports/submissions/{submission_id}/generate` | `job.analyze` | Read/generate theo service |
| GET | `/reports/submissions/{id}/export/pdf` | `report.export` | Export PDF |
| GET | `/reports/submissions/{id}/export/docx` | `report.export` | Export DOCX |
| GET | `/reports/submissions/{id}/export/xlsx` | `report.export` | Export XLSX |
| GET | `/reports/{report_id}/history` | `report.view_own_scope` | History |
| GET | `/report-exports/{export_id}/download` | `report.export` | Download export |

### Dashboard

| Method | Path | Permission | Mô tả |
|---|---|---|---|
| GET | `/dashboard/summary` | `report.view_own_scope` | Summary |
| GET | `/dashboard/recent-activities` | `report.view_own_scope` | Activities |
| GET | `/dashboard/weekly-trend` | `report.view_own_scope` | Trend |

### Admin

| Method | Path | Permission | Mô tả |
|---|---|---|---|
| GET | `/admin/system/ai-health` | `admin.metadata_provider` | AI health |
| POST | `/admin/relevance/diagnose` | `admin.metadata_provider` | Diagnose C4 |
| GET | `/admin/audit-logs` | `admin.audit_log` | Audit |
| GET | `/admin/metadata-providers` | `admin.metadata_provider` | Provider list |
| PUT | `/admin/metadata-providers/{id}` | `admin.metadata_provider` | Update provider |
| GET | `/admin/users` | `admin.user_manage` | User list |
| POST | `/admin/users` | `admin.user_manage` | Create user |
| PUT | `/admin/users/{id}` | `admin.user_manage` | Update user |
| DELETE | `/admin/users/{id}` | `admin.user_manage` | Deactivate user |

## 6. Upload example

```http
POST /api/v1/submissions/upload
Content-Type: multipart/form-data
Authorization: Bearer <token>
```

Fields: `assignment_id`, `owner_label` (optional), `file`.

Response tối thiểu:

```json
{
  "submission_id": "uuid",
  "file_id": "uuid",
  "job_id": "uuid",
  "status": "UPLOADED"
}
```

## 7. Analyze/poll

```http
POST /api/v1/submissions/{submission_id}/analyze
```

```json
{
  "job_id": "uuid",
  "submission_id": "uuid",
  "status": "QUEUED",
  "progress": 0
}
```

Client poll `GET /jobs/{job_id}`, dừng ở completed/failed/cancelled, không retry vô hạn và hiển thị lỗi có cấu trúc.

## 8. Consistency issues

1. Analyze và process không gọi cùng pipeline.
2. Retry chưa dùng pipeline thật.
3. Error wrapping chưa đồng nhất client/server.
4. Nhiều list API chưa pagination.
5. Chưa có idempotency key public.
6. Permission scoring config tồn tại nhưng router quản lý config chưa được xác nhận.

## 9. Versioning

Breaking change phải tạo `/api/v2` hoặc migration window, cập nhật frontend, OpenAPI, docs, compatibility test và changelog.
