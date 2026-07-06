# Kế hoạch hoàn thiện TrustLens v1.2

**Trạng thái tài liệu:** canonical completion backlog
**Mục tiêu:** chuyển TrustLens từ mức demo/pilot có điều kiện sang một bản v1.2 có thể nghiệm thu bằng bằng chứng.
**Nguyên tắc:** một hạng mục chỉ được ghi `Completed` khi có mã nguồn, migration nếu cần, kiểm thử thực thi và bằng chứng kết quả.

## 1. Phân loại trạng thái

| Trạng thái | Ý nghĩa |
|---|---|
| `Completed` | Đã triển khai và có bằng chứng kiểm tra phù hợp |
| `Partial` | Có implementation nhưng thiếu coverage, hardening hoặc bằng chứng vận hành |
| `Open` | Chưa triển khai hoặc chỉ có thiết kế |
| `Blocked` | Phụ thuộc quyết định, hạ tầng hoặc dữ liệu chưa có |

## 2. Điều kiện đóng mốc v1.2

TrustLens v1.2 chỉ được ghi `Release Candidate` khi đồng thời đạt các điều kiện sau:

1. Luồng `login → course → class → assignment → upload → analyze → report → export` chạy với PostgreSQL thật và không dùng mock.
2. Backend unit, integration, ownership-negative và migration tests pass trong CI.
3. Frontend có component/service tests tối thiểu và browser E2E smoke pass.
4. CI checkout đầy đủ frontend/backend submodule và là required check của nhánh bảo vệ.
5. Worker restart không làm mất job, không chạy trùng và có cơ chế stale-job recovery được kiểm thử.
6. Không có lỗi bảo mật `Critical` hoặc `High` chưa có quyết định chấp nhận rủi ro.
7. Trust Score lưu version, evidence, confidence và không được mô tả như phán quyết gian lận.
8. Có benchmark/calibration baseline hoặc giới hạn sử dụng được phê duyệt rõ ràng.
9. Có backup, restore drill và biên bản kết quả.
10. Tài liệu API, vận hành, giới hạn và checklist release khớp implementation.

## 3. Backlog bắt buộc

### V12-P0-01 — CI hỗ trợ Git submodule

- **Trạng thái:** `Open`.
- **Rủi ro:** workflow làm việc trong `apps/backend` và `apps/frontend`, nhưng checkout mặc định không bảo đảm submodule được tải.
- **Thay đổi yêu cầu:**

```yaml
- uses: actions/checkout@v4
  with:
    submodules: recursive
    fetch-depth: 0
```

- **Acceptance criteria:** backend, frontend và docs workflows chạy trên pull request mới; log chứng minh hai submodule được checkout đúng commit pointer.

### V12-P0-02 — PostgreSQL integration suite

- **Trạng thái:** `Open`.
- **Phạm vi:** auth, ownership, upload, analyze, job polling, report, export, retry và retention.
- **Acceptance criteria:** migration từ database rỗng pass; test dùng PostgreSQL 16; dữ liệu test được cô lập; không phụ thuộc provider bên ngoài không kiểm soát.

### V12-P0-03 — Browser E2E smoke

- **Trạng thái:** `Open`.
- **Phạm vi tối thiểu:** login, tạo course/class/assignment, upload DOCX/PDF, chạy analyze, theo dõi progress, mở report và tải export.
- **Acceptance criteria:** chạy trong CI; dùng backend mode; fail khi mock mode được bật ngoài cấu hình test riêng.

### V12-P0-04 — Ownership negative tests

- **Trạng thái:** `Open`.
- **Kịch bản:** Lecturer B không được đọc, sửa, phân tích, xuất hoặc retry tài nguyên của Lecturer A.
- **Acceptance criteria:** kiểm tra mọi resource ID trực tiếp; response là `403` hoặc `404` theo contract; không lộ metadata qua error body.

### V12-P0-05 — Contract batch analysis

- **Trạng thái:** `Open`.
- **Hiện trạng:** frontend có client `/analysis-batches`; backend canonical router chưa công bố contract tương ứng.
- **Quyết định bắt buộc:**
  - triển khai backend batch; hoặc
  - loại bỏ/ẩn UI và service batch khỏi v1.2.
