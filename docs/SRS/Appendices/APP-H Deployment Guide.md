## Docker component
```docker
services:  
frontend: React + Tailwind build  
api: FastAPI + Uvicorn  
worker: Celery worker for file processing and scoring  
postgres: PostgreSQL database  
redis: Queue broker  
storage: local volume or object storage adapter
```