# 11. Triển khai và Vận hành

## 1. Cấu trúc repository

```text
TrustLens/
├── apps/
│   ├── frontend/   # Git submodule
│   └── backend/    # Git submodule
├── docs/
├── .gitmodules
└── README.md
```

Clone đầy đủ:

```bash
git clone <root-repository>
cd TrustLens
git submodule update --init --recursive
```

## 2. Backend local

### Yêu cầu

- Python 3.11+.
- PostgreSQL.
- Virtual environment.
- Quyền ghi upload/export directories.

### Cài đặt

```powershell
cd apps/backend
python -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install -r requirements.txt
```

### Environment

```env
PROJECT_NAME=TrustLens API
API_V1_PREFIX=/api/v1
DATABASE_URL=postgresql+psycopg://postgres:<password>@localhost:5432/trustlens_db
SECRET_KEY=<strong-random-secret>
CORS_ORIGINS=http://localhost:5173
UPLOAD_DIR=uploads
MAX_UPLOAD_SIZE_MB=20

TRUST_SCORE_VERSION=trust-score-v1.2
RELEVANCE_PROVIDER=gemini
RELEVANCE_FALLBACK_PROVIDER=local
RELEVANCE_PROMPT_VERSION=c4-v2
RELEVANCE_THRESHOLD_PROFILE=gemini-embedding-2-768-c4-v2

GEMINI_API_KEY=
OPENALEX_API_KEY=
CROSSREF_MAILTO=<contact-email>

AI_DATA_MODE=sanitized_text_only
AI_PERSIST_RAW_INPUT=false
AI_LOG_INPUT_TEXT=false
```

Không dùng default secret ở staging/production.

### Migration và run

```powershell
alembic upgrade head
uvicorn app.main:app --reload
```

- API: `http://localhost:8000/api/v1`
- Swagger: `http://localhost:8000/docs`
- Health: `http://localhost:8000/api/v1/health`

## 3. Frontend local

```powershell
cd apps/frontend
npm install
```

Backend mode:

```env
VITE_API_BASE_URL=http://localhost:8000/api/v1
VITE_USE_MOCK=false
VITE_API_LOGGING=true
```

```powershell
npm run dev:backend
```

Mock mode:

```env
VITE_USE_MOCK=true
```

```powershell
npm run dev:mock
```

Staging/production phải `VITE_USE_MOCK=false` và không fallback ngầm sang mock.

Build:

```powershell
npm run lint
npm run build
npm run preview
```

## 4. Storage

Local filesystem phải không nằm trong public static root, chỉ backend truy cập, có quota/backup/retention, đối soát record-file, tên lưu an toàn và malware scan trước production.

## 5. Background processing

`BackgroundTasks` hiện chạy cùng process API. Hạn chế:

- Restart có thể mất task.
- Không broker retry/dead-letter.
- Khó scale ngang.
- Long task tranh tài nguyên API.

Production gate yêu cầu durable queue hoặc giải pháp tương đương có bằng chứng.

## 6. Health và observability

Endpoint hiện có: `GET /api/v1/health`.

Cần bổ sung:

- Readiness cho DB/storage/migration.
- Liveness process.
- Provider health.
- Queue depth.
- Job failure/duration.
- Provider latency/rate-limit.
- Export failure.
- Disk usage.
- Backup status.
- Correlation ID.

## 7. Log

Không log password, token, secret, full report text, raw AI input hoặc sensitive provider payload.

Structured fields đề xuất:

```text
timestamp level request_id correlation_id user_id
resource_type resource_id job_id stage duration_ms error_code
```

## 8. Backup và restore

Production cần PostgreSQL + file/object backup, encryption, retention, off-site copy, restore drill, RPO/RTO và point-in-time consistency. Có backup job chưa đồng nghĩa có DR.

## 9. Production checklist

### Security

- [ ] Secret không default.
- [ ] HTTPS.
- [ ] CORS allow-list.
- [ ] Rate limiting.
- [ ] Malware scan.
- [ ] CSP/security headers.
- [ ] Dependency/secret scan.
- [ ] Least-privilege DB.
- [ ] Private storage.
- [ ] Protected audit.

### Reliability

- [ ] Unified pipeline.
- [ ] Durable queue.
- [ ] Retry/idempotency.
- [ ] Worker monitoring.
- [ ] Backup/restore tested.
- [ ] Provider timeout/fallback.
- [ ] No mock fallback.

### Quality

- [ ] Integration/E2E.
- [ ] Ownership negative tests.
- [ ] Benchmark.
- [ ] Migration test.
- [ ] Export validation.
- [ ] Docs aligned.

### Operations

- [ ] Runbook/alerts/contacts.
- [ ] Retention/cost/quota.
- [ ] Rollback plan.

## 10. Rollback

Mỗi deployment cần previous build, DB compatibility, feature flags, scoring compatibility, worker/API compatibility và cách xử lý job đang chạy. Không rollback scoring bằng cách sửa report lịch sử.

## 11. Environment separation

| Môi trường | Mock | Dữ liệu | Mục tiêu |
|---|---|---|---|
| Local mock | Có | Giả | UI |
| Local backend | Không | Dev | Tích hợp |
| Test | Không | Fixture | CI |
| Staging | Không | Phi production | Nghiệm thu |
| Production | Không | Thật | Vận hành |

## 12. Runbook ngắn

### API không chạy

Kiểm tra `DATABASE_URL`, migration, `SECRET_KEY`, port.

### Upload lỗi

Kiểm tra assignment open, permission, MIME/size và quyền thư mục.

### Job đứng

Kiểm tra API process, job state, provider timeout. Với `BackgroundTasks`, restart không tự phục hồi.

### Không có report

Xác nhận dùng endpoint analyze/pipeline thật; kiểm tra job error và report relation.

### Provider unavailable

Kiểm tra key/quota/rate limit/fallback; không chuyển thành kết luận nguồn giả.
