# 01. Tổng quan Sản phẩm TrustLens

## 1. Bài toán

Giảng viên và người đánh giá báo cáo thường phải kiểm tra thủ công danh mục tài liệu tham khảo để xác định:

- Tài liệu có tồn tại và truy vết được hay không.
- Metadata citation có khớp nguồn học thuật hay không.
- Nguồn có phù hợp chủ đề và đủ cập nhật hay không.
- Danh mục có sai style, trùng lặp hoặc tập trung quá mức không.

TrustLens hỗ trợ sàng lọc và thẩm định các tín hiệu này; hệ thống không thay thế phán đoán học thuật của chuyên gia.

## 2. Tuyên bố giá trị

TrustLens tiếp nhận PDF/DOCX, trích xuất tài liệu tham khảo, đối chiếu metadata, chấm điểm có giải thích và tạo report gồm:

- Bằng chứng metadata.
- Trust Score và Confidence Score.
- Cảnh báo và khuyến nghị.
- Kết quả theo từng citation.
- Export PDF, DOCX, XLSX.

## 3. Người dùng

| Nhóm | Nhu cầu | Trạng thái |
|---|---|---|
| Giảng viên | Quản lý lớp/assignment, upload, analyze, report | Implemented |
| Quản trị viên | User/provider/audit/AI diagnostics | Implemented/Partial |
| Sinh viên | Tự nộp bài và xem phản hồi | Deferred |
| QA/vận hành | Theo dõi lỗi, test pipeline, provider | Partial |
| LMS | Đồng bộ lớp, assignment, submission | Planned |

## 4. Năng lực hiện tại

### Quản lý học thuật

- Tạo/liệt kê học phần.
- Tạo, cập nhật, xóa, liệt kê lớp.
- Tạo, cập nhật, liệt kê assignment.
- Giới hạn theo giảng viên sở hữu hoặc admin.

### Tiếp nhận tài liệu

- Nhận PDF/DOCX.
- Kiểm tra extension, MIME, kích thước và file rỗng.
- Sinh checksum.
- Lưu File, Submission và Processing Job.

### Phân tích

1. Validate.
2. Extract.
3. Detect reference section.
4. Parse citation.
5. Normalize.
6. Verify metadata.
7. Score.
8. Build report.
9. Audit.

### Báo cáo

- Xem điểm tổng và theo citation.
- Xem warning, evidence, confidence.
- Export PDF/DOCX/XLSX.
- Xem report history ở mức API.

### Quản trị

- Quản lý user.
- Xem audit.
- Quản lý metadata provider.
- Diagnose relevance và AI health.

## 5. Không được tuyên bố đã hoàn thành

- OCR.
- Plagiarism verdict.
- Full-text validation sau paywall.
- Citation-in-context.
- Batch processing.
- Durable distributed queue.
- Realtime WebSocket/SSE.
- Student portal đầy đủ.
- LMS integration.
- Multi-tenancy.
- Production autoscaling/WAF/DR hoàn chỉnh.

## 6. Nguyên tắc sản phẩm

### Hỗ trợ quyết định, không thay chuyên gia

Điểm cao không chứng minh nội dung khoa học đúng; điểm thấp không chứng minh gian lận.

### Không đồng nhất “không tìm thấy” với “giả”

`NOT_FOUND` chỉ có nghĩa provider chưa trả về metadata phù hợp. Nguyên nhân có thể là chưa index, metadata sai, loại nguồn không được bao phủ, giới hạn truy cập hoặc lỗi mạng.

### Evidence-first

Mỗi điểm thành phần phải có reason, evidence và confidence.

### Versioned scoring

Report phải lưu phiên bản cấu hình điểm.

### Backend là ranh giới bảo mật

Ẩn route/nút ở frontend không thay thế kiểm tra quyền ở API.

## 7. Chỉ số thành công đề xuất

| Nhóm | Chỉ số |
|---|---|
| Tính đúng | Precision/recall parser và metadata match |
| Hiệu năng | Thời gian tạo job và xử lý theo trang/citation |
| Tin cậy | Tỷ lệ job hoàn tất/retry/provider lỗi |
| Giải thích | Tỷ lệ component có evidence/confidence |
| Bảo mật | Truy cập chéo ownership bị chặn |
| Khả dụng | Export thành công, file xử lý được |
| Học thuật | Mức đồng thuận với đánh giá chuyên gia |

## 8. Mức trưởng thành

TrustLens đã có pipeline thật, nhưng chưa production-ready do background task chưa durable, retry chưa thống nhất, thiếu bằng chứng integration/E2E, mock fallback có thể che lỗi, bảo mật production còn khoảng trống và chưa có benchmark học thuật công khai.
