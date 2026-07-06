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
