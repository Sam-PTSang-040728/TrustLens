# Known Limitations for TrustLens v1.2

**Status:** release-facing limitation register
**Application version:** `1.2.0`

These limitations are part of the release contract and must be visible in pilot or
release materials.

## 1. Mandatory Limitations

1. Scanned/image-only PDFs are not OCR-supported in v1.2.
2. Plagiarism detection is not implemented.
3. Citation-in-context verification is not implemented.
4. Research methodology quality assessment is not implemented.
5. Metadata verification depends on Crossref/OpenAlex/provider availability.
6. URL-only evidence does not prove that a source is academic.
7. Academic calibration is not complete.
8. Local scan policy is not an enterprise antivirus/malware scanner.
9. Auth rate limiting is process-local.
10. Local file storage is not suitable for large production deployments without
    additional storage, backup, and security controls.
11. Multi-tenancy is not implemented.
12. Public registration role safety is a blocker because public registration creates
    active lecturers in the current source.
13. Application version remains `1.2.0`.

## 2. File and Extraction Limits

- Supported baseline formats are text-layer PDF and DOCX.
- Malformed, encrypted, or unusually structured files may fail extraction.
- Missing or unusual reference headings may cause reference detection or citation
  splitting errors.
- Raw extraction evidence should be preserved for reviewer inspection.

## 3. Metadata Limits

- Provider coverage is incomplete.
- Provider timeout/rate limits can reduce confidence or force fallback behavior.
- `NOT_FOUND` means the configured providers did not find metadata; it does not prove
  a source is fake.
- `PROVIDER_UNAVAILABLE` means provider evidence is unavailable and must not be
  treated as `NOT_FOUND`.
- Metadata from providers can itself be incomplete or wrong.

## 4. Trust Score Limits

- Trust Score is a review-support signal, not a probability of scientific correctness.
- Low scores do not prove misconduct.
- High scores do not prove a cited work is correct, appropriate, or methodologically
  sound.
- Credibility scoring uses available metadata/evidence and cannot replace expert
  review.
- Recency depends on field context; old foundational works are not automatically
  inappropriate.
- Fuzzy duplicate detection can produce false positives or false negatives.
- Report scores must not be used for formal academic decisions without approved
  benchmark/calibration evidence and policy.

## 5. Security and Privacy Limits

- Local scan policy is not a production malware scanner.
- Process-local rate limiting is not enough for multi-replica deployments.
- Browser token storage increases XSS impact until cookie/CSP hardening is completed.
- Public registration role safety is blocked.
- Production deployments must configure exact CORS origins, secrets, retention, and
  provider policies.
- Restore evidence is not complete.

## 6. Reliability Limits

- The database-backed queue is the current pilot baseline, but full crash,
  concurrency, stale recovery, and restore evidence is still required.
- External providers are dependencies outside TrustLens control.
- No production SLO/SLA should be stated before performance benchmarking.
- Backup automation without restore drill evidence is not enough.

## 7. Use Policy Boundary

TrustLens results must be reviewed by a lecturer or reviewer together with the
evidence. Do not use TrustLens to automatically:

- conclude that a student committed fraud or misconduct;
- grade, penalize, or discipline a student;
- reject a source only because a provider did not find it;
- replace expert assessment of research quality.

## 8. Limitation Removal Rule

A limitation can be removed only when:

1. implementation/configuration exists;
2. migration exists if persisted data changes;
3. unit/integration/E2E/security evidence exists as appropriate;
4. API, contract, and docs are updated;
5. release owner approves the evidence.

Do not remove a limitation because a roadmap item exists or a mock/demo flow works.
