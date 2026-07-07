# TrustLens Documentation v1.2

**Status:** canonical documentation baseline
**Application version:** `1.2.0`
**Backend version:** `1.2.0`
**Frontend version:** `1.2.0`
**API version:** `v1`
**API prefix:** `/api/v1`
**Scoring version:** `trust-score-v1.2`
**Docs folder:** `docs/v1.2/`

Source code is the source of truth for implemented behavior. Roadmap and archive
files must not be presented as implemented features.

P0 is not declared unconditionally complete. The current snapshot has implemented
code paths for the main upload, analyze, queue, report, export, auth, and admin
flows, but full sign-off still requires PostgreSQL integration evidence, ownership
negative tests, browser E2E evidence, restore evidence, and academic calibration.

Do not create a successor patch-version docs folder or change application, frontend,
backend, API, or Trust Score versions as part of this documentation baseline.

## Status Vocabulary

| Status | Meaning |
|---|---|
| `Implemented` | Source code exists and has a real runtime call path. |
| `Verified` | Executed test evidence exists for the capability. |
| `Partial` | Implementation exists but is incomplete or lacks enough test evidence. |
| `Planned` | Not implemented; roadmap/backlog only. |
| `Blocked` | A defect, missing decision, or missing evidence prevents acceptance. |

## Evidence Hierarchy

Use this order when deciding whether a capability is complete:

1. Executed integration, E2E, or security evidence.
2. Mounted router and real service invocation.
3. Model and migration.
4. Frontend API client and route integration.
5. Unit tests.
6. Current documentation.
7. Roadmap or archived plans.

A workflow file, test file, UI screen, model, endpoint skeleton, or roadmap item by
itself is not enough evidence to mark a capability `Implemented` or `Verified`.

## Structure

| Area | Documents |
|---|---|
| Product | [Project overview](product/Project_Overview.md) |
| Requirements | [SRS](requirements/SRS.md), [Use cases](requirements/Use_Cases.md) |
| Scoring | [Trust Score specification](scoring/Trust_Score_Specification.md), [Benchmark and calibration plan](scoring/Benchmark_and_Calibration_Plan.md) |
| Architecture | [System context and scope](architecture/System_Context_and_Scope.md), [Architecture](architecture/Architecture.md), [Data model](architecture/Data_Model.md), [Diagrams](architecture/diagrams/) |
| API | [API reference](api/README.md), [Frontend-backend contract matrix](api/Frontend_Backend_Contract_Matrix.md) |
| Security | [Security and privacy](security/Security_and_Privacy.md), [Production security hardening](security/Production_Security_Hardening.md) |
| Operations | [Deployment and operations](operations/Deployment_and_Operations.md), [Worker and queue reliability](operations/Worker_Queue_Reliability.md), [Release readiness checklist](operations/Release_Readiness_Checklist.md) |
| Testing | [Test plan and quality gates](testing/Test_Plan.md), [Integration and E2E specification](testing/Integration_and_E2E_Test_Specification.md) |
| Limitations | [Known limitations](limitations/Known_Limitations.md) |
| Planning | [P0/P1 completion status](planning/P0_P1_Completion_Status.md), [v1.2 completion backlog](planning/V1_2_Completion_Backlog.md), [Roadmap](planning/Roadmap.md), [Known gaps and decisions](planning/Known_Gaps_and_Decisions.md) |
| Governance | [Contributing](governance/Contributing.md), [Versioning](governance/Versioning.md) |
| Archive | [Source baseline](archive/Source_Baseline.md), [Migration notes](archive/Migration_Notes.md), [Old plans](archive/old-plans/), [Reviews](archive/reviews/) |

## Baseline Rules

1. Update v1.2 docs when code changes API, schema, permissions, pipeline behavior,
   scoring logic, or deployment behavior.
2. Keep future work in roadmap/backlog files, not in SRS as implemented behavior.
3. Keep Trust Score v1.2 on C1-C7 unless the scoring version is intentionally changed.
4. Keep public registration role safety marked `Blocked` until code no longer creates
   an active lecturer account directly from public registration.
5. Keep production readiness claims conditional on release-gate evidence.
