sequenceDiagram
    actor L as Lecturer
    participant FE as React Frontend
    participant API as FastAPI
    participant DB as PostgreSQL
    participant BG as BackgroundTasks
    participant META as Metadata Provider
    participant EMB as Embedding Provider
    L->>FE: Chọn assignment + PDF/DOCX
    FE->>API: POST /submissions/upload
    API->>DB: File + Submission + Job
    API-->>FE: submission_id, job_id
    FE->>API: POST /submissions/{id}/analyze
    API->>DB: New Job QUEUED
    API->>BG: run_analysis_pipeline(job_id)
    API-->>FE: 202 Accepted
    BG->>DB: VALIDATING / EXTRACTING
    BG->>DB: DETECTING / PARSING
    loop citations
        BG->>META: Query metadata
        META-->>BG: Candidate + evidence
    end
    BG->>EMB: Relevance request
    EMB-->>BG: Similarity + evidence
    BG->>DB: C1-C7 + Report + Audit
    loop Poll every 2s
        FE->>API: GET /jobs/{job_id}
        API-->>FE: status + progress
    end
    FE->>API: GET /reports/submissions/{id}
    API-->>FE: Report
