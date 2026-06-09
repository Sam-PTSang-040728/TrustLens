# TrustLens_Trust_Score_Specification_v1.0

**Tên tài liệu:** Đặc tả công thức Trust Score cho TrustLens v1.0  
**Tên dự án:** TrustLens - Hệ thống thẩm định độ tin cậy và phù hợp của danh mục tài liệu tham khảo trong báo cáo Công nghệ Thông tin  
**Phiên bản:** v1.0  
**Ngày ban hành:** 07/06/2026  
**Trạng thái:** Draft dùng cho thiết kế thuật toán, backend, frontend, kiểm thử và báo cáo kỹ thuật  
**Chủ sở hữu nghiệp vụ:** Product/BA + AI/NLP Owner  
**Đối tượng sử dụng:** BA, DS/NLP, Backend, Frontend, QA/Test, người viết báo cáo kỹ thuật, người demo sản phẩm  
**Phạm vi:** Báo cáo, tiểu luận, đồ án, bài nộp học thuật thuộc lĩnh vực Công nghệ Thông tin, Khoa học Dữ liệu, AI, Web Application, An toàn thông tin và các lĩnh vực công nghệ liên quan.

---

## 0. Tóm tắt điều hành

`Trust Score` là điểm tổng hợp của TrustLens dùng để phản ánh mức độ **đáng tin cậy**, **có thể xác minh**, **phù hợp với nội dung báo cáo**, **cập nhật**, **đúng chuẩn trích dẫn** và **ít rủi ro học thuật** của danh mục tài liệu tham khảo trong một bài nộp.

TrustLens v1.0 sử dụng mô hình chấm điểm hai tầng:

| Tầng | Đối tượng | Điểm đầu ra | Mục đích |
|---|---|---|---|
| Level 1 | Một tài liệu tham khảo riêng lẻ | `Reference Trust Score` | Đánh giá từng nguồn trích dẫn. |
| Level 2 | Toàn bộ danh mục tài liệu tham khảo của báo cáo | `Report Trust Score` | Đánh giá chất lượng chung của danh mục tài liệu tham khảo. |

Công thức lõi:

```text
Reference_Trust_Score = C1 + C2 + C3 + C4 + C5 + C6 + C7 + C8
```

Trong đó:

| Mã | Thành phần | Trọng số |
|---|---|---:|
| C1 | Metadata Completeness Score | 10 |
| C2 | Metadata Verification Score | 20 |
| C3 | Source Credibility Score | 20 |
| C4 | Relevance Score | 20 |
| C5 | Recency Score | 10 |
| C6 | Citation Quality Score | 10 |
| C7 | Source Diversity Contribution Score | 5 |
| C8 | Academic Risk Integrity Score | 5 |
|  | **Tổng** | **100** |

Điểm cấp báo cáo:

```text
Report_Trust_Score = clamp(Weighted_Average(Reference_Trust_Score_i) - Report_Penalty, 0, 100)
```

Điểm này không thay thế phán đoán chuyên môn của giảng viên. TrustLens cung cấp cơ chế đánh giá có cấu trúc, có bằng chứng, có khả năng giải thích và có thể kiểm tra lại.

---

## 1. Mục đích tài liệu

Tài liệu này đặc tả chi tiết cách TrustLens v1.0 tính điểm `Trust Score`, gồm:

1. Định nghĩa các điểm thành phần.
2. Công thức quy đổi từng tiêu chí về thang điểm.
3. Trọng số, ngưỡng cảnh báo và điều kiện phạt điểm.
4. Cơ chế tính điểm ở cấp từng tài liệu tham khảo.
5. Cơ chế tổng hợp điểm ở cấp toàn bộ báo cáo.
6. Cơ chế tách `Trust Score` khỏi `Confidence Score`.
7. Quy tắc sinh nhãn kết luận, cảnh báo và gợi ý xử lý.
8. Dữ liệu đầu vào/đầu ra cần cho Backend và Frontend.
9. Pseudocode để triển khai thuật toán v1.0.
10. Bộ ví dụ kiểm thử để đối chiếu kết quả.

---

## 2. Căn cứ thiết kế

Trust Score v1.0 được thiết kế dựa trên các yêu cầu nghiệp vụ sau:

| Căn cứ | Nội dung được dùng để thiết kế |
|---|---|
| Project Overview | TrustLens cần thẩm định độ tin cậy và mức độ phù hợp của danh mục tài liệu tham khảo trong báo cáo Công nghệ Thông tin. |
| Phân công BA/AI/NLP | Vai trò Product/BA + AI/NLP Owner chịu trách nhiệm thiết kế tiêu chí đánh giá, quy tắc học thuật, mô hình điểm và công thức Trust Score. |
| Nhóm chức năng hệ thống | Hệ thống cần trích xuất tài liệu tham khảo, chuẩn hóa citation, kiểm tra tồn tại học thuật, đánh giá độ tin cậy, độ cập nhật, độ phù hợp, chấm điểm, cảnh báo, kết luận và xuất kết quả. |
| Backend scope | Backend cần xử lý file PDF/DOCX, tích hợp Metadata API, xác minh DOI/tiêu đề/tác giả/năm/nguồn công bố, tính điểm và lưu kết quả. |
| Kế hoạch triển khai | Trust Score, Relevance Score và Citation Quality Score là hạng mục cần hoàn chỉnh trong Phase 2. |
| Yêu cầu cuộc thi | Giải pháp cần có sản phẩm chạy được hoặc mô hình thực nghiệm, có khả năng giải trình, chất lượng dữ liệu và quy trình xử lý rõ ràng. |

---

## 3. Phạm vi áp dụng của Trust Score v1.0

### 3.1. Có đánh giá trong v1.0

| Mã | Phạm vi | Mô tả |
|---|---|---|
| SCOPE-01 | Reference parsing | Tách danh mục tài liệu tham khảo từ PDF/DOCX. |
| SCOPE-02 | Metadata extraction | Nhận diện tác giả, năm, tiêu đề, nguồn công bố, DOI, URL. |
| SCOPE-03 | Metadata verification | Đối chiếu metadata qua DOI, Crossref, Semantic Scholar, OpenAlex hoặc URL chính thức nếu có. |
| SCOPE-04 | Source credibility | Đánh giá loại nguồn và độ uy tín tương đối của nơi công bố. |
| SCOPE-05 | Relevance | So sánh nguồn tham khảo với chủ đề, tóm tắt hoặc nội dung báo cáo. |
| SCOPE-06 | Recency | Kiểm tra mức độ cập nhật theo lĩnh vực. |
| SCOPE-07 | Citation style | Kiểm tra hình thức citation theo APA 7, IEEE, MLA, ACM ở mức rule-based. |
| SCOPE-08 | Risk flags | Phát hiện DOI sai, URL chết, nguồn thiếu thông tin, nguồn không học thuật, nguồn có khả năng bịa đặt. |
| SCOPE-09 | Report aggregation | Tổng hợp điểm cấp báo cáo, thống kê lỗi và sinh kết luận. |

### 3.2. Chưa đánh giá đầy đủ trong v1.0

| Mã | Giới hạn | Lý do |
|---|---|---|
| LIM-01 | Kiểm tra đạo văn toàn văn | Cần module plagiarism riêng. |
| LIM-02 | Kiểm chứng nội dung full-text của từng nguồn | Nhiều bài báo có paywall hoặc giới hạn truy cập. |
| LIM-03 | Đánh giá chất lượng phương pháp nghiên cứu trong từng paper | Cần mô hình review học thuật chuyên sâu theo từng lĩnh vực. |
| LIM-04 | Phân loại tạp chí săn mồi ở mức pháp lý | Cần danh sách đối chiếu được cập nhật và kiểm chứng độc lập. |
| LIM-05 | Xác minh mọi citation in-text khớp với từng câu trong nội dung | Cần module citation-in-context alignment nâng cao. |
| LIM-06 | Kết luận học thuật tuyệt đối | TrustLens chỉ đưa ra chỉ báo hỗ trợ quyết định. |

---

## 4. Thuật ngữ và ký hiệu

### 4.1. Thuật ngữ

| Thuật ngữ | Định nghĩa |
|---|---|
| Reference | Một mục tài liệu tham khảo trong danh mục cuối bài. |
| Citation | Dấu hiệu trích dẫn trong nội dung bài, ví dụ `[1]`, `(Nguyen, 2024)`, footnote. |
| Bibliographic metadata | Metadata thư mục: tác giả, năm, tiêu đề, DOI, URL, venue, publisher, volume, issue, pages. |
| DOI | Digital Object Identifier, mã định danh số cho tài liệu học thuật. |
| Venue | Nơi công bố: journal, conference, book, documentation site, repository, website. |
| Trust Score | Điểm tổng hợp đánh giá mức độ tin cậy và phù hợp. |
| Relevance Score | Điểm đo mức liên quan giữa tài liệu tham khảo và nội dung báo cáo. |
| Citation Quality Score | Điểm hình thức và tính nhất quán của citation/reference. |
| Confidence Score | Mức độ tự tin của hệ thống đối với kết quả đánh giá. Không đồng nghĩa với Trust Score. |
| Hard Flag | Cảnh báo nghiêm trọng có thể làm giảm mạnh điểm hoặc yêu cầu kiểm tra thủ công. |
| Soft Flag | Cảnh báo nhẹ, thường dùng để gợi ý sửa lỗi. |
| Clamp | Hàm giới hạn giá trị trong một khoảng. |
| Normalization | Quy đổi dữ liệu về cùng thang đo. |
| Weight | Trọng số phản ánh vai trò tương đối của điểm thành phần. |

### 4.2. Ký hiệu toán học

| Ký hiệu | Ý nghĩa |
|---|---|
| `R_i` | Tài liệu tham khảo thứ `i`. |
| `n` | Số lượng tài liệu tham khảo trong báo cáo. |
| `Ck_i` | Điểm thành phần thứ `k` của tài liệu `R_i`. |
| `RTS_i` | Reference Trust Score của tài liệu `R_i`. |
| `W_i` | Trọng số quan trọng của tài liệu `R_i` khi tổng hợp cấp báo cáo. |
| `RTP` | Report Penalty. |
| `Report_TS` | Report Trust Score. |
| `Sim(a,b)` | Độ tương đồng ngữ nghĩa giữa hai vector embedding. |
| `Age` | Số năm từ năm xuất bản đến năm đánh giá. |
| `clamp(x,a,b)` | Nếu `x<a` trả về `a`, nếu `x>b` trả về `b`, còn lại trả về `x`. |

