
| **ID** | **Rủi ro**                                    | **Tác động** | **Khả năng**    | **Biện pháp kiểm soát**                                                             |
|--------|-----------------------------------------------|--------------|-----------------|-------------------------------------------------------------------------------------|
| R-01   | Không trích xuất được text từ PDF scan        | Cao          | Trung bình      | Giới hạn MVP ở PDF text-based/DOCX; hiển thị NEED_OCR; chuẩn bị file demo phù hợp.  |
| R-02   | Citation có định dạng quá đa dạng             | Cao          | Cao             | Hỗ trợ rule cho APA/IEEE cơ bản; lưu raw_text; dùng confidence và fallback unknown. |
| R-03   | Metadata API rate limit/timeout               | Trung bình   | Cao             | Cache kết quả DOI/title; retry; fallback provider; cho phép partial result.         |
| R-04   | Relevance score không chính xác               | Trung bình   | Trung bình      | Dùng ngưỡng thận trọng; đánh nhãn needs review; kiểm thử trên dữ liệu mẫu thủ công. |
| R-05   | Điểm Trust Score bị xem là kết luận tuyệt đối | Cao          | Trung bình      | Thêm disclaimer và explanation; dùng ngôn ngữ hỗ trợ thẩm định.                     |
| R-06   | Không kịp triển khai quá nhiều chức năng      | Cao          | Cao             | Ưu tiên MVP end-to-end; các chức năng admin nâng cao chuyển SHOULD/COULD.           |
| R-07   | Thiếu dữ liệu demo hợp pháp                   | Cao          | Trung bình      | Tự tạo báo cáo mẫu và citation mẫu; dùng nguồn metadata công khai.                  |
| R-08   | Export lỗi layout                             | Trung bình   | Trung bình      | Ưu tiên export bảng đơn giản; test file xuất trước demo.                            |
| R-09   | Sai quyền truy cập dữ liệu                    | Cao          | Thấp/Trung bình | Kiểm tra authorization server-side; test 403.                                       |
| R-10   | Demo không ổn định do internet                | Cao          | Trung bình      | Có cache metadata và dữ liệu demo đã phân tích sẵn; có local fallback.              |
