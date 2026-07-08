# TRUSTLENS — KẾ HOẠCH NÂNG CẤP V1.2.1

**Mã tài liệu:** TL-PLAN-1.2.1  
**Trạng thái:** Proposed / Ready for execution  
**Ngày lập:** 08/07/2026  
**Phạm vi kho mã nguồn:**

- Root: `PhanTanSang-netizen/trustlens` (GitHub hiện có thể chuyển hướng sang chủ sở hữu mới)
- Backend submodule: `TrustLens-Backend`
- Frontend submodule: `Frontend-TrustLens`
- Baseline ứng dụng: `1.2.0`
- API: `/api/v1`
- Trust Score: `trust-score-v1.2`

---

## 1. Mục tiêu phát hành

Phiên bản `1.2.1` là **patch release** nhằm chuyển TrustLens từ trạng thái “đã có các luồng chính nhưng thiếu bằng chứng nghiệm thu” sang trạng thái **release candidate có thể kiểm thử và trình diễn trong môi trường pilot có kiểm soát**.

Mục tiêu chính:

1. Khắc phục lỗi an toàn đăng ký tài khoản giảng viên.
2. Chứng minh các luồng cốt lõi chạy trên PostgreSQL thật.
3. Chứng minh phân quyền và cô lập dữ liệu giữa các giảng viên.
4. Bổ sung kiểm thử trình duyệt cho luồng end-to-end.
5. Chứng minh worker queue không xử lý trùng, có heartbeat và phục hồi job stale.
6. Chuẩn hóa hợp đồng lỗi và bằng chứng CI.
7. Hoàn tất restore drill và gói bằng chứng phát hành.
8. Đồng bộ phiên bản backend, frontend, root repository và tài liệu thành `1.2.1`.

### Nguyên tắc version

`1.2.1` không bổ sung năng lực sản phẩm mới và không thay đổi API theo cách phá vỡ tương thích.

Giữ nguyên:

- API prefix: `/api/v1`
- Trust Score version: `trust-score-v1.2`
- Bộ tiêu chí C1–C7 và trọng số hiện tại
- Kiến trúc modular monolith
- Database-backed processing queue

Nếu benchmark yêu cầu đổi trọng số, ngưỡng, công thức hoặc calibration profile có ảnh hưởng đến điểm, công việc đó phải chuyển sang `v1.3.0` và tăng phiên bản Trust Score.

---

## 2. Giả định phát hành

Kế hoạch này mặc định `v1.2.1` phục vụ **demo/cuộc thi hoặc controlled pilot**, không phải triển khai công khai quy mô lớn.

Trong `v1.2.1`:

- Tắt đăng ký công khai trong staging/production.
- Tài khoản pilot do admin tạo trước.
- Không sử dụng dữ liệu sinh viên thật chưa được ẩn danh trong CI hoặc demo.
- Không công bố hệ thống là production-ready nếu chưa đạt toàn bộ security gate.
- OCR, batch processing, LMS integration, multi-tenancy và distributed broker nằm ngoài phạm vi.

---

## 3. Phạm vi công việc

### 3.1. Trong phạm vi

| ID | Hạng mục | Mức | Owner chính | Kết quả đầu ra |
|---|---|---:|---|---|
| TL121-00 | Khóa baseline và release scope | P0 | Phan Tấn Sang | SHA root/backend/frontend, migration head, release profile |
| TL121-01 | Hạn chế đăng ký công khai | P0 | Backend + Frontend | Config, API behavior, UI behavior, integration tests |
| TL121-02 | PostgreSQL integration suite | P0 | Backend | Test suite trên PostgreSQL 16, migration evidence |
| TL121-03 | Ownership negative matrix | P0 | Backend | Test cô lập Course/Class/Assignment/Submission/Job/Report/Export |
| TL121-04 | Error contract normalization | P0 | Backend + Frontend | Contract tests và mapper lỗi dùng chung |
| TL121-05 | Worker lease, heartbeat và recovery | P0 | Backend | Concurrency/crash/stale recovery test report |
| TL121-06 | Frontend contract fixes | P0 | Frontend | Validation, error/loading/empty states, version UI |
| TL121-07 | Browser E2E smoke | P0 | Frontend + Backend | Playwright E2E cho core lecturer flow |
| TL121-08 | Restore drill | P0 | Backend/DevOps | Restore report, checksum và DB-file reconciliation |
| TL121-09 | CI và submodule release gate | P0 | Backend/Release owner | CI run URLs, pinned pointers, no skipped critical tests |
| TL121-10 | Benchmark evidence không đổi scoring | P1 | BA/NLP | Dataset card, fixed vectors, limitation note |
| TL121-11 | Version bump và release package | P0 | Cả nhóm | `1.2.1`, changelog, release notes, checklist |
| TL121-12 | RC validation và GO/NO-GO | P0 | Cả nhóm | `v1.2.1-rc.1`, decision record, stable tag nếu đạt |

