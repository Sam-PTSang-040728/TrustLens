# API Reference

**Application version:** `1.2.0`
**API version:** `v1`
**Base path:** `/api/v1`
**Status:** source-aligned reference

Development base URL:

```text
http://localhost:8000/api/v1
```

Swagger in local development:

```text
http://localhost:8000/docs
```

## 1. Authentication

Business endpoints use:

```http
Authorization: Bearer <access_token>
```

Public endpoints:

```text
GET  /health
GET  /health/ready
GET  /health/metrics
POST /auth/register
POST /auth/login
POST /auth/refresh
```

Auth endpoints:

| Method | Path | Status | Notes |
|---|---|---|---|
| POST | `/auth/register` | Implemented | Public register currently creates active lecturers; role safety is `Blocked`. |
| POST | `/auth/login` | Implemented | Issues access and refresh tokens. |
| POST | `/auth/refresh` | Implemented | Rotates refresh token. |
| POST | `/auth/logout` | Implemented | Revokes refresh token. |

Auth rate limiting is implemented in process memory. Multi-replica deployments need
a shared limiter before production sign-off.

## 2. Official Error Schema

Canonical response shape:

```json
{
  "error_code": "ERROR_CODE",
  "message": "Human-readable message",
  "details": {},
  "retryable": false,
  "correlation_id": "string"
}
```

**Status:** `Partial`

The backend has exception handlers and `x-correlation-id` middleware, but full
endpoint-by-endpoint contract coverage still requires integration/contract tests.

## 3. Pagination

Paginated list responses use:

```json
{
  "items": [],
  "page": 1,
  "page_size": 20,
  "total": 0,
  "has_next": false
}
```

Primary list APIs are expected to follow this contract. Coverage should be verified
by API contract tests before marking pagination `Verified`.

## 4. Endpoint Inventory

### Health

| Method | Path | Auth | Description |
|---|---|---|---|
| GET | `/health` | Public | Process health and version. |
| GET | `/health/ready` | Public | Readiness for database and queue. |
| GET | `/health/metrics` | Public | Queue status counts and oldest queued age. |

### Users

| Method | Path | Auth/permission | Description |
|---|---|---|---|
| GET | `/users/me` | Authenticated | Current user profile. |
| PATCH | `/users/me` | Authenticated | Update allowed profile fields. |

### Courses

| Method | Path | Permission | Description |
|---|---|---|---|
| POST | `/courses` | `course.manage` | Create course. |
| GET | `/courses` | `course.manage` | List courses by authorized scope. |

### Classes

| Method | Path | Permission | Description |
|---|---|---|---|
| POST | `/classes` | `course.manage` | Create class. |
| GET | `/classes` | `course.manage` | List classes by scope. |
| PUT | `/classes/{class_identifier}` | `course.manage` | Update class. |
| DELETE | `/classes/{class_identifier}` | `course.manage` | Delete class. |
| GET | `/classes/{class_identifier}/submissions` | `course.manage` | List class submissions. |

### Assignments

| Method | Path | Permission | Description |
|---|---|---|---|
| POST | `/assignments` | `assignment.manage` | Create assignment. |
| GET | `/assignments` | `assignment.manage` | List/filter assignments. |
| PATCH | `/assignments/{assignment_id}` | `assignment.manage` | Update assignment. |

### Submissions

| Method | Path | Permission | Description | Notes |
|---|---|---|---|---|
| POST | `/submissions/upload` | `submission.upload` | Upload PDF/DOCX. | Canonical upload. |
| POST | `/submissions/{submission_id}/analyze` | `job.analyze` | Enqueue analysis. | Canonical analysis endpoint. |
| DELETE | `/submissions/{submission_id}` | `submission.upload` | Soft-delete submission. | Audited. |
| POST | `/submissions/{submission_id}/detect-references` | `job.analyze` | Run reference detection stage. | Debug/internal stage endpoint. |
| POST | `/submissions/{submission_id}/parse-citations` | `job.analyze` | Run citation parsing stage. | Debug/internal stage endpoint. |
| POST | `/submissions/{submission_id}/verify-metadata` | `job.analyze` | Run metadata verification stage. | Debug/internal stage endpoint. |

### Jobs

| Method | Path | Permission | Description | Notes |
|---|---|---|---|---|
| GET | `/jobs/{job_id}` | `report.view_own_scope` | Read job status. | Implemented. |
| GET | `/jobs/submissions/{submission_id}/latest` | `report.view_own_scope` | Read latest submission job. | Implemented. |
| POST | `/jobs/{job_id}/retry` | `job.analyze` | Retry terminal job. | Creates lineage-linked job. |
| POST | `/jobs/submissions/{submission_id}/process` | `job.analyze` | Process submission. | Backward-compatible alias. |

