# TrustLens Documentation v1.2

**Status:** canonical documentation baseline
**Scope:** root repository, frontend, and backend implementation snapshot
**Rule:** source code is the source of truth for implemented behavior; roadmap and
archive files must not be presented as implemented features.

**Milestone status:** P0 is not declared unconditionally complete. The current
snapshot has implementation evidence for several P0 code paths, but full v1.2
sign-off still requires executable PostgreSQL integration/E2E evidence, CI submodule
validation, production hardening, restore evidence, and academic calibration. See
[P0/P1 completion status](planning/P0_P1_Completion_Status.md) and the
[v1.2 completion backlog](planning/V1_2_Completion_Backlog.md).

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
| Governance | [Contributing](governance/Contributing.md) |
| Archive | [Source baseline](archive/Source_Baseline.md), [Migration notes](archive/Migration_Notes.md), [Old plans](archive/old-plans/), [Reviews](archive/reviews/) |

## Release Evidence Hierarchy

Use the following order when deciding whether a capability is complete:

1. Executed integration/E2E/security evidence.
2. Mounted router and invoked service implementation.
3. Model and migration.
4. Frontend client and route integration.
5. Unit tests.
6. Current documentation.
7. Roadmap or archived plans.

A workflow file, test file, UI screen, or roadmap item by itself is not evidence that
the capability works end to end.

## Update Rules

1. Update the relevant v1.2 document in the same change as code that changes API,
   schema, permissions, pipeline behavior, scoring logic, or deployment behavior.
2. Mark a capability as `Implemented` only when code, migration if needed, and
   verification evidence exist.
3. Keep future work in [Roadmap](planning/Roadmap.md) or the
   [completion backlog](planning/V1_2_Completion_Backlog.md), not in the SRS as if it
   already exists.
4. Update [Known limitations](limitations/Known_Limitations.md) when a limitation is
   added, removed, or materially changed.
5. Do not declare a release ready until the
   [Release readiness checklist](operations/Release_Readiness_Checklist.md) has evidence.
6. Keep root `docs/` minimal: only this versioned docs folder and the root index.

