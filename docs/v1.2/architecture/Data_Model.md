# Logical Data Model

**Status:** source-aligned v1.2 baseline

## 1. Traceability

TrustLens data must remain traceable through this chain:

```text
User
-> Course/Class/Assignment
-> File/Submission
-> ProcessingJob
-> ExtractedDocument/ReferenceSection
-> Citation/MetadataRecord
-> CitationScore/Warning
-> Report/ReportExport
```

## 2. Entity Groups

### Identity

- `User`
- `UserProfile`
- `RolePermission`
- `RefreshToken`
- `AuditLog`

Public self-registration currently creates an active lecturer account. Registration
approval fields must not be documented as implemented until migrations, services,
API, and tests exist.

### Academic Scope

- `Course`
- `ClassModel`
- `Assignment`
- `Term`
- `Student`

Lecturer access is scoped through owned classes/assignments. Admin scope is system
wide in the current baseline. Multi-tenancy is not implemented.

### Submission and Processing

- `File`
- `Submission`
- `ProcessingJob`
- `ExtractedDocument`
- `ReferenceSection`

### Citation and Metadata

- `Citation`
- `CitationField`
- `MetadataProvider`
- `MetadataRecord`

### Scoring and Report

- `ScoreComponent`
- `TrustScore`
- `CitationScore`
- `Warning`
- `ScoringConfig`
- `Report`
- `ReportExport`

## 3. Logical Relationships

```mermaid
erDiagram
    USER ||--o| USER_PROFILE : has
    USER ||--o{ CLASS : owns
    USER ||--o{ FILE : uploads
    USER ||--o{ AUDIT_LOG : performs
    COURSE ||--o{ CLASS : contains
    CLASS ||--o{ ASSIGNMENT : contains
    ASSIGNMENT ||--o{ SUBMISSION : receives
    ASSIGNMENT }o--o| SCORING_CONFIG : uses
    FILE ||--|| SUBMISSION : backs
    SUBMISSION ||--o{ PROCESSING_JOB : processes
    SUBMISSION ||--o| EXTRACTED_DOCUMENT : produces
    SUBMISSION ||--o| REFERENCE_SECTION : contains
    SUBMISSION ||--o{ CITATION : contains
    SUBMISSION ||--o{ METADATA_RECORD : verifies
    SUBMISSION ||--o| REPORT : produces
    REFERENCE_SECTION ||--o{ CITATION : groups
    CITATION ||--o{ CITATION_FIELD : parses
    CITATION ||--o{ METADATA_RECORD : matches
    CITATION ||--o| CITATION_SCORE : scores
    CITATION ||--o{ WARNING : raises
    REPORT ||--o{ REPORT_EXPORT : exports
    REPORT }o--|| PROCESSING_JOB : generated_by
```

## 4. Queue Fields

`ProcessingJob` includes the queue and retry fields required by the current
database-backed worker baseline:

| Field | Purpose |
|---|---|
| `status` | Queue/pipeline state. |
| `progress` | 0-100 progress. |
| `step` / `current_step` | Human-readable current stage. |
| `attempt_count` | Claim/retry attempt visibility. |
| `claimed_by` | Worker identity. |
| `heartbeat_at` | Worker liveness signal. |
| `next_run_at` | Retry/stale scheduling. |
| `retry_of_job_id` | Retry lineage. |
| `report_id` | Completed job report link. |
| `error_code`, `error_message`, `error_details` | Structured failure evidence. |

Active job uniqueness is enforced for active pipeline states so one submission does
not have multiple active analysis jobs.

## 5. File Security Fields

`File` records include:

| Field | Purpose |
|---|---|
| `storage_state` | Quarantine/accepted/rejected-style lifecycle state. |
| `scan_status` | Pending/clean/suspicious/infected/failed-style scan result. |
| `scan_provider` | Scan policy/provider name. |
| `scan_signature_version` | Signature/policy version. |
| `scanned_at` | Scan timestamp. |
| `scan_details` | Scan evidence/details. |
| `checksum` | Integrity/deduplication support. |
| `mime_type` | Server-recorded MIME. |
| `size_bytes` | Server-recorded size. |
| `stored_path` | Server-controlled storage location. |

Analysis requires accepted storage and clean scan status.

## 6. State Model

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

Completed analysis jobs must have a persisted `report_id`. If a future job type does
not build a report, it must use a separate explicit contract.

## 7. Integrity Rules

- Email is unique after normalization.
- Inactive users must not authenticate.
- Lecturer access is limited to owned academic scope.
- Admin has system-wide scope in v1.2.
- File paths are server-generated and must not come directly from clients.
- Citation raw text is preserved for parser review.
- Metadata records keep provider, query, status, confidence, evidence, and raw
  provider response subject to retention policy.
- `PROVIDER_UNAVAILABLE` is distinct from `NOT_FOUND`.
- Reports store scoring/config version and must not be retroactively mutated when
  scoring logic changes.

## 8. Open Data-Model Gaps

| Gap | Status |
|---|---|
| Registration approval lifecycle fields | Planned |
| Tenant isolation | Planned |
| Formal legal-basis/consent model | Planned |
| Restore evidence and DB-file reconciliation evidence | Blocked |
| Benchmark dataset/model registry | Planned |
| Production object storage abstraction | Planned |
