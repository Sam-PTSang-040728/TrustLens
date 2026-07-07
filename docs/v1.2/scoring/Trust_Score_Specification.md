# Trust Score Specification v1.2

**Scoring version:** `trust-score-v1.2`
**Status:** source-aligned baseline
**Source of truth:** `apps/backend/app/core/trust_score_definition.py`,
`apps/backend/app/services/scoring_service.py`

Trust Score is a review-support signal based on available citation, metadata,
relevance, and warning evidence. It is not an automated conclusion about fraud,
academic misconduct, scientific truth, or grading outcome.

## 1. Formula

```text
ReferenceScore = clamp(
    C1 + C2 + C3 + C4 + C5 + C6 + C7 - penalties,
    0,
    100
)
```

TrustLens v1.2 uses C1-C7. Do not use the older eight-component draft model for this
baseline.

## 2. Components

| Code | Component | Weight |
|---|---|---:|
| C1 | Metadata completeness | 10 |
| C2 | Metadata verification | 25 |
| C3 | Source credibility | 20 |
| C4 | Relevance | 20 |
| C5 | Recency | 10 |
| C6 | Citation style | 10 |
| C7 | Duplicate/concentration | 5 |

Total weight: `100`.

## 3. Labels

| Score | Label |
|---:|---|
| `>= 85` | `reliable` |
| `>= 70` | `acceptable` |
| `>= 50` | `needs_review` |
| `< 50` | `high_risk` |

Labels support review prioritization only.

## 4. C2 Metadata Verification Points

| Status | Points |
|---|---:|
| `VERIFIED` | 25 |
| `PARTIAL_MATCH` | 18 |
| `AMBIGUOUS` | 10 |
| `URL_ONLY` | 7 |
| `NOT_FOUND` | 3 |
| `PROVIDER_UNAVAILABLE` | 8 |
| `INVALID_IDENTIFIER` | 0 |
| `IDENTIFIER_METADATA_CONFLICT` | 0 |

Rules:

- `URL_ONLY` means the URL signal is not academic verification.
- `NOT_FOUND` does not prove a source is fake.
- `PROVIDER_UNAVAILABLE` must not be treated as `NOT_FOUND`.
- DOI/metadata conflict requires evidence and should be visible in report warnings.

## 5. Penalties and Label Caps

| Code | Penalty | Label cap |
|---|---:|---|
| `INVALID_IDENTIFIER` | 10 | `high_risk` |
| DOI/metadata conflict | 15 | `high_risk` |
| `DUPLICATE_REFERENCE` | 5 | `needs_review` |
| `RETRACTED_SOURCE` | 30 | `high_risk` |
| `EXPRESSION_OF_CONCERN` | 10 | `needs_review` |
| `LOW_RELEVANCE` | 3 | `needs_review` |

Penalties must be explainable and visible separately from component weights.

## 6. Component Notes

### C1 Metadata Completeness

Checks available citation fields such as authors, title, year, DOI/URL, venue, and
publisher. Required fields may vary by source type.

### C2 Metadata Verification

Uses provider and matcher status. DOI match, title similarity, authors/year, candidate
count, candidate margin, and provider availability all affect the evidence.

### C3 Source Credibility

Uses available source type, venue, publisher, citation signal, domain, and
publication-status evidence. Metadata cannot prove methodology quality.

### C4 Relevance

Uses report body context, assignment context, citation title/abstract/metadata, and
the configured embedding or fallback scorer. Evidence must record provider/model and
fallback information.

### C5 Recency

Default thresholds:

| Level | Age |
|---|---:|
| Fresh | `<= 3 years` |
| Acceptable | `<= 5 years` |
| Review | `<= 10 years` |
| Old | `<= 15 years` |
| Very old | `> 15 years` |

Field-specific interpretation is required. Older foundational sources are not
automatically invalid.

### C6 Citation Style

Compares detected style with assignment requirements. Correct style does not prove
metadata correctness.

### C7 Duplicate/Concentration

Duplicate signals:

1. normalized DOI match;
2. normalized URL match;
3. fuzzy title, author, and year match.

C7 also tracks source concentration risk.

## 7. Confidence

Confidence is separate from Trust Score. Provider unavailability, fallback scoring,
missing abstract, or sparse metadata should reduce confidence and be shown in evidence.

## 8. Evidence Shape

Representative component payload:

```json
{
  "score": 18.0,
  "max_score": 20,
  "raw_score": 18.0,
  "raw_max_score": 20,
  "ratio": 0.9,
  "reason": "Short explanation",
  "evidence": {},
  "confidence": 0.86
}
```

## 9. Versioning Rule

Do not change `trust-score-v1.2` unless the scoring algorithm changes.

Increment scoring version when changing weights, label thresholds, component logic,
aggregate logic, penalty logic, provider/model behavior with score impact, calibration
profile, source mapping, or duplicate thresholds. A scoring version change requires
test vectors, benchmark notes, docs, and compatibility guidance.

## 10. Calibration Requirement

Academic calibration is not complete in this baseline. Required artifacts before
formal accuracy claims:

- dataset card;
- labeling guide;
- fixed test vectors;
- benchmark report;
- calibration profile;
- limitations;
- scoring changelog.
