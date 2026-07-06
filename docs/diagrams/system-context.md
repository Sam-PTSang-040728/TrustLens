flowchart LR
    Guest[Khách truy cập]
    Lecturer[Giảng viên]
    Admin[Quản trị viên]
    Student[Sinh viên - deferred]
    FE[React Frontend]
    API[FastAPI Backend]
    DB[(PostgreSQL)]
    FS[(File Storage)]
    Meta[Crossref / OpenAlex]
    Embed[Gemini / Local Embedding]
    Guest --> FE
    Lecturer --> FE
    Admin --> FE
    Student -. future .-> FE
    FE -->|HTTPS JSON / multipart| API
    API --> DB
    API --> FS
    API --> Meta
    API --> Embed
