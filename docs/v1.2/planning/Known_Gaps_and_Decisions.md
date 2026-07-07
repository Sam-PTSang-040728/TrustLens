# Known Gaps and Decisions

**Status:** source-aligned v1.2 baseline
**Authoritative status matrix:** [P0/P1 completion status](P0_P1_Completion_Status.md)

## 1. Architecture Decisions

| ID | Decision | Status |
|---|---|---|
| ADR-001 | Trust Score v1.2 uses seven components with total weight 100. | Current baseline |
| ADR-002 | `POST /api/v1/submissions/{submission_id}/analyze` is canonical. | Current baseline |
| ADR-003 | Process endpoint is a backward-compatible alias. | Current baseline |
| ADR-004 | TrustLens remains a modular monolith; split workloads only with evidence. | Current baseline |
| ADR-005 | Mock mode is explicit only through `VITE_USE_MOCK=true`. | Current baseline |
| ADR-006 | Provider not-found/unavailable statuses are not academic truth judgments. | Current baseline |
| ADR-007 | Scoring-impacting logic changes require a scoring version change. | Current baseline |
| ADR-008 | v1.2 uses a database-backed queue in `processing_jobs`. | Current baseline |

## 2. Gap Register

| ID | Severity | Gap | Impact | Status |
|---|---|---|---|---|
| GAP-001 | Critical | Process/retry placeholder behavior | False completion | Remediated in code; needs integration/E2E evidence |
| GAP-002 | Critical | Mock fallback | False success | Remediated in code; needs E2E evidence |
| GAP-003 | Critical | Default secret | Token compromise | Remediated in code; needs CI/security evidence |
| GAP-004 | High | Durable queued execution | Lost/duplicated jobs | Implemented for pilot with database queue; needs crash/concurrency evidence |
| GAP-005 | High | Missing integration/E2E evidence | Cannot fully sign off | Blocked |
| GAP-006 | High | Old scoring docs contradicted seven-component code | Misleading reports | Remediated in canonical docs |
| GAP-007 | High | Error schema drift | Client parsing failures | Partial; contract tests needed |
| GAP-008 | High | CORS/security headers need deployment validation | Origin/security risk | Partial |
| GAP-009 | High | Auth hardening incomplete | Account attacks | Partial; shared limiter/CSP/cookie work remains |
| GAP-010 | High | Browser token storage | XSS impact | Planned hardening |
| GAP-011 | High | Production malware scanner absent | Upload risk | Planned |
| GAP-012 | High | Retention/restore evidence incomplete | Privacy/data loss | Blocked |
| GAP-013 | Medium | OCR absent | Scanned PDFs fail | Planned |
| GAP-014 | Medium | Pagination consistency needs tests | Scale/client drift | Partial |
| GAP-015 | Medium | Stage endpoints widen API surface | Contract complexity | Open decision |
| GAP-016 | Medium | Some model skeletons may be unused | Complexity | Entity audit needed |
| GAP-017 | Medium | Frontend scoring presets may drift from backend | Config drift | Contract tests needed |
| GAP-018 | Medium | Academic benchmark missing | Validity unknown | Planned |
| GAP-019 | Medium | Disclaimer review incomplete | Misinterpretation | Partial |
| GAP-020 | Low | Encoding/i18n inconsistencies | UX/docs quality | Partial |

## 3. Open Questions

| ID | Question | Proposed owner |
|---|---|---|
| OQ-001 | What is the final public policy for report aggregate penalties? | Product + scoring owner |
| OQ-002 | Which registration lifecycle is required for lecturers? | Product + security |
| OQ-003 | How much evidence should students see in a future portal? | Product |
| OQ-004 | What retention period is approved by policy? | Product + privacy |
| OQ-005 | Which providers may receive which text fields? | Security + privacy |
| OQ-006 | When should the database queue be replaced by a broker? | Backend + DevOps |
| OQ-007 | Is multi-tenancy required for the next deployment context? | Product |
| OQ-008 | How far may assignment-specific weights vary? | Product + scoring owner |
| OQ-009 | Should report history be immutable or mutable? | Backend + product |
| OQ-010 | Who owns benchmark labeling and approval? | Academic owner |

## 4. Consistency Rule

The system is consistent only when API contract, state machine, pipeline, scoring
definition, permission vocabulary, error schema, mock policy, ownership model, and
docs all match source code and executable evidence.
