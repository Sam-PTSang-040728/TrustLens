# D08. Actor & Use Case Overview

```mermaid
flowchart LR
    Lecturer["Giảng viên"] --> UC1["Đăng nhập"]
    Lecturer --> UC2["Tạo lớp/học phần"]
    Lecturer --> UC3["Tạo assignment"]
    Lecturer --> UC4["Upload bài nộp"]
    Lecturer --> UC5["Chạy phân tích"]
    Lecturer --> UC6["Xem dashboard report"]
    Lecturer --> UC7["Thêm ghi chú"]
    Lecturer --> UC8["Xuất báo cáo"]

    Admin["Quản trị hệ thống"] --> UC9["Quản lý người dùng"]
    Admin --> UC10["Cấu hình trọng số"]
    Admin --> UC11["Quản lý metadata provider"]
    Admin --> UC12["Xem audit log"]

    Worker["Queue Worker"] --> UC13["Extract file"]
    Worker --> UC14["Verify metadata"]
    Worker --> UC15["Compute Trust Score"]

    Metadata["Metadata API"] --> UC16["Trả metadata candidate"]
```

# D11. Upload-to-Report Activity Flow

```mermaid
flowchart TD
    Start(["Start"]) --> Login{"Người dùng đã đăng nhập?"}
    Login -- "Không" --> Auth["Đăng nhập"]
    Auth --> SelectAssignment
    Login -- "Có" --> SelectAssignment["Chọn lớp và assignment"]

    SelectAssignment --> Upload["Upload file PDF/DOCX"]
    Upload --> Validate{"File hợp lệ?"}
    Validate -- "Không" --> Reject["Trả lỗi 400/413/415"] --> End1(["End"])
    Validate -- "Có" --> CreateSubmission["Tạo submission và lưu file"]
    CreateSubmission --> CreateJob["Tạo processing_job = QUEUED"]
    CreateJob --> Poll["UI polling trạng thái job"]
    CreateJob --> Worker["Worker nhận job"]

    Worker --> Extract["Extract text"]
    Extract --> Detect["Detect reference section"]
    Detect --> Split["Split citations"]
    Split --> Normalize["Normalize fields"]
    Normalize --> Verify["Metadata verification"]
    Verify --> Score["Compute scores"]
    Score --> Warning["Generate warnings"]
    Warning --> Report["Generate report JSON"]
    Report --> Completed["Job COMPLETED"]
    Completed --> Dashboard["UI hiển thị dashboard"]
    Dashboard --> Export["Export nếu người dùng chọn"]
    Export --> End2(["End"])
```


# APP-B.1 Use Case Exceptions

| **Mã** | **Tình huống**                                     | **Xử lý bắt buộc**                                                                                      |
|--------|----------------------------------------------------|---------------------------------------------------------------------------------------------------------|
| EX-01  | File PDF chỉ chứa ảnh scan, không trích được text. | Trả trạng thái NEED_OCR hoặc UNSUPPORTED_SCAN; không tính điểm sai; gợi ý dùng file text-based.         |
| EX-02  | Không phát hiện phần tài liệu tham khảo.           | Tạo cảnh báo NO_REFERENCE_SECTION; cho phép người dùng chọn vùng/nhập thủ công trong phiên bản mở rộng. |
| EX-03  | Metadata API timeout hoặc rate limit.              | Retry theo exponential backoff; nếu vẫn lỗi, gắn trạng thái UNKNOWN_METADATA và cho phép retry sau.     |
| EX-04  | Citation thiếu tiêu đề hoặc năm.                   | Vẫn lưu citation raw_text; gắn lỗi MISSING_REQUIRED_FIELD; giảm citation format score.                  |
| EX-05  | Trùng citation trong cùng bài.                     | Gắn cảnh báo DUPLICATE_REFERENCE; không tính trùng vào điểm toàn bài nếu cấu hình yêu cầu.              |
| EX-06  | File quá lớn hoặc định dạng không đúng.            | Từ chối upload, trả lỗi rõ ràng về giới hạn dung lượng/định dạng.                                       |
| EX-07  | Người dùng không có quyền xem assignment.          | Trả HTTP 403, ghi audit log.                                                                            |
| EX-08  | Một citation có metadata match mơ hồ.              | Gắn trạng thái AMBIGUOUS_MATCH, hiển thị top candidates và confidence.                                  |
