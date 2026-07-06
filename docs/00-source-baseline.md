# 00. Source Baseline và Phương pháp Rà soát

## 1. Mục tiêu

Tài liệu này xác định bằng chứng dùng để viết lại `docs/`, đồng thời ngăn việc suy diễn từ kế hoạch cũ thành hiện trạng triển khai.

## 2. Repository được rà soát

### Root repository

- `Sam-PTSang-040728/TrustLens`
- Snapshot: `c9ad1d3f7e46b7795e8aef86a5c526adfb34f013`
- File trọng yếu: `README.md`, `.gitmodules`, `docs/trustlens.md`, ba kế hoạch P0/P1/P2.

### Backend submodule

- `Sam-PTSang-040728/TrustLens-Backend`
- Snapshot: `bc0023ae37f28b3b1b833bc92c93c1bdb5f9c63a`
- Nhóm file: router, endpoint, config, permission, Trust Score definition, model, service pipeline/scoring/job, requirements, unit test và docs backend.

### Frontend submodule

- `tranquynhnhu102025-hash/Frontend-TrustLens`
- Snapshot: `0dded036f2632ece4401b6bc1b2c219050c56815`
- Nhóm file: `package.json`, router, permission, API client, Trust Score config và màn hình phân tích.

## 3. Thứ tự ưu tiên bằng chứng

1. Router và endpoint đang được mount.
2. Service thực thi được endpoint gọi.
3. Model và migration.
4. Frontend route và API client.
5. Unit test hiện có.
6. README kỹ thuật.
7. Tài liệu cũ và roadmap.

Kế hoạch P0/P1/P2 không được dùng để khẳng định một tính năng đã triển khai.

## 4. Tiêu chí trạng thái

### Implemented

- Có route/entry point rõ ràng.
- Có service/model hỗ trợ.
- Không chỉ là skeleton.
- Không phụ thuộc hoàn toàn vào mock.
- Xác định được input/output.

### Partial

- Có một phần mã nguồn nhưng thiếu test hoặc luồng đầy đủ.
- Hai endpoint cùng mục tiêu gọi hai pipeline khác nhau.
- UI/backend chưa tích hợp hoàn chỉnh.
- Có fallback che lỗi thật.

### Planned

Chỉ tồn tại trong roadmap, ví dụ Celery/Redis, batch, OCR, WebSocket/SSE, LMS, multi-tenancy, MLOps và autoscaling.

## 5. Giới hạn rà soát

- Không chạy trực tiếp repository trong lần biên soạn này.
- Không kiểm tra database hoặc production data.
- Không xác nhận GitHub Actions.
- Không xác nhận độ chính xác thuật toán bằng benchmark ngoài.
- Không kiểm tra lịch sử secret.
- Có file test không đồng nghĩa test đã pass.

Kết luận trong tài liệu là **code-inspection baseline**, không phải chứng nhận production.

## 6. Sai lệch chính

| Mã | Sai lệch | Hệ quả |
|---|---|---|
| GAP-001 | P0 dùng C1–C8; code dùng C1–C7 v1.2 | Báo cáo sai công thức điểm |
| GAP-002 | P0 mô tả Celery/Redis; code dùng `BackgroundTasks` | Không được tuyên bố durable queue |
| GAP-003 | Analyze gọi pipeline thật; process/retry gọi placeholder | Có thể completed nhưng không có report thật |
| GAP-004 | API doc cũ nói admin/report-export chưa mount; router hiện đã mount | API doc cũ stale |
| GAP-005 | Frontend fallback mock khi backend user-management lỗi | Có thể báo thành công giả |
| GAP-006 | Chưa thấy integration/E2E baseline | Chưa đủ bằng chứng nghiệm thu |
| GAP-007 | Config có secret dev mặc định | Rủi ro dùng nhầm production |
| GAP-008 | PDF không có text layer bị từ chối | Phạm vi PDF hẹp hơn mô tả phổ thông |