### 3.2. Ngoài phạm vi

- OCR cho PDF scan.
- Batch analysis.
- WebSocket/SSE.
- Redis/Celery hoặc message broker phân tán.
- Multi-tenancy.
- LMS integration.
- Thay đổi công thức hoặc trọng số Trust Score.
- Production object storage.
- Mở đăng ký giảng viên đại trà.
- Tự động kết luận gian lận học thuật.
- Thay đổi `/api/v1` sang API mới.

---

## 4. Kế hoạch triển khai chi tiết

## Phase 0 — Scope lock và baseline

**Thời lượng:** 0,5 ngày  
**Phụ trách:** Phan Tấn Sang + Nguyễn Minh Trúc

### Công việc

- Tạo branch:
  - root: `release/v1.2.1`
  - backend: `fix/v1.2.1-release-gates`
  - frontend: `fix/v1.2.1-release-gates`
- Ghi nhận:
  - root commit SHA;
  - backend submodule SHA;
  - frontend submodule SHA;
  - Alembic migration head;
  - Node/Python/PostgreSQL versions.
- Chốt release profile: controlled pilot.
- Chốt quyết định registration: **tắt public registration ngoài development**.
- Tạo `CHANGELOG.md` hoặc `docs/v1.2/releases/v1.2.1.md`.
- Không tạo bản sao toàn bộ thư mục `docs/v1.2.1/`.

### Acceptance criteria

- Có baseline manifest được commit.
- Không còn hạng mục P0 chưa có owner.
- Scope không chứa tính năng mới thuộc minor release.

---

## Phase 1 — Security và API contract

**Thời lượng:** 1 ngày  
**Phụ trách:** Phan Tấn Sang, Trần Quỳnh Như

### TL121-01 — Registration safety

Phương án khuyến nghị cho patch release:

- Thêm `PUBLIC_REGISTRATION_ENABLED`.
- `development`: có thể bật.
- `staging/production`: mặc định tắt.
- Khi tắt, `POST /api/v1/auth/register` trả `403` với error code ổn định, ví dụ `AUTH_PUBLIC_REGISTRATION_DISABLED`.
- Admin tạo tài khoản pilot qua admin API hiện hữu hoặc seed script kiểm soát.
- Frontend ẩn link đăng ký hoặc hiển thị thông báo “Tài khoản do quản trị viên cấp”.
- Không tạo trực tiếp tài khoản lecturer active từ public endpoint.

### TL121-04 — Error contract

Hợp đồng chuẩn:

```json
{
  "error_code": "STRING_CODE",
  "message": "Human-readable message",
  "details": {},
  "retryable": false,
  "correlation_id": "uuid"
}
```

Bổ sung contract tests cho:

- auth;
- courses/classes/assignments;
- submissions/upload;
- analyze/jobs/retry;
- reports/exports;
- admin;
- validation errors;
- unhandled errors.

### TL121-06 — Frontend contract fixes

- Đồng bộ yêu cầu mật khẩu frontend với backend.
- Không hard-code `minLength=6` khi backend yêu cầu 10.
- Tạo error mapper dùng `error_code`, `message`, `correlation_id`.
- Kiểm tra UI loading/error/empty/retry.
- Production build phải xác nhận `VITE_USE_MOCK !== true`.
- Hiển thị version runtime hoặc build version `1.2.1`.

### Acceptance criteria

- Không thể tự tạo active lecturer từ public internet trong staging/production.
- Frontend không hướng dẫn người dùng nhập mật khẩu yếu hơn contract backend.
- Tất cả endpoint family trả cùng error schema.

---

## Phase 2 — PostgreSQL, authorization và worker reliability

**Thời lượng:** 2 ngày  
**Phụ trách:** Phan Tấn Sang

### TL121-02 — PostgreSQL integration

Test trên PostgreSQL 16, không dùng SQLite cho:

- UUID;
- JSONB;
- partial unique index;
- row locking;
- `SKIP LOCKED`;
- transaction rollback;
- migration lifecycle.

Bắt buộc:

1. `alembic upgrade head` từ database rỗng.
2. Upgrade từ snapshot migration trước.
3. Auth → upload → analyze → worker → report → export.
4. Retry job thất bại.
5. Một active job trên mỗi submission.
6. Completed job bắt buộc có `report_id`.
7. Rollback không để file accepted mồ côi.

### TL121-03 — Ownership negative tests

Lecturer B không được:

- đọc/sửa Course của Lecturer A;
- đọc/sửa Class;
- đọc/sửa Assignment;
- đọc/xóa/analyze Submission;
- đọc/retry Processing Job;
- đọc Report;
- tải Export hoặc file;
- suy luận owner qua response body.

