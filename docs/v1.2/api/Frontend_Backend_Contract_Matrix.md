# Ma trận contract Frontend–Backend v1.2

**Mục tiêu:** ngăn frontend gọi endpoint không tồn tại, dùng sai identifier hoặc che lỗi backend bằng mock.
**Nguồn sự thật:** OpenAPI backend, router được mount và client service frontend đang dùng.

## 1. Quy tắc contract

1. Mọi endpoint production phải có route backend, schema request/response và permission.
2. Frontend không tự suy diễn `report_id` từ `submission_id`.
3. Mock chỉ được dùng khi `VITE_USE_MOCK=true`.
4. Error body dùng cấu trúc thống nhất:

```json
{
  "error_code": "RESOURCE_NOT_FOUND",
  "message": "Human-readable message",
  "details": {},
  "correlation_id": "uuid"
}
```

5. List API dùng page contract thống nhất khi endpoint đã được pagination hóa.

## 2. Core contract matrix

| Capability | Frontend route/service | Backend API | Identifier chính | Trạng thái |
|---|---|---|---|---|
| Register | auth service | `POST /auth/register` | email | Aligned |
| Login | auth service | `POST /auth/login` | email | Aligned |
| Refresh | auth service | `POST /auth/refresh` | refresh token | Aligned |
| Logout | auth service | `POST /auth/logout` | refresh token | Aligned |
| Courses | class/course service | `/courses` | course ID | Verify pagination |
| Classes | classes screens | `/classes` | class ID | Verify pagination |
| Assignments | class detail | `/assignments` | assignment ID | Verify pagination |
| Upload | upload screen | `POST /submissions/upload` | assignment ID | Aligned |
| Analyze | job service | `POST /submissions/{id}/analyze` | submission ID | Aligned |
| Job status | analyzing screen | `GET /jobs/{id}` | job ID | Aligned |
| Retry | job service | `POST /jobs/{id}/retry` | terminal job ID | Aligned |
| Report | report screen | `GET /reports/{id}` | report ID | Aligned |
| Export | report service | `/report-exports` | report/export ID | Verify formats |
| Dashboard | dashboard service | `/dashboard` | current user scope | Aligned |
| Admin users | admin service | `/admin/users` | user ID | Verify pagination |
| Audit | admin service | `/admin/audit-logs` | page/filter | Verify pagination |
| Batch | batch service | `/analysis-batches` | batch ID | **Not aligned** |

## 3. Batch analysis decision record

Frontend hiện có contract dự kiến:

```text
POST /analysis-batches
POST /analysis-batches/{batch_id}/start
GET  /analysis-batches/{batch_id}
POST /analysis-batches/{batch_id}/cancel
POST /analysis-batches/{batch_id}/retry-failed
```

Trước khi release v1.2 phải chọn một trong hai phương án.

### Phương án A — Batch thuộc v1.2

Backend phải có:

- `analysis_batches` và `analysis_batch_items`;
- ownership theo assignment/class;
- idempotency khi start;
- per-item failure isolation;
- cancel chỉ ảnh hưởng queued items hoặc có cooperative cancellation rõ ràng;
- retry failed tạo lineage;
- pagination/filter cho items;
- integration/E2E tests.

### Phương án B — Batch ngoài phạm vi v1.2

Frontend phải:

- ẩn batch UI khỏi production build;
- không import/call `batchService` trong luồng production;
- ghi batch vào roadmap;
- giữ mock demo tách biệt và có banner môi trường.

Không chấp nhận trạng thái UI hiển thị batch nhưng backend trả `404`.

## 4. Pagination contract

```json
{
  "items": [],
  "page": 1,
  "page_size": 20,
  "total": 0,
  "has_next": false
}
```

Frontend phải xử lý:

- empty page;
- last page sau khi xóa;
- `page_size` giới hạn;
- loading/error/retry;
- không giả định response là array trực tiếp.

## 5. Job state contract

Terminal states tối thiểu:

- `COMPLETED`;
- `FAILED_VALIDATION`;
- `FAILED_EXTRACTION`;
- `FAILED_METADATA`;
- `FAILED_SCORING`;
- `FAILED_INTERNAL`;
- `CANCELLED` nếu được hỗ trợ.

Frontend phải dựa trên backend state, không tăng progress bằng timer giả trong backend mode.

Completed job bắt buộc có `report_id`. Nếu thiếu `report_id`, UI phải coi là integrity error, không điều hướng bằng submission ID.