---

## 5. Kiến trúc điểm tổng quát

### 5.1. Luồng tính điểm

```text
Input file PDF/DOCX
    ↓
Extract text
    ↓
Detect reference section
    ↓
Parse individual references
    ↓
Normalize metadata
    ↓
Verify metadata externally
    ↓
Compute component scores C1–C8
    ↓
Compute Reference Trust Score
    ↓
Aggregate Report Trust Score
    ↓
Generate classification, warning, evidence, recommendation
```

### 5.2. Hai điểm chính

| Điểm | Thang đo | Đối tượng | Công dụng |
|---|---:|---|---|
| `Reference_Trust_Score` | 0–100 | Một nguồn tham khảo | Cho biết nguồn riêng lẻ có đáng tin cậy và phù hợp hay không. |
| `Report_Trust_Score` | 0–100 | Toàn bộ danh mục tài liệu tham khảo | Cho biết chất lượng tổng thể của danh mục. |

### 5.3. Điểm phụ nhưng bắt buộc lưu

| Điểm phụ | Thang đo | Vai trò |
|---|---:|---|
| `Confidence_Score` | 0–100 | Mức độ tự tin của hệ thống về kết quả. |
| `Extraction_Confidence` | 0–100 | Độ tự tin khi tách reference từ file. |
| `Verification_Confidence` | 0–100 | Độ tự tin khi xác minh metadata. |
| `Relevance_Confidence` | 0–100 | Độ tự tin khi tính semantic relevance. |
| `Risk_Level` | `low/medium/high/critical` | Mức rủi ro học thuật cần hiển thị cho người dùng. |

---

## 6. Nguyên tắc tính điểm

### 6.1. Nguyên tắc 1: Trust Score phải giải thích được

Mỗi điểm thành phần phải có:

| Thành phần | Bắt buộc |
|---|---|
| `score` | Điểm số định lượng. |
| `max_score` | Điểm tối đa. |
| `reason` | Lý do chấm điểm. |
| `evidence` | Dữ liệu làm căn cứ: DOI, URL, metadata match, similarity, năm xuất bản. |
| `flags` | Danh sách cảnh báo nếu có. |

### 6.2. Nguyên tắc 2: Không đồng nhất “không xác minh được” với “sai”

Nếu hệ thống không xác minh được nguồn do thiếu metadata hoặc API không phản hồi, kết quả phải được ghi là:

```text
verification_status = "unverified"
```

Không được tự động ghi là:

```text
verification_status = "fake"
```

Chỉ dùng nhãn `fake_suspected` khi có nhiều bằng chứng bất thường, ví dụ DOI không tồn tại, tiêu đề không khớp, tác giả/năm sai rõ ràng, URL không truy cập được và không có nguồn thay thế.

### 6.3. Nguyên tắc 3: Tách Trust Score và Confidence Score

| Trường hợp | Trust Score | Confidence Score | Diễn giải |
|---|---:|---:|---|
| Nguồn tốt, DOI xác minh được | Cao | Cao | Kết quả đáng tin. |
| Nguồn tốt nhưng thiếu DOI, chỉ có URL | Trung bình–cao | Trung bình | Nguồn có thể tốt nhưng bằng chứng xác minh hạn chế. |
| Nguồn blog không học thuật, URL sống | Thấp–trung bình | Cao | Hệ thống tự tin rằng nguồn không mạnh về học thuật. |
| Metadata quá thiếu | Thấp | Thấp | Điểm thấp nhưng cần kiểm tra thủ công. |

### 6.4. Nguyên tắc 4: Điểm phù hợp không phụ thuộc hoàn toàn vào độ tin cậy

Một nguồn học thuật uy tín nhưng không liên quan đến chủ đề báo cáo vẫn bị giảm `C4 - Relevance Score`.

Một tài liệu chính thức của framework, API hoặc thư viện có thể không phải journal/conference nhưng vẫn có điểm tin cậy cao nếu bài báo cáo đang triển khai công nghệ đó.

### 6.5. Nguyên tắc 5: Tính cập nhật phụ thuộc lĩnh vực

Tài liệu về AI, cybersecurity, framework web, cloud, DevOps nên được ưu tiên nguồn mới. Tài liệu về thuật toán nền tảng, toán học, hệ quản trị cơ sở dữ liệu cơ bản hoặc chuẩn kỹ thuật ổn định có thể chấp nhận nguồn cũ hơn.

---

## 7. Công thức cấp tài liệu tham khảo

### 7.1. Công thức chính

Với mỗi tài liệu tham khảo `R_i`:

```text
RTS_i = C1_i + C2_i + C3_i + C4_i + C5_i + C6_i + C7_i + C8_i
```

Trong đó:

```text
0 <= RTS_i <= 100
```

Điểm thành phần:

| Mã | Tên | Khoảng điểm |
|---|---|---:|
| C1 | Metadata Completeness Score | 0–10 |
| C2 | Metadata Verification Score | 0–20 |
| C3 | Source Credibility Score | 0–20 |
| C4 | Relevance Score | 0–20 |
| C5 | Recency Score | 0–10 |
| C6 | Citation Quality Score | 0–10 |
| C7 | Source Diversity Contribution Score | 0–5 |
| C8 | Academic Risk Integrity Score | 0–5 |

### 7.2. Hàm giới hạn

```text
clamp(x, min, max) = min(max(x, min), max)
```

Mọi điểm sau khi tính phải được giới hạn vào khoảng hợp lệ.

### 7.3. Làm tròn điểm

| Loại điểm | Quy tắc làm tròn |
|---|---|
| Điểm thành phần | Làm tròn 2 chữ số thập phân. |
| Reference Trust Score | Làm tròn 1 chữ số thập phân. |
| Report Trust Score | Làm tròn 1 chữ số thập phân. |
| Điểm hiển thị UI | Có thể làm tròn về số nguyên nếu cần đơn giản hóa. |

---

## 8. C1 - Metadata Completeness Score

### 8.1. Mục tiêu

C1 đo mức đầy đủ của metadata được trích xuất từ một reference. Metadata đầy đủ giúp hệ thống xác minh nguồn, đánh giá độ tin cậy và kiểm tra định dạng citation.

### 8.2. Công thức C1

```text
C1 = 10 × Metadata_Completeness_Ratio
```

Trong đó:

```text
Metadata_Completeness_Ratio = Sum(field_weight_j × present_j)
```

`present_j = 1` nếu trường có dữ liệu hợp lệ, `0` nếu thiếu hoặc không đọc được.

### 8.3. Trọng số trường metadata mặc định

| Trường metadata | Trọng số | Ghi chú |
|---|---:|---|
| `title` | 0.25 | Trường quan trọng nhất để match nguồn. |
| `author_or_org` | 0.20 | Có thể là cá nhân hoặc tổ chức. |
| `year_or_date` | 0.15 | Dùng cho recency và verification. |
| `venue_or_publisher` | 0.15 | Journal, conference, publisher, website, organization. |
| `identifier` | 0.15 | DOI, ISBN, arXiv ID, standard ID, GitHub repo, URL chính thức. |
| `citation_structure` | 0.10 | Reference có cấu trúc nhận diện được. |
| **Tổng** | **1.00** |  |

### 8.4. Quy tắc hợp lệ của từng trường

| Trường | Điều kiện hợp lệ |
|---|---|
| `title` | Có ít nhất 5 ký tự có nghĩa; không chỉ là URL hoặc chuỗi lỗi. |
| `author_or_org` | Có tên tác giả, nhóm tác giả, tổ chức hoặc vendor chính thức. |
| `year_or_date` | Có năm 4 chữ số hợp lý, ví dụ 1990–năm hiện tại. |
| `venue_or_publisher` | Có tên nguồn công bố, hội nghị, tạp chí, nhà xuất bản, website hoặc tổ chức phát hành. |
| `identifier` | DOI/URL/ISBN/arXiv ID/standard ID/repository URL có cấu trúc hợp lệ. |
| `citation_structure` | Có thể tách được thành một reference độc lập. |

### 8.5. Pseudocode C1

```python
def compute_c1(metadata):
    weights = {
        "title": 0.25,
        "author_or_org": 0.20,
        "year_or_date": 0.15,
        "venue_or_publisher": 0.15,
        "identifier": 0.15,
        "citation_structure": 0.10,
    }
    ratio = 0
    for field, weight in weights.items():
        if is_valid_metadata_field(metadata.get(field), field):
            ratio += weight
    return clamp(10 * ratio, 0, 10)
```

### 8.6. Cảnh báo liên quan C1

| Mã cảnh báo | Mức độ | Điều kiện |
|---|---|---|
| `C1_MISSING_TITLE` | High | Thiếu tiêu đề. |
| `C1_MISSING_AUTHOR` | Medium | Thiếu tác giả hoặc tổ chức. |
| `C1_MISSING_YEAR` | Medium | Thiếu năm xuất bản/cập nhật. |
| `C1_MISSING_VENUE` | Medium | Thiếu nơi công bố. |
| `C1_MISSING_IDENTIFIER` | Low | Không có DOI/URL/ISBN/arXiv ID. |
| `C1_PARSE_LOW_CONFIDENCE` | High | Parser không tự tin khi tách reference. |

---

## 9. C2 - Metadata Verification Score

### 9.1. Mục tiêu

C2 đo khả năng xác minh nguồn qua dữ liệu bên ngoài. Đây là điểm quan trọng để phát hiện nguồn không tồn tại, DOI sai, metadata sai hoặc reference bị bịa đặt.

### 9.2. Kênh xác minh ưu tiên

| Ưu tiên | Kênh | Loại nguồn phù hợp |
|---:|---|---|
| 1 | DOI Resolver/Crossref | Journal, conference, book chapter. |
| 2 | DataCite | Dataset, software, technical object có DOI. |
| 3 | Semantic Scholar | Paper học thuật, AI/CS paper. |
| 4 | OpenAlex | Bài báo, sách, venue, tác giả. |
| 5 | arXiv API | Preprint. |
| 6 | DBLP | Computer science paper, conference. |
| 7 | Official URL check | Documentation, standard, technical report, website. |
| 8 | Manual fallback | Nguồn không đủ dữ liệu hoặc API lỗi. |

### 9.3. Công thức C2

$$
C2 = \text{DOI-ID-Score} + \text{Title-Match-Score} + Author_Match_Score + Year_Match_Score + Venue_Match_Score + Access_Resolve_Score
$$

| Tiểu mục | Điểm tối đa | Mô tả |
|---|---:|---|
| `DOI_ID_Score` | 6 | DOI/identifier tồn tại và resolve được. |
| `Title_Match_Score` | 5 | Tiêu đề trong bài khớp metadata bên ngoài. |
| `Author_Match_Score` | 3 | Tác giả/tổ chức khớp. |
| `Year_Match_Score` | 2 | Năm xuất bản khớp hoặc sai lệch chấp nhận được. |
| `Venue_Match_Score` | 2 | Journal/conference/publisher/website khớp. |
| `Access_Resolve_Score` | 2 | URL/landing page truy cập được hoặc có bản ghi metadata hợp lệ. |
| **Tổng** | **20** |  |

### 9.4. Quy tắc tính từng tiểu mục

#### 9.4.1. DOI_ID_Score

| Điểm | Điều kiện |
|---:|---|
| 6 | DOI/identifier hợp lệ và resolve được tới bản ghi đúng. |
| 4 | DOI/identifier có cấu trúc đúng nhưng API không phản hồi; có thể xác minh bằng kênh khác. |
| 2 | Không có DOI nhưng có URL/repository/standard ID chính thức. |
| 0 | DOI sai định dạng, không tồn tại hoặc dẫn tới nguồn khác. |

#### 9.4.2. Title_Match_Score

Sử dụng similarity giữa tiêu đề trong reference và tiêu đề từ metadata bên ngoài.

```text
Title_Sim = normalized_string_similarity(reference_title, external_title)
```

| Điểm | Điều kiện |
|---:|---|
| 5 | `Title_Sim >= 0.95` |
| 4 | `0.85 <= Title_Sim < 0.95` |
| 3 | `0.70 <= Title_Sim < 0.85` |
| 1 | `0.50 <= Title_Sim < 0.70` |
| 0 | `Title_Sim < 0.50` hoặc không có tiêu đề để so sánh. |

#### 9.4.3. Author_Match_Score

| Điểm | Điều kiện |
|---:|---|
| 3 | Tác giả đầu hoặc tổ chức chính khớp rõ ràng. |
| 2 | Một phần tác giả khớp, có sai khác nhỏ do viết tắt/tên đệm. |
| 1 | Chỉ khớp tổ chức hoặc họ tác giả nhưng chưa chắc chắn. |
| 0 | Không khớp hoặc không có dữ liệu. |

#### 9.4.4. Year_Match_Score

| Điểm | Điều kiện |
|---:|---|
| 2 | Năm khớp chính xác. |
| 1 | Sai lệch 1 năm do online first, conference year hoặc version update. |
| 0 | Sai lệch trên 1 năm hoặc thiếu năm. |

#### 9.4.5. Venue_Match_Score

| Điểm | Điều kiện |
|---:|---|
| 2 | Venue/publisher khớp rõ. |
| 1 | Khớp một phần hoặc dùng tên viết tắt hợp lý. |
| 0 | Không khớp hoặc thiếu venue. |

#### 9.4.6. Access_Resolve_Score

| Điểm | Điều kiện |
|---:|---|
| 2 | DOI/URL/landing page truy cập được hoặc API trả metadata hợp lệ. |
| 1 | Truy cập gián tiếp qua bản ghi metadata, repository hoặc cached source. |
| 0 | Không truy cập được, URL chết, DOI lỗi hoặc không có kênh xác minh. |

### 9.5. Trạng thái xác minh

| Trạng thái | Điều kiện |
|---|---|
| `verified` | C2 >= 16 và không có hard flag. |
| `partially_verified` | 10 <= C2 < 16. |
| `unverified` | 4 <= C2 < 10 hoặc thiếu dữ liệu/API lỗi. |
| `mismatch` | Có DOI/metadata trả về nhưng tiêu đề/tác giả/năm không khớp đáng kể. |
| `fake_suspected` | DOI không tồn tại, URL chết, metadata không khớp nhiều trường và không có kênh xác minh thay thế. |

---

## 10. C3 - Source Credibility Score

### 10.1. Mục tiêu

C3 đánh giá độ uy tín học thuật hoặc kỹ thuật của nguồn công bố. Điểm này không chỉ dựa vào loại nguồn, mà còn xét tổ chức phát hành, venue, tính chính thức, khả năng kiểm chứng và dấu hiệu rủi ro.

### 10.2. Công thức C3

```text
C3 = Source_Type_Base + Venue_Quality + Publisher_Authority + Impact_Evidence + Transparency_Bonus - Credibility_Deduction
```

Sau đó:

```text
C3 = clamp(C3, 0, 20)
```

### 10.3. Source_Type_Base

| Loại nguồn | Điểm nền |
|---|---:|
| Peer-reviewed journal article | 8 |
| Peer-reviewed conference paper | 8 |
| Academic book/book chapter | 7 |
| Standard/specification chính thức | 8 |
| Official documentation của vendor/tổ chức | 7 |
| Technical report/white paper của tổ chức uy tín | 6 |
| Dataset/software repository có DOI hoặc tổ chức rõ | 6 |
| Preprint có tác giả và repository rõ | 5 |
| Blog kỹ thuật của tổ chức/công ty | 4 |
| Blog cá nhân có tác giả rõ | 3 |
| Website tin tức/cộng đồng | 2 |
| Mạng xã hội/forum/Q&A | 1 |
| Nguồn không rõ tác giả/tổ chức | 0 |

### 10.4. Venue_Quality

| Điều kiện | Điểm |
|---|---:|
| Venue học thuật/chuyên ngành rõ, có hồ sơ metadata hoặc indexing | 4 |
| Conference/journal/book publisher nhận diện được nhưng chưa đánh giá sâu | 3 |
| Documentation site chính thức hoặc standard body | 3 |
| Repository/technical platform có tổ chức quản lý rõ | 2 |
| Website/blog có lịch sử nội dung kỹ thuật | 1 |
| Venue không rõ hoặc không có bằng chứng | 0 |

### 10.5. Publisher_Authority

| Điều kiện | Điểm |
|---|---:|
| Nhà xuất bản/tổ chức học thuật/kỹ thuật có uy tín hoặc vendor chính thức | 3 |
| Tổ chức/công ty có liên quan trực tiếp đến công nghệ được nêu | 2 |
| Cá nhân có thông tin tác giả rõ nhưng chưa xác minh chuyên môn | 1 |
| Không rõ publisher/tác giả | 0 |

### 10.6. Impact_Evidence

| Điều kiện | Điểm |
|---|---:|
| Có citation count/usage/adoption rõ hoặc được trích dẫn rộng rãi | 3 |
| Có một số bằng chứng ảnh hưởng, ví dụ repository stars, downloads, references | 2 |
| Có dấu hiệu được sử dụng nhưng chưa đủ dữ liệu định lượng | 1 |
| Không có dữ liệu | 0 |

### 10.7. Transparency_Bonus

| Điều kiện | Điểm |
|---|---:|
| Có DOI, license, version, date, author affiliation hoặc changelog rõ | 2 |
| Có một phần thông tin minh bạch | 1 |
| Không có thông tin minh bạch | 0 |

### 10.8. Credibility_Deduction

| Điều kiện | Điểm trừ |
|---|---:|
| Nguồn quảng cáo/marketing trá hình, không có dữ liệu kỹ thuật | -3 |
| Nguồn không rõ tác giả, không rõ ngày, không rõ tổ chức | -3 |
| Nguồn AI-generated không khai báo hoặc không kiểm chứng được | -4 |
| Nguồn có dấu hiệu sai lệch metadata nghiêm trọng | -5 |
| URL chết, DOI sai, không có kênh xác minh thay thế | -5 |

---

## 11. C4 - Relevance Score

### 11.1. Mục tiêu

C4 đo mức độ phù hợp giữa tài liệu tham khảo và nội dung báo cáo. Đây là thành phần quan trọng vì nguồn đáng tin cậy nhưng không liên quan vẫn không hỗ trợ tốt cho lập luận học thuật.

### 11.2. Dữ liệu dùng để tính relevance

| Nguồn dữ liệu | Mức ưu tiên | Ghi chú |
|---|---:|---|
| Tiêu đề báo cáo | Cao | Luôn nên có. |
| Abstract/tóm tắt báo cáo | Cao | Nếu bài có phần tóm tắt. |
| Keywords | Cao | Có thể trích từ nội dung hoặc nhập thủ công. |
| Heading/section titles | Trung bình | Giúp nhận diện chủ đề. |
| Citation context | Rất cao | Các câu xung quanh nơi nguồn được trích dẫn. |
| Toàn văn báo cáo | Trung bình | Cần xử lý nhiễu. |
| Tiêu đề nguồn tham khảo | Cao | Luôn dùng. |
| Abstract nguồn tham khảo | Rất cao | Nếu metadata API cung cấp. |
| Keywords nguồn tham khảo | Cao | Nếu có. |

### 11.3. Công thức C4 tổng quát

```text
C4 = 20 × Relevance_Ratio
```

Trong đó:

```text
Relevance_Ratio = 0.30 × Sim_Title
                + 0.30 × Sim_Abstract
                + 0.20 × Sim_Keywords
                + 0.20 × Sim_Citation_Context
```

Nếu thiếu một thành phần, trọng số còn lại được tái chuẩn hóa:

```text
Relevance_Ratio = Sum(available_weight_j × sim_j) / Sum(available_weight_j)
```

Sau đó:

```text
C4 = clamp(20 × Relevance_Ratio, 0, 20)
```

### 11.4. Quy đổi cosine similarity

Nếu dùng Sentence Transformers hoặc embedding model tương đương:

```text
Raw_Sim = cosine_similarity(vector_a, vector_b)
```

Do cosine similarity có thể dao động theo mô hình, TrustLens quy đổi về `0–1` bằng hàm:

```text
Norm_Sim = clamp((Raw_Sim - 0.20) / (0.80 - 0.20), 0, 1)
```

Giải thích:

| Raw similarity | Norm_Sim gần đúng | Diễn giải |
|---:|---:|---|
| < 0.20 | 0.00 | Không liên quan hoặc nhiễu. |
| 0.20–0.40 | 0.00–0.33 | Liên quan yếu. |
| 0.40–0.60 | 0.33–0.67 | Liên quan trung bình. |
| 0.60–0.80 | 0.67–1.00 | Liên quan mạnh. |
| > 0.80 | 1.00 | Rất liên quan hoặc gần trùng chủ đề. |

### 11.5. Relevance theo vai trò nguồn

Một reference có thể có vai trò khác nhau trong báo cáo. Nếu hệ thống nhận diện được vai trò, điểm C4 có thể điều chỉnh nhẹ.

| Vai trò | Điều kiện | Điều chỉnh |
|---|---|---:|
| `core_method` | Nguồn liên quan trực tiếp tới phương pháp/mô hình/công nghệ chính | +2, tối đa 20 |
| `background` | Nguồn nền tảng, định nghĩa, lý thuyết | +1, tối đa 20 |
| `comparison` | Nguồn dùng để so sánh giải pháp | +1, tối đa 20 |
| `incidental` | Nguồn chỉ xuất hiện phụ, không gắn với lập luận chính | -1 |
| `off_topic` | Nội dung nguồn không liên quan chủ đề | -5 |

### 11.6. Quy tắc fallback nếu không có abstract nguồn

Nếu không có abstract từ metadata API:

```text
Relevance_Ratio = 0.45 × Sim_Title + 0.25 × Sim_Keywords + 0.30 × Sim_Citation_Context
```

Nếu chỉ có tiêu đề nguồn và tiêu đề báo cáo:

```text
Relevance_Ratio = Sim_Title
C4 = 20 × Sim_Title × Confidence_Adjustment
```

Trong đó:

```text
Confidence_Adjustment = 0.75
```

Vì chỉ dùng tiêu đề nên hệ thống giảm độ tự tin.

### 11.7. Ngưỡng diễn giải C4

| C4 | Nhãn | Ý nghĩa |
|---:|---|---|
| 17–20 | Highly Relevant | Rất phù hợp với chủ đề, phương pháp hoặc nội dung chính. |
| 13–16.9 | Relevant | Phù hợp, có liên hệ rõ. |
| 9–12.9 | Partially Relevant | Liên quan một phần, cần xem lại vai trò trích dẫn. |
| 5–8.9 | Weakly Relevant | Liên quan yếu. |
| 0–4.9 | Not Relevant | Không phù hợp hoặc thiếu dữ liệu để chứng minh liên quan. |

---

## 12. C5 - Recency Score

### 12.1. Mục tiêu

C5 đánh giá độ cập nhật của nguồn theo lĩnh vực. Công nghệ thay đổi nhanh nên nguồn quá cũ có thể không còn phù hợp, đặc biệt với AI, bảo mật, framework web, cloud, DevOps hoặc thư viện phần mềm.

### 12.2. Công thức C5

```text
Age = Evaluation_Year - Publication_Year
```

Mỗi lĩnh vực có `Freshness_Window` và `Maximum_Acceptable_Age`.

```text
Freshness_Ratio = 1 - max(0, Age - Freshness_Window) / (Maximum_Acceptable_Age - Freshness_Window)
```

```text
C5 = 10 × clamp(Freshness_Ratio, 0, 1)
```

### 12.3. Bảng ngưỡng theo lĩnh vực

| Lĩnh vực | Freshness_Window | Maximum_Acceptable_Age | Ghi chú |
|---|---:|---:|---|
| AI/ML/Deep Learning/GenAI | 3 năm | 7 năm | Nên ưu tiên nguồn mới. |
| Cybersecurity | 3 năm | 6 năm | Lỗ hổng và kỹ thuật tấn công thay đổi nhanh. |
| Web framework/frontend/backend | 3 năm | 6 năm | Version thay đổi nhanh. |
| Cloud/DevOps/MLOps | 3 năm | 6 năm | Công cụ và platform thay đổi nhanh. |
| Data Science/Analytics | 4 năm | 8 năm | Một phần phương pháp ổn định hơn. |
| Database/Application architecture | 5 năm | 10 năm | Lý thuyết nền ổn định, công nghệ vẫn cần cập nhật. |
| Software Engineering | 5 năm | 10 năm | Có nhiều tài liệu nền tảng. |
| Algorithms/Math/Statistics foundation | 10 năm | 30 năm | Có thể dùng công trình kinh điển. |
| Standards/Protocol | Theo version | Theo version | Dùng bản mới nhất hoặc bản đang được hệ thống áp dụng. |
| Official documentation | 2 năm | 5 năm | Nên khớp version công nghệ. |

### 12.4. Seminal work override

Một nguồn có thể được đánh dấu là `seminal_work = true` nếu là công trình nền tảng, thuật toán kinh điển, mô hình lý thuyết hoặc chuẩn kỹ thuật vẫn còn giá trị.

Nếu `seminal_work = true`:

```text
C5 = max(C5, 8)
```

Điều kiện dùng override:

| Điều kiện | Bắt buộc |
|---|---|
| Nguồn là lý thuyết/công trình nền tảng | Có |
| Được dùng cho phần nền tảng/định nghĩa/lịch sử/phương pháp gốc | Có |
| Không dùng để chứng minh xu hướng hiện tại | Có |
| Có metadata xác minh được | Khuyến nghị |

### 12.5. Version-sensitive recency

Với official documentation hoặc software repository, năm xuất bản chưa đủ. Cần kiểm tra version.

| Trường hợp | C5 |
|---|---:|
| Documentation khớp version đang dùng trong dự án | 9–10 |
| Documentation mới nhưng khác major version | 6–8 |
| Documentation cũ hơn major version hiện tại | 3–5 |
| Documentation không rõ version/date | 2–4 |

---

## 13. C6 - Citation Quality Score

### 13.1. Mục tiêu

C6 đánh giá chất lượng hình thức citation và reference theo chuẩn được chọn. TrustLens v1.0 ưu tiên APA 7th, IEEE, MLA, ACM.

### 13.2. Công thức C6

```text
C6 = Style_Detection + Required_Fields + Ordering_Format + In_Text_Match + Consistency
```

| Tiểu mục | Điểm tối đa |
|---|---:|
| `Style_Detection` | 2 |
| `Required_Fields` | 3 |
| `Ordering_Format` | 2 |
| `In_Text_Match` | 2 |
| `Consistency` | 1 |
| **Tổng** | **10** |

### 13.3. Style_Detection

| Điểm | Điều kiện |
|---:|---|
| 2 | Hệ thống nhận diện rõ style và reference tuân theo style đó. |
| 1 | Có dấu hiệu của một style nhưng chưa nhất quán. |
| 0 | Không nhận diện được style hoặc trộn nhiều style. |

### 13.4. Required_Fields

| Điểm | Điều kiện |
|---:|---|
| 3 | Đủ trường bắt buộc theo loại nguồn và style. |
| 2 | Thiếu một trường nhỏ. |
| 1 | Thiếu nhiều trường nhưng vẫn nhận diện được nguồn. |
| 0 | Thiếu trường nghiêm trọng. |

### 13.5. Ordering_Format

| Điểm | Điều kiện |
|---:|---|
| 2 | Đúng thứ tự, dấu câu, italic/capitalization ở mức parser nhận diện. |
| 1 | Có lỗi nhỏ nhưng không ảnh hưởng xác minh. |
| 0 | Sai cấu trúc nghiêm trọng. |

### 13.6. In_Text_Match

| Điểm | Điều kiện |
|---:|---|
| 2 | Mọi reference có citation trong nội dung hoặc được đánh dấu bibliography hợp lệ. |
| 1 | Có một số reference không được trích hoặc citation không khớp nhẹ. |
| 0 | Nhiều reference không có citation trong nội dung hoặc citation không khớp danh mục. |

### 13.7. Consistency

| Điểm | Điều kiện |
|---:|---|
| 1 | Toàn bộ danh mục dùng nhất quán một style. |
| 0 | Trộn nhiều style hoặc format không thống nhất. |

### 13.8. Cảnh báo C6

| Mã cảnh báo | Mức độ | Điều kiện |
|---|---|---|
| `C6_STYLE_MIXED` | Medium | Trộn APA/IEEE/MLA/ACM. |
| `C6_MISSING_IN_TEXT_CITATION` | Medium | Reference có trong danh mục nhưng không thấy in-text citation. |
| `C6_ORPHAN_CITATION` | High | In-text citation không có mục tương ứng trong danh mục. |
| `C6_BAD_ORDERING` | Low | Sai thứ tự thành phần. |
| `C6_INCOMPLETE_FORMAT` | Medium | Thiếu trường theo style. |

---

## 14. C7 - Source Diversity Contribution Score

### 14.1. Mục tiêu

C7 đo đóng góp của từng nguồn vào tính cân bằng và đa dạng của danh mục. Danh mục quá lệ thuộc vào một website, một loại nguồn hoặc một nhóm tác giả có thể làm giảm chất lượng học thuật.

### 14.2. Công thức C7 cấp reference

C7 là điểm cấp reference nhưng phụ thuộc vào toàn bộ danh mục:

```text
C7_i = 5 × Diversity_Contribution_i
```

Trong đó:

```text
Diversity_Contribution_i = 0.40 × Source_Type_Diversity_i
                         + 0.30 × Publisher_Diversity_i
                         + 0.20 × Author_Diversity_i
                         + 0.10 × Domain_Diversity_i
```

### 14.3. Quy tắc tính nhanh trong v1.0

| Điều kiện | C7 gợi ý |
|---|---:|
| Nguồn thuộc nhóm học thuật/kỹ thuật chính, không trùng lặp bất thường | 4–5 |
| Nguồn cùng loại với nhiều nguồn khác nhưng vẫn hợp lý | 3–4 |
| Nguồn thuộc domain/publisher xuất hiện quá nhiều | 1–3 |
| Nguồn trùng lặp, copy cùng metadata hoặc không đóng góp mới | 0–1 |

### 14.4. Penalty cấp báo cáo liên quan diversity

| Điều kiện | Report Penalty |
|---|---:|
| > 50% nguồn từ cùng một domain không phải official docs | -5 |
| > 40% nguồn là blog/website không học thuật | -5 |
| Có nhiều reference trùng lặp nội dung | -3 đến -8 |
| Không có nguồn học thuật/kỹ thuật chính thức nào | -10 |

---

## 15. C8 - Academic Risk Integrity Score

### 15.1. Mục tiêu

C8 đánh giá mức rủi ro học thuật của từng nguồn. Khác với C1–C7, C8 bắt đầu từ điểm tối đa và bị trừ khi có dấu hiệu rủi ro.

### 15.2. Công thức C8

```text
C8 = clamp(5 - Risk_Deduction, 0, 5)
```

### 15.3. Bảng Risk_Deduction

| Mã rủi ro | Mức độ | Điểm trừ | Điều kiện |
|---|---|---:|---|
| `RISK_DOI_INVALID` | High | 3 | DOI sai định dạng hoặc không resolve. |
| `RISK_METADATA_MISMATCH` | High | 3 | DOI trả về bài khác hoặc title/author/year không khớp. |
| `RISK_FAKE_SUSPECTED` | Critical | 5 | Nhiều bằng chứng cho thấy nguồn có thể bịa đặt. |
| `RISK_DEAD_URL` | Medium | 2 | URL không truy cập được. |
| `RISK_NO_AUTHOR_NO_DATE` | Medium | 2 | Không có tác giả và ngày. |
| `RISK_NON_ACADEMIC` | Medium | 2 | Nguồn không học thuật nhưng dùng cho lập luận học thuật trọng tâm. |
| `RISK_AI_GENERATED_UNVERIFIED` | High | 4 | Nguồn có dấu hiệu do AI tạo hoặc không thể xác minh. |
| `RISK_PREDATORY_SUSPECTED` | High | 4 | Venue có dấu hiệu bất thường, cần kiểm tra thủ công. |
| `RISK_RETRACTED_SUSPECTED` | Critical | 5 | Có dấu hiệu tài liệu bị rút lại, nếu API hỗ trợ. |

### 15.4. Mức rủi ro tổng hợp

| Risk_Deduction | Risk Level | Ý nghĩa |
|---:|---|---|
| 0 | Low | Không phát hiện rủi ro đáng kể. |
| 1–2 | Medium | Có lỗi cần xem lại. |
| 3–4 | High | Có rủi ro học thuật rõ. |
| >=5 | Critical | Cần kiểm tra thủ công, có thể loại nguồn. |

---

## 16. Công thức cấp báo cáo

### 16.1. Weighted Average

Với `n` tài liệu tham khảo:

```text
Report_TS_Raw = Sum(W_i × RTS_i) / Sum(W_i)
```

Trong đó:

```text
W_i >= 1
```

Nếu chưa xác định vai trò reference:

```text
W_i = 1
```

### 16.2. Trọng số theo vai trò citation

Nếu hệ thống nhận diện được vị trí và vai trò citation trong nội dung:

| Vai trò | Điều kiện | W_i |
|---|---|---:|
| Core method/model | Được trích trong phần phương pháp, mô hình, kiến trúc, thuật toán | 1.50 |
| Key evidence/result | Được dùng làm căn cứ so sánh hoặc chứng minh kết quả | 1.30 |
| Background/theory | Nguồn nền tảng, định nghĩa, tổng quan | 1.15 |
| Implementation doc | Tài liệu chính thức cho công nghệ được dùng | 1.20 |
| Incidental citation | Trích dẫn phụ, ít liên quan lập luận chính | 1.00 |
| Uncited reference | Có trong danh mục nhưng không xuất hiện trong nội dung | 0.80 |

### 16.3. Report Penalty

```text
Report_Trust_Score = clamp(Report_TS_Raw - Report_Penalty, 0, 100)
```

Report Penalty là tổng điểm trừ cấp báo cáo:

```text
Report_Penalty = P_min_refs + P_unverified_ratio + P_critical_flags + P_diversity + P_citation_consistency + P_duplicate + P_missing_core_sources
```

### 16.4. Bảng penalty cấp báo cáo

| Penalty | Điều kiện | Điểm trừ |
|---|---|---:|
| `P_min_refs` | Số nguồn thấp hơn ngưỡng tối thiểu | 0–10 |
| `P_unverified_ratio` | Tỷ lệ nguồn không xác minh được quá cao | 0–15 |
| `P_critical_flags` | Có hard flag nghiêm trọng | 0–20 |
| `P_diversity` | Danh mục lệ thuộc quá nhiều vào một domain/loại nguồn | 0–10 |
| `P_citation_consistency` | Trộn style, nhiều orphan citation | 0–10 |
| `P_duplicate` | Nhiều nguồn trùng lặp | 0–8 |
| `P_missing_core_sources` | Thiếu nguồn học thuật/kỹ thuật chính cho chủ đề | 0–12 |

### 16.5. Công thức từng penalty

#### 16.5.1. P_min_refs

```text
if n >= Min_Reference_Count: P_min_refs = 0
else P_min_refs = 10 × (Min_Reference_Count - n) / Min_Reference_Count
```

Ngưỡng gợi ý:

| Loại bài | Min_Reference_Count |
|---|---:|
| Bài tập ngắn | 5 |
| Tiểu luận môn học | 8 |
| Báo cáo đồ án nhỏ | 10 |
| Báo cáo kỹ thuật/đề tài | 12 |
| Nghiên cứu khoa học sinh viên | 15 |

#### 16.5.2. P_unverified_ratio

```text
Unverified_Ratio = count(unverified or fake_suspected references) / n
```

| Unverified_Ratio | Penalty |
|---:|---:|
| <= 0.10 | 0 |
| 0.10–0.25 | 3 |
| 0.25–0.40 | 7 |
| 0.40–0.60 | 12 |
| > 0.60 | 15 |

#### 16.5.3. P_critical_flags

```text
P_critical_flags = min(20, 8 × critical_count + 3 × high_count)
```

#### 16.5.4. P_citation_consistency

| Điều kiện | Penalty |
|---|---:|
| Không phát hiện lỗi style đáng kể | 0 |
| Trộn 2 style nhưng ít lỗi | 3 |
| Trộn nhiều style hoặc nhiều reference thiếu trường | 6 |
| Nhiều orphan citation/missing in-text citation | 10 |

---

## 17. Phân loại điểm

### 17.1. Phân loại Reference Trust Score

| Điểm | Nhãn | Ý nghĩa | Hành động |
|---:|---|---|---|
| 85–100 | `very_reliable` | Nguồn rất tốt, xác minh được, phù hợp và ít lỗi. | Có thể giữ. |
| 70–84.9 | `reliable` | Nguồn đạt yêu cầu, có lỗi nhỏ. | Có thể giữ, chỉnh citation nếu cần. |
| 55–69.9 | `needs_review` | Nguồn có điểm trung bình, cần kiểm tra. | Rà soát metadata, relevance hoặc citation. |
| 40–54.9 | `low_reliability` | Nguồn yếu hoặc không đủ phù hợp. | Cân nhắc thay thế. |
| 0–39.9 | `not_reliable` | Nguồn không đáng tin hoặc có rủi ro cao. | Nên thay thế hoặc kiểm tra thủ công. |

### 17.2. Phân loại Report Trust Score

| Điểm | Nhãn | Ý nghĩa | Hành động |
|---:|---|---|---|
| 85–100 | `excellent_reference_quality` | Danh mục nguồn mạnh, phù hợp, có khả năng giải trình tốt. | Sẵn sàng nộp/chấm. |
| 70–84.9 | `acceptable_reference_quality` | Đạt yêu cầu, còn lỗi nhỏ. | Sửa lỗi được cảnh báo. |
| 55–69.9 | `requires_revision` | Cần chỉnh sửa rõ rệt. | Bổ sung nguồn, sửa citation, thay nguồn yếu. |
| 40–54.9 | `weak_reference_quality` | Danh mục yếu, có nhiều rủi ro. | Nên sửa trước khi nộp/chấm. |
| 0–39.9 | `unacceptable_reference_quality` | Không đủ tin cậy ở mức danh mục. | Không nên chấp nhận nếu chưa rà soát. |

### 17.3. Cơ chế override nhãn bởi hard flag

Dù điểm số cao, nhãn có thể bị hạ nếu có hard flag:

| Điều kiện | Quy tắc override |
|---|---|
| Có `fake_suspected` | Reference label tối đa là `not_reliable`. |
| Có `metadata_mismatch` nghiêm trọng | Reference label tối đa là `low_reliability`. |
| Có `critical_count >= 2` trong báo cáo | Report label tối đa là `requires_revision`. |
| `Unverified_Ratio > 0.60` | Report label tối đa là `weak_reference_quality`. |
| Không có danh mục tài liệu tham khảo | Report score = 0, label = `unacceptable_reference_quality`. |

---

## 18. Confidence Score

### 18.1. Mục tiêu

`Confidence Score` đo mức độ tin cậy của chính hệ thống đối với kết quả đã tính. Điểm này giúp giảng viên biết kết quả nào có thể dùng trực tiếp và kết quả nào cần kiểm tra thủ công.

### 18.2. Công thức Confidence Score cấp reference

```text
Reference_Confidence = 100 × (
    0.25 × Extraction_Confidence
  + 0.30 × Verification_Confidence
  + 0.25 × Relevance_Confidence
  + 0.20 × Citation_Parse_Confidence
)
```

Các thành phần đều chuẩn hóa về 0–1.

### 18.3. Quy tắc tính nhanh

| Thành phần | Điều kiện làm tăng confidence | Điều kiện làm giảm confidence |
|---|---|---|
| Extraction | Reference tách rõ, ít lỗi OCR/parser | Reference bị vỡ dòng, thiếu dấu câu, cấu trúc lẫn nội dung khác |
| Verification | DOI/metadata API trả dữ liệu khớp | API lỗi, thiếu DOI/URL, metadata mâu thuẫn |
| Relevance | Có abstract, keywords, citation context | Chỉ có tiêu đề, không có citation context |
| Citation Parse | Style nhận diện rõ | Trộn style, format không chuẩn |

### 18.4. Diễn giải Confidence Score

| Confidence | Nhãn | Ý nghĩa |
|---:|---|---|
| 85–100 | `high_confidence` | Kết quả có cơ sở dữ liệu tốt. |
| 70–84.9 | `moderate_confidence` | Có thể dùng, nhưng nên xem cảnh báo. |
| 50–69.9 | `low_confidence` | Cần kiểm tra thủ công một phần. |
| 0–49.9 | `very_low_confidence` | Không nên dùng kết quả như kết luận cuối. |

---

## 19. Chuẩn hóa dữ liệu trước khi tính điểm

### 19.1. Chuẩn hóa text

Trước khi match metadata hoặc tính embedding:

1. Chuyển Unicode về dạng chuẩn NFC.
2. Loại bỏ khoảng trắng thừa.
3. Chuẩn hóa dấu ngoặc, dấu gạch ngang, dấu chấm câu.
4. Chuyển DOI về lowercase.
5. Loại bỏ prefix `https://doi.org/` khi so sánh DOI.
6. Chuẩn hóa tên tác giả theo họ/tên viết tắt nếu có thể.
7. Chuẩn hóa tiêu đề bằng cách bỏ dấu câu không cần thiết và lowercase.
8. Không xóa thuật ngữ kỹ thuật quan trọng như `BERT`, `CNN`, `SQL`, `React`, `FastAPI`, `OAuth`.

### 19.2. Chuẩn hóa DOI

```text
Input: https://doi.org/10.1145/1234567.1234568
Output: 10.1145/1234567.1234568
```

Quy tắc DOI hợp lệ ở mức cơ bản:

```regex
^10\.\d{4,9}/[-._;()/:A-Z0-9]+$
```

So khớp không phân biệt hoa thường.

### 19.3. Chuẩn hóa URL

| Bước | Mô tả |
|---|---|
| 1 | Thêm scheme nếu thiếu: `https://`. |
| 2 | Loại bỏ tracking parameters nếu không cần: `utm_*`. |
| 3 | Chuẩn hóa domain lowercase. |
| 4 | Kiểm tra HTTP status nếu có backend hỗ trợ. |
| 5 | Phân biệt URL chính thức và mirror/copy. |

### 19.4. Chuẩn hóa loại nguồn

| Dấu hiệu | Loại nguồn |
|---|---|
| Có DOI, journal name, volume/issue/pages | `journal_article` |
| Có proceedings/conference | `conference_paper` |
| Có publisher, ISBN hoặc book title | `book` hoặc `book_chapter` |
| Có arXiv ID | `preprint` |
| Domain docs.* hoặc official vendor docs | `official_documentation` |
| Có standard ID, RFC, ISO, IEEE Std | `standard_specification` |
| GitHub/GitLab/Zenodo | `software_or_dataset` |
| Blog/site article | `web_article` |
| StackOverflow/Reddit/forum | `forum_or_qna` |

---

## 20. Xử lý theo loại nguồn

### 20.1. Journal article

| Thành phần | Quy tắc ưu tiên |
|---|---|
| C1 | Bắt buộc title, author, year, journal. DOI khuyến nghị mạnh. |
| C2 | Ưu tiên DOI/Crossref/OpenAlex/Semantic Scholar. |
| C3 | Điểm cao nếu venue học thuật rõ. |
| C4 | Dùng title + abstract + keywords. |
| C5 | Theo lĩnh vực. |
| C6 | APA/IEEE/ACM thường yêu cầu volume/issue/pages/DOI. |

### 20.2. Conference paper

| Thành phần | Quy tắc ưu tiên |
|---|---|
| C1 | Cần author, year, title, conference/proceedings. |
| C2 | DOI/DBLP/Semantic Scholar/OpenAlex. |
| C3 | Điểm cao nếu conference thuộc lĩnh vực CNTT và có proceedings rõ. |
| C4 | Dùng abstract nếu có. |
| C5 | Với AI/CS, nguồn conference thường cần cập nhật. |

### 20.3. Official documentation

| Thành phần | Quy tắc ưu tiên |
|---|---|
| C1 | Cần organization/vendor, title, URL, version/date nếu có. |
| C2 | Xác minh bằng official domain và URL sống. |
| C3 | Có thể cao nếu tài liệu chính thức của công nghệ đang dùng. |
| C4 | Cao nếu báo cáo triển khai đúng công nghệ đó. |
| C5 | Ưu tiên version/date gần với version trong dự án. |
| C6 | Cần ngày truy cập nếu style yêu cầu. |

### 20.4. Blog kỹ thuật

| Thành phần | Quy tắc ưu tiên |
|---|---|
| C1 | Cần author/org, title, site, date, URL. |
| C2 | URL sống, domain rõ. |
| C3 | Thường thấp hơn paper/docs chính thức. |
| C4 | Có thể phù hợp cho kinh nghiệm triển khai, nhưng không nên làm nguồn lõi cho khẳng định học thuật. |
| C5 | Cần cập nhật nếu liên quan công nghệ thay đổi nhanh. |

### 20.5. Preprint

| Thành phần | Quy tắc ưu tiên |
|---|---|
| C1 | Cần author, year, title, arXiv ID hoặc DOI. |
| C2 | Xác minh qua arXiv/Semantic Scholar/OpenAlex. |
| C3 | Trung bình, vì chưa chắc peer-reviewed. |
| C4 | Có thể rất phù hợp với AI/ML mới. |
| C5 | Thường mới, nhưng cần cảnh báo `not_peer_reviewed`. |

---

## 21. Hard flags và soft flags

### 21.1. Hard flags

Hard flag là cảnh báo nghiêm trọng. Một hard flag có thể hạ nhãn kết luận dù điểm tổng chưa quá thấp.

| Mã | Điều kiện | Tác động |
|---|---|---|
| `HF_FAKE_SUSPECTED` | DOI/URL/metadata đều không xác minh được và nhiều trường bất thường | Reference label tối đa `not_reliable`. |
| `HF_DOI_METADATA_MISMATCH` | DOI tồn tại nhưng title/author/year không khớp | Reference label tối đa `low_reliability`. |
| `HF_RETRACTED_SUSPECTED` | Nguồn có dấu hiệu bị rút lại | Reference label tối đa `not_reliable`. |
| `HF_NO_REFERENCES` | Không phát hiện danh mục tài liệu tham khảo | Report score = 0. |
| `HF_TOO_MANY_UNVERIFIED` | Tỷ lệ unverified > 60% | Report label tối đa `weak_reference_quality`. |
| `HF_ORPHAN_CITATION_MANY` | Nhiều citation trong bài không có reference tương ứng | Report label tối đa `requires_revision`. |

### 21.2. Soft flags

| Mã | Điều kiện | Tác động |
|---|---|---|
| `SF_MISSING_DOI` | Không có DOI nhưng vẫn có metadata khác | Trừ C1/C2 nhẹ. |
| `SF_OLD_SOURCE` | Nguồn cũ hơn freshness window | Giảm C5. |
| `SF_NON_ACADEMIC_SOURCE` | Nguồn blog/website/forum | Giảm C3 nếu dùng cho lập luận học thuật. |
| `SF_LOW_RELEVANCE` | C4 thấp | Gợi ý thay nguồn phù hợp hơn. |
| `SF_CITATION_STYLE_MIXED` | Trộn nhiều style | Giảm C6 và report penalty. |
| `SF_LOW_CONFIDENCE` | Confidence thấp | Cần kiểm tra thủ công. |

---

## 22. Output JSON đề xuất

### 22.1. Reference-level output

```json
{
  "reference_id": "ref_001",
  "raw_text": "Nguyen, A. (2024). ...",
  "normalized_metadata": {
    "title": "Example paper title",
    "authors": ["Nguyen A"],
    "year": 2024,
    "venue": "Example Conference",
    "publisher": "Example Publisher",
    "doi": "10.xxxx/example",
    "url": "https://doi.org/10.xxxx/example",
    "source_type": "conference_paper"
  },
  "verification": {
    "status": "verified",
    "source": "Crossref",
    "external_record_id": "10.xxxx/example",
    "title_similarity": 0.97,
    "author_match": true,
    "year_match": true,
    "venue_match": true
  },
  "scores": {
    "c1_metadata_completeness": 9.5,
    "c2_metadata_verification": 18.0,
    "c3_source_credibility": 17.0,
    "c4_relevance": 16.5,
    "c5_recency": 9.0,
    "c6_citation_quality": 8.0,
    "c7_diversity_contribution": 4.5,
    "c8_academic_integrity": 5.0,
    "reference_trust_score": 87.5
  },
  "confidence": {
    "extraction_confidence": 0.91,
    "verification_confidence": 0.94,
    "relevance_confidence": 0.82,
    "citation_parse_confidence": 0.88,
    "reference_confidence_score": 89.5
  },
  "classification": {
    "label": "very_reliable",
    "risk_level": "low"
  },
  "flags": [],
  "recommendations": [
    "Có thể giữ nguồn này trong danh mục tài liệu tham khảo."
  ]
}
```

### 22.2. Report-level output

```json
{
  "document_id": "doc_001",
  "file_name": "student_report.pdf",
  "detected_citation_style": "APA_7",
  "reference_count": 12,
  "summary": {
    "report_trust_score_raw": 78.4,
    "report_penalty": 5.0,
    "report_trust_score": 73.4,
    "label": "acceptable_reference_quality",
    "risk_level": "medium",
    "confidence_score": 81.2
  },
  "statistics": {
    "verified_count": 8,
    "partially_verified_count": 2,
    "unverified_count": 2,
    "fake_suspected_count": 0,
    "average_relevance": 14.8,
    "average_citation_quality": 7.5,
    "old_source_count": 3,
    "non_academic_source_count": 2,
    "orphan_citation_count": 1,
    "uncited_reference_count": 2
  },
  "penalties": {
    "p_min_refs": 0,
    "p_unverified_ratio": 3,
    "p_critical_flags": 0,
    "p_diversity": 0,
    "p_citation_consistency": 2,
    "p_duplicate": 0,
    "p_missing_core_sources": 0
  },
  "top_warnings": [
    {
      "code": "SF_CITATION_STYLE_MIXED",
      "severity": "medium",
      "message": "Danh mục có dấu hiệu trộn nhiều kiểu trích dẫn."
    }
  ],
  "recommendations": [
    "Chuẩn hóa toàn bộ danh mục theo một style duy nhất.",
    "Bổ sung DOI hoặc URL chính thức cho các nguồn chưa xác minh được.",
    "Thay thế các nguồn blog không học thuật nếu đang dùng cho lập luận chính."
  ],
  "references": [
    "ref_001",
    "ref_002"
  ]
}
```

---

## 23. Mapping sang cơ sở dữ liệu

### 23.1. Bảng `references`

| Field | Type gợi ý | Mô tả |
|---|---|---|
| `id` | UUID | ID reference. |
| `document_id` | UUID | ID file/báo cáo. |
| `raw_text` | TEXT | Chuỗi reference gốc. |
| `normalized_title` | TEXT | Tiêu đề chuẩn hóa. |
| `authors_json` | JSONB | Danh sách tác giả. |
| `year` | INT | Năm xuất bản. |
| `venue` | TEXT | Journal/conference/site/publisher. |
| `doi` | TEXT | DOI nếu có. |
| `url` | TEXT | URL nếu có. |
| `source_type` | TEXT | Loại nguồn. |
| `created_at` | TIMESTAMP | Thời điểm tạo. |

### 23.2. Bảng `reference_scores`

| Field | Type gợi ý | Mô tả |
|---|---|---|
| `id` | UUID | ID score. |
| `reference_id` | UUID | Reference được chấm. |
| `c1_metadata_completeness` | NUMERIC(5,2) | C1. |
| `c2_metadata_verification` | NUMERIC(5,2) | C2. |
| `c3_source_credibility` | NUMERIC(5,2) | C3. |
| `c4_relevance` | NUMERIC(5,2) | C4. |
| `c5_recency` | NUMERIC(5,2) | C5. |
| `c6_citation_quality` | NUMERIC(5,2) | C6. |
| `c7_diversity_contribution` | NUMERIC(5,2) | C7. |
| `c8_academic_integrity` | NUMERIC(5,2) | C8. |
| `reference_trust_score` | NUMERIC(5,2) | Tổng điểm. |
| `confidence_score` | NUMERIC(5,2) | Confidence. |
| `classification_label` | TEXT | Nhãn phân loại. |
| `risk_level` | TEXT | Mức rủi ro. |
| `evidence_json` | JSONB | Bằng chứng chấm điểm. |
| `flags_json` | JSONB | Danh sách cảnh báo. |

### 23.3. Bảng `report_scores`

| Field | Type gợi ý | Mô tả |
|---|---|---|
| `id` | UUID | ID report score. |
| `document_id` | UUID | ID file/báo cáo. |
| `reference_count` | INT | Số nguồn tham khảo. |
| `report_trust_score_raw` | NUMERIC(5,2) | Điểm trước penalty. |
| `report_penalty` | NUMERIC(5,2) | Tổng penalty. |
| `report_trust_score` | NUMERIC(5,2) | Điểm cuối. |
| `confidence_score` | NUMERIC(5,2) | Confidence chung. |
| `classification_label` | TEXT | Nhãn tổng thể. |
| `risk_level` | TEXT | Risk tổng thể. |
| `statistics_json` | JSONB | Thống kê nguồn. |
| `recommendations_json` | JSONB | Gợi ý xử lý. |

---

## 24. API response gợi ý

### 24.1. Endpoint tính điểm

```http
POST /api/v1/documents/{document_id}/score
```

Response:

```json
{
  "document_id": "doc_001",
  "status": "scored",
  "report_score": {
    "trust_score": 73.4,
    "label": "acceptable_reference_quality",
    "risk_level": "medium",
    "confidence_score": 81.2
  },
  "reference_scores_url": "/api/v1/documents/doc_001/references/scores"
}
```

### 24.2. Endpoint xem chi tiết từng reference

```http
GET /api/v1/documents/{document_id}/references/scores
```

Response rút gọn:

```json
{
  "document_id": "doc_001",
  "references": [
    {
      "reference_id": "ref_001",
      "title": "Example paper title",
      "source_type": "conference_paper",
      "trust_score": 87.5,
      "label": "very_reliable",
      "risk_level": "low",
      "top_flags": []
    }
  ]
}
```

### 24.3. Endpoint xuất báo cáo

```http
GET /api/v1/documents/{document_id}/report/export?format=pdf
```

Định dạng hỗ trợ v1.0:

| Format | Dùng cho |
|---|---|
| PDF | Báo cáo gửi giảng viên. |
| DOCX | Báo cáo chỉnh sửa hoặc phụ lục. |
| XLSX | Bảng điểm chi tiết từng reference. |
| JSON | Debug và tích hợp FE. |

---

## 25. Pseudocode thuật toán tổng thể

```python
def compute_trust_score(document):
    text = extract_text(document.file)
    reference_section = detect_reference_section(text)
    raw_references = parse_references(reference_section)

    report_context = build_report_context(text)
    scored_refs = []

    for raw_ref in raw_references:
        metadata = extract_metadata(raw_ref)
        normalized = normalize_metadata(metadata)
        verification = verify_metadata(normalized)

        c1 = compute_metadata_completeness(normalized)
        c2 = compute_metadata_verification(normalized, verification)
        c3 = compute_source_credibility(normalized, verification)
        c4 = compute_relevance(normalized, verification, report_context)
        c5 = compute_recency(normalized, report_context.domain)
        c6 = compute_citation_quality(raw_ref, text, document.expected_style)
        c7 = None  # computed after all references are available
        c8 = compute_academic_risk_integrity(normalized, verification)

        scored_refs.append({
            "raw_ref": raw_ref,
            "metadata": normalized,
            "verification": verification,
            "scores": {
                "c1": c1,
                "c2": c2,
                "c3": c3,
                "c4": c4,
                "c5": c5,
                "c6": c6,
                "c8": c8,
            },
        })

    diversity_scores = compute_diversity_scores(scored_refs)

    for idx, item in enumerate(scored_refs):
        item["scores"]["c7"] = diversity_scores[idx]
        item["reference_trust_score"] = clamp(sum(item["scores"].values()), 0, 100)
        item["confidence_score"] = compute_reference_confidence(item)
        item["label"] = classify_reference(item)
        item["flags"] = generate_reference_flags(item)

    raw_report_score = weighted_average_reference_scores(scored_refs, text)
    report_penalty = compute_report_penalty(scored_refs, text)
    final_report_score = clamp(raw_report_score - report_penalty, 0, 100)

    return {
        "references": scored_refs,
        "report": {
            "raw_score": raw_report_score,
            "penalty": report_penalty,
            "trust_score": final_report_score,
            "label": classify_report(final_report_score, scored_refs),
            "confidence_score": compute_report_confidence(scored_refs),
            "recommendations": generate_report_recommendations(scored_refs),
        }
    }
```

---

## 26. Ví dụ tính điểm

### 26.1. Ví dụ 1: Paper học thuật phù hợp, DOI xác minh được

| Thành phần | Điểm |
|---|---:|
| C1 Metadata Completeness | 10 |
| C2 Metadata Verification | 19 |
| C3 Source Credibility | 18 |
| C4 Relevance | 17 |
| C5 Recency | 9 |
| C6 Citation Quality | 8 |
| C7 Diversity Contribution | 5 |
| C8 Academic Integrity | 5 |
| **Reference Trust Score** | **91** |

Kết luận:

```text
very_reliable
```

### 26.2. Ví dụ 2: Blog kỹ thuật chính thức, phù hợp với phần triển khai

| Thành phần | Điểm |
|---|---:|
| C1 | 8 |
| C2 | 12 |
| C3 | 13 |
| C4 | 18 |
| C5 | 9 |
| C6 | 7 |
| C7 | 4 |
| C8 | 4 |
| **Reference Trust Score** | **75** |

Kết luận:

```text
reliable
```

Diễn giải: Không phải nguồn học thuật mạnh, nhưng có thể chấp nhận nếu là official documentation/blog kỹ thuật chính thức liên quan trực tiếp đến công nghệ triển khai.

### 26.3. Ví dụ 3: Website không rõ tác giả, không ngày, phù hợp yếu

| Thành phần | Điểm |
|---|---:|
| C1 | 4 |
| C2 | 3 |
| C3 | 3 |
| C4 | 6 |
| C5 | 2 |
| C6 | 4 |
| C7 | 2 |
| C8 | 1 |
| **Reference Trust Score** | **25** |

Kết luận:

```text
not_reliable
```

Gợi ý:

```text
Thay bằng paper, documentation chính thức hoặc technical report có metadata rõ.
```

### 26.4. Ví dụ 4: DOI tồn tại nhưng metadata không khớp

| Thành phần | Điểm |
|---|---:|
| C1 | 9 |
| C2 | 5 |
| C3 | 8 |
| C4 | 10 |
| C5 | 8 |
| C6 | 7 |
| C7 | 4 |
| C8 | 0 |
| **Reference Trust Score trước override** | **51** |

Hard flag:

```text
HF_DOI_METADATA_MISMATCH
```

Nhãn sau override:

```text
low_reliability
```

---

## 27. Bộ kiểm thử tối thiểu cho Trust Score

| Test Case | Input | Expected |
|---|---|---|
| TS-001 | Reference có DOI hợp lệ, metadata khớp | C2 >= 16, label `reliable` trở lên. |
| TS-002 | Reference thiếu DOI nhưng có URL official docs | C2 trung bình, C3 có thể cao nếu official. |
| TS-003 | DOI sai định dạng | Flag `RISK_DOI_INVALID`, C2 thấp. |
| TS-004 | DOI trả về title khác | Flag `HF_DOI_METADATA_MISMATCH`. |
| TS-005 | Nguồn quá cũ trong AI/ML | C5 giảm mạnh nếu không phải seminal work. |
| TS-006 | Nguồn cũ nhưng là thuật toán nền tảng | C5 được seminal override nếu đánh dấu đúng. |
| TS-007 | Blog cá nhân dùng cho lập luận chính | C3 thấp, có `SF_NON_ACADEMIC_SOURCE`. |
| TS-008 | Trộn APA và IEEE | C6 giảm, report penalty citation consistency. |
| TS-009 | Reference không xuất hiện trong nội dung | C6 In_Text_Match giảm. |
| TS-010 | Citation trong nội dung không có reference | Flag `C6_ORPHAN_CITATION`. |
| TS-011 | Trên 60% nguồn unverified | Report label tối đa `weak_reference_quality`. |
| TS-012 | Không phát hiện danh mục tài liệu tham khảo | Report score = 0. |
| TS-013 | Nhiều nguồn cùng một domain blog | C7 giảm, P_diversity tăng. |
| TS-014 | API metadata lỗi tạm thời | Không gán fake; status `unverified`, confidence giảm. |
| TS-015 | Official documentation đúng version | C3/C4/C5 cao nếu phù hợp với báo cáo. |

---

## 28. Quy tắc hiển thị trên Frontend

### 28.1. Màu trạng thái gợi ý

| Điểm | Nhãn | Màu gợi ý |
|---:|---|---|
| 85–100 | Very Reliable | Xanh lá |
| 70–84 | Reliable | Xanh dương |
| 55–69 | Needs Review | Vàng |
| 40–54 | Low Reliability | Cam |
| 0–39 | Not Reliable | Đỏ |

### 28.2. Thông tin bắt buộc hiển thị với mỗi reference

| Trường | Bắt buộc |
|---|---|
| Reference text gốc | Có |
| Metadata đã trích xuất | Có |
| Trust Score | Có |
| Điểm C1–C8 | Có |
| Verification status | Có |
| Relevance label | Có |
| Risk level | Có |
| Warning flags | Có |
| Recommendation | Có |
| Evidence/source API | Khuyến nghị |

### 28.3. Thông tin bắt buộc hiển thị ở report summary

| Trường | Bắt buộc |
|---|---|
| Report Trust Score | Có |
| Nhãn phân loại | Có |
| Số lượng reference | Có |
| Tỷ lệ verified/unverified | Có |
| Số hard flags | Có |
| Average relevance | Có |
| Average citation quality | Có |
| Top warnings | Có |
| Gợi ý sửa ưu tiên | Có |

---

## 29. Quy tắc sinh gợi ý sửa

### 29.1. Gợi ý cấp reference

| Điều kiện | Gợi ý |
|---|---|
| C1 thấp | Bổ sung tác giả, năm, tiêu đề, nguồn công bố, DOI/URL. |
| C2 thấp | Kiểm tra lại DOI/URL hoặc thay bằng nguồn có metadata xác minh được. |
| C3 thấp | Thay bằng paper, sách, standard, official documentation hoặc technical report đáng tin hơn. |
| C4 thấp | Thay bằng nguồn liên quan trực tiếp đến chủ đề/phương pháp/công nghệ của báo cáo. |
| C5 thấp | Cập nhật nguồn mới hơn hoặc giải thích đây là công trình nền tảng. |
| C6 thấp | Chuẩn hóa citation theo một style duy nhất. |
| C7 thấp | Giảm phụ thuộc vào một domain/loại nguồn, bổ sung nguồn đa dạng hơn. |
| C8 thấp | Kiểm tra thủ công, thay nguồn nếu có dấu hiệu sai lệch hoặc không tồn tại. |

### 29.2. Gợi ý cấp báo cáo

TrustLens ưu tiên gợi ý theo thứ tự:

1. Sửa hard flags trước.
2. Thay nguồn `not_reliable`.
3. Xác minh các nguồn `unverified`.
4. Bổ sung nguồn học thuật/kỹ thuật chính thức.
5. Tăng relevance cho các phần phương pháp/công nghệ chính.
6. Chuẩn hóa citation style.
7. Bổ sung nguồn mới hơn cho lĩnh vực thay đổi nhanh.

---

## 30. Calibration và điều chỉnh trọng số

### 30.1. Lý do cần calibration

Trọng số v1.0 là thiết kế khởi tạo. Sau khi có dữ liệu thật, nhóm cần hiệu chỉnh để tránh:

1. Chấm quá cao nguồn không học thuật nhưng format đẹp.
2. Chấm quá thấp official documentation cần thiết cho đồ án phần mềm.
3. Phạt quá mạnh nguồn cũ nhưng là công trình nền tảng.
4. Đánh giá relevance sai do embedding chưa phù hợp tiếng Việt/tiếng Anh chuyên ngành.

### 30.2. Bộ dữ liệu calibration tối thiểu

| Nhóm mẫu | Số lượng gợi ý |
|---|---:|
| Báo cáo tốt, nguồn học thuật đầy đủ | 5–10 |
| Báo cáo trung bình, citation lỗi nhẹ | 5–10 |
| Báo cáo có nhiều blog/website | 5–10 |
| Báo cáo AI/ML cần nguồn mới | 5–10 |
| Báo cáo web/app có official docs | 5–10 |
| Reference có DOI sai/metadata mismatch | 10–20 reference |

### 30.3. Chỉ số đánh giá thuật toán

| Chỉ số | Mục tiêu |
|---|---|
| Agreement với giảng viên | So sánh nhãn TrustLens với nhãn chuyên gia. |
| Precision của hard flags | Hard flag phải ít báo sai. |
| Recall của nguồn rủi ro | Không bỏ sót nguồn bịa đặt/không tồn tại. |
| Correlation giữa Trust Score và đánh giá thủ công | Kiểm tra tính hợp lý của điểm tổng. |
| Stability | Cùng input phải trả kết quả ổn định. |
| Explainability coverage | Tỷ lệ điểm có evidence/reason đầy đủ. |

### 30.4. Quy tắc thay đổi trọng số

Mọi thay đổi trọng số phải ghi vào changelog:

```text
- Ngày thay đổi
- Người thay đổi
- Lý do thay đổi
- Bộ test bị ảnh hưởng
- Kết quả trước/sau
```

---

## 31. Changelog đề xuất

| Phiên bản | Ngày | Thay đổi |
|---|---|---|
| v1.0 | 07/06/2026 | Khởi tạo công thức Trust Score, C1–C8, Report Score, Penalty, Confidence Score, JSON output, test cases. |

---

## 32. Checklist triển khai v1.0

### 32.1. BA/DS/NLP

- [ ] Chốt trọng số C1–C8.
- [ ] Chốt ngưỡng phân loại Reference Trust Score.
- [ ] Chốt ngưỡng phân loại Report Trust Score.
- [ ] Chốt hard flags và soft flags.
- [ ] Tạo data mẫu cho từng loại nguồn.
- [ ] Tạo test case cho DOI đúng/sai, URL chết, nguồn cũ, relevance thấp.
- [ ] Kiểm thử embedding relevance với báo cáo mẫu.

### 32.2. Backend

- [ ] Tạo module `reference_parser`.
- [ ] Tạo module `metadata_normalizer`.
- [ ] Tạo module `metadata_verifier`.
- [ ] Tạo module `trust_score_engine`.
- [ ] Tạo bảng `references`.
- [ ] Tạo bảng `reference_scores`.
- [ ] Tạo bảng `report_scores`.
- [ ] Tạo API tính điểm.
- [ ] Lưu evidence JSON.
- [ ] Xử lý API timeout/fallback.

### 32.3. Frontend

- [ ] Hiển thị Report Trust Score.
- [ ] Hiển thị bảng reference và điểm từng nguồn.
- [ ] Hiển thị C1–C8 ở dạng breakdown.
- [ ] Hiển thị warning flags.
- [ ] Hiển thị recommendation.
- [ ] Thêm filter theo risk level.
- [ ] Thêm export report.

### 32.4. QA/Test

- [ ] Test file PDF.
- [ ] Test file DOCX.
- [ ] Test reference APA.
- [ ] Test reference IEEE.
- [ ] Test mixed style.
- [ ] Test DOI valid.
- [ ] Test DOI invalid.
- [ ] Test URL dead.
- [ ] Test no references.
- [ ] Test report with high unverified ratio.

---

## 33. Phụ lục A - Bảng điểm tóm tắt

| Thành phần | Max | Mục tiêu | Dữ liệu cần |
|---|---:|---|---|
| C1 Metadata Completeness | 10 | Đủ thông tin để đánh giá | Raw reference, parsed metadata |
| C2 Metadata Verification | 20 | Xác minh nguồn tồn tại và khớp metadata | DOI, Crossref, OpenAlex, Semantic Scholar, URL |
| C3 Source Credibility | 20 | Đánh giá uy tín nguồn | Source type, venue, publisher, citation/impact evidence |
| C4 Relevance | 20 | Đo phù hợp với báo cáo | Embedding, title, abstract, keywords, citation context |
| C5 Recency | 10 | Đo tính cập nhật | Publication year, field/domain, version |
| C6 Citation Quality | 10 | Đo đúng chuẩn trích dẫn | Citation style, fields, in-text match |
| C7 Diversity Contribution | 5 | Đo đóng góp vào đa dạng nguồn | Domain, source type, author, publisher |
| C8 Academic Integrity | 5 | Đo rủi ro học thuật | DOI mismatch, fake suspected, dead URL, non-academic risk |

---

## 34. Phụ lục B - Quy tắc cấu hình file YAML gợi ý

```yaml
trust_score:
  version: "1.0"
  components:
    c1_metadata_completeness: 10
    c2_metadata_verification: 20
    c3_source_credibility: 20
    c4_relevance: 20
    c5_recency: 10
    c6_citation_quality: 10
    c7_diversity_contribution: 5
    c8_academic_integrity: 5
  classification:
    very_reliable: [85, 100]
    reliable: [70, 84.9]
    needs_review: [55, 69.9]
    low_reliability: [40, 54.9]
    not_reliable: [0, 39.9]
  relevance:
    raw_similarity_min: 0.20
    raw_similarity_max: 0.80
    weights:
      title: 0.30
      abstract: 0.30
      keywords: 0.20
      citation_context: 0.20
  recency:
    ai_ml:
      freshness_window: 3
      maximum_age: 7
    cybersecurity:
      freshness_window: 3
      maximum_age: 6
    web_framework:
      freshness_window: 3
      maximum_age: 6
    data_science:
      freshness_window: 4
      maximum_age: 8
    algorithms_foundation:
      freshness_window: 10
      maximum_age: 30
  report_penalty:
    max_critical_flags: 20
    max_unverified_ratio: 15
    max_diversity: 10
    max_citation_consistency: 10
```

---

## 35. Kết luận

`Trust Score` v1.0 là mô hình điểm có cấu trúc, phù hợp để triển khai thử nghiệm cho TrustLens trong phạm vi cuộc thi và báo cáo kỹ thuật. Công thức này có ba đặc điểm chính:

1. **Có thể giải thích:** Mỗi điểm đều có lý do, bằng chứng và cảnh báo đi kèm.
2. **Có thể triển khai:** Các thành phần C1–C8 có thể chuyển thành module backend độc lập.
3. **Có thể hiệu chỉnh:** Trọng số, ngưỡng, penalty và rules có thể được cập nhật sau khi có dữ liệu đánh giá thực tế.

Trong v1.0, TrustLens không đưa ra phán quyết học thuật tuyệt đối. Hệ thống cung cấp chỉ báo định lượng và bằng chứng hỗ trợ giảng viên đánh giá danh mục tài liệu tham khảo một cách có hệ thống hơn.