Quy tắc response:

- `403` hoặc `404`;
- không lộ email owner, file path, storage key, report summary hoặc provider payload;
- audit ghi nhận denied event, không ghi successful action.

### TL121-05 — Worker reliability

Các test bắt buộc:

1. Hai worker claim cùng một job — chỉ một worker thành công.
2. Kill worker sau claim — stale recovery hoạt động.
3. Job dài cập nhật heartbeat.
4. Heartbeat còn hiệu lực không bị requeue.
5. Retry tạo lineage mới.
6. Hai analyze request không tạo hai active jobs.
7. Report persistence failure không thể để job `COMPLETED`.

### Acceptance criteria

- PostgreSQL integration test pass ổn định ít nhất hai lần liên tiếp.
- Không có IDOR trong ownership matrix.
- Không có duplicate report/job.
- Worker metrics phản ánh queued/running/failed/stale.

---

## Phase 3 — Browser E2E và fixture corpus

**Thời lượng:** 1 ngày  
**Phụ trách:** Trần Quỳnh Như + Phan Tấn Sang + Nguyễn Minh Trúc

### Công cụ đề xuất

- Playwright.
- PostgreSQL 16 service.
- Backend API và worker chạy riêng.
- Provider ngoài dùng mock server deterministic.
- Mock UI bị tắt.

### E2E-01 — Core lecturer flow

```text
Login
→ create course
→ create class
→ create assignment
→ upload DOCX
→ analyze
→ poll real progress
→ open report by report_id
→ export PDF/DOCX/XLSX
→ reload and retain job state
```

Lặp thêm với PDF text layer.

### E2E-02 — Error recovery

- unsupported file;
- PDF không có text layer;
- provider degraded;
- failed job;
- retry;
- cross-user access.

### Fixture tối thiểu

- DOCX APA clean.
- DOCX IEEE mixed.
- DOCX không có references.
- PDF text layer.
- PDF scan.
- PDF encrypted/malformed.
- Oversized file.
- MIME spoof.
- Duplicate DOI/URL.
- Provider timeout.

### Acceptance criteria

- Không có request tới mock data.
- Không có false-success UI.
- Failed E2E lưu screenshot/video.
- Fixture có checksum, expected output và synthetic-data note.

---

## Phase 4 — Restore, CI và evidence package

**Thời lượng:** 1 ngày  
**Phụ trách:** Phan Tấn Sang + Nguyễn Minh Trúc

### TL121-08 — Restore drill

- Backup PostgreSQL.
- Backup accepted files, exports và metadata cần thiết.
- Restore sang environment tách biệt.
- Chạy migration validation.
- Đối chiếu DB record với file checksum.
- Kiểm tra submission/report/export sau restore.
- Ghi RPO/RTO thực tế.
- Lập danh sách orphan/missing file nếu có.

### TL121-09 — CI release gate

Root CI phải:

- checkout recursive submodules;
- xác nhận submodule SHA đúng baseline;
- chạy backend lint/test/migration/audit;
- chạy frontend lint/component/E2E/build/audit;
- lưu test artifacts;
- không bỏ qua P0 tests.

CI commands mục tiêu:

```bash
# Backend
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
npm audit --audit-level=high
```

### Evidence package

- backend CI URL;
- frontend CI URL;
- docs CI URL;
- migration log;
- PostgreSQL integration report;
- ownership report;
- worker concurrency/crash report;
- E2E report;
- export parser report;
- restore report;
- dependency audit;
- skipped-test register.

---

## Phase 5 — Version bump và release

**Thời lượng:** 0,5–1 ngày  
**Phụ trách:** Cả nhóm

### Version changes

Backend:

- `APP_VERSION=1.2.1`
- README/env example.
- Health/root response.

Frontend:

- `package.json` → `1.2.1`
- `package-lock.json` → `1.2.1`
- build/runtime version display.

Root/docs:

- release notes;
- changelog;
- release checklist;
- source baseline;
- current submodule pointers;
- known limitations.

Không đổi:

- `/api/v1`;
- `trust-score-v1.2`;
- C1–C7;
- scoring weights/thresholds.

### Release sequence

1. Merge backend PR.
2. Tag backend `v1.2.1`.
3. Merge frontend PR.
4. Tag frontend `v1.2.1`.
5. Cập nhật root submodule pointers.
6. Chạy full root CI.
7. Tag root `v1.2.1-rc.1`.
8. Thực hiện smoke test pilot.
9. GO/NO-GO.
10. Nếu GO, tag stable `v1.2.1`.

---

## 5. Lịch đề xuất 08–14/07/2026

