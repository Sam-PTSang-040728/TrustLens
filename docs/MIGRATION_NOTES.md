# Migration Notes: `docs/` cũ → `docs/` mới

## 1. Mục tiêu

Cấu trúc cũ có bốn tài liệu lớn nhưng không tạo được bộ tài liệu vận hành nhất quán. Cấu trúc mới tách theo artifact và ưu tiên implementation.

## 2. Ánh xạ

| File cũ | Vấn đề | Đích mới |
|---|---|---|
| `trustlens.md` | Review SRS, trộn khuyến nghị và nguồn cũ | SRS, architecture, testing |
| `TrustLens_Backend_P0_Remediation_Plan_v1.0.md` | Nhiều mục tiêu khác code | Roadmap, gaps/decisions |
| `TrustLens_Backend_P1_Enhancement_Plan_v1.0.md` | Trộn feature plan và design | Roadmap, data model, operations |
| `TrustLens_Backend_P2_Production_Intelligence_Plan_v1.0.md` | Tương lai dễ bị hiểu là đã có | Roadmap |
| `.obsidian/workspace.json` | Metadata editor cá nhân | Không version trong baseline |

## 3. Nội dung giữ lại

- Tách SRS khỏi design.
- Traceability/verification.
- P0/P1/P2 theo stage.
- Durable queue target.
- Batch/OCR/LMS/multi-tenant roadmap.
- Explainability/confidence/provider fallback.
- Audit/retention/security.

## 4. Nội dung sửa

### Trust Score

C1–C8 cũ → C1–C7 v1.2 theo code.

### Queue

As-is là `BackgroundTasks`; durable queue là to-be, chưa khóa Celery.

### API

Inventory dựng lại từ router. Ghi chú admin/report-export chưa mount đã bị loại.

### Pipeline

Phân biệt real pipeline và placeholder; retry `Partial`.

### Mock

Mock chỉ local explicit; fallback ngầm là gap.

## 5. Cách áp dụng

1. Tạo branch `docs/rewrite`.
2. Dùng Git history/tag để lưu docs cũ, không cần giữ stale file trên main.
3. Thay `docs/` bằng folder này.
4. Xóa `.obsidian/workspace.json` khỏi version control.
5. Cập nhật root README trỏ `docs/README.md`.
6. Chạy markdown link check.
7. Review bởi backend, frontend, BA/NLP.
8. Merge sau khi xác nhận snapshot.

## 6. Backend docs riêng

- `API_Documents.md` nên generate từ OpenAPI hoặc trỏ về API reference mới.
- `backend-activity-log.md` nên chuyển thành release notes/changelog.
- Không duy trì hai API inventory thủ công độc lập.

## 7. Cập nhật phiên bản tiếp theo

Cập nhật snapshot SHA, implementation status, API inventory, scoring version, gap register và roadmap exit criteria.
