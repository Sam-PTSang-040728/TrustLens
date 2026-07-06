# Đặc tả kiểm thử Integration và End-to-End v1.2

**Mục tiêu:** cung cấp bằng chứng thực thi cho các contract quan trọng nhất của TrustLens.
**Phạm vi:** backend + PostgreSQL + file storage + worker + frontend; provider ngoài được stub/mocked có kiểm soát ở integration test.

## 1. Test environments

| Môi trường | Database | Queue | Provider | Mock UI |
|---|---|---|---|---|
| Unit | Không bắt buộc | Stub | Stub | Không áp dụng |
| Integration | PostgreSQL 16 | Database queue | Mock server deterministic | Tắt |
| E2E | PostgreSQL 16 | Worker process | Mock server hoặc sandbox approved | Tắt |
| Manual pilot | Pilot database | Database queue | Provider thật | Tắt |

Không dùng SQLite để thay thế PostgreSQL cho test liên quan JSONB, UUID, partial index, row locking hoặc `skip locked`.

## 2. Fixture corpus tối thiểu

| Mã | Fixture | Kết quả mong đợi |
|---|---|---|
| FX-01 | DOCX có references chuẩn | Completed + report |
| FX-02 | PDF text layer | Completed + report |
| FX-03 | PDF scan | `PDF_HAS_NO_TEXT_LAYER` |
| FX-04 | PDF malformed/encrypted | validation/extraction failure |
| FX-05 | Không có reference section | `NO_REFERENCE_SECTION` |
| FX-06 | APA và IEEE | detected style có evidence |
| FX-07 | DOI đúng | verified/partial theo provider fixture |
| FX-08 | DOI conflict | conflict warning, không kết luận giả |
| FX-09 | URL sống phi học thuật | URL reachable nhưng credibility bị giới hạn |
| FX-10 | Provider timeout | fallback/degraded confidence |
| FX-11 | Duplicate DOI/URL | C7 evidence |
| FX-12 | Fuzzy duplicate | match evidence và confidence |
| FX-13 | File lớn hơn giới hạn | `413` |
| FX-14 | MIME/signature spoof | reject |
| FX-15 | EICAR/macro marker | quarantine reject |

Fixture phải được version hóa và không chứa dữ liệu cá nhân thật.

## 3. Backend integration scenarios

### IT-AUTH-01 — Register, login, refresh, logout

1. Register với mật khẩu hợp lệ.
2. Login nhận access/refresh token.
3. Refresh lần đầu thành công và rotate token.
4. Dùng lại refresh token cũ bị từ chối.
5. Logout token mới.
6. Refresh sau logout bị từ chối.

**Pass:** state token được lưu và audit; không log plaintext token.

### IT-OWN-01 — Cross-user isolation

1. Lecturer A tạo course/class/assignment/submission/report.
2. Lecturer B thử đọc và sửa từng resource bằng UUID trực tiếp.
3. Lecturer B thử analyze, retry và export.

**Pass:** không có response chứa dữ liệu của Lecturer A; audit không được tạo như thao tác thành công.

### IT-UPLOAD-01 — Upload accepted path

1. Upload DOCX/PDF hợp lệ.
2. Kiểm tra checksum, safe stored name, scan status và storage state.
3. Xác nhận file nằm ngoài public static root.

**Pass:** chỉ file `clean` mới có state `accepted`.

### IT-UPLOAD-02 — Rejected upload cleanup

1. Upload EICAR hoặc file signature sai.
2. Kiểm tra response contract.
3. Kiểm tra quarantine không còn orphan ngoài retention policy.

### IT-JOB-01 — Canonical analyze

1. Upload fixture.
2. Gọi `/submissions/{id}/analyze`.
3. Worker claim job.
4. Poll `/jobs/{id}` đến terminal state.
5. Đọc report bằng `report_id`.

**Pass:** một active job/submission; completed job luôn có report; report lưu scoring version.

### IT-JOB-02 — Retry lineage

1. Tạo failure deterministic.
2. Retry terminal job.
3. Sửa nguyên nhân và chạy worker.

**Pass:** job mới có `retry_of_job_id`; job cũ không bị sửa thành queued; report mới tham chiếu job mới.

### IT-JOB-03 — Concurrent workers

Chạy ít nhất hai worker đồng thời trên cùng queue.

**Pass:** một job chỉ được claim một lần; không tạo duplicate report; `attempt_count` chính xác.

### IT-EXPORT-01 — Export validation

Sinh PDF, DOCX và XLSX; mở file bằng thư viện parser tương ứng; kiểm tra MIME, kích thước, tên file và quyền tải.

### IT-MIG-01 — Database lifecycle

- Upgrade database rỗng đến head.
- Upgrade từ snapshot migration trước.
- Kiểm tra partial unique index.
- Kiểm tra downgrade chỉ khi policy dự án hỗ trợ.

## 4. Frontend E2E scenarios

### E2E-01 — Core lecturer flow

```text
Login
→ create course
→ create class
→ create assignment
→ upload file
→ start analysis
→ observe real progress
→ open report by report_id
→ download export
```

**Pass:** không request tới mock data; progress lấy từ backend; reload trang không mất trạng thái job.

### E2E-02 — Error recovery

- Upload unsupported file.
- Provider degraded.
- Job failed.
- Retry job.

**Pass:** UI hiển thị error code/message hữu ích; không hiển thị thành công giả.

### E2E-03 — Authorization UI and API

Ẩn route/menu không đủ quyền, nhưng test bắt buộc gọi API trực tiếp để chứng minh backend enforcement.

### E2E-04 — Batch decision

Nếu batch thuộc v1.2, chạy create/start/status/cancel/retry-failed. Nếu không thuộc v1.2, UI không được phát request `/analysis-batches`.

## 5. CI commands chuẩn

```bash
# Backend
pip install -r requirements-dev.txt
ruff check app tests
alembic upgrade head
pytest tests/unit tests/integration --cov=app --cov-report=term-missing
pip-audit -r requirements.txt

# Frontend
npm ci
npm run lint
npm run test -- --run
npm run build
npm run e2e
```

Tên script frontend có thể thay đổi, nhưng release gate phải có unit/component và browser E2E command rõ ràng.

## 6. Coverage và quality thresholds

- Không dùng coverage tổng thể như điều kiện duy nhất.
- Bắt buộc cover state transition, ownership, error path, score boundary và retry.
- Mọi bug production/pilot phải có regression test.
- Test provider phải deterministic; không để quota/network biến động làm CI flaky.

## 7. Evidence package

Mỗi release lưu:

- workflow run URL;
- test summary;
- migration head;
- fixture version;
- coverage report;
- dependency audit;
- E2E screenshots/video khi cần;
- danh sách test skipped và lý do.

## 8. Fixture PDF/DOCX Manifest

Fixture corpus phai co file vat ly trong repository test artifact hoac generated
deterministically trong CI. Moi fixture can co checksum, generator/source, expected
error code va license/synthetic-data note.

| Fixture ID | Format | Noi dung bat buoc | Expected path |
|---|---|---|---|
| `fx-docx-apa-clean` | DOCX | References heading ro, APA citations, DOI hop le | Analyze completed, report co C1-C7 evidence |
| `fx-docx-ieee-mixed` | DOCX | IEEE + mot loi style | Completed voi C6 warning |
| `fx-docx-no-references` | DOCX | Khong co references section | Terminal `NO_REFERENCE_SECTION` |
| `fx-docx-macro-marker` | DOCX | Synthetic macro/active-content marker | Upload rejected hoac quarantine theo scan policy |
| `fx-pdf-text-clean` | PDF | Text layer, references ro, DOI/URL mix | Analyze completed, report co `report_id` |
| `fx-pdf-scanned` | PDF | Image-only pages | Terminal `PDF_HAS_NO_TEXT_LAYER` hoac equivalent extraction code |
| `fx-pdf-encrypted` | PDF | Password/encrypted fixture | Validation/extraction failure, no accepted analysis |
| `fx-pdf-malformed` | PDF | Truncated/corrupt xref | Validation/extraction failure |
| `fx-pdf-large` | PDF | Lon hon configured upload limit | `413` |
| `fx-upload-eicar` | Any | EICAR string in quarantine input | Rejected/quarantined, never promoted to accepted storage |

