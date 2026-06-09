
| **Trạng thái**    | **Điều kiện vào trạng thái**                                   | **Hành động tiếp theo**                                          |
|-------------------|----------------------------------------------------------------|------------------------------------------------------------------|
| UPLOADED          | File đã được tải lên và lưu metadata cơ bản.                   | Chờ validate file.                                               |
| VALIDATING        | Backend kiểm tra MIME type, kích thước, hash, quyền truy cập.  | Nếu hợp lệ chuyển sang QUEUED, nếu lỗi chuyển FAILED_VALIDATION. |
| QUEUED            | Job phân tích đã được tạo trong queue.                         | Worker nhận job.                                                 |
| EXTRACTING        | Worker đang trích xuất text và layout.                         | Chuyển sang PARSING nếu trích xuất được reference section.       |
| PARSING           | Đang tách citation và chuẩn hóa trường dữ liệu.                | Chuyển sang VERIFYING.                                           |
| VERIFYING         | Đang tra cứu metadata external/internal.                       | Chuyển sang SCORING.                                             |
| SCORING           | Đang tính điểm thành phần và Trust Score.                      | Chuyển sang COMPLETED.                                           |
| COMPLETED         | Có kết quả phân tích đầy đủ hoặc một phần.                     | Cho phép xem dashboard và export.                                |
| PARTIAL_COMPLETED | Một số citation hoặc metadata source lỗi nhưng vẫn có kết quả. | Hiển thị cảnh báo unknown và cho phép retry.                     |
| FAILED            | Pipeline lỗi không phục hồi được.                              | Hiển thị lý do và cho phép upload lại/retry.                     |