## 6. Contract test yêu cầu

- Generate hoặc snapshot OpenAPI.
- Kiểm tra frontend service path/method với OpenAPI.
- Kiểm tra enum job status và label Trust Score.
- Kiểm tra page contract.
- Kiểm tra error schema.
- Fail CI khi frontend dùng endpoint không tồn tại.

## 7. Change governance

Mọi thay đổi endpoint, field, enum hoặc permission phải cập nhật đồng thời:

1. backend schema/router;
2. OpenAPI snapshot;
3. frontend type/service;
4. integration/contract test;
5. tài liệu này và API reference.

## 8. Observed Contract Evidence

Snapshot kiem tra hien tai:

| Evidence | Path | Ket luan |
|---|---|---|
| Backend router mount | `apps/backend/app/api/v1/api_router.py` | Khong include router nao cho `/analysis-batches` |
| Backend endpoint search | `apps/backend/app/api/v1/endpoints/*` | Khong co endpoint batch analysis |
| Frontend service | `apps/frontend/src/services/batchService.ts` | Production mode calls `/analysis-batches` create/start/status/cancel/retry-failed |
| Frontend UI integration | `apps/frontend/src/features/classes/ClassDetailScreen.tsx` | Imports `batchService` va co flow khoi chay/poll/retry batch |

Contract result: `/analysis-batches` is **P0 not aligned**. It is not a documentation
gap; it is a runtime contract gap when mock mode is off.

## 9. Standard Response Contracts

### Error response

All production errors should normalize to:

```json
{
  "error_code": "RESOURCE_NOT_FOUND",
  "message": "Human-readable message",
  "details": {},
  "retryable": false,
  "correlation_id": "uuid"
}
```

Rules:

- `message` is safe for UI display and must not include stack traces, local paths,
  owner metadata, API keys, tokens or provider raw payloads.
- `details` is optional but must be structured and redacted.
- `correlation_id` is mandatory for backend errors and should be surfaced in support
  copy/logs.
- Auth/ownership failures must use the same schema as validation/internal failures.

### Pagination

Paginated endpoints return the same shape:

```json
{
  "items": [],
  "page": 1,
  "page_size": 20,
  "total": 0,
  "has_next": false
}
```

Frontend must not assume a direct array for endpoints that have been paginated.

### Job state

Job responses must expose enough state for browser E2E and recovery:

```json
{
  "job_id": "uuid",
  "submission_id": "uuid",
  "status": "queued",
  "progress": 0,
  "stage": "validating",
  "report_id": null,
  "error": null,
  "retry_of_job_id": null
}
```

Rules:

- Terminal completed jobs must have `report_id`.
- Failed jobs must expose stable `error_code`.
- UI must not invent progress in backend mode.
- Retry must target terminal job IDs, not submissions directly unless backend exposes
  a documented alias.

## 10. `/analysis-batches` Remediation Options

### Option A: implement batch in v1.2

Backend must add:

- mounted router under `/api/v1/analysis-batches`;
- `analysis_batches` and `analysis_batch_items` models/migrations;
- create/start/status/cancel/retry-failed endpoints;
- ownership checks through assignment/class scope;
- idempotent start and retry lineage;
- per-item job/report/error state;
- PostgreSQL integration, ownership-negative, worker concurrency and browser E2E tests.

Acceptance:

- Frontend batch flow passes in backend mode with no mock fallback.
- Cancelling does not corrupt running jobs; behavior is documented.
- Batch status never reports completed item without `report_id`.

### Option B: defer batch outside v1.2

Frontend must:

- hide batch UI in production;
- avoid importing/calling `batchService` in production routes;
- keep demo-only batch under explicit mock/demo flag;
- add browser network assertion that no `/analysis-batches` request is made.

Acceptance:

- Production E2E over class detail flow emits zero `/analysis-batches` requests.
- API docs and roadmap mark batch as deferred.
- No user-facing text implies batch is supported in v1.2.

## 11. API Change Rules

Any endpoint, field, enum, permission or error-code change must land with:

1. backend router/schema/service change;
2. migration when persisted shape changes;
3. OpenAPI snapshot or contract fixture update;
4. frontend service/type update;
5. contract/integration tests;
6. docs update in this matrix and API reference;
7. migration/compatibility note for breaking changes.

Breaking changes require a versioned endpoint or documented migration window.