Acceptance:

- CI khong dung tai lieu sinh vien that chua duoc an danh.
- Test phai assert checksum de tranh fixture drift.
- Fixture expected output duoc version theo scoring version.
- Provider responses cho DOI/URL phai deterministic qua mock server hoac cassette approved.

## 9. PostgreSQL Integration Test Rules

Integration tests must run against PostgreSQL, not SQLite, for any behavior involving
UUID, JSONB, row locks, partial indexes, transactions, or `SKIP LOCKED`.

Required checks:

- Alembic upgrade from empty database to head.
- Alembic upgrade from previous-release snapshot to head.
- Partial unique index enforces one active job per submission.
- Transaction rollback leaves no accepted file without matching DB record.
- Test data is isolated per test by schema/database reset or transaction cleanup.
- Provider calls are deterministic and do not depend on live quota/network.

## 10. Ownership Negative Matrix

Lecturer B must receive `403` or `404` without owner metadata when accessing Lecturer
A resources directly by ID.

| Resource | Read | Write | Analyze/retry/export |
|---|---:|---:|---:|
| Course | Required | Required | Not applicable |
| Class | Required | Required | Not applicable |
| Assignment | Required | Required | Required through submission analyze path |
| Submission | Required | Required/delete | Analyze required |
| Processing job | Required | Not applicable | Retry required |
| Report | Required | Not applicable | Export required |
| File/export download | Required | Not applicable | Required |
| Admin/audit resource | Required by role | Required by role | Not applicable |

Acceptance:

- Response body must not reveal owner email, class name, assignment title, file path,
  storage key, report summary, or provider payload.
- Audit must record denied access as a denied event, not as a successful action.
- Frontend route hiding is not sufficient; API direct-call tests are mandatory.

## 11. Worker Concurrency and Crash Recovery

Required scenarios:

1. Two workers attempt to claim the same queued job; exactly one succeeds.
2. Worker process is killed after claim but before report persistence; stale recovery
   requeues or fails according to lease policy.
3. Long-running provider loop updates heartbeat before stale threshold.
4. Stale detector does not requeue a job whose heartbeat is current.
5. Retried terminal job creates new lineage and does not mutate the old terminal job.
6. Duplicate analyze requests on one submission do not create two active jobs.
7. Report persistence failure cannot leave job as `COMPLETED` without `report_id`.

Acceptance:

- No duplicate report rows for the same job.
- `attempt_count`, `claimed_by`, `heartbeat_at`, `started_at`, `completed_at` and
  terminal error fields are asserted.
- Metrics expose queued/running/failure/stale counts after the scenario.

## 12. Retry, Export, Migration and Browser E2E Gates

Retry:

- Failed validation jobs are not auto-retried.
- User retry from terminal job creates `retry_of_job_id`.
- Provider transient failures use bounded backoff and preserve evidence.

Export:

- PDF/DOCX/XLSX generated files are opened by parsers in CI.
- Export download enforces ownership and uses safe filenames/MIME.
- Export content includes scoring version, C1-C7 evidence and disclaimer.

Migration:

- Migration head is asserted in CI.
- Downgrade is tested only if the project explicitly supports downgrade.
- Data migration checks include nullability, enum compatibility and partial indexes.

Browser E2E:

- E2E must exercise login, course/class/assignment creation, PDF upload, DOCX upload,
  analyze, progress polling, report open, export download and retry.
- Browser network assertions must fail on `/analysis-batches` unless batch is in v1.2
  scope and backend contract exists.
- Screenshots/video are stored for failed CI runs.
