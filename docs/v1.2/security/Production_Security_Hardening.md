# Production Security Hardening v1.2

**Mục tiêu:** xác định các kiểm soát bắt buộc trước khi TrustLens xử lý dữ liệu thật ngoài controlled pilot.
**Lưu ý:** tài liệu này không thay thế pentest hoặc đánh giá pháp lý.

## 1. Security release gates

Không release production khi còn một trong các điều kiện sau:

- secret mặc định hoặc secret xuất hiện trong repository/log;
- CORS cho phép wildcard hoặc mọi `*.vercel.app` với credentials;
- chưa có ownership negative tests;
- upload chưa qua malware scanner approved;
- không có encrypted backup và restore evidence;
- frontend production bật mock;
- lỗi `Critical/High` từ dependency scan chưa được xử lý hoặc chấp nhận rủi ro;
- token/session không có revoke/expiry policy;
- file storage có thể truy cập trực tiếp bằng path đoán được.

## 2. Authentication và session

### Bắt buộc

- Password policy tối thiểu theo cấu hình và block password phổ biến.
- Access token ngắn hạn; refresh token rotation và replay rejection.
- Logout revoke server-side.
- Disable hoặc revoke toàn bộ session khi tài khoản bị vô hiệu hóa.
- Log sự kiện auth nhưng không log password/token.

### Production hardening

Đánh giá chuyển refresh token sang cookie:

```text
HttpOnly
Secure
SameSite=Lax hoặc Strict theo luồng ứng dụng
Path giới hạn
CSRF protection nếu dùng cookie authentication
```

Nếu tiếp tục dùng browser storage, phải có CSP mạnh, dependency control và threat model XSS được phê duyệt.

## 3. Rate limiting và trusted proxy

Limiter in-memory chỉ phù hợp single-process pilot. Production phải dùng shared store.

Yêu cầu:

- key theo scope + identity + trusted client IP;
- cấu hình trusted proxy;
- không tin `X-Forwarded-For` trực tiếp từ internet;
- giới hạn login/register/refresh và endpoint tốn tài nguyên;
- metric cho blocked requests;
- không tiết lộ email tồn tại qua response khác biệt không cần thiết.

## 4. CORS và security headers

Production dùng exact-origin allow-list, ví dụ:

```env
CORS_ORIGINS=https://trustlens.example.edu
```

Không dùng regex cho toàn bộ nền tảng hosting dùng chung.

Header tối thiểu:

```text
Content-Security-Policy
X-Content-Type-Options: nosniff
Referrer-Policy
Permissions-Policy
Strict-Transport-Security
frame-ancestors qua CSP
```

CSP phải được kiểm thử với React build, API, font/image và download flow thực tế.

## 5. Upload và file storage

### Validation layers

1. extension allow-list;
2. size limit;
3. magic-byte/signature;
4. structured format validation;
5. quarantine;
6. malware scan;
7. accepted storage promotion;
8. analysis gate;
9. retention/cleanup.

Local policy scanner chỉ là wiring kiểm soát, không phải production malware scanner.

Production scanner phải có:

- signature version;
- scanner result provenance;
- timeout và unavailable policy;
- infected/suspicious/failed states;
- quarantine cleanup;
- archive bomb và active-content policy;
- audit event;
- không cho phép manual bypass không ghi nhận.

File phải nằm ngoài public web root; download đi qua authorization backend hoặc signed URL ngắn hạn.

## 6. Authorization và IDOR

Backend là enforcement point. Frontend route guard chỉ hỗ trợ UX.

Bắt buộc test cross-user cho:

- course;
- class;
- assignment;
- submission;
- file;
- processing job;
- report;
- export;
- audit/admin resource.

Không trả ownership metadata trong lỗi cho user không có quyền.

## 7. AI/provider privacy

- Chỉ gửi dữ liệu cần thiết cho task.
- Loại PII không cần thiết.
- Không log raw report text hoặc embedding input mặc định.
- Ghi provider, model, prompt version, fallback và confidence.
- Xác định retention, training use, region và subprocessor của provider.
- Có chế độ local/fallback khi không được phép gửi dữ liệu ra ngoài.

## 8. Database, secrets và storage

- DB user least privilege.
- TLS cho database ngoài local.
- Secret manager hoặc environment injection; không commit `.env`.
- Backup encrypted, off-site và access audited.
- Object/file storage private.
- Rotation plan cho `SECRET_KEY`, provider keys và database credentials.

