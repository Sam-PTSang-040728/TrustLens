# TrustLens Documentation v1.2

**Status:** canonical documentation baseline  
**Scope:** root repository, frontend, and backend implementation snapshot  
**Rule:** source code is the source of truth for implemented behavior; roadmap and
archive files must not be presented as implemented features.

## Structure

| Area | Documents |
|---|---|
| Product | [Project overview](product/Project_Overview.md) |
| Requirements | [SRS](requirements/SRS.md), [Use cases](requirements/Use_Cases.md) |
| Scoring | [Trust Score specification](scoring/Trust_Score_Specification.md) |
| Architecture | [System context and scope](architecture/System_Context_and_Scope.md), [Architecture](architecture/Architecture.md), [Data model](architecture/Data_Model.md), [Diagrams](architecture/diagrams/) |
| API | [API reference](api/README.md) |
| Security | [Security and privacy](security/Security_and_Privacy.md) |
| Operations | [Deployment and operations](operations/Deployment_and_Operations.md) |
| Testing | [Test plan and quality gates](testing/Test_Plan.md) |
| Planning | [Roadmap](planning/Roadmap.md), [Known gaps and decisions](planning/Known_Gaps_and_Decisions.md) |
| Governance | [Contributing](governance/Contributing.md) |
| Archive | [Source baseline](archive/Source_Baseline.md), [Migration notes](archive/Migration_Notes.md), [Old plans](archive/old-plans/), [Reviews](archive/reviews/) |

## Update Rules

1. Update the relevant v1.2 document in the same change as code that changes API,
   schema, permissions, pipeline behavior, scoring logic, or deployment behavior.
2. Mark a capability as `Implemented` only when code, migration if needed, and
   verification evidence exist.
3. Keep future work in [Roadmap](planning/Roadmap.md) or archive, not in the SRS
   as if it already exists.
4. Keep root `docs/` minimal: only this versioned docs folder and the root index.
