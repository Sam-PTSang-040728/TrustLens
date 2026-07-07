# Test Plan and Quality Gates

**Status:** source-aligned v1.2 baseline

Do not mark a capability `Verified` only because a test file or workflow file exists.
Verification requires executed evidence.

## 1. Evidence Currently Available

| Evidence | Status | Notes |
|---|---|---|
| Backend compile/import smoke | Available historically; rerun required for current change | `python -m compileall app tests` is the intended command. |
| Frontend lint | Available historically; rerun required for current change | `npm run lint`. |
| Frontend build | Available historically; rerun required for current change | `npm run build`. |
| CI workflow scaffolding | Implemented | Backend, frontend, docs workflows exist. |
| Backend unit test files | Implemented | Unit tests exist for scoring, metadata, relevance, duplicate/conflict, and pilot controls. |
| Backend dev dependencies | Implemented | `requirements-dev.txt` includes test/lint tooling. |

## 2. Evidence Not Yet Sufficient

| Gap | Release status |
|---|---|
| PostgreSQL integration suite | Blocked |
| Ownership negative suite | Blocked |
| Browser E2E | Blocked |
| Restore drill | Blocked |
| Performance benchmark | Planned |
| Accessibility audit | Planned |
| Academic calibration | Planned |
| Full error-schema contract coverage | Partial |
| Worker crash/concurrency/stale recovery evidence | Partial |

## 3. Release Gate

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

## 4. Backend Gate

Minimum command set:

```powershell
cd apps/backend
python -m compileall app tests
ruff check app tests
pytest
alembic heads
```

With a running PostgreSQL instance:

```powershell
alembic upgrade head
```

Integration tests should cover:

- auth register/login/refresh/logout;
- public register role-safety blocker once fixed;
- course/class/assignment ownership;
- upload validation and scan states;
- analyze enqueue and worker processing;
- job polling and retry lineage;
- report/export authorization;
- retention dry-run/apply;
- provider unavailable and `NOT_FOUND` semantics;
- error schema and correlation ID.

## 5. Frontend Gate

Minimum command set:

```powershell
cd apps/frontend
npm ci
npm run lint
npm run build
```

Required future suites:

- service/client contract tests;
- route guard tests;
- upload/analyze/report component tests;
- browser E2E in backend mode;
- accessibility smoke/audit;
- no production fallback to mock mode.

## 6. Core E2E Flow

The core flow is not `Verified` until it runs against backend mode and a real database:

```text
Register/Login
-> Course
-> Class
-> Assignment
-> Upload PDF/DOCX
-> Analyze
-> Poll persisted job
-> Worker builds C1-C7 report
-> Export PDF/DOCX/XLSX
-> Retry failed terminal job
-> Cross-user negative access denied
```

## 7. Fixture Matrix

| Fixture | Purpose |
|---|---|
| Text-layer PDF | Happy path extraction. |
| DOCX | Happy path extraction. |
| Scanned/image-only PDF | Clear OCR-not-supported failure. |
| Malformed/encrypted PDF | Validation/extraction failure. |
| Missing reference section | Detection failure. |
| APA/IEEE references | Style and parsing coverage. |
| Missing year | C1/C5 behavior. |
| Valid DOI | C2 verified path. |
| DOI metadata conflict | Conflict evidence and penalty. |
| URL-only non-academic source | URL-only semantics. |
| Provider timeout | Provider unavailable behavior. |
| Duplicate DOI/URL/title | C7 behavior. |
| Vietnamese/English report text | C4 relevance. |
| Oversize file | Upload limit. |
| MIME spoof | Upload security. |
| EICAR sample | Scanner integration once production scanner exists. |

## 8. Regression Rules

- Trust Score weights sum to 100.
- Boundaries at 49.99/50/69.99/70/84.99/85 are covered.
- Confidence does not silently change score semantics.
- `PROVIDER_UNAVAILABLE` is not treated as `NOT_FOUND`.
- URL reachability is not academic verification.
- Public registration does not create active lecturers once fixed.
- Retry creates a new job and preserves lineage.
- Completed analysis jobs have `report_id`.

## 9. Documentation Gate

Documentation is in scope for release quality. Run:

```powershell
git diff --check
git grep -n "<old eight-component scoring term>"
git grep -n "<unconditional production claim>"
```

Markdown links and internal references should be checked before release.