## 9. Logging và error handling

Log structured fields:

```text
timestamp level correlation_id user_id action resource_type resource_id
job_id stage duration_ms status_code error_code
```

Không log:

- password;
- access/refresh token;
- API key;
- full report text;
- raw provider payload nhạy cảm;
- local filesystem path trong response.

Mọi response lỗi production phải qua schema chuẩn và có `correlation_id`.

## 10. Security test checklist

- [ ] Invalid/expired/forged access token.
- [ ] Refresh replay và revoked token.
- [ ] Role escalation.
- [ ] Cross-user IDOR trên mọi resource.
- [ ] Brute force/rate limit qua nhiều replicas.
- [ ] Invalid CORS origin.
- [ ] XSS/CSP smoke.
- [ ] MIME spoof, malformed PDF, macro DOCX, EICAR.
- [ ] Path traversal và stored-name guessing.
- [ ] Error information leak.
- [ ] Secret scan và dependency audit.
- [ ] Deleted resource access.
- [ ] Backup access và restore authorization.

## 11. Risk acceptance

Mọi kiểm soát chưa triển khai phải có:

- risk owner;
- mức độ;
- phạm vi ảnh hưởng;
- biện pháp tạm thời;
- hạn xử lý;
- điều kiện không được triển khai production.

## 12. Control Acceptance Matrix

| Control | Minimum production requirement | Acceptance evidence |
|---|---|---|
| CORS | Exact origin allow-list; no wildcard with credentials | Invalid-origin integration test and deployed config review |
| CSP | Explicit `default-src`, `script-src`, `connect-src`, `img-src`, `font-src`, `frame-ancestors` | React production build smoke and browser CSP violation check |
| Token storage | Access token short lived; refresh rotation/replay rejection; cookie decision documented | Auth integration tests for refresh replay/logout/revocation |
| Cookie mode | If refresh cookie is used: `HttpOnly`, `Secure`, `SameSite`, path scoped, CSRF control | Browser auth flow plus CSRF negative test |
| Shared rate limit | Redis/shared store or equivalent; trusted proxy parsing only | Two-replica rate-limit test with same quota bucket |
| Malware scanner | Approved production scanner, timeout/unavailable/infected/suspicious states | EICAR test, scanner unavailable test, quarantine cleanup test |
| IDOR | Backend ownership checks for all resource IDs | Ownership negative test matrix pass |
| Secret management | No default/placeholder secrets; env/secret manager injection; rotation plan | Secret scan, startup fail-fast, rotation runbook |
| Error redaction | Standard error schema; no stack/path/token/provider payload leaks | Error response snapshot and log redaction test |

## 13. Shared Rate Limit Requirements

In-memory limiting is acceptable only for local development and single-process pilot.
Production must use a shared store.

Required behavior:

- quota key includes route group, authenticated user or anonymous identity, and trusted
  client IP;
- login/register/refresh/upload/analyze/export/admin endpoints have separate buckets;
- `X-Forwarded-For` is trusted only from configured proxy networks;
- rate-limit responses use the standard error schema and do not reveal whether an
  email/account exists;
- metrics expose allowed, blocked and store-error counts.

Release gate: a test must send requests through two API replicas and prove the quota
is shared.

## 14. Malware Scanner Production States

Scanner integration must distinguish:

| State | Behavior |
|---|---|
| `clean` | Promote from quarantine to accepted storage |
| `infected` | Reject, keep/delete according to quarantine policy, audit event required |
| `suspicious` | Reject or require manual review according to approved policy |
| `scanner_unavailable` | Fail closed for production uploads unless incident risk is accepted |
| `scan_timeout` | Fail closed and record retry/manual-review policy |
| `scan_error` | Fail closed with redacted error response |

Files must not be analyzed until scan state is `clean`.

## 15. Security Release Gate Summary

Release decision must be `NO-GO` when any of these are true:

- unresolved Critical/High dependency or code security issue without approved risk;
- ownership negative tests are missing or failing;
- production scanner is absent for real user uploads;
- CORS allows broad shared-hosting domains with credentials;
- production frontend can enable mock mode;
- secrets are default, weak, committed or logged;
- error responses leak owner metadata, local file paths, tokens or provider payloads;
- backup exists but restore has not been proven for production/pilot data.
