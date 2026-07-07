# Software Requirements Specification

**Document ID:** `TL-SRS-v1.2`
**Status:** source-aligned baseline
**Application version:** `1.2.0`
**API version:** `v1`
**Scoring version:** `trust-score-v1.2`

## 1. Conventions

| Status | Meaning |
|---|---|
| `Implemented` | Source code exists and has a real runtime call path. |
| `Verified` | Executed test evidence exists. |
| `Partial` | Implementation exists but lacks coverage, hardening, or a complete flow. |
| `Planned` | Not implemented. |
| `Blocked` | A defect, missing decision, or missing evidence prevents acceptance. |

## 2. Functional Requirements

### 2.1 Auth and Account Safety

| Requirement | Status | Source/evidence |
|---|---|---|
| Register with name, email, and password | Implemented | `auth.py`, `auth_service.py` |
| Reject duplicate email | Implemented | `auth.py` |
| Login and issue access/refresh tokens | Implemented | `auth.py`, `refresh_token_service.py` |
| Refresh token rotation | Implemented | `/auth/refresh` rotates server-side refresh token records |
| Logout/revoke refresh token | Implemented | `/auth/logout` revokes the submitted refresh token |
| Auth rate limit | Implemented, process-local | `core/rate_limit.py`, auth endpoints |
| Password policy | Implemented | `is_password_strong_enough`, `PASSWORD_MIN_LENGTH` |
| Public register role safety | Blocked | Public registration creates active lecturers |
| Email verification | Planned | No verified runtime flow |
| Admin approval for lecturers | Planned | No approval-state migration or service flow |

#### FR-AUTH-006: Public Registration Safety

Public registration MUST NOT directly create an active lecturer account.

**Status:** `Blocked`

Current code creates a new public user with:

```python
role="lecturer"
is_active=True
```

Expected future account lifecycle:

```text
PENDING_LECTURER
-> EMAIL_VERIFIED
-> ADMIN_APPROVED
-> LECTURER
```

This lifecycle is a v1.2 requirement/backlog item only. It must not be described as
implemented until code, migration, API, tests, and docs are updated.

### 2.2 Course, Class, and Assignment

| Requirement | Status |
|---|---|
| Create and list courses | Implemented |
| Create, list, update, and delete classes by authorized scope | Implemented |
| Create, list, and update assignments by authorized scope | Implemented |
| Enforce class and assignment ownership for lecturers | Implemented |
| Provide admin-wide scope | Implemented |
| Prove all negative ownership cases with executable tests | Blocked |

### 2.3 Upload and Submission

| Requirement | Status |
|---|---|
| Upload PDF/DOCX via multipart | Implemented |
| Validate extension, MIME/signature, size, and empty files | Implemented |
| Store uploads in quarantine before accepted storage | Implemented |
| Apply local scan policy and promote only clean files | Implemented |
| Persist File, Submission, and initial ProcessingJob records | Implemented |
| Store checksum, MIME, size, path, scan state, and storage state | Implemented |
| Soft-delete submission with audit | Implemented |
| Enterprise malware scanning | Planned |
| OCR for scanned PDFs | Planned |

### 2.4 Job and Pipeline

Canonical endpoint:

```http
POST /api/v1/submissions/{submission_id}/analyze
```

Backward-compatible alias:

```http
POST /api/v1/jobs/submissions/{submission_id}/process
```

Retry endpoint:

```http
POST /api/v1/jobs/{job_id}/retry
```

Current queue:

```text
processing_jobs table
database-backed worker queue
```

The canonical worker command is:

```powershell
python -m app.workers.tasks
```

`JOB_QUEUE_MODE=database` is the normal runtime mode. `JOB_QUEUE_MODE=inline` is
for local debugging only.

| Requirement | Status |
|---|---|
| Analyze returns `202 Accepted` and `job_id` | Implemented |
| Process alias runs the same canonical pipeline | Implemented |
| Retry creates a new lineage-linked job | Implemented |
| At most one active job per submission | Implemented |
| Retry preserves lineage | Implemented |
| Completed analysis job has `report_id` | Implemented |
| Worker recovers stale jobs with enough crash/concurrency evidence | Partial |
| Browser E2E proving upload -> analyze -> report -> export | Blocked |

Pipeline states:

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

Operational rules:

- one active job per submission;
- retry creates a new job;
- retry keeps lineage through `retry_of_job_id`;
- completed analysis jobs must have `report_id`;
- provider errors must degrade safely and preserve evidence;
- scanned PDFs without OCR/text layer must return a clear extraction error.

