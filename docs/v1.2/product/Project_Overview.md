# TrustLens Product Overview

**Status:** source-aligned v1.2 baseline

## 1. Problem

Lecturers and reviewers often need to inspect reference lists manually to understand:

- whether cited sources can be traced;
- whether citation metadata matches known academic records;
- whether sources are relevant and reasonably current;
- whether the reference list has style, duplicate, or concentration risks.

TrustLens supports this review. It does not replace expert academic judgment.

## 2. Value Proposition

TrustLens accepts PDF/DOCX submissions, extracts reference lists, verifies available
metadata, calculates an explainable Trust Score, and generates reports with:

- metadata evidence;
- Trust Score and confidence;
- warnings and recommendations;
- per-citation evidence;
- PDF, DOCX, and XLSX exports.

## 3. Users

| Group | Need | Status |
|---|---|---|
| Lecturer | Manage classes/assignments, upload, analyze, review reports. | Implemented |
| Admin | Manage users/providers/audit and diagnostics. | Implemented/Partial |
| Student | Submit work and view feedback. | Planned |
| QA/operator | Monitor failures, tests, providers, workers. | Partial |
| LMS | Synchronize classes, assignments, and submissions. | Planned |

## 4. Current Capabilities

### Academic Management

- Create/list courses.
- Create, update, delete, and list classes.
- Create, update, and list assignments.
- Enforce lecturer ownership or admin scope.

### Upload

- Accept PDF/DOCX.
- Validate extension, MIME/signature, size, and empty files.
- Store quarantine and accepted file states.
- Apply local scan policy.
- Persist File, Submission, and ProcessingJob records.

### Analysis

1. Validate file and context.
2. Extract text.
3. Detect reference section.
4. Parse citations.
5. Normalize.
6. Verify metadata.
7. Score with seven Trust Score components.
8. Build report.
9. Write audit evidence.

### Report

- Overall and per-citation score.
- Warnings, evidence, and confidence.
- PDF/DOCX/XLSX exports.
- Report history API surface.

### Admin

- User management.
- Audit log.
- Metadata provider management.
- Relevance diagnostics and AI health.
- Retention purge dry-run/apply.

## 5. Not Claimed as Complete

- OCR.
- Plagiarism verdicts.
- Paywalled full-text validation.
- Citation-in-context verification.
- Batch processing as a public production contract.
- Realtime WebSocket/SSE progress.
- Full student portal.
- LMS integration.
- Multi-tenancy.
- Autoscaling/WAF/disaster-recovery completion.
- Academic calibration.
- Public registration role safety.

## 6. Product Principles

### Review Support, Not Automatic Judgment

High scores do not prove scientific correctness. Low scores do not prove fraud or
misconduct.

### Not-Found Is Not Fake

`NOT_FOUND` means configured providers did not return a suitable metadata match. It
may be caused by coverage gaps, metadata errors, unsupported source types, access
limits, or transient network/provider problems.

### Evidence First

Every score component should have reason, evidence, and confidence.

### Versioned Scoring

Reports must store scoring/config version so older reports remain explainable.

### Backend Is the Security Boundary

Hidden frontend routes or buttons do not replace backend authorization and ownership
checks.

## 7. Success Metrics

| Area | Metric |
|---|---|
| Correctness | Parser and metadata-match precision/recall. |
| Performance | Job creation and processing time by pages/citations. |
| Reliability | Job completion, retry, and provider-failure rates. |
| Explainability | Component evidence/confidence coverage. |
| Security | Cross-owner access denied. |
| Usability | Export success and actionable error states. |
| Academic validity | Agreement with expert review after calibration. |

## 8. Maturity

TrustLens has a real analysis pipeline and database-backed queue baseline. It still
requires PostgreSQL integration, ownership negative tests, browser E2E, restore
evidence, production security hardening, registration safety remediation, and academic
calibration before unconditional release sign-off.
