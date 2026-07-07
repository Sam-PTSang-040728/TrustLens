```mermaid
sequenceDiagram
    actor L as Lecturer
    participant FE as React Frontend
    participant API as FastAPI
    participant DB as PostgreSQL
    participant Q as processing_jobs
    participant W as Worker
    participant META as Metadata Provider
    participant EMB as Embedding Provider

    L->>FE: Select assignment + PDF/DOCX
    FE->>API: POST /submissions/upload
    API->>DB: File + Submission + initial Job
    API-->>FE: submission_id, file_id, job_id
    FE->>API: POST /submissions/{id}/analyze
    API->>DB: Create/reuse queued job
    API->>Q: Enqueue analysis job
    API-->>FE: 202 Accepted
    W->>Q: Claim queued job
    W->>DB: VALIDATING / EXTRACTING
    W->>DB: DETECTING_REFERENCES / PARSING_CITATIONS
    loop citations
        W->>META: Query metadata
        META-->>W: Candidate + evidence
    end
    W->>EMB: Relevance request
    EMB-->>W: Similarity + evidence
    W->>DB: Score C1-C7 + Report + Audit
    W->>Q: COMPLETED with report_id
    loop Poll
        FE->>API: GET /jobs/{job_id}
        API-->>FE: status + progress + report_id
    end
    FE->>API: GET /reports/{report_id}
    API-->>FE: Report
```
