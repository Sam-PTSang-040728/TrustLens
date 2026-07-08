# Changelog

## 1.2.1 - 2026-07-08

Patch release scope for controlled pilot readiness.

### Security

- Added `PUBLIC_REGISTRATION_ENABLED` and disabled public registration by default outside development.
- `POST /api/v1/auth/register` now returns `403` with `AUTH_PUBLIC_REGISTRATION_DISABLED` when public registration is closed.

### Frontend

- Aligned registration password length with the backend default of 10 characters.
- Added API error formatting for the structured error contract.
- Hid public registration prompts when pilot registration is closed.
- Bumped runtime version display to `1.2.1`.

### Documentation

- Added v1.2.1 release notes, baseline manifest, evidence register, and GO/NO-GO checklist.
- Kept API version `/api/v1` and Trust Score version `trust-score-v1.2`.
