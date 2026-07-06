# Contributing to TrustLens

## Quality Gate

Before opening or merging a PR, run the same checks used by CI:

```powershell
cd apps/backend
pip install -r requirements-dev.txt
ruff check app tests
alembic upgrade head
pytest --cov=app --cov-report=term-missing

cd ../frontend
npm ci
npm run lint
npm run build
```

## Change Rules

- API, schema, permissions, scoring, queue, security, or deployment changes must update the matching file under `docs/v1.2/`.
- Alembic migrations must be included with model changes.
- Do not bypass required checks by weakening workflow files without review.
- Keep mock-mode behavior explicit; do not silently hide backend failures with mock data in production-like modes.
