# Deployment and Operations

**Status:** source-aligned v1.2 baseline

## 1. Repository Layout

```text
TrustLens/
|-- apps/
|   |-- frontend/   # Git submodule
|   `-- backend/    # Git submodule
|-- docs/
|-- .gitmodules
`-- README.md
```

Clone with submodules:

```powershell
git submodule update --init --recursive
git submodule status
```

## 2. Backend Local Setup

```powershell
cd apps/backend
python -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install -r requirements.txt
pip install -r requirements-dev.txt
```

Example environment:

```env
PROJECT_NAME=TrustLens API
APP_VERSION=1.2.0
API_V1_PREFIX=/api/v1
APP_ENV=development
DATABASE_URL=postgresql+psycopg://postgres:<password>@localhost:5432/trustlens_db
SECRET_KEY=<strong-random-secret-at-least-32-characters>
CORS_ORIGINS=http://localhost:5173
JOB_QUEUE_MODE=database
QUARANTINE_DIR=uploads/quarantine
ACCEPTED_UPLOAD_DIR=uploads/accepted
FILE_SCAN_MODE=strict
FILE_SCAN_PROVIDER=local-policy-scanner
TRUST_SCORE_VERSION=trust-score-v1.2
RELEVANCE_PROVIDER=gemini
RELEVANCE_FALLBACK_PROVIDER=local
RELEVANCE_PROMPT_VERSION=c4-v2
RELEVANCE_THRESHOLD_PROFILE=gemini-embedding-2-768-c4-v2
AI_DATA_MODE=sanitized_text_only
AI_PERSIST_RAW_INPUT=false
AI_LOG_INPUT_TEXT=false
DATA_RETENTION_DAYS=180
EXPORT_RETENTION_DAYS=14
AUDIT_RETENTION_DAYS=365
```

`SECRET_KEY` is required. Development/test values must be at least 32 characters.
Staging/production values must be at least 48 characters and must not contain sample
or placeholder terms.

Run migrations:

```powershell
alembic upgrade head
```

Run API:

```powershell
uvicorn app.main:app --reload
```

Run worker:

```powershell
python -m app.workers.tasks
```

Important queue rule:

```text
JOB_QUEUE_MODE=database
JOB_QUEUE_MODE=inline only for local debugging
```

## 3. Frontend Local Setup

```powershell
cd apps/frontend
npm ci
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

Mock mode is only for local UI development:

```env
VITE_USE_MOCK=true
```

```powershell
npm run dev:mock
```

Build gate:

```powershell
npm run lint
npm run build
```

## 4. Health and Operations Endpoints

| Endpoint | Purpose |
|---|---|
| `GET /api/v1/health` | Process/version health. |
| `GET /api/v1/health/ready` | Database and queue readiness. |
| `GET /api/v1/health/metrics` | Queue counts and oldest queued job age. |
| `POST /api/v1/admin/retention/purge?dry_run=true` | Retention purge dry run. |

## 5. Storage

Current baseline uses local filesystem storage with quarantine and accepted
directories. Analysis requires accepted storage and clean scan status.

Production deployments still need:

- private storage;
- authorized download path;
- encryption decision;
- quota and disk alerts;
- backup/restore evidence;
- approved malware scanner;
- rejected-file cleanup policy.

## 6. Runbook

### API Does Not Start

Check `DATABASE_URL`, migration state, `SECRET_KEY`, Python dependencies, and port
availability.

### Upload Fails

Check assignment status, permission, file size, extension, MIME/signature, scan
status, and storage directory permissions.

### Queue Stuck

Check:

1. `GET /api/v1/health/metrics`;
2. whether `python -m app.workers.tasks` is running;
3. `claimed_by`, `heartbeat_at`, `next_run_at`, and `attempt_count`;
4. provider timeouts or storage failures.

For local debugging, a one-batch/one-run worker mode may be used if supported by the
worker command options in the current backend source.

### No Report After Completed Job

Completed analysis jobs must have `report_id`. If missing, treat as data integrity
failure and inspect pipeline logs, job error fields, and report persistence.

### Provider Unavailable

Provider unavailability must reduce confidence or degrade gracefully. It must not be
converted into a conclusion that the source is fake.

### Refresh-Token Incident

Revoke affected refresh tokens. Rotate `SECRET_KEY` only with a planned global logout
window and deployment coordination.

## 7. Backup and Restore

Full sign-off requires PostgreSQL and file/object storage backup from a consistent
point, restore into a separate environment, checksum verification, and DB-file
reconciliation. A backup job without restore evidence is not enough.

## 8. Release Operations Checklist

- API and worker deployed as separate processes.
- `JOB_QUEUE_MODE=database`.
- Migrations applied.
- Health/readiness/metrics reachable.
- Secrets configured by environment.
- Exact CORS origins configured.
- Mock mode disabled outside local mock development.
- Retention values approved.
- Restore drill completed before real data is in scope.
- Known limitations visible in pilot/release materials.
