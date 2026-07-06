# 09. Bảo mật và Quyền riêng tư

## 1. Tài sản và rủi ro

Tài sản: tài khoản/token, file report, extracted text, class/submission, metadata, score/report, audit, provider secret, database/storage.

Rủi ro: IDOR, role escalation, upload độc hại, đoán path, XSS lấy token, default secret, gửi dữ liệu quá mức cho provider, mock che lỗi, log lộ nội dung, CORS rộng và brute force.

## 2. Kiểm soát đã có

- JWT access/refresh.
- User inactive bị từ chối khi refresh.
- Backend permission dependency.
- Ownership check cho class/assignment/submission/job/report/export.
- Upload validation: extension, MIME, size, empty, safe name, checksum.
- Audit cho sign-up, login, profile, upload, analyze, delete, admin.
- Axios dev logger redact token/password/secret.

## 3. Khoảng trống

### Default secret

Production phải fail-fast khi secret thiếu, bằng default, quá ngắn hoặc đã lộ.

### Password/rate limit

Mức 6 ký tự là thấp. Cần length mạnh hơn, block password phổ biến, throttling/lockout, reset an toàn và email verification nếu áp dụng.

### Token storage

`localStorage` tăng tác động XSS. Cần CSP, dependency audit, sanitization, refresh rotation/revocation và đánh giá cookie HttpOnly Secure SameSite.

### CORS

Regex mọi subdomain `vercel.app` quá rộng; production dùng allow-list deployment cụ thể.

### File malware

MIME/extension không thay malware scan. Cần quarantine, scan, reject active content, quota và tách storage.

### Mock fallback

Ngoài explicit mock mode, backend lỗi phải hiển thị lỗi thật; không được báo thành công bằng dữ liệu giả.

### Background task

`BackgroundTasks` mất task khi process restart; đây là rủi ro integrity/reliability.

### Error detail

Production phải lọc path, stack trace, secret, raw provider payload nhạy cảm và report text.

## 4. Authorization matrix

| Hành động | Admin | Lecturer | Student |
|---|---:|---:|---:|
| Login | ✓ | ✓ | ✓ |
| Course | ✓ | ✓ | — |
| Class | ✓ | Theo ownership | — |
| Assignment | ✓ | Theo ownership | — |
| Upload/analyze | ✓ | Theo scope | — |
| Report/export | ✓ | Theo scope | — |
| User admin | ✓ | — | — |
| Audit/provider | ✓ | — | — |

## 5. Privacy by design

### Data minimization

- Chỉ gửi chunk/context cần thiết.
- Loại dữ liệu nhận dạng không cần.
- Không lưu/log raw AI input mặc định.

### Purpose limitation

File chỉ dùng để trích xuất citation, verify metadata, score và tạo report. Không tái sử dụng huấn luyện model khi chưa có cơ sở/ thông báo phù hợp.

### Retention cần khóa

| Dữ liệu | Retention | Xóa |
|---|---|---|
| File gốc | TBD | Soft delete → purge |
| Extracted text | TBD | Theo submission |
| Metadata response | TBD | Cache có hạn |
| Report/export | TBD | Theo course policy |
| Audit | Dài hơn nghiệp vụ | Controlled purge |
| Token/session | Ngắn | Expiry/revoke |

### Provider ngoài

Phải công bố provider nhận dữ liệu gì, vùng lưu, retention, training use, opt-out, subprocessor và quản lý API key.

## 6. Production controls

TLS, secret manager, least-privilege DB, private object storage, authorized download, malware scan, rate limit, CSP/security headers, dependency/SAST/secret scan, encrypted backup, restore drill, tamper-evident audit, abnormal login monitoring và incident response.

## 7. Security tests

1. IDOR trên mọi resource.
2. Role escalation.
3. Token giả/hết hạn/refresh replay.
4. MIME spoof/path traversal/oversize/malformed PDF.
5. Stored file guessing.
6. Invalid CORS origin.
7. Brute force.
8. Error information leak.
9. Mock disabled outside mock mode.
10. Provider secret redaction.
11. Access deleted resources.

## 8. Nguyên tắc kết luận

Request trả 200 nhưng lộ dữ liệu user khác vẫn là thất bại. UI báo thành công bằng mock khi backend thất bại vẫn là thất bại toàn vẹn.