### 2.5 Extraction and Citation

| Requirement | Status |
|---|---|
| Extract DOCX text | Implemented |
| Extract text-layer PDF text | Implemented |
| Reject PDF without text layer with a clear error | Implemented |
| Detect reference section | Implemented |
| Parse citation entries and sequence numbers | Implemented |
| Preserve raw reference/citation evidence | Implemented |
| Citation-in-context verification | Planned |
| Plagiarism detection | Planned |
| Research methodology assessment | Planned |

### 2.6 Metadata Verification

Implemented runtime providers/evaluators:

- Crossref;
- OpenAlex;
- URL checker;
- publication-status evaluator.

Semantic Scholar must not be documented as implemented unless the backend canonical
verification path starts calling it and executable evidence is added.

Status vocabulary:

```text
VERIFIED
PARTIAL_MATCH
AMBIGUOUS
NOT_FOUND
URL_ONLY
PROVIDER_UNAVAILABLE
INVALID_IDENTIFIER
IDENTIFIER_METADATA_CONFLICT
```

Rules:

- a working URL does not prove a source is academic;
- `NOT_FOUND` does not prove a source is fake;
- `PROVIDER_UNAVAILABLE` must not be treated as `NOT_FOUND`;
- DOI/metadata conflict must include evidence;
- the full report must not be sent to metadata providers.

### 2.7 Trust Score

Trust Score v1.2 uses C1-C7.

| Code | Component | Weight |
|---|---|---:|
| C1 | Metadata completeness | 10 |
| C2 | Metadata verification | 25 |
| C3 | Source credibility | 20 |
| C4 | Relevance | 20 |
| C5 | Recency | 10 |
| C6 | Citation style | 10 |
| C7 | Duplicate/concentration | 5 |

| Requirement | Status |
|---|---|
| C1-C7 weights sum to 100 | Implemented |
| Label thresholds are versioned | Implemented |
| Confidence is separate from score | Implemented |
| Report stores scoring version/config version | Implemented |
| Academic calibration package | Planned |

Trust Score is a review-support signal, not an automated conclusion about fraud,
academic misconduct, scientific truth, or grading outcome.

### 2.8 Report and Export

| Requirement | Status |
|---|---|
| Report links submission and job | Implemented |
| Report includes score, label, confidence, components, citations, and warnings | Implemented |
| Read report by report ID | Implemented |
| Read report by submission legacy route | Implemented |
| Export PDF/DOCX/XLSX | Implemented |
| Download export with authorization | Implemented |
| Restore/export artifact verification | Blocked |

### 2.9 Dashboard and Admin

| Requirement | Status |
|---|---|
| Dashboard summary, recent activity, and trend | Implemented |
| Admin user management | Implemented |
| Admin audit log | Implemented |
| Admin metadata provider management | Implemented |
| Admin AI health and relevance diagnostics | Implemented |
| Retention purge dry-run/apply endpoint | Implemented |

## 3. Non-Functional Requirements

### Security

- Backend authorization and ownership checks are mandatory.
- Passwords must be hashed and never returned or logged.
- Secrets must fail fast when missing, too short, or placeholder-like.
- Uploads must be checked for path traversal, spoofed file type, size, and scan state.
- Auth rate limiting exists but is process-local.
- CORS must use exact allow-lists in deployed environments.
- Audit logs must cover auth, upload, analyze, delete, and admin changes.
- Public registration role safety is a release blocker until fixed.

### Reliability

- Job state and progress must be persisted.
- Retry must create a new job and retain lineage.
- Provider failures must not become false academic conclusions.
- PostgreSQL integration, restore, and worker crash/concurrency evidence are required
  before unconditional release sign-off.

### Privacy and AI

- Only minimum necessary citation/reference context should be sent to providers.
- Raw AI input logging is disabled by default.
- Provider/model/prompt/version evidence must be traceable.
- Formal legal basis and retention approval remain open for production deployment.

### Maintainability

- API prefix remains `/api/v1`.
- Application, backend, and frontend remain `1.2.0`.
- Scoring remains `trust-score-v1.2`.
- API/schema/scoring changes must update docs and tests in the same change.

## 4. Release Gate

Required release-gate commands/evidence:

```text
ruff
pytest
alembic upgrade head
PostgreSQL integration
ownership negative tests
frontend lint/build
browser E2E
secret/dependency scan
restore evidence
```

No capability may be marked `Verified` solely because a test file, workflow file, UI
screen, model, or roadmap item exists.
