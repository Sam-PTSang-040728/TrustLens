# TrustLens Versioning Policy

**Status:** canonical for v1.2
**Current application version:** `1.2.0`
**Current API version:** `v1`
**Current scoring version:** `trust-score-v1.2`

This document defines how TrustLens versions are named, exposed, bumped, and
validated across backend, frontend, API, docs, and Trust Score logic.

## 1. Version Types

| Version type | Current value | Owner | Source of truth | Where it is exposed |
|---|---:|---|---|---|
| Application version | `1.2.0` | Release owner | Backend `APP_VERSION`, frontend package metadata | Backend root, `/api/v1/health`, frontend UI |
| API version | `v1` | Backend owner | Mounted route prefix `/api/v1` | URLs and health payload |
| Frontend package version | `1.2.0` | Frontend owner | `apps/frontend/package.json` | Build metadata and UI display |
| Scoring version | `trust-score-v1.2` | Scoring owner | `TRUST_SCORE_VERSION` and scoring spec | Reports, exports, health payload |
| Prompt/calibration versions | Provider-specific | AI/scoring owner | Backend settings and calibration profiles | Evidence metadata and admin health |
| Database migration revisions | Alembic revision IDs | Backend owner | `apps/backend/alembic/versions/` | Migration history |

## 2. Semantic Versioning

TrustLens application releases use `MAJOR.MINOR.PATCH`.

| Change type | Bump | Examples |
|---|---|---|
| Breaking API, schema, permission, or deployment contract change | `MAJOR` | Move from `/api/v1` to `/api/v2`; remove a response field without a migration window |
| Backward-compatible product/API capability | `MINOR` | Add an optional response field; add a new screen; add a new report export option |
| Backward-compatible fix or documentation-only correction | `PATCH` | Bug fix, UI copy fix, internal refactor, docs clarification |

Pre-release labels may be used for non-production builds, for example
`1.3.0-rc.1` or `1.3.0-beta.2`. Production releases should use stable SemVer
values.

## 3. API Versioning

The active public API prefix is `/api/v1`.

Create `/api/v2` when a change breaks an existing client contract, including:

- removing or renaming public fields;
- changing enum values or status semantics;
- changing authentication or permission requirements in a way existing clients
  cannot satisfy;
- changing request payload shape without backward compatibility;
- changing pagination, filtering, or error response semantics incompatibly.

Backward-compatible additions can stay in `/api/v1` when old clients can ignore
the new fields safely.

Every API version change must update:

- OpenAPI/FastAPI metadata;
- `docs/v1.2/api/README.md`;
- frontend service contracts;
- compatibility or migration notes;
- integration/E2E tests for the affected workflow.

## 4. Backend Requirements

Backend version must be centralized in `APP_VERSION`.

The backend must expose application and API version information through:

- `GET /`;
- `GET /api/v1/health`;
- `GET /api/v1/health/ready`.

The health payload must include at least:

```json
{
  "status": "ok",
  "service": "TrustLens Backend",
  "version": "1.2.0",
  "api_version": "v1",
  "scoring_version": "trust-score-v1.2"
}
```

Readiness may omit scoring details but must include the application version and
API version so deployment checks can identify the running build.

## 5. Frontend Requirements

Frontend package metadata must track the application version in
`apps/frontend/package.json` and `apps/frontend/package-lock.json`.

The UI should display the running application version in an always-available
authenticated surface such as the sidebar or settings page. Runtime overrides may
use `VITE_APP_VERSION` when a deployment pipeline injects build metadata.

Frontend changes that depend on a backend contract must document the minimum
supported API version in the relevant service or release notes.

## 6. Trust Score Versioning

The scoring version is intentionally separate from the application version. A future
application patch release can still use `trust-score-v1.2` if the score logic did
not change.

Increment the scoring version when any of the following changes:

- component weight;
- label threshold;
- component calculation logic;
- aggregate or penalty logic;
- provider/model choice with material scoring impact;
- calibration profile;
- source mapping;
- duplicate/concentration threshold.

Every scoring version change must include:

- changelog entry;
- fixed test vectors;
- benchmark/calibration summary;
- compatibility note for old reports;
- export/report evidence showing the saved scoring version.

## 7. Release Checklist

Before tagging a release:

1. Backend `APP_VERSION` matches the intended release.
2. Frontend package version matches the intended release.
3. Health endpoints return the intended version.
4. API docs describe any contract change.
5. Scoring spec documents whether scoring version changed.
6. Database migrations are present when model/schema changes require them.
7. Release readiness checklist links the verification evidence.

## 8. Current v1.2 Baseline

For the v1.2 documentation baseline:

- application version is `1.2.0`;
- API version remains `v1`;
- scoring version remains `trust-score-v1.2`;
- no API v2 route exists;
- frontend and backend expose the same application version.
