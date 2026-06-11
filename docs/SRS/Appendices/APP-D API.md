# APP-D.1 API Contract

| **Method** | **Endpoint**                  | **Mục đích**                      | **Request chính**              | **Response chính**                    |
|------------|-------------------------------|-----------------------------------|--------------------------------|---------------------------------------|
| POST       | /api/auth/login               | Đăng nhập                         | email, password                | access_token, refresh_token, user     |
| POST       | /api/auth/refresh             | Làm mới token                     | refresh_token                  | access_token                          |
| GET        | /api/users/me                 | Lấy thông tin người dùng hiện tại | \-                             | user profile + permissions            |
| GET        | /api/courses                  | Danh sách học phần                | filters                        | course list                           |
| POST       | /api/courses                  | Tạo học phần                      | course payload                 | course                                |
| GET        | /api/classes                  | Danh sách lớp                     | filters                        | class list                            |
| POST       | /api/classes                  | Tạo lớp                           | class payload                  | class                                 |
| GET        | /api/assignments              | Danh sách assignment              | class_id                       | assignment list                       |
| POST       | /api/assignments              | Tạo assignment                    | assignment payload             | assignment                            |
| POST       | /api/submissions/upload       | Upload file báo cáo               | multipart file + assignment_id | submission + file + initial job state |
| GET        | /api/submissions/{id}         | Xem submission                    | submission_id                  | submission detail                     |
| POST       | /api/submissions/{id}/analyze | Tạo job phân tích                 | submission_id                  | job_id                                |
| GET        | /api/jobs/{id}                | Theo dõi trạng thái job           | job_id                         | status, progress, step, error_code    |
| POST       | /api/jobs/{id}/retry          | Retry job lỗi                     | job_id                         | new job_id                            |
| GET        | /api/reports/{submission_id}  | Lấy report dashboard              | submission_id                  | report JSON                           |
| GET        | /api/reports/{id}/citations   | Danh sách citation chi tiết       | report_id + filters            | citation rows                         |
| GET        | /api/reports/{id}/export      | Xuất báo cáo                      | format=pdf/docx/xlsx           | file download                         |
| POST       | /api/reports/{id}/notes       | Thêm ghi chú giảng viên           | note payload                   | note                                  |
| GET        | /api/admin/scoring-configs    | Danh sách cấu hình điểm           | \-                             | config list                           |
| POST       | /api/admin/scoring-configs    | Tạo cấu hình điểm                 | weights, thresholds            | config                                |
| GET        | /api/admin/audit-logs         | Xem audit log                     | filters                        | log list                              |
| GET        | /api/health                   | Health check                      | \-                             | status                                |

## APP-D.2 API Error Convention

Quy tắc đặt tên mã lỗi

- FILE\_\*: lỗi upload, định dạng, dung lượng, file hỏng.
- EXTRACT\_\*: lỗi trích xuất text, layout, scan/OCR.
- CITATION\_\*: lỗi tách citation, thiếu trường, sai style.
- METADATA\_\*: lỗi provider, DOI, title match.
- SCORING\_\*: lỗi trọng số, scoring config, missing score.
- AUTH\_\*: lỗi xác thực/phân quyền.
- EXPORT\_\*: lỗi xuất báo cáo.

```json
{  
	"error_code": "FILE_UNSUPPORTED_TYPE",  
	"message": "File không thuộc định dạng được hỗ trợ.",  
	"details": {  
		"allowed_types": ["application/pdf", "application/vnd.openxmlformats-officedocument.wordprocessingml.document"],  
		"received_type": "image/png"  
	},  
	"trace_id": "req_20260607_0001"  
}
```


# Error Handling Flow

Xem tại [[D27. API Error Handling Flow]]

| **HTTP status** | **Trường hợp**                    | **Ví dụ error_code**                     |
| --------------- | --------------------------------- | ---------------------------------------- |
| 400             | Request sai dữ liệu               | VALIDATION_ERROR, INVALID_SCORING_WEIGHT |
| 401             | Chưa xác thực hoặc token hết hạn  | UNAUTHORIZED, TOKEN_EXPIRED              |
| 403             | Không đủ quyền                    | FORBIDDEN_ROLE, FORBIDDEN_OWNERSHIP      |
| 404             | Không tìm thấy tài nguyên         | SUBMISSION_NOT_FOUND, REPORT_NOT_FOUND   |
| 409             | Xung đột dữ liệu                  | DUPLICATE_EMAIL, DUPLICATE_FILE          |
| 413             | File quá lớn                      | FILE_TOO_LARGE                           |
| 415             | Sai định dạng file                | FILE_UNSUPPORTED_TYPE                    |
| 429             | Rate limit provider hoặc hệ thống | PROVIDER_RATE_LIMITED                    |
| 500             | Lỗi hệ thống                      | INTERNAL_ERROR                           |
| 503             | Dịch vụ phụ thuộc không sẵn sàng  | METADATA_PROVIDER_UNAVAILABLE            |

![[D27-API-error-handling-flow.png]]