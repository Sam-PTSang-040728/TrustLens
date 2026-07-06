# Giới hạn đã biết của TrustLens v1.2

Tài liệu này phải được công bố cùng bản pilot/release để tránh diễn giải quá mức năng lực hệ thống.

## 1. Phạm vi file

- Hỗ trợ PDF có text layer và DOCX.
- PDF scan hoặc nội dung chỉ ở dạng ảnh không được OCR trong v1.2.
- PDF malformed, encrypted hoặc bố cục hiếm có thể extraction thất bại.
- Parser có thể sai khi references không có heading rõ hoặc định dạng quá bất thường.

## 2. Metadata verification

- Provider không bao phủ mọi loại nguồn.
- `NOT_FOUND` chỉ có nghĩa không tìm thấy trong provider/config hiện tại; không chứng minh nguồn giả.
- URL trả `200` không chứng minh nguồn học thuật hoặc metadata đúng.
- Provider timeout/rate limit có thể làm confidence giảm hoặc dùng fallback.
- Metadata của chính provider có thể thiếu hoặc sai.

## 3. Trust Score

- Trust Score là tín hiệu hỗ trợ rà soát, không phải xác suất đúng khoa học.
- Điểm thấp không tự động chứng minh gian lận.
- Điểm cao không chứng minh nội dung bài báo hoặc báo cáo là đúng.
- Credibility dựa trên evidence khả dụng, không thể đánh giá đầy đủ chất lượng phương pháp nghiên cứu.
- Recency phụ thuộc lĩnh vực; tài liệu nền tảng cũ không mặc định là không phù hợp.
- Fuzzy duplicate có false positive/false negative.
- Report score chưa được xem là calibrated nếu chưa có benchmark được phê duyệt.

## 4. Relevance

- Relevance phụ thuộc phần text trích xuất, assignment context và title/abstract có sẵn.
- Abstract thiếu làm giảm evidence.
- Local lexical fallback yếu hơn semantic provider.
- Ngôn ngữ, thuật ngữ chuyên ngành và nội dung liên ngành có thể làm sai kết quả.
- Hệ thống chưa xác minh đầy đủ việc một citation có hỗ trợ chính xác từng mệnh đề trong báo cáo.

## 5. Citation style

- Style detection không thay thế kiểm tra thủ công toàn bộ chuẩn APA/IEEE/MLA/ACM.
- Một citation đúng dấu câu vẫn có thể sai metadata.
- Mixed style hoặc local institutional rules có thể cần cấu hình riêng.

## 6. Security và privacy

- Local policy scanner không tương đương production antivirus/malware scanner.
- Rate limiting in-memory không phù hợp multi-replica deployment.
- Browser token storage làm tăng tác động của XSS cho đến khi có cookie/CSP hardening đầy đủ.
- Mọi deployment phải cấu hình exact CORS origins, secrets, retention và provider policy riêng.

## 7. Reliability

- Database-backed queue phù hợp pilot nhưng cần heartbeat/lease và concurrency evidence đầy đủ.
- Provider ngoài là dependency không kiểm soát hoàn toàn.
- Không có SLO/SLA production trước performance benchmark.
- Backup tồn tại không đồng nghĩa restore đã được chứng minh.

## 8. Frontend và batch

- Batch analysis không được coi là supported nếu backend chưa có contract tương ứng.
- Mock mode chỉ dùng cho phát triển UI và phải được đánh dấu rõ.
- Accessibility chưa được tuyên bố đạt chuẩn khi chưa có audit.

## 9. Use policy

Kết quả TrustLens cần được giảng viên hoặc reviewer xem cùng evidence. Không dùng hệ thống để tự động:

- kết luận sinh viên gian lận;
- trừ điểm hoặc kỷ luật;
- loại bỏ tài liệu chỉ vì provider không tìm thấy;
- thay thế đánh giá chuyên gia về chất lượng nghiên cứu.

## 10. Cách báo cáo lỗi

Khi ghi nhận lỗi, cung cấp:

- correlation ID;
- job/report ID nếu có;
- loại file và kích thước;
- error code;
- scoring version;
- provider/fallback status;
- dữ liệu mẫu đã được ẩn danh.

Không gửi password, token, API key hoặc báo cáo chứa dữ liệu nhạy cảm qua issue công khai.

## 11. Evidence and Policy Boundary

Known limitations are part of the release contract. They must be visible in pilot
materials, not hidden in internal notes.

Mandatory policy statement:

> Trust Score is a review-support signal based on available metadata and evidence. It
> is not an automatic conclusion about fraud, academic misconduct, scientific truth or
> grading outcome.

Operational consequences:

- reviewers must inspect evidence before taking action;
- provider `NOT_FOUND` does not prove a source is fake;
- low score does not prove misconduct;
- high score does not prove the cited work is correct or appropriate;
- benchmark/calibration status must be disclosed when not approved.

## 12. Release-Specific Open Limitations

| Area | Limitation | Required disclosure/action |
|---|---|---|
| OCR | Scanned/image-only PDFs are not supported in v1.2 | UI/report must show extraction limitation, not generic failure |
| Parser | Unusual reference layouts can fail or split citations incorrectly | Preserve raw extraction evidence for review |
| Metadata provider | Provider coverage is incomplete and can be unavailable | Show provider/fallback/confidence |
| Relevance | Vietnamese/English/domain variation can affect C4 | Do not overstate semantic accuracy before benchmark |
| Security | Local scan policy is not a production malware scanner | Require approved scanner for real uploads |
| Queue | Database queue needs heartbeat/lease/concurrency evidence | Do not claim production reliability without crash tests |
| Batch | `/analysis-batches` is unsupported unless backend contract is implemented | Hide batch UI in production or complete backend batch |
| Accessibility | No accessibility conformance claim without audit | Run smoke/audit before public release |

## 13. Limitation Removal Rule

A limitation can be removed only when:

1. code/config change exists;
2. migration exists if persisted data changed;
3. unit/integration/E2E/security evidence exists as appropriate;
4. docs/API/contract matrix are updated;
5. release owner approves the evidence.

Do not remove a limitation because a roadmap item exists or a mock/demo flow works.