- **Acceptance criteria:** không có frontend route/service production gọi endpoint không tồn tại.

### V12-P0-06 — Worker lease và heartbeat

- **Trạng thái:** `Partial`.
- **Vấn đề:** job có `heartbeat_at`, nhưng pipeline phải cập nhật heartbeat trong suốt quá trình xử lý dài.
- **Acceptance criteria:** heartbeat được cập nhật theo interval; stale recovery không requeue job còn sống; test hai worker không chạy cùng một job.

### V12-P1-01 — Production malware scanner

- **Trạng thái:** `Open`.
- **Yêu cầu:** tích hợp scanner được phê duyệt; timeout, scanner unavailable, infected và suspicious phải có state rõ ràng; file bị từ chối không được chuyển sang accepted storage.
- **Acceptance criteria:** có EICAR integration test và cleanup quarantine/orphan test.

### V12-P1-02 — Shared rate limiting

- **Trạng thái:** `Open`.
- **Yêu cầu:** limiter dùng Redis hoặc kho chia sẻ; chỉ tin `X-Forwarded-For` từ trusted proxy.
- **Acceptance criteria:** quota nhất quán qua ít nhất hai API replicas.

### V12-P1-03 — CSP, cookie và CORS

- **Trạng thái:** `Open`.
- **Yêu cầu:** CORS allow-list exact origin; CSP; security headers; đánh giá chuyển refresh token sang `HttpOnly Secure SameSite` cookie.
- **Acceptance criteria:** invalid origin test pass; CSP không chặn luồng ứng dụng; token không xuất hiện trong log.

### V12-P1-04 — Backup và restore drill

- **Trạng thái:** `Open`.
- **Yêu cầu:** backup PostgreSQL và file storage theo cùng consistency point; tài liệu RPO/RTO; restore vào môi trường tách biệt.
- **Acceptance criteria:** có biên bản restore, checksum file và đối soát report/submission.

### V12-P1-05 — Accessibility và frontend quality

- **Trạng thái:** `Open`.
- **Yêu cầu:** keyboard navigation, focus state, form label, contrast, error announcement, screen-reader semantics.
- **Acceptance criteria:** không có lỗi accessibility nghiêm trọng trên các màn hình login, upload, analyzing và report.

### V12-P1-06 — Benchmark và calibration

- **Trạng thái:** `Open`.
- **Yêu cầu:** dataset card, labeling guide, double annotation, fixed test vectors, benchmark report và scoring changelog.
- **Acceptance criteria:** công bố precision/recall hoặc agreement phù hợp từng component; không dùng một accuracy tổng hợp để che lỗi theo nhóm nguồn.

## 4. Thứ tự triển khai

| Thứ tự | Nhóm | Kết quả cần đạt |
|---:|---|---|
| 1 | CI submodule + migration | Quality gate thực sự chạy |
| 2 | Integration + ownership | Chứng minh backend contract và isolation |
| 3 | E2E + batch decision | Chứng minh luồng người dùng không mock |
| 4 | Worker heartbeat | Loại rủi ro duplicate processing |
| 5 | Security hardening | Đủ điều kiện pilot có dữ liệu thật |
| 6 | Backup/restore | Đủ bằng chứng phục hồi |
| 7 | Benchmark/calibration | Đủ cơ sở tuyên bố học thuật có giới hạn |
| 8 | Accessibility/performance | Hoàn thiện release quality |

## 5. Quy tắc cập nhật trạng thái

Mỗi thay đổi trạng thái phải ghi:

- commit hoặc pull request;
- test command và kết quả;
- môi trường thực thi;
- limitation còn lại;
- người phê duyệt hoặc owner hạng mục.

Không chuyển `Partial` thành `Completed` chỉ vì code compile hoặc file workflow tồn tại.

## 6. P0/P1 Status Ledger

Bang nay bo sung danh muc P0/P1 con thieu va la muc luc ra quyet dinh. Khi doi
trang thai, phai gan evidence vao commit/PR, CI run, test command, moi truong va
owner phe duyet.