| Ngày | Công việc chính | Đầu ra |
|---|---|---|
| 08/07 | Scope lock, baseline, branch, registration decision | Baseline manifest |
| 09/07 | Registration safety, error contract, frontend validation | Security/API PRs |
| 10/07 | PostgreSQL integration và migration tests | DB test report |
| 11/07 | Ownership matrix và worker concurrency/recovery | Security/queue report |
| 12/07 | Playwright E2E, fixture corpus, UI fixes | E2E artifacts |
| 13/07 | Restore drill, CI, version bump, release notes | `v1.2.1-rc.1` |
| 14/07 | Pilot smoke, defect fixes, GO/NO-GO | `v1.2.1` hoặc NO-GO |

### Điều kiện lịch khả thi

Lịch trên chỉ khả thi khi:

- không thêm OCR, batch, LMS hoặc scoring logic mới;
- registration dùng phương án “disable public registration” thay vì xây approval workflow đầy đủ;
- provider ngoài được stub deterministic trong CI;
- cả ba thành viên làm song song theo owner.

---

## 6. Phân công

### Phan Tấn Sang — Backend / Release owner

- Registration config và API.
- PostgreSQL integration.
- Ownership negative tests.
- Worker concurrency/recovery.
- Migration/backup/restore.
- Root CI và submodule pointers.
- Tag/release.

### Trần Quỳnh Như — Frontend owner

- Register UX theo policy mới.
- Password/error contract.
- Version display.
- Component test setup.
- Playwright E2E.
- Error/loading/empty/retry states.
- Mock-mode production guard.

### Nguyễn Minh Trúc — BA / Academic / QA evidence

- Chốt acceptance criteria.
- Fixture corpus và expected results.
- Fixed Trust Score vectors.
- Benchmark report không thay scoring.
- Disclaimer/limitations.
- Evidence register.
- Release notes và GO/NO-GO checklist.

---

## 7. Definition of Done

`v1.2.1` chỉ được phát hành stable khi:

- [ ] Backend, frontend và root cùng version `1.2.1`.
- [ ] API vẫn `/api/v1`.
- [ ] Trust Score vẫn `trust-score-v1.2`.
- [ ] Public registration không tạo active lecturer trong staging/production.
- [ ] PostgreSQL migration và integration tests pass.
- [ ] Ownership negative matrix pass.
- [ ] Error contract tests pass.
- [ ] Worker concurrency/crash/stale recovery pass.
- [ ] Browser E2E pass với mock UI tắt.
- [ ] PDF/DOCX/XLSX export mở được bằng parser.
- [ ] Restore drill và DB-file reconciliation hoàn tất.
- [ ] Production build không bật mock mode.
- [ ] Không còn Critical/High defect trong release scope.
- [ ] Known limitations và disclaimer được cập nhật.
- [ ] Evidence register có link, owner và ngày.
- [ ] Release decision là `GO`.

Nếu một điều kiện P0 không đạt, chỉ được giữ tag `v1.2.1-rc.x`; không phát hành stable.

---

## 8. Rủi ro chính và biện pháp

| Rủi ro | Mức | Biện pháp |
|---|---:|---|
| Xây approval lifecycle quá lớn cho patch | High | Tắt public registration trong pilot; chuyển workflow đầy đủ sang v1.3 |
| E2E flaky do API ngoài | High | Mock server/cassette deterministic |
| SQLite che lỗi PostgreSQL | High | Bắt buộc PostgreSQL 16 trong integration/E2E |
| Frontend chưa có test scripts | High | Thêm Vitest/Playwright trước khi gọi release-ready |
| Submodule pointer không khớp | High | SHA assertion trong root CI |
| Benchmark dẫn đến đổi scoring | Medium | Chỉ ghi bằng chứng; đổi scoring chuyển sang v1.3 |
| Restore có DB record nhưng thiếu file | High | Checksum manifest và reconciliation report |
| Demo dùng dữ liệu thật | High | Chỉ dùng synthetic/anonymized fixtures |
| Lịch 7 ngày quá ngắn | High | Không mở rộng scope; NO-GO nếu thiếu evidence |

---

## 9. Các issue/PR đề xuất

### Backend

1. `fix(auth): disable public registration outside development`
2. `test(db): add PostgreSQL integration and migration suite`
3. `test(authz): add cross-user ownership negative matrix`
4. `test(api): enforce structured error contract`
5. `test(worker): prove lease heartbeat concurrency and stale recovery`
6. `ops: add backup restore drill scripts and evidence template`
7. `chore(release): bump backend to 1.2.1`

### Frontend

1. `fix(auth): align register UX and password contract`
2. `test(ui): add component test runner`
3. `test(e2e): add Playwright core lecturer flow`
4. `fix(ui): normalize API error display`
5. `chore(release): bump frontend to 1.2.1`

### Root

1. `ci: enforce submodule SHA and full release gates`
2. `docs: add v1.2.1 release notes and evidence register`
3. `chore(release): update submodule pointers for v1.2.1`