`POST /jobs/submissions/{submission_id}/process` is a backward-compatible alias.
New clients should use `/submissions/{submission_id}/analyze`.

Job state sequence:

```text
QUEUED
-> VALIDATING
-> EXTRACTING
-> DETECTING_REFERENCES
-> PARSING_CITATIONS
-> NORMALIZING
-> VERIFYING_METADATA
-> SCORING
-> BUILDING_REPORT
-> COMPLETED
```

Failure states:

```text
FAILED_VALIDATION
FAILED_EXTRACTION
FAILED_METADATA
FAILED_SCORING
FAILED_INTERNAL
CANCELLED
```

### Reports and Exports

| Method | Path | Permission | Description |
|---|---|---|---|
| GET | `/reports/{report_id}` | `report.view_own_scope` | Read report by report ID. |
| GET | `/reports/submissions/{submission_id}` | `report.view_own_scope` | Read report by submission ID. |
| POST | `/reports/submissions/{submission_id}/generate` | `job.analyze` | Generate/read report through service. |
| GET | `/reports/submissions/{submission_id}/export/pdf` | `report.export` | Create PDF export. |
| GET | `/reports/submissions/{submission_id}/export/docx` | `report.export` | Create DOCX export. |
| GET | `/reports/submissions/{submission_id}/export/xlsx` | `report.export` | Create XLSX export. |
| GET | `/reports/{report_id}/history` | `report.view_own_scope` | Read report history. |
| GET | `/report-exports/{export_id}/download` | `report.export` | Download export artifact. |

### Dashboard

| Method | Path | Permission | Description |
|---|---|---|---|
| GET | `/dashboard/summary` | `report.view_own_scope` | Summary metrics. |
| GET | `/dashboard/recent-activities` | `report.view_own_scope` | Recent activity. |
| GET | `/dashboard/weekly-trend` | `report.view_own_scope` | Weekly trend. |

### Admin

| Method | Path | Permission | Description |
|---|---|---|---|
| GET | `/admin/system/ai-health` | `admin.metadata_provider` | AI/provider health. |
| POST | `/admin/relevance/diagnose` | `admin.metadata_provider` | Diagnose C4 relevance. |
| GET | `/admin/audit-logs` | `admin.audit_log` | List audit logs. |
| GET | `/admin/metadata-providers` | `admin.metadata_provider` | List providers. |
| PUT | `/admin/metadata-providers/{id}` | `admin.metadata_provider` | Update provider. |
| GET | `/admin/users` | `admin.user_manage` | List users. |
| POST | `/admin/users` | `admin.user_manage` | Create user. |
| PUT | `/admin/users/{id}` | `admin.user_manage` | Update user. |
| DELETE | `/admin/users/{id}` | `admin.user_manage` | Deactivate user. |
| POST | `/admin/retention/purge` | Admin | Retention purge dry-run/apply. |

## 5. Upload Example

```http
POST /api/v1/submissions/upload
Content-Type: multipart/form-data
Authorization: Bearer <token>
```

Form fields:

| Field | Required | Notes |
|---|---:|---|
| `assignment_id` | Yes | UUID assignment ID. |
| `owner_label` | No | Optional label. |
| `file` | Yes | PDF or DOCX. |

Minimal response shape:

```json
{
  "submission_id": "uuid",
  "file_id": "uuid",
  "job_id": "uuid",
  "status": "UPLOADED"
}
```

## 6. Analyze and Poll

```http
POST /api/v1/submissions/{submission_id}/analyze
```

```json
{
  "job_id": "uuid",
  "submission_id": "uuid",
  "status": "queued",
  "progress": 0,
  "created_at": "2026-07-07T00:00:00Z"
}
```

Client flow:

1. Upload file.
2. Call canonical analyze endpoint.
3. Poll `GET /api/v1/jobs/{job_id}`.
4. Stop polling on completed, failed, or cancelled terminal states.
5. Navigate to report by `report_id` when available.

## 7. Versioning

The current API prefix is `/api/v1`; the current application version is `1.2.0`.
`GET /api/v1/health` exposes version information for deployment/client checks.

Breaking API changes require a new API version or a documented compatibility window,
frontend update, API documentation update, tests, and release notes.