| Priority | Item | Trang thai | Dieu kien chuyen trang thai |
|---|---|---|---|
| P0 | Canonical analyze/process/retry pipeline | `Completed` | Code path thong nhat da co; release gate van can PostgreSQL integration va E2E evidence |
| P0 | Explicit-only frontend mock mode | `Completed` | Production build khong bat mock; CI fail neu mock mode bi bat ngoai fixture rieng |
| P0 | Structured error contract | `Partial` | Contract tests phai xac nhan moi endpoint tra chung schema va co `correlation_id` |
| P0 | CI submodule checkout | `Open` | Pull request workflow log chung minh checkout backend/frontend submodule dung commit |
| P0 | PostgreSQL integration suite | `Open` | Integration tests chay tren PostgreSQL 16 voi migration tu database rong |
| P0 | Browser E2E smoke | `Open` | Browser E2E chay o backend mode va fail neu frontend fallback mock |
| P0 | Ownership negative tests | `Open` | Lecturer B khong doc/sua/analyze/export/retry duoc resource cua Lecturer A |
| P0 | Batch analysis contract | `Blocked` | Can quyet dinh scope: implement backend `/analysis-batches` hoac an batch UI/service khoi production |
| P0 | Worker heartbeat/lease evidence | `Partial` | Long-running stages cap nhat heartbeat; stale recovery va two-worker claim tests pass |
| P1 | Auth/session hardening | `Partial` | Cookie/CSP decision, shared rate limit va revoke/replay evidence duoc phe duyet |
| P1 | Production malware scanner | `Open` | Scanner approved, EICAR test, quarantine cleanup va unavailable policy pass |
| P1 | Shared rate limiting | `Open` | Rate limit dung shared store va nhat quan qua it nhat hai API replicas |
| P1 | Backup/restore drill | `Open` | Restore bien ban, file checksum va DB-file reconciliation hoan tat |
| P1 | Accessibility/frontend quality | `Open` | Component/service/accessibility/browser evidence duoc luu trong CI |
| P1 | Benchmark/calibration | `Blocked` | Can dataset governance, annotation owners, rubric va approval policy |
| P1 | Release readiness decision | `Open` | GO/NO-GO/GO WITH ACCEPTED RISKS record co owner, evidence va accepted-risk expiry |

## 7. Acceptance Criteria by Item

Moi backlog item phai dong theo cung mot mau toi thieu:

- **Implementation evidence:** route/service/model/migration/config da ton tai va duoc review.
- **Executable evidence:** unit/integration/E2E/security test command va ket qua pass.
- **Operational evidence:** metric, log, runbook, restore drill hoac scanner evidence neu lien quan.
- **Contract evidence:** OpenAPI/API docs, frontend service type va error/pagination/job-state schema khop nhau.
- **Risk evidence:** limitation, accepted risk, expiry date va owner neu chua the dat production-grade.

Mot item khong duoc chuyen sang `Completed` neu chi co tai lieu, UI mock, workflow
file, hoac unit test don le ma chua co evidence phu hop voi blast radius.

## 8. Implementation Priority

1. Fix CI checkout va PostgreSQL migration gate de dam bao evidence co nen tang.
2. Them PostgreSQL integration, ownership negative va contract tests.
3. Dong quyet dinh `/analysis-batches`: implement backend batch hoac remove/flag production UI.
4. Them browser E2E cho core lecturer flow voi PDF/DOCX fixtures.
5. Hoan thien worker heartbeat, lease, stale recovery va crash recovery tests.
6. Hoan thien security hardening: CORS/CSP/token/cookie/shared rate limit/scanner.
7. Lam backup/restore drill va operational metrics/alerts.
8. Khoa benchmark dataset, annotation protocol, fixed vectors va calibration report.
9. Chay release readiness checklist va lap GO/NO-GO record.

## 9. v1.2 Closure Conditions

v1.2 chi duoc dong khi tat ca P0 la `Completed`, P1 con lai co mot trong hai
trang thai hop le:

- `Completed` voi evidence day du; hoac
- `Partial/Open/Blocked` nhung co accepted-risk record ro rang, chi ap dung cho
  controlled pilot, khong anh huong bao mat du lieu, khong lam frontend overclaim
  nang luc Trust Score va co ngay het han.

Khong chap nhan `GO WITH ACCEPTED RISKS` cho Critical/High security issue, IDOR,
mat du lieu, production mock fallback, hoac Trust Score bi dien giai nhu phan
quyet gian lan.
