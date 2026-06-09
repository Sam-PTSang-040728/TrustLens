# TrustLens_Evaluation_Criteria_v1.0

**Tên tài liệu:** Bộ tiêu chí đánh giá tài liệu tham khảo cho TrustLens v1.0  
**Tên dự án:** TrustLens - Hệ thống thẩm định độ tin cậy và phù hợp của danh mục tài liệu tham khảo trong báo cáo Công nghệ Thông tin  
**Phiên bản:** v1.0  
**Ngày ban hành:** 07/06/2026  
**Phạm vi áp dụng:** Báo cáo, tiểu luận, đồ án và bài nộp học thuật thuộc lĩnh vực Công nghệ Thông tin, Khoa học Dữ liệu, AI, Web Application, An toàn thông tin và các lĩnh vực công nghệ liên quan.  
**Đối tượng sử dụng:** Giảng viên, nhóm phát triển TrustLens, người kiểm thử hệ thống, sinh viên cần kiểm tra chất lượng danh mục tài liệu tham khảo trước khi nộp bài.  
**Chủ sở hữu nghiệp vụ:** Product/BA + AI/NLP Owner.  
**Trạng thái:** Draft dùng cho v1.0.

---

```table-of-contents
```

---
## 1. Mục đích tài liệu

Tài liệu này định nghĩa bộ tiêu chí đánh giá danh mục tài liệu tham khảo trong TrustLens v1.0. Bộ tiêu chí được thiết kế để hệ thống có thể:

1. Kiểm tra tài liệu tham khảo có tồn tại học thuật hay không.
2. Đánh giá độ tin cậy của nguồn được trích dẫn.
3. Đánh giá mức độ phù hợp giữa tài liệu tham khảo và chủ đề báo cáo.
4. Đánh giá độ cập nhật của nguồn trong bối cảnh lĩnh vực Công nghệ Thông tin thay đổi nhanh.
5. Đánh giá chất lượng định dạng trích dẫn theo các chuẩn phổ biến như APA 7th, IEEE, MLA, ACM.
6. Tính điểm tổng hợp ở cấp từng tài liệu tham khảo và cấp toàn bộ báo cáo.
7. Sinh cảnh báo, kết luận và gợi ý chỉnh sửa cho giảng viên hoặc sinh viên.

Tài liệu này không thay thế quyết định chuyên môn của giảng viên. TrustLens cung cấp cơ chế chấm điểm có căn cứ, giải thích được và có thể kiểm tra lại.

---

## 2. Căn cứ thiết kế tiêu chí

Bộ tiêu chí v1.0 được xây dựng dựa trên các yêu cầu nghiệp vụ của TrustLens:

- Hệ thống tiếp nhận file báo cáo của sinh viên, phân tích danh mục tài liệu tham khảo và đưa ra kết quả đánh giá.
- Product/BA + AI/NLP Owner chịu trách nhiệm thiết kế tiêu chí đánh giá, xây dựng quy tắc học thuật và mô hình Trust Score.
- Backend cần tích hợp metadata API để xác minh DOI, tiêu đề, tác giả, năm xuất bản và nguồn công bố.
- Các nhóm chức năng chính gồm: trích xuất tài liệu tham khảo, chuẩn hóa citation, kiểm tra tồn tại học thuật, đánh giá độ tin cậy, đánh giá độ cập nhật, đánh giá độ phù hợp, chấm điểm, cảnh báo, kết luận và xuất kết quả.
- Cuộc thi IT Solution Challenge 2026 nhấn mạnh chất lượng kỹ thuật, chất lượng dữ liệu, quy trình xử lý dữ liệu, hiệu quả triển khai, tính ứng dụng và khả năng giải trình sản phẩm.

---

## 3. Phạm vi đánh giá

### 3.1. Đối tượng được đánh giá

TrustLens v1.0 đánh giá các đối tượng sau:

| Mã đối tượng | Đối tượng | Mô tả |
|---|---|---|
| OBJ-01 | Tài liệu tham khảo riêng lẻ | Một mục trong danh mục tài liệu tham khảo cuối bài. |
| OBJ-02 | Danh mục tài liệu tham khảo | Toàn bộ danh sách tài liệu tham khảo của báo cáo. |
| OBJ-03 | Mối liên hệ giữa tài liệu tham khảo và nội dung báo cáo | Mức độ nguồn được trích dẫn có liên quan tới chủ đề, phương pháp, công nghệ hoặc lập luận trong báo cáo. |
| OBJ-04 | Hình thức citation | Cấu trúc, trường thông tin, tính nhất quán và chuẩn trích dẫn. |
| OBJ-05 | Rủi ro học thuật | Nguồn không xác minh được, nguồn có khả năng bịa đặt, nguồn quá cũ, nguồn không học thuật hoặc trích dẫn sai. |

### 3.2. Đối tượng chưa đánh giá trong v1.0

| Mã giới hạn | Nội dung chưa xử lý đầy đủ | Hướng mở rộng |
|---|---|---|
| LIM-01 | Kiểm tra đạo văn toàn văn | Tích hợp plagiarism detection ở phiên bản sau. |
| LIM-02 | Kiểm tra chính xác từng câu được trích dẫn | Cần mapping citation-in-context với nội dung nguồn gốc. |
| LIM-03 | Xác minh nội dung toàn văn của bài báo có paywall | Chỉ dùng metadata, abstract, title, venue và các trường công khai. |
| LIM-04 | Phát hiện tạp chí săn mồi ở mức pháp lý | Cần danh sách kiểm chứng độc lập và quy trình cập nhật thủ công. |
| LIM-05 | Đánh giá chất lượng phương pháp nghiên cứu của từng bài báo | Cần mô hình review học thuật chuyên sâu theo lĩnh vực. |

---

## 4. Thuật ngữ và định nghĩa

| Thuật ngữ              | Định nghĩa                                                                                                                                                       |
| ---------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Reference              | Một mục tài liệu tham khảo trong danh mục cuối bài.                                                                                                              |
| Citation               | Dấu hiệu trích dẫn trong nội dung bài, ví dụ `[1]`, `(Nguyen, 2024)` hoặc footnote.                                                                              |
| Bibliographic metadata | Thông tin thư mục: tác giả, năm, tiêu đề, nguồn công bố, DOI, URL, nhà xuất bản, số tập, số kỳ, trang.                                                           |
| DOI                    | Digital Object Identifier, mã định danh số cho tài liệu học thuật.                                                                                               |
| Venue                  | Nơi công bố: journal, conference, book, repository, website, documentation site.                                                                                 |
| Trust Score            | Điểm tổng hợp phản ánh mức độ đáng tin cậy và phù hợp của tài liệu tham khảo.                                                                                    |
| Relevance Score        | Điểm phù hợp giữa tài liệu tham khảo và chủ đề/nội dung báo cáo.                                                                                                 |
| Citation Quality Score | Điểm đánh giá hình thức và tính đầy đủ của trích dẫn.                                                                                                            |
| Metadata Verification  | Quá trình đối chiếu DOI, tiêu đề, tác giả, năm, nguồn công bố với CSDL bên ngoài.                                                                                |
| Academic Source        | Nguồn có tính học thuật hoặc kỹ thuật đáng kiểm chứng: journal, conference, sách chuyên khảo, chuẩn kỹ thuật, tài liệu chính thức của tổ chức/công ty công nghệ. |
| Non-academic Source    | Blog cá nhân, bài quảng cáo, nội dung mạng xã hội, trang không rõ tác giả, nguồn không có cơ chế kiểm chứng.                                                     |
| Seminal Work           | Công trình nền tảng được chấp nhận rộng rãi dù đã xuất bản lâu, ví dụ thuật toán, mô hình, giao thức hoặc chuẩn kỹ thuật kinh điển.                              |
| Hard Flag              | Cảnh báo nghiêm trọng có thể làm giảm mạnh điểm hoặc yêu cầu giảng viên kiểm tra thủ công.                                                                       |
| Soft Flag              | Cảnh báo nhẹ, không loại bỏ nguồn nhưng cần xem xét.                                                                                                             |
| Confidence Score       | Mức độ tự tin của hệ thống về kết quả đánh giá, tách biệt với Trust Score.                                                                                       |

---

## 5. Nguyên tắc đánh giá

### 5.1. Nguyên tắc học thuật

1. **Có thể xác minh:** Nguồn nên có metadata đủ để đối chiếu qua DOI, tiêu đề, tác giả, năm hoặc URL chính thức.
2. **Phù hợp ngữ cảnh:** Nguồn tốt nhưng không liên quan đến chủ đề báo cáo vẫn bị giảm điểm phù hợp.
3. **Không đánh đồng mọi loại nguồn:** Journal, conference, sách, tiêu chuẩn kỹ thuật, tài liệu chính thức và blog kỹ thuật có vai trò khác nhau.
4. **Tính cập nhật phụ thuộc lĩnh vực:** AI, an toàn thông tin, cloud, framework web cần nguồn mới hơn so với các nội dung nền tảng như thuật toán cổ điển, toán học, cơ sở dữ liệu quan hệ.
5. **Citation đúng không đồng nghĩa nguồn tốt:** Hình thức trích dẫn đúng chỉ là một thành phần; nguồn vẫn phải tồn tại, đáng tin cậy và phù hợp.
6. **Điểm máy không thay thế đánh giá chuyên môn:** Giảng viên có quyền override kết quả nếu có lý do học thuật.

### 5.2. Nguyên tắc kỹ thuật

1. Mỗi điểm thành phần phải có bằng chứng dữ liệu đi kèm.
2. Mỗi cảnh báo phải có mã lỗi, mức độ và gợi ý xử lý.
3. Kết quả phải có khả năng truy vết từ output về dữ liệu đầu vào.
4. Hệ thống phải tách riêng `score`, `reason`, `evidence`, `confidence`.
5. Không gán nhãn nguồn là sai nếu thiếu dữ liệu; chỉ nên ghi là `unverified` hoặc `needs_review`.
6. Với nguồn không đủ metadata, ưu tiên cảnh báo kiểm tra thủ công thay vì loại bỏ tuyệt đối.

---

## 6. Mô hình đánh giá tổng quát

TrustLens v1.0 sử dụng mô hình đánh giá hai tầng:

| Tầng | Tên tầng | Đầu vào | Đầu ra |
|---|---|---|---|
| Level 1 | Reference-level Evaluation | Một tài liệu tham khảo đã chuẩn hóa | Reference Trust Score, điểm thành phần, cảnh báo. |
| Level 2 | Report-level Evaluation | Toàn bộ danh mục tài liệu tham khảo và nội dung báo cáo | Report Trust Score, phân loại tổng thể, thống kê lỗi, gợi ý chỉnh sửa. |

### 6.1. Reference Trust Score

Mỗi tài liệu tham khảo được chấm theo thang 100 điểm.

| Mã tiêu chí | Nhóm tiêu chí | Trọng số | Vai trò |
|---|---:|---:|---|
| C1 | Độ đầy đủ và khả năng trích xuất metadata | 10 | Kiểm tra tài liệu có đủ thông tin để đánh giá hay không. |
| C2 | Xác minh tồn tại học thuật và khớp metadata | 20 | Xác định nguồn có thật, có thể đối chiếu với CSDL hoặc URL chính thức. |
| C3 | Độ tin cậy học thuật/kỹ thuật của nguồn | 20 | Đánh giá uy tín nguồn công bố, loại nguồn, publisher, citation count, tính chính thống. |
| C4 | Độ phù hợp với chủ đề và nội dung báo cáo | 20 | Đo mức liên quan giữa nguồn và báo cáo. |
| C5 | Độ cập nhật theo lĩnh vực | 10 | Kiểm tra năm xuất bản có phù hợp với tốc độ thay đổi của lĩnh vực hay không. |
| C6 | Chất lượng định dạng citation | 10 | Kiểm tra đúng chuẩn, đầy đủ trường, nhất quán style. |
| C7 | Tính cân bằng và đa dạng nguồn | 5 | Giảm rủi ro phụ thuộc quá mức vào một loại nguồn, một website hoặc một tác giả. |
| C8 | Rủi ro học thuật và toàn vẹn nguồn | 5 | Phát hiện nguồn bịa đặt, DOI sai, URL chết, nguồn AI-generated không khai báo, nguồn thiếu minh bạch. |
|  | **Tổng** | **100** |  |

### 6.2. Công thức điểm cấp tài liệu tham khảo

$$
\text{Reference Trust Score} = C1 + C2 + C3 + C4 + C5 + C6 + C7 + C8
$$

Trong đó mỗi tiêu chí đã được quy đổi theo số điểm tối đa tương ứng.

### 6.3. Report Trust Score

```text
Report_Trust_Score_Raw = average(Reference_Trust_Score_i, weight_i)
Report_Trust_Score_Final = clamp(Report_Trust_Score_Raw - Report_Penalty, 0, 100)
```

Trong v1.0, nếu hệ thống chưa xác định được mức độ quan trọng của từng nguồn, `weight_i = 1` cho mọi tài liệu tham khảo. Ở các phiên bản sau, trọng số có thể tăng nếu nguồn được trích nhiều lần trong phần phương pháp, mô hình, kết quả hoặc lập luận chính.

### 6.4. Phân loại tổng thể

| Điểm | Nhãn phân loại | Ý nghĩa | Hành động đề xuất |
|---:|---|---|---|
| 85–100 | Very Reliable | Danh mục tài liệu tham khảo có chất lượng cao, nguồn xác minh tốt, phù hợp và cập nhật. | Có thể chấp nhận, chỉ kiểm tra thủ công các cảnh báo nhỏ. |
| 70–84 | Reliable | Danh mục đạt yêu cầu, có một số lỗi nhỏ về citation, độ mới hoặc metadata. | Cần chỉnh sửa một số mục trước khi nộp/chấm. |
| 55–69 | Needs Review | Có tỷ lệ đáng kể nguồn thiếu xác minh, chưa phù hợp hoặc lỗi định dạng. | Giảng viên/sinh viên cần rà soát. |
| 40–54 | Low Reliability | Nhiều nguồn yếu, cũ, không học thuật hoặc không khớp chủ đề. | Cần thay thế hoặc bổ sung nguồn học thuật. |
| 0–39 | Not Reliable | Danh mục không đủ tin cậy, có nhiều nguồn không tồn tại hoặc rủi ro nghiêm trọng. | Không nên chấp nhận nếu chưa sửa. |

---

## 7. C1 - Độ đầy đủ và khả năng trích xuất metadata

### 7.1. Mục tiêu

C1 đánh giá liệu một mục tài liệu tham khảo có đủ thông tin thư mục để hệ thống phân tích tiếp hay không. Đây là tiêu chí nền tảng, vì thiếu metadata sẽ làm giảm khả năng xác minh nguồn, đánh giá độ tin cậy và kiểm tra citation.

### 7.2. Trường dữ liệu cần có

| Loại nguồn | Trường bắt buộc | Trường khuyến nghị |
|---|---|---|
| Journal article | Author, year, title, journal name | DOI, volume, issue, pages, publisher. |
| Conference paper | Author, year, title, conference/proceedings name | DOI, location, publisher, pages. |
| Book | Author/editor, year, title, publisher | ISBN, edition, place of publication. |
| Book chapter | Chapter author, year, chapter title, book title, editor/publisher | DOI, pages. |
| Technical report | Organization/author, year, title, issuing body | Report number, URL, access date. |
| Standard/specification | Organization, year/version, title, standard ID | URL, version, issuing body. |
| Official documentation | Organization/vendor, title, version/date, URL | Access date, product/version. |
| Website/blog | Author/organization, date, title, site name, URL | Access date, last updated date. |
| Dataset/software | Creator/organization, year/version, title/name, repository | DOI/URL, license, version. |

### 7.3. Thang điểm C1

| Điểm | Điều kiện |
|---:|---|
| 10 | Đủ trường bắt buộc, có DOI hoặc URL chính thức, cấu trúc rõ ràng. |
| 8 | Đủ trường bắt buộc nhưng thiếu DOI/URL; vẫn có thể xác minh bằng tiêu đề và tác giả. |
| 6 | Thiếu một trường quan trọng nhưng vẫn nhận diện được nguồn. |
| 4 | Thiếu nhiều trường; chỉ xác minh được một phần. |
| 2 | Chỉ có tiêu đề hoặc URL; không đủ để đánh giá tin cậy đầy đủ. |
| 0 | Không trích xuất được mục reference hoặc dữ liệu bị lỗi nghiêm trọng. |

### 7.4. Cảnh báo C1

| Mã cảnh báo | Mức độ | Điều kiện phát sinh | Gợi ý xử lý |
|---|---|---|---|
| C1-MISSING-AUTHOR | Medium | Thiếu tác giả/tổ chức chịu trách nhiệm. | Bổ sung tác giả hoặc tổ chức phát hành. |
| C1-MISSING-YEAR | Medium | Thiếu năm xuất bản/cập nhật. | Bổ sung năm hoặc ngày truy cập nếu là website. |
| C1-MISSING-TITLE | High | Thiếu tiêu đề tài liệu. | Bổ sung tiêu đề đầy đủ. |
| C1-MISSING-SOURCE | High | Thiếu tên journal/conference/publisher/website. | Bổ sung nguồn công bố. |
| C1-AMBIGUOUS-REF | Medium | Một dòng reference có thể chứa nhiều nguồn hoặc bị tách lỗi. | Kiểm tra lại xuống dòng và dấu câu. |
| C1-UNSUPPORTED-FORMAT | Low | Định dạng chưa được parser hỗ trợ. | Chuẩn hóa theo APA/IEEE/MLA/ACM. |

---

## 8. C2 - Xác minh tồn tại học thuật và khớp metadata

### 8.1. Mục tiêu

C2 kiểm tra nguồn có tồn tại và metadata trong bài nộp có khớp với nguồn bên ngoài hay không. Đây là tiêu chí quan trọng để phát hiện nguồn bịa đặt, DOI sai, tiêu đề bị sửa hoặc citation không khớp.

### 8.2. Nguồn xác minh ưu tiên

| Thứ tự ưu tiên | Kênh xác minh | Dữ liệu dùng để đối chiếu |
|---:|---|---|
| 1 | DOI resolver / Crossref / DataCite | DOI, title, author, year, publisher, container title. |
| 2 | Semantic Scholar / OpenAlex | Title, author, year, venue, citation count, abstract. |
| 3 | Publisher official page | Title, author, publication info, DOI, URL. |
| 4 | Repository chính thức | arXiv, institutional repository, GitHub release, Zenodo. |
| 5 | Website chính thức của tổ chức/vendor | Documentation, standard, white paper, product manual. |
| 6 | Manual review | Khi API không trả đủ dữ liệu hoặc nguồn đặc thù. |

### 8.3. Quy tắc khớp metadata

| Trường | Điều kiện khớp mạnh | Điều kiện khớp trung bình | Không khớp |
|---|---|---|---|
| DOI | DOI giống tuyệt đối sau khi normalize | DOI thiếu nhưng title khớp mạnh | DOI khác tài liệu. |
| Title | Similarity >= 0.95 | Similarity từ 0.80 đến 0.94 | Similarity < 0.80. |
| Author | Tác giả đầu và ít nhất 50% tác giả khớp | Tác giả đầu khớp nhưng danh sách thiếu | Tác giả đầu không khớp. |
| Year | Năm giống tuyệt đối | Lệch 1 năm do online-first/print | Lệch trên 1 năm không giải thích được. |
| Venue | Journal/conference/publisher khớp | Tên viết tắt khớp với tên đầy đủ | Venue khác. |

### 8.4. Thang điểm C2

| Điểm | Điều kiện |
|---:|---|
| 20 | DOI/URL chính thức xác minh được; title, author, year, venue khớp mạnh. |
| 17 | Không có DOI nhưng title, author, year và venue khớp mạnh qua CSDL học thuật. |
| 14 | Nguồn tồn tại, nhưng có một trường metadata lệch nhẹ hoặc thiếu. |
| 10 | Nguồn tồn tại nhưng chỉ xác minh được bằng title hoặc URL; metadata chưa đầy đủ. |
| 6 | Nguồn có dấu hiệu tồn tại nhưng độ khớp thấp, cần kiểm tra thủ công. |
| 3 | URL truy cập được nhưng không đủ bằng chứng học thuật hoặc không khớp rõ ràng. |
| 0 | Không tìm thấy nguồn, DOI sai, URL chết, hoặc metadata mâu thuẫn nghiêm trọng. |

### 8.5. Cảnh báo C2

| Mã cảnh báo | Mức độ | Điều kiện phát sinh | Gợi ý xử lý |
|---|---|---|---|
| C2-DOI-NOT-FOUND | High | DOI không resolve được. | Kiểm tra lại DOI hoặc thay bằng nguồn chính xác. |
| C2-DOI-MISMATCH | Critical | DOI trỏ tới tài liệu khác. | Sửa DOI hoặc thay nguồn; cần kiểm tra thủ công. |
| C2-TITLE-MISMATCH | High | Tiêu đề trong reference khác tiêu đề metadata. | Đối chiếu lại từ nguồn gốc. |
| C2-AUTHOR-MISMATCH | High | Tác giả không khớp. | Kiểm tra có trích nhầm nguồn không. |
| C2-YEAR-MISMATCH | Medium | Năm xuất bản lệch. | Xác định online-first, conference year hoặc print year. |
| C2-SOURCE-NOT-VERIFIED | Medium | Không đủ dữ liệu xác minh. | Cung cấp DOI/URL/publisher chính thức. |
| C2-BROKEN-URL | Medium | URL không truy cập được. | Cập nhật URL hoặc dùng DOI. |

---

## 9. C3 - Độ tin cậy học thuật/kỹ thuật của nguồn

### 9.1. Mục tiêu

C3 đánh giá mức độ đáng tin cậy của nguồn sau khi đã hoặc đang được xác minh. Tiêu chí này không chỉ xét nguồn có tồn tại, mà còn xét chất lượng, uy tín, tính học thuật và tính phù hợp của loại nguồn trong báo cáo Công nghệ Thông tin.

### 9.2. Phân loại nguồn

| Nhóm nguồn | Mức độ ưu tiên | Ví dụ | Ghi chú đánh giá |
|---|---|---|---|
| Peer-reviewed journal | Rất cao | Journal article có DOI, publisher rõ ràng | Phù hợp cho cơ sở lý thuyết, nghiên cứu liên quan. |
| Peer-reviewed conference | Rất cao | ACM/IEEE/Springer conference paper | Đặc biệt phù hợp trong CNTT/AI. |
| Book chuyên khảo | Cao | Sách học thuật, giáo trình đại học | Phù hợp cho nền tảng lý thuyết. |
| Standard/specification | Cao | W3C, ISO, RFC, NIST, IEEE Standard | Phù hợp cho giao thức, chuẩn kỹ thuật. |
| Official documentation | Trung bình đến cao | React, FastAPI, PostgreSQL docs | Phù hợp cho mô tả triển khai, không nên thay thế nguồn học thuật cho lập luận nghiên cứu. |
| Technical report/white paper | Trung bình | Báo cáo từ tổ chức uy tín | Cần kiểm tra tổ chức phát hành. |
| Preprint | Trung bình | arXiv, SSRN | Cần ghi rõ chưa peer-reviewed nếu dùng cho kết luận mạnh. |
| Dataset/software repository | Trung bình | Zenodo, GitHub release, Kaggle dataset | Cần version, license, tác giả/tổ chức. |
| Blog kỹ thuật có tác giả rõ | Thấp đến trung bình | Engineering blog của công ty uy tín | Chỉ nên dùng bổ trợ cho thực hành kỹ thuật. |
| News/article phổ thông | Thấp | Bài báo truyền thông | Không nên dùng làm nguồn chính cho lập luận kỹ thuật. |
| Wikipedia/community wiki | Thấp | Wikipedia, StackOverflow answer | Chỉ dùng tham khảo nền; không nên là nguồn học thuật chính. |
| Social media/AI-generated | Rất thấp | Facebook post, ChatGPT output | Không chấp nhận làm nguồn học thuật nếu không có căn cứ gốc. |

### 9.3. Thang điểm C3

| Điểm | Điều kiện |
|---:|---|
| 20 | Nguồn peer-reviewed hoặc chuẩn kỹ thuật chính thức, metadata rõ, venue/publisher uy tín, phù hợp vai trò học thuật. |
| 17 | Nguồn học thuật đáng tin cậy nhưng thiếu một số thông tin phụ như citation count hoặc ranking. |
| 14 | Sách, technical report, official documentation hoặc preprint chất lượng tốt, tổ chức rõ ràng. |
| 11 | Official documentation hoặc blog kỹ thuật của tổ chức uy tín, phù hợp cho phần triển khai nhưng không phải nguồn nghiên cứu chính. |
| 8 | Nguồn phổ thông có tác giả/tổ chức rõ, có thể dùng bối cảnh nhưng không nên dùng làm bằng chứng chính. |
| 5 | Blog cá nhân, website chưa rõ quy trình biên tập, thiếu bằng chứng chuyên môn. |
| 2 | Nguồn không rõ tác giả, không rõ tổ chức, không có ngày, không có bằng chứng xác minh. |
| 0 | Nguồn bị nghi ngờ bịa đặt, mạo danh, DOI sai, hoặc nội dung không truy vết được. |

### 9.4. Quy tắc bổ sung theo loại báo cáo CNTT

| Nội dung báo cáo | Loại nguồn nên ưu tiên | Loại nguồn cần hạn chế |
|---|---|---|
| Cơ sở lý thuyết AI/ML/Data Science | Paper, textbook, survey, benchmark paper | Blog, tutorial, video. |
| Triển khai framework/libraries | Official docs, release docs, technical docs | Blog không rõ phiên bản. |
| An toàn thông tin | Standard, CVE/NVD, NIST, vendor advisory, peer-reviewed paper | Tin tức phổ thông, post mạng xã hội. |
| Web/backend/database | Official docs, architecture docs, textbook, technical report | Tutorial không rõ tác giả. |
| UX/UI | Guideline chính thức, sách, paper HCI | Pinterest/Dribbble không có phân tích. |
| Phân tích thị trường/case study | Report từ tổ chức uy tín, dataset mở | Bài quảng cáo, số liệu không nguồn. |

### 9.5. Cảnh báo C3

| Mã cảnh báo | Mức độ | Điều kiện phát sinh | Gợi ý xử lý |
|---|---|---|---|
| C3-NON-ACADEMIC-CORE | High | Nguồn không học thuật được dùng cho lập luận cốt lõi. | Thay bằng paper, sách, standard hoặc tài liệu chính thức. |
| C3-LOW-CREDIBILITY | Medium | Website/blog không rõ tác giả hoặc tổ chức. | Bổ sung nguồn có uy tín cao hơn. |
| C3-PREPRINT-ONLY | Medium | Chủ đề quan trọng nhưng chỉ có preprint. | Bổ sung paper đã peer-reviewed hoặc ghi rõ giới hạn. |
| C3-WIKI-AS-MAIN | Medium | Wikipedia/community wiki là nguồn chính. | Dùng nguồn gốc được wiki trích dẫn thay vì wiki. |
| C3-VENDOR-BIAS | Medium | Chỉ dùng tài liệu từ nhà cung cấp cho đánh giá khách quan. | Bổ sung nguồn độc lập. |
| C3-PREDATORY-RISK | High | Venue/publisher có dấu hiệu thiếu minh bạch. | Kiểm tra thủ công danh mục tạp chí/hội nghị. |

---

## 10. C4 - Độ phù hợp với chủ đề và nội dung báo cáo

### 10.1. Mục tiêu

C4 đánh giá nguồn được trích dẫn có thực sự liên quan tới báo cáo hay không. Một nguồn có uy tín cao vẫn bị giảm điểm nếu không hỗ trợ trực tiếp cho chủ đề, phương pháp, công nghệ, dữ liệu hoặc lập luận của báo cáo.

### 10.2. Dữ liệu đầu vào cho C4

| Dữ liệu | Mục đích |
|---|---|
| Tiêu đề báo cáo | Xác định chủ đề chính. |
| Tóm tắt/mục tiêu báo cáo | Xác định vấn đề, đối tượng, phạm vi. |
| Từ khóa báo cáo | Tăng độ chính xác khi so khớp. |
| Các heading trong báo cáo | Xác định cấu trúc nội dung. |
| Đoạn văn quanh citation | Đánh giá citation-in-context nếu có. |
| Title/abstract/keywords của nguồn | So khớp ngữ nghĩa với báo cáo. |
| Venue/source type | Xác định lĩnh vực công bố. |

### 10.3. Phương pháp đánh giá đề xuất

TrustLens v1.0 có thể kết hợp nhiều tín hiệu:

1. **Keyword overlap:** So khớp từ khóa giữa báo cáo và nguồn.
2. **Semantic similarity:** Dùng embedding để đo độ gần ngữ nghĩa giữa abstract/title nguồn và nội dung báo cáo.
3. **Citation-in-context similarity:** Nếu phát hiện đoạn văn chứa citation, so sánh đoạn đó với title/abstract nguồn.
4. **Domain classification:** Phân loại lĩnh vực nguồn và lĩnh vực báo cáo.
5. **Manual rule:** Cho phép giảng viên đánh dấu nguồn là phù hợp dù similarity thấp nếu đó là seminal work hoặc nguồn nền tảng.

### 10.4. Thang điểm C4

| Điểm | Điều kiện |
|---:|---|
| 20 | Nguồn liên quan trực tiếp đến chủ đề, phương pháp, công nghệ hoặc lập luận chính; similarity cao; citation-in-context phù hợp. |
| 17 | Nguồn liên quan rõ ràng nhưng không phải trọng tâm chính. |
| 14 | Nguồn liên quan ở mức nền tảng hoặc bối cảnh. |
| 10 | Nguồn liên quan gián tiếp; chỉ hỗ trợ một phần nhỏ. |
| 6 | Nguồn cùng lĩnh vực rộng nhưng không gắn rõ với bài báo cáo. |
| 3 | Nguồn có vài từ khóa chung nhưng nội dung không hỗ trợ lập luận. |
| 0 | Nguồn không liên quan hoặc bị trích dẫn sai ngữ cảnh. |

### 10.5. Ngưỡng similarity tham khảo

| Semantic similarity | Điểm gợi ý | Diễn giải |
|---:|---:|---|
| >= 0.75 | 18–20 | Rất phù hợp. |
| 0.60–0.74 | 14–17 | Phù hợp. |
| 0.45–0.59 | 8–13 | Liên quan trung bình, cần kiểm tra ngữ cảnh. |
| 0.30–0.44 | 3–7 | Liên quan yếu. |
| < 0.30 | 0–2 | Không phù hợp hoặc không đủ dữ liệu. |

> Ghi chú: Ngưỡng similarity phải được hiệu chỉnh bằng dữ liệu mẫu của nhóm. Không nên dùng ngưỡng cố định như kết luận tuyệt đối khi chưa kiểm thử.

### 10.6. Cảnh báo C4

| Mã cảnh báo | Mức độ | Điều kiện phát sinh | Gợi ý xử lý |
|---|---|---|---|
| C4-LOW-RELEVANCE | High | Similarity thấp và không có bằng chứng liên quan. | Thay bằng nguồn gần chủ đề hơn. |
| C4-OUT-OF-SCOPE | Medium | Nguồn thuộc lĩnh vực khác, chỉ liên quan rất xa. | Kiểm tra lại lý do trích dẫn. |
| C4-CONTEXT-MISMATCH | High | Đoạn văn quanh citation không khớp với nội dung nguồn. | Sửa citation hoặc thay nguồn. |
| C4-TITLE-ONLY-MATCH | Medium | Chỉ tiêu đề có từ khóa, abstract không liên quan. | Không nên dựa vào tiêu đề; kiểm tra abstract/full text. |
| C4-NO-ABSTRACT | Low | Không có abstract để so khớp. | Dùng title/keywords hoặc kiểm tra thủ công. |

---

## 11. C5 - Độ cập nhật theo lĩnh vực

### 11.1. Mục tiêu

C5 đánh giá năm xuất bản/cập nhật của nguồn có phù hợp với tốc độ thay đổi của lĩnh vực hay không. Trong Công nghệ Thông tin, nguồn về framework, AI model, cybersecurity, cloud và dữ liệu thường cần mới hơn nguồn về toán học, thuật toán nền tảng hoặc lý thuyết kinh điển.

### 11.2. Phân nhóm tốc độ thay đổi lĩnh vực

| Nhóm | Lĩnh vực | Khung thời gian khuyến nghị |
|---|---|---|
| Fast-changing | AI/ML hiện đại, GenAI, cybersecurity, cloud, framework web, mobile, DevOps | Ưu tiên 0–5 năm gần nhất. |
| Medium-changing | Database, software engineering, UX, data analytics, distributed systems | Ưu tiên 0–7 năm gần nhất. |
| Slow-changing | Toán rời rạc, thống kê cơ bản, thuật toán nền tảng, cấu trúc dữ liệu, nguyên lý hệ điều hành | Có thể chấp nhận nguồn cũ nếu là seminal/textbook. |
| Version-sensitive | Official documentation, API, framework, library | Phải khớp version đang dùng hoặc gần thời điểm triển khai. |

### 11.3. Thang điểm C5

| Điểm | Điều kiện |
|---:|---|
| 10 | Nguồn trong khung thời gian khuyến nghị hoặc là standard/documentation đúng version. |
| 8 | Hơi cũ nhưng vẫn phù hợp; không ảnh hưởng đáng kể đến nội dung. |
| 6 | Cũ hơn khuyến nghị nhưng là nguồn nền tảng hoặc vẫn được trích dẫn rộng rãi. |
| 4 | Cũ và dùng cho nội dung có thay đổi nhanh; cần bổ sung nguồn mới. |
| 2 | Quá cũ, không có lý do học thuật rõ ràng. |
| 0 | Không có năm xuất bản/cập nhật hoặc năm sai/mâu thuẫn nghiêm trọng. |

### 11.4. Quy tắc seminal work

Một nguồn cũ có thể không bị trừ mạnh nếu thỏa ít nhất một điều kiện:

1. Là công trình gốc của thuật toán, mô hình, giao thức hoặc khái niệm nền tảng.
2. Là sách giáo trình/chuyên khảo được sử dụng rộng rãi.
3. Là chuẩn kỹ thuật còn hiệu lực.
4. Là nguồn lịch sử dùng để mô tả quá trình phát triển, không dùng để khẳng định trạng thái hiện tại.

### 11.5. Cảnh báo C5

| Mã cảnh báo | Mức độ | Điều kiện phát sinh | Gợi ý xử lý |
|---|---|---|---|
| C5-OUTDATED-FAST-FIELD | High | Nguồn quá 5 năm cho chủ đề thay đổi nhanh. | Bổ sung nguồn mới hơn. |
| C5-MISSING-YEAR | Medium | Không có năm xuất bản/cập nhật. | Bổ sung năm hoặc ngày truy cập. |
| C5-DOCS-VERSION-MISMATCH | High | Documentation không khớp version công nghệ đang dùng. | Dùng tài liệu đúng version. |
| C5-OLD-BUT-SEMINAL | Low | Nguồn cũ nhưng có dấu hiệu seminal. | Không nhất thiết thay, nhưng nên ghi rõ vai trò nguồn nền tảng. |

---

## 12. C6 - Chất lượng định dạng citation

### 12.1. Mục tiêu

C6 đánh giá tài liệu tham khảo có đúng chuẩn trích dẫn đã chọn hay không. TrustLens v1.0 ưu tiên các chuẩn phổ biến trong môi trường học thuật: APA 7th, IEEE, MLA, ACM.

### 12.2. Yêu cầu chung

| Yêu cầu | Mô tả |
|---|---|
| Xác định được style | Hệ thống nhận diện hoặc người dùng chọn APA/IEEE/MLA/ACM. |
| Nhất quán toàn báo cáo | Không trộn nhiều style nếu không có yêu cầu đặc biệt. |
| Đủ trường cốt lõi | Author, year, title, source, DOI/URL nếu có. |
| Đúng thứ tự trường | Mỗi style có quy tắc trình bày riêng. |
| Đúng dấu câu và format | Dấu chấm, dấu phẩy, ngoặc, in nghiêng, số thứ tự, capitalization. |
| Đồng bộ in-text citation và reference list | Mục trong danh mục nên được trích trong nội dung, và citation trong nội dung nên có reference tương ứng. |

### 12.3. Thang điểm C6

| Điểm | Điều kiện |
|---:|---|
| 10 | Đúng style, đủ trường, nhất quán, có DOI/URL đúng format nếu có. |
| 8 | Một vài lỗi nhỏ về dấu câu/capitalization nhưng không ảnh hưởng xác minh. |
| 6 | Thiếu một số trường phụ hoặc có lỗi style trung bình. |
| 4 | Trộn style, sai thứ tự nhiều trường, thiếu DOI/URL quan trọng. |
| 2 | Citation khó đọc, thiếu nhiều trường, không nhất quán. |
| 0 | Không theo chuẩn nào hoặc không thể phân tích. |

### 12.4. Quy tắc theo style

#### 12.4.1. APA 7th

| Thành phần | Yêu cầu cơ bản |
|---|---|
| Author | Họ, chữ cái đầu tên. |
| Year | Năm trong ngoặc đơn. |
| Title | Sentence case đối với article/chapter. |
| Source | Journal/book/site name; journal thường in nghiêng. |
| DOI/URL | Dùng DOI dạng URL nếu có. |
| In-text | `(Author, Year)` hoặc `Author (Year)`. |

#### 12.4.2. IEEE

| Thành phần | Yêu cầu cơ bản |
|---|---|
| Numbering | Số thứ tự trong ngoặc vuông `[1]`. |
| Author | Initial + surname. |
| Title | Đặt trong ngoặc kép đối với bài báo. |
| Source | Tên conference/journal, volume/issue/pages nếu có. |
| Year | Thường đặt gần cuối. |
| In-text | Dùng số `[1]`, `[2]`, không dùng author-year. |

#### 12.4.3. ACM

| Thành phần | Yêu cầu cơ bản |
|---|---|
| Author | Tên tác giả đầy đủ hoặc theo mẫu ACM. |
| Year | Năm xuất bản. |
| Title | Tiêu đề bài. |
| Source | Proceedings/journal/publisher. |
| DOI | Khuyến nghị có DOI. |
| In-text | Tùy template ACM, thường dùng author-year hoặc numeric tùy cấu hình. |

#### 12.4.4. MLA

| Thành phần | Yêu cầu cơ bản |
|---|---|
| Author | Họ, tên. |
| Title | Title case; article trong ngoặc kép, book/site in nghiêng. |
| Container | Tên nguồn chứa. |
| Publisher/date | Nhà xuất bản/ngày. |
| URL/access date | Cần với web source nếu có. |
| In-text | `(Author page)` nếu có số trang. |

### 12.5. Cảnh báo C6

| Mã cảnh báo | Mức độ | Điều kiện phát sinh | Gợi ý xử lý |
|---|---|---|---|
| C6-STYLE-MIXED | Medium | Trộn nhiều style trong cùng danh mục. | Chọn một style thống nhất. |
| C6-IN-TEXT-MISSING | Medium | Reference có trong danh mục nhưng không thấy citation trong nội dung. | Kiểm tra có trích dẫn thật hay không. |
| C6-REF-MISSING | High | Có citation trong nội dung nhưng thiếu mục reference cuối bài. | Bổ sung reference tương ứng. |
| C6-DOI-FORMAT-ERROR | Low | DOI trình bày sai format. | Chuẩn hóa DOI. |
| C6-INCOMPLETE-FIELDS | Medium | Thiếu trường cần thiết theo style. | Bổ sung trường bị thiếu. |
| C6-NUMBERING-ERROR | Medium | IEEE numbering bị trùng, thiếu hoặc sai thứ tự. | Đánh số lại danh mục. |

---

## 13. C7 - Tính cân bằng và đa dạng nguồn

### 13.1. Mục tiêu

C7 đánh giá danh mục tài liệu tham khảo có bị phụ thuộc quá mức vào một loại nguồn, một website, một tác giả hoặc một nhà xuất bản hay không. Tiêu chí này chủ yếu có ý nghĩa ở cấp toàn bộ báo cáo, nhưng vẫn có thể gán điểm hỗ trợ ở cấp từng nguồn.

### 13.2. Tín hiệu đánh giá

| Tín hiệu | Rủi ro cần phát hiện |
|---|---|
| Tỷ lệ nguồn học thuật | Danh mục có quá ít paper/sách/standard. |
| Tỷ lệ nguồn web/blog | Phụ thuộc vào nguồn không học thuật. |
| Tỷ lệ nguồn từ cùng domain | Lệ thuộc một website hoặc một nhà cung cấp. |
| Tỷ lệ nguồn quá cũ | Thiếu cập nhật. |
| Tỷ lệ nguồn cùng tác giả/nhóm | Thiếu góc nhìn đa chiều. |
| Phân bố loại nguồn | Không có nguồn nền tảng hoặc không có nguồn thực hành phù hợp. |

### 13.3. Thang điểm C7 cấp tài liệu tham khảo

| Điểm | Điều kiện |
|---:|---|
| 5 | Nguồn đóng góp vào sự đa dạng hợp lý của danh mục. |
| 4 | Nguồn thuộc nhóm đang chiếm tỷ lệ vừa phải. |
| 3 | Nguồn không làm mất cân bằng nhưng cũng không bổ sung đa dạng rõ ràng. |
| 2 | Nguồn thuộc nhóm đang bị dùng quá nhiều, ví dụ cùng domain/vendor. |
| 1 | Nguồn làm tăng rủi ro phụ thuộc vào web/blog hoặc một nhóm tác giả. |
| 0 | Nguồn trùng lặp hoặc gần như trùng lặp với nguồn khác. |

### 13.4. Ngưỡng cảnh báo cấp báo cáo

| Điều kiện | Cảnh báo | Mức độ |
|---|---|---|
| > 40% nguồn từ blog/website không chính thức | C7-WEB-OVERUSE | High |
| > 50% nguồn từ cùng một domain/vendor | C7-SINGLE-DOMAIN-DEPENDENCY | Medium |
| < 30% nguồn học thuật trong báo cáo nghiên cứu | C7-LOW-ACADEMIC-RATIO | High |
| Có nhiều mục trùng title/DOI | C7-DUPLICATE-REFS | Medium |
| Không có nguồn mới trong chủ đề fast-changing | C7-NO-RECENT-SOURCES | High |

---

## 14. C8 - Rủi ro học thuật và toàn vẹn nguồn

### 14.1. Mục tiêu

C8 phát hiện các dấu hiệu có thể ảnh hưởng nghiêm trọng tới tính chính xác, đạo đức học thuật hoặc khả năng chấp nhận danh mục tài liệu tham khảo.

### 14.2. Nhóm rủi ro

| Nhóm rủi ro | Mô tả | Mức độ mặc định |
|---|---|---|
| Nguồn không tồn tại | Không tìm được DOI/title/URL hoặc metadata mâu thuẫn. | Critical |
| DOI trỏ sai tài liệu | DOI tồn tại nhưng metadata không khớp reference. | Critical |
| Nguồn mạo danh | Website giả, publisher không rõ, domain không chính thức. | High |
| Nguồn thiếu minh bạch | Không tác giả, không ngày, không tổ chức. | Medium |
| URL chết | Link không truy cập được tại thời điểm kiểm tra. | Medium |
| AI-generated source | Nội dung do AI tạo nhưng không có nguồn gốc học thuật. | High |
| Nguồn quảng cáo | Nội dung có mục đích marketing, không kiểm chứng độc lập. | Medium |
| Trùng lặp nguồn | Cùng tài liệu xuất hiện nhiều lần dưới format khác nhau. | Medium |
| Citation laundering | Trích một nguồn phụ nhưng diễn giải như nguồn gốc. | High |

### 14.3. Thang điểm C8

| Điểm | Điều kiện |
|---:|---|
| 5 | Không phát hiện rủi ro; metadata, URL, loại nguồn và citation nhất quán. |
| 4 | Có cảnh báo nhẹ nhưng không ảnh hưởng đáng kể. |
| 3 | Có một rủi ro mức medium, cần kiểm tra nhưng chưa đủ kết luận sai. |
| 2 | Có nhiều rủi ro medium hoặc một rủi ro high chưa xác nhận. |
| 1 | Rủi ro high có bằng chứng tương đối rõ. |
| 0 | Rủi ro critical: nguồn không tồn tại, DOI sai nghiêm trọng hoặc citation bịa đặt. |

### 14.4. Hard flags

Các hard flag sau cần được hiển thị rõ trong báo cáo kết quả:

| Mã hard flag | Điều kiện | Tác động đề xuất |
|---|---|---|
| HF-FAKE-REFERENCE | Không xác minh được nguồn sau nhiều chiến lược tìm kiếm và metadata có dấu hiệu bịa đặt. | Trừ mạnh điểm report; yêu cầu kiểm tra thủ công. |
| HF-DOI-MISMATCH | DOI dẫn tới tài liệu khác. | Điểm C2 và C8 gần 0; yêu cầu sửa ngay. |
| HF-NO-REFERENCE-LIST | Không phát hiện danh mục tài liệu tham khảo. | Không tính được Trust Score đầy đủ. |
| HF-LOW-VERIFIABLE-RATIO | Tỷ lệ nguồn xác minh được dưới 50%. | Report-level penalty lớn. |
| HF-COPYRIGHT-RISK | Tài liệu có dấu hiệu dùng nguồn không hợp pháp hoặc dữ liệu không được phép công bố. | Yêu cầu giảng viên kiểm tra. |
| HF-AI-SOURCE-AS-ACADEMIC | Dùng nội dung AI-generated như tài liệu học thuật mà không dẫn nguồn gốc. | Gắn cảnh báo nghiêm trọng. |

---

## 15. Tiêu chí cấp báo cáo

Ngoài điểm từng tài liệu tham khảo, TrustLens cần đánh giá toàn bộ danh mục để nhận diện vấn đề hệ thống.

### 15.1. Chỉ số tổng hợp cấp báo cáo

| Mã chỉ số | Tên chỉ số | Công thức gợi ý | Ý nghĩa |
|---|---|---|---|
| R-AVG | Điểm trung bình reference | Average Reference Trust Score | Chất lượng trung bình của nguồn. |
| R-MEDIAN | Trung vị điểm reference | Median Reference Trust Score | Giảm ảnh hưởng của ngoại lệ. |
| R-VERIFIED | Tỷ lệ nguồn xác minh được | verified_refs / total_refs | Khả năng kiểm chứng danh mục. |
| R-ACADEMIC | Tỷ lệ nguồn học thuật/kỹ thuật chính thống | academic_refs / total_refs | Mức độ học thuật. |
| R-RECENT | Tỷ lệ nguồn cập nhật | recent_refs / total_refs | Độ mới theo lĩnh vực. |
| R-RELEVANT | Tỷ lệ nguồn phù hợp | relevant_refs / total_refs | Mức độ liên quan đến báo cáo. |
| R-CITATION-OK | Tỷ lệ citation đúng format | valid_citation_refs / total_refs | Chất lượng hình thức trích dẫn. |
| R-HARD-FLAG | Số hard flag | count(hard_flags) | Rủi ro nghiêm trọng. |
| R-DUPLICATE | Tỷ lệ nguồn trùng | duplicate_refs / total_refs | Chất lượng quản lý danh mục. |

### 15.2. Penalty cấp báo cáo

| Mã penalty | Điều kiện | Mức trừ đề xuất |
|---|---|---:|
| P-NO-REF-LIST | Không phát hiện danh mục tài liệu tham khảo | 30 |
| P-LOW-VERIFIED | Tỷ lệ nguồn xác minh được < 50% | 15 |
| P-VERY-LOW-VERIFIED | Tỷ lệ nguồn xác minh được < 30% | 25 |
| P-HARD-FLAG | Mỗi hard flag critical | 5–15, tối đa 30 |
| P-WEB-OVERUSE | > 40% nguồn là blog/web không chính thức | 10 |
| P-LOW-ACADEMIC | < 30% nguồn học thuật trong báo cáo nghiên cứu | 10 |
| P-OUTDATED | > 40% nguồn quá cũ trong lĩnh vực thay đổi nhanh | 8 |
| P-CITATION-INCONSISTENT | Trộn style hoặc nhiều lỗi format nghiêm trọng | 5–10 |
| P-DUPLICATE | > 10% reference bị trùng | 5 |
| P-MISSING-IN-TEXT | > 30% reference không được trích trong nội dung | 8 |

### 15.3. Công thức penalty

```text
Report_Penalty = min(sum(active_penalties), 50)
```

Giới hạn penalty tối đa 50 điểm để tránh việc một báo cáo bị trừ quá mức do nhiều cảnh báo chồng lặp từ cùng một nguyên nhân.

---

## 16. Ma trận tiêu chí theo mục đích sử dụng nguồn

Không phải nguồn nào cũng cần cùng mức độ học thuật. TrustLens nên đánh giá nguồn theo vai trò thực tế trong báo cáo.

| Vai trò nguồn | Ví dụ | Yêu cầu tối thiểu | Mức nghiêm ngặt |
|---|---|---|---|
| Lý thuyết nền tảng | Định nghĩa thuật toán, mô hình, khái niệm | Sách, paper, survey, standard | Cao |
| Nghiên cứu liên quan | Công trình trước đó, benchmark, model comparison | Paper peer-reviewed/preprint rõ ràng | Rất cao |
| Phương pháp triển khai | Framework, API, thư viện | Official docs đúng version | Trung bình đến cao |
| Dữ liệu/thống kê | Dataset, số liệu thị trường, benchmark | Dataset/report có nguồn gốc rõ | Cao |
| Bối cảnh ứng dụng | Nhu cầu thực tiễn, case study | Report, official source, nguồn đáng tin | Trung bình |
| Minh họa kỹ thuật | Tutorial, ví dụ code | Official docs hoặc tutorial uy tín | Trung bình |
| Ý kiến/nhận định | Blog, article, interview | Tác giả/tổ chức rõ, không dùng làm bằng chứng chính | Thấp đến trung bình |

---

## 17. Quy tắc đánh giá theo loại tài liệu tham khảo

### 17.1. Journal article

| Tiêu chí | Yêu cầu |
|---|---|
| Metadata | Author, year, title, journal, volume/issue/pages, DOI nếu có. |
| Reliability | Ưu tiên publisher rõ, journal có quy trình peer review. |
| Recency | Tùy lĩnh vực; AI/cybersecurity ưu tiên 0–5 năm. |
| Warning | Journal không rõ publisher, DOI sai, thiếu title, thiếu năm. |

### 17.2. Conference paper

| Tiêu chí | Yêu cầu |
|---|---|
| Metadata | Author, year, title, conference/proceedings, DOI/publisher/pages nếu có. |
| Reliability | Hội nghị chuyên ngành CNTT có vai trò cao, đặc biệt trong AI, HCI, security, systems. |
| Recency | Ưu tiên mới với công nghệ thay đổi nhanh. |
| Warning | Conference không rõ danh tính, proceedings không tồn tại, venue mạo danh. |

### 17.3. Book/textbook

| Tiêu chí | Yêu cầu |
|---|---|
| Metadata | Author/editor, year, title, edition, publisher. |
| Reliability | Nhà xuất bản học thuật, giáo trình đại học, sách chuyên khảo. |
| Recency | Có thể cũ nếu là lý thuyết nền tảng. |
| Warning | Thiếu publisher, không rõ edition, nguồn scan không hợp pháp. |

### 17.4. Standard/specification

| Tiêu chí | Yêu cầu |
|---|---|
| Metadata | Tổ chức phát hành, mã chuẩn, version, title, year/date, URL. |
| Reliability | ISO, IEEE, RFC, W3C, NIST hoặc tổ chức chuẩn có thẩm quyền. |
| Recency | Phải xác định còn hiệu lực hoặc là version đang dùng. |
| Warning | Dùng bản cũ khi chuẩn mới đã thay thế, thiếu version. |

### 17.5. Official documentation

| Tiêu chí | Yêu cầu |
|---|---|
| Metadata | Tên tổ chức/vendor, title, product/version, URL, access date. |
| Reliability | Phù hợp cho mô tả API, framework, cài đặt, cấu hình. |
| Recency | Phải đúng version công nghệ trong dự án. |
| Warning | Dùng documentation của version khác, link chết, page không chính thức. |

### 17.6. Website/blog/news

| Tiêu chí | Yêu cầu |
|---|---|
| Metadata | Author/organization, date, title, site name, URL, access date. |
| Reliability | Chỉ nên dùng bổ trợ; ưu tiên engineering blog của tổ chức uy tín. |
| Recency | Cần ngày cập nhật rõ ràng. |
| Warning | Không tác giả, không ngày, nội dung quảng cáo, quan điểm không kiểm chứng. |

### 17.7. Dataset/software

| Tiêu chí | Yêu cầu |
|---|---|
| Metadata | Creator, year/version, name, repository, DOI/URL, license. |
| Reliability | Zenodo, Figshare, Kaggle, GitHub release, official dataset portal. |
| Recency | Phải đúng version đã dùng trong báo cáo. |
| Warning | Không license, không version, repository cá nhân không mô tả dữ liệu. |

---

## 18. Confidence Score

Trust Score phản ánh chất lượng nguồn; Confidence Score phản ánh mức độ tự tin của hệ thống khi chấm điểm.

### 18.1. Thang Confidence Score

| Confidence | Điều kiện |
|---|---|
| High | Có DOI/URL chính thức, metadata khớp mạnh, abstract hoặc dữ liệu đủ để đánh giá relevance. |
| Medium | Nguồn xác minh được nhưng thiếu abstract, thiếu một số trường hoặc cần suy luận. |
| Low | Metadata thiếu, nguồn không có DOI, URL không ổn định hoặc similarity dựa trên title-only. |
| Unknown | Không đủ dữ liệu để đánh giá tự động. |

### 18.2. Quy tắc hiển thị

| Trust Score | Confidence | Cách diễn giải |
|---:|---|---|
| Cao | High | Có thể tin cậy tương đối. |
| Cao | Low | Có điểm cao do một số tiêu chí tốt, nhưng cần kiểm tra thủ công. |
| Thấp | High | Có bằng chứng rõ về vấn đề. |
| Thấp | Low | Chưa đủ dữ liệu; không nên kết luận nguồn sai. |

---

## 19. Output chuẩn cho từng tài liệu tham khảo

### 19.1. Bảng kết quả hiển thị cho người dùng

| Trường | Mô tả |
|---|---|
| Reference ID | Mã nguồn trong báo cáo, ví dụ REF-001. |
| Raw Reference | Dòng citation gốc. |
| Parsed Metadata | Metadata đã trích xuất. |
| Source Type | Journal, conference, book, docs, blog, dataset, unknown. |
| Verification Status | verified, partially_verified, unverified, mismatch. |
| Trust Score | Điểm 0–100. |
| Score Breakdown | Điểm C1–C8. |
| Confidence | High/Medium/Low/Unknown. |
| Flags | Danh sách cảnh báo. |
| Explanation | Lý do hệ thống chấm điểm. |
| Suggested Fix | Gợi ý sửa. |
| Evidence | DOI, URL, API source, metadata matched. |
| Manual Review Required | true/false. |

### 19.2. JSON output đề xuất

```json
{
  "reference_id": "REF-001",
  "raw_reference": "Smith, J. (2023). Example paper...",
  "source_type": "journal_article",
  "parsed_metadata": {
    "authors": ["Smith, J."],
    "year": 2023,
    "title": "Example paper",
    "venue": "Journal of Example Computing",
    "doi": "10.xxxx/example",
    "url": null
  },
  "verification": {
    "status": "verified",
    "source": "Crossref",
    "matched_fields": ["doi", "title", "author", "year", "venue"],
    "mismatch_fields": []
  },
  "scores": {
    "C1_metadata_completeness": 10,
    "C2_metadata_verification": 20,
    "C3_source_reliability": 18,
    "C4_topic_relevance": 17,
    "C5_recency": 10,
    "C6_citation_quality": 8,
    "C7_source_diversity": 5,
    "C8_integrity_risk": 5,
    "reference_trust_score": 93
  },
  "confidence": "High",
  "flags": [],
  "explanation": "Nguồn có DOI xác minh được, metadata khớp mạnh, venue học thuật và phù hợp với chủ đề báo cáo.",
  "suggested_fix": null,
  "manual_review_required": false
}
```

---

## 20. Output chuẩn cấp báo cáo

### 20.1. Thống kê tổng quan

| Trường | Mô tả |
|---|---|
| Total References | Tổng số tài liệu tham khảo. |
| Verified References | Số và tỷ lệ nguồn xác minh được. |
| Academic Sources | Số và tỷ lệ nguồn học thuật/kỹ thuật chính thống. |
| High Relevance Sources | Số và tỷ lệ nguồn phù hợp cao. |
| Outdated Sources | Số và tỷ lệ nguồn quá cũ. |
| Citation Format Issues | Số lỗi định dạng. |
| Hard Flags | Số cảnh báo nghiêm trọng. |
| Report Trust Score | Điểm tổng hợp sau penalty. |
| Overall Classification | Very Reliable / Reliable / Needs Review / Low Reliability / Not Reliable. |

### 20.2. JSON output đề xuất

```json
{
  "document_id": "DOC-2026-0001",
  "report_title": "Example IT Report",
  "evaluation_version": "TrustLens_Evaluation_Criteria_v1.0",
  "summary": {
    "total_references": 25,
    "verified_references": 19,
    "verified_ratio": 0.76,
    "academic_sources": 16,
    "academic_ratio": 0.64,
    "high_relevance_sources": 18,
    "outdated_sources": 3,
    "citation_format_issues": 7,
    "hard_flags": 1,
    "report_trust_score_raw": 78.4,
    "report_penalty": 8,
    "report_trust_score_final": 70.4,
    "classification": "Reliable"
  },
  "top_issues": [
    {
      "code": "C6-STYLE-MIXED",
      "severity": "Medium",
      "count": 5,
      "suggestion": "Chuẩn hóa toàn bộ danh mục theo một style duy nhất."
    }
  ],
  "references": []
}
```

---

## 21. Quy trình đánh giá tự động

### 21.1. Pipeline xử lý

| Bước | Tên bước | Mô tả | Output |
|---:|---|---|---|
| 1 | File ingestion | Nhận file PDF/DOCX, lưu file, tạo document ID. | File record. |
| 2 | Reference section detection | Phát hiện khu vực tài liệu tham khảo. | Reference section text. |
| 3 | Reference segmentation | Tách từng mục reference. | Raw reference list. |
| 4 | Citation style detection | Nhận diện APA/IEEE/MLA/ACM hoặc unknown. | Citation style. |
| 5 | Metadata parsing | Trích author, year, title, venue, DOI, URL. | Parsed metadata. |
| 6 | Metadata normalization | Chuẩn hóa DOI, URL, tên tác giả, title, năm. | Normalized metadata. |
| 7 | External verification | Đối chiếu Crossref/OpenAlex/Semantic Scholar/publisher URL. | Verification result. |
| 8 | Source classification | Phân loại journal/conference/book/docs/blog/etc. | Source type. |
| 9 | Reliability scoring | Chấm C2, C3, C8. | Reliability components. |
| 10 | Relevance scoring | So sánh với chủ đề/nội dung báo cáo. | C4. |
| 11 | Recency scoring | Kiểm tra năm theo lĩnh vực. | C5. |
| 12 | Citation quality scoring | Chấm format, consistency, missing fields. | C6. |
| 13 | Diversity/report analysis | Tính C7 và các chỉ số report-level. | Report metrics. |
| 14 | Aggregation | Tính điểm reference và report. | Trust Score. |
| 15 | Explanation generation | Sinh lý do, cảnh báo, gợi ý sửa. | Evaluation report. |

### 21.2. Yêu cầu truy vết

Mỗi kết quả chấm điểm phải lưu được:

1. Dòng reference gốc.
2. Metadata đã parse.
3. Metadata sau normalize.
4. API hoặc nguồn đối chiếu đã dùng.
5. Trường nào khớp, trường nào không khớp.
6. Công thức điểm thành phần.
7. Cảnh báo và bằng chứng tạo cảnh báo.
8. Thời điểm đánh giá.
9. Phiên bản bộ tiêu chí.
10. Trạng thái override nếu giảng viên sửa thủ công.

---

## 22. Ma trận cảnh báo tổng hợp

| Mã cảnh báo | Nhóm | Mức độ | Có cần review thủ công? | Tác động điểm |
|---|---|---|---|---|
| C1-MISSING-TITLE | Metadata | High | Có | Giảm C1, C2. |
| C1-MISSING-YEAR | Metadata | Medium | Có thể | Giảm C1, C5. |
| C2-DOI-NOT-FOUND | Verification | High | Có | Giảm C2, C8. |
| C2-DOI-MISMATCH | Verification | Critical | Có | C2 gần 0, hard flag. |
| C2-TITLE-MISMATCH | Verification | High | Có | Giảm C2. |
| C3-NON-ACADEMIC-CORE | Reliability | High | Có | Giảm C3, penalty nếu nhiều. |
| C3-PREPRINT-ONLY | Reliability | Medium | Có thể | Giảm nhẹ C3 nếu dùng làm kết luận mạnh. |
| C4-LOW-RELEVANCE | Relevance | High | Có | Giảm C4. |
| C4-CONTEXT-MISMATCH | Relevance | High | Có | Giảm C4, có thể hard flag nếu nghiêm trọng. |
| C5-OUTDATED-FAST-FIELD | Recency | High | Có thể | Giảm C5, penalty nếu nhiều. |
| C6-STYLE-MIXED | Citation | Medium | Không bắt buộc | Giảm C6, report penalty. |
| C6-REF-MISSING | Citation | High | Có | Giảm C6, hard flag nếu nhiều. |
| C7-WEB-OVERUSE | Diversity | High | Có thể | Report penalty. |
| C7-DUPLICATE-REFS | Diversity | Medium | Không bắt buộc | Giảm C7, report penalty. |
| HF-FAKE-REFERENCE | Integrity | Critical | Có | Hard penalty. |
| HF-NO-REFERENCE-LIST | Integrity | Critical | Có | Không tính đầy đủ Trust Score. |

---

## 23. Quy tắc gợi ý chỉnh sửa

### 23.1. Gợi ý theo lỗi metadata

| Lỗi | Gợi ý mặc định |
|---|---|
| Thiếu tác giả | “Bổ sung tác giả cá nhân hoặc tổ chức chịu trách nhiệm.” |
| Thiếu năm | “Bổ sung năm xuất bản/cập nhật; nếu là website, thêm ngày truy cập.” |
| Thiếu nguồn công bố | “Bổ sung tên journal/conference/publisher/website.” |
| Thiếu DOI | “Nếu tài liệu có DOI, bổ sung DOI để tăng khả năng xác minh.” |
| URL chết | “Thay bằng DOI, URL publisher hoặc bản archived hợp pháp.” |

### 23.2. Gợi ý theo lỗi reliability

| Lỗi | Gợi ý mặc định |
|---|---|
| Nguồn không học thuật cho lập luận chính | “Thay bằng paper, sách học thuật, tiêu chuẩn kỹ thuật hoặc tài liệu chính thức.” |
| Blog cá nhân | “Chỉ giữ nếu dùng làm ví dụ triển khai; bổ sung nguồn học thuật cho phần lý thuyết.” |
| Vendor bias | “Bổ sung nguồn độc lập để cân bằng nhận định.” |
| Preprint | “Ghi rõ đây là preprint hoặc bổ sung bản đã peer-reviewed nếu có.” |

### 23.3. Gợi ý theo lỗi relevance

| Lỗi | Gợi ý mặc định |
|---|---|
| Nguồn không liên quan | “Thay bằng nguồn có title/abstract gần hơn với chủ đề báo cáo.” |
| Citation sai ngữ cảnh | “Kiểm tra đoạn văn chứa citation; nguồn có thể không hỗ trợ phát biểu hiện tại.” |
| Chỉ khớp từ khóa | “Đọc abstract/full text để xác nhận nội dung thật sự liên quan.” |

### 23.4. Gợi ý theo lỗi recency

| Lỗi | Gợi ý mặc định |
|---|---|
| Nguồn quá cũ | “Bổ sung nguồn trong 3–5 năm gần nhất cho lĩnh vực thay đổi nhanh.” |
| Documentation sai version | “Dùng tài liệu đúng version framework/API đang triển khai.” |
| Nguồn cũ nhưng seminal | “Có thể giữ, nhưng nên bổ sung nguồn hiện đại để phản ánh trạng thái hiện tại.” |

### 23.5. Gợi ý theo lỗi citation format

| Lỗi | Gợi ý mặc định |
|---|---|
| Trộn style | “Chọn một chuẩn duy nhất cho toàn bài, ví dụ APA 7th hoặc IEEE.” |
| Thiếu in-text citation | “Kiểm tra nguồn này có được trích trong nội dung hay không.” |
| Thiếu reference cuối bài | “Bổ sung mục reference tương ứng với citation trong nội dung.” |
| Sai numbering IEEE | “Sắp xếp và đánh số lại theo thứ tự xuất hiện trong nội dung.” |

---

## 24. Quy tắc override của giảng viên

### 24.1. Mục đích override

Giảng viên có thể điều chỉnh kết quả khi hệ thống chưa đủ dữ liệu hoặc có tình huống chuyên môn đặc thù.

### 24.2. Loại override

| Loại override | Mô tả |
|---|---|
| Accept Source | Chấp nhận nguồn dù có cảnh báo. |
| Reject Source | Loại nguồn dù hệ thống chấm điểm trung bình/cao. |
| Adjust Score | Điều chỉnh điểm một hoặc nhiều tiêu chí. |
| Mark Seminal | Đánh dấu nguồn cũ là nguồn nền tảng. |
| Change Source Role | Đổi vai trò nguồn: lý thuyết, triển khai, bối cảnh, dữ liệu. |
| Resolve Flag | Đánh dấu cảnh báo đã được xử lý. |

### 24.3. Log override bắt buộc

| Trường | Bắt buộc? | Mô tả |
|---|---|---|
| override_id | Có | Mã thao tác override. |
| user_id | Có | Người thực hiện. |
| reference_id | Có | Nguồn được điều chỉnh. |
| old_score | Có | Điểm trước override. |
| new_score | Có nếu chỉnh điểm | Điểm sau override. |
| reason | Có | Lý do học thuật. |
| timestamp | Có | Thời điểm thực hiện. |
| affected_criteria | Có | C1–C8 hoặc report-level. |

---

## 25. Quy tắc kiểm thử bộ tiêu chí

### 25.1. Nhóm test case bắt buộc

| Nhóm test | Mô tả | Kỳ vọng |
|---|---|---|
| TC-VALID-APA | Reference APA đầy đủ, DOI đúng | Điểm cao, không cảnh báo nghiêm trọng. |
| TC-VALID-IEEE | Reference IEEE đầy đủ | Nhận diện đúng style, điểm C6 cao. |
| TC-MISSING-YEAR | Thiếu năm | C1/C5 giảm, cảnh báo C1-MISSING-YEAR. |
| TC-DOI-MISMATCH | DOI trỏ sai bài | C2/C8 thấp, hard flag. |
| TC-FAKE-REFERENCE | Tài liệu không tồn tại | C2 thấp, HF-FAKE-REFERENCE. |
| TC-LOW-RELEVANCE | Nguồn xác minh được nhưng không liên quan | C4 thấp. |
| TC-OLD-AI-SOURCE | Nguồn quá cũ cho AI hiện đại | C5 thấp, C5-OUTDATED-FAST-FIELD. |
| TC-SEMINAL-OLD | Nguồn cũ nhưng seminal | C5 không bị trừ mạnh, flag nhẹ. |
| TC-BLOG-CORE-CLAIM | Blog dùng làm nguồn chính cho lý thuyết | C3 thấp, C3-NON-ACADEMIC-CORE. |
| TC-MIXED-STYLE | Trộn APA và IEEE | C6 thấp, report penalty. |
| TC-DUPLICATE-REF | Trùng DOI/title | C7 giảm, cảnh báo duplicate. |
| TC-NO-REF-LIST | Không có danh mục tài liệu tham khảo | Hard flag, không tính đầy đủ score. |

### 25.2. Chỉ số đánh giá chất lượng bộ tiêu chí

| Chỉ số | Mô tả | Mục tiêu v1.0 |
|---|---|---|
| Parser success rate | Tỷ lệ reference tách được từ file mẫu. | >= 80% với file chuẩn. |
| Metadata extraction accuracy | Tỷ lệ trường author/year/title/DOI parse đúng. | >= 75% trên sample nội bộ. |
| Verification precision | Tỷ lệ nguồn được xác minh đúng. | >= 80% trên sample có DOI. |
| Flag usefulness | Tỷ lệ cảnh báo được giảng viên đánh giá là hữu ích. | >= 70% trong thử nghiệm. |
| Explanation clarity | Mức độ dễ hiểu của lý do chấm điểm. | Đạt đánh giá thủ công “đủ hiểu”. |

---

## 26. Mapping tiêu chí với màn hình sản phẩm

| Màn hình | Tiêu chí liên quan | Nội dung hiển thị |
|---|---|---|
| Upload File | C1, parser readiness | File type, trạng thái đọc file, lỗi parse. |
| Processing Status | Toàn bộ pipeline | Tiến độ: extract, verify, score, export. |
| Reference List | C1–C8 | Danh sách nguồn, điểm, trạng thái xác minh. |
| Reference Detail | C1–C8 | Metadata, evidence, flags, explanation. |
| Report Dashboard | Report-level metrics | Trust Score tổng, tỷ lệ xác minh, tỷ lệ nguồn học thuật, lỗi chính. |
| Suggested Fixes | Flags | Gợi ý sửa theo từng lỗi. |
| Export Report | Toàn bộ | Xuất PDF/DOCX/XLSX bảng đánh giá. |
| Manual Review | Override | Chấp nhận/từ chối/chỉnh điểm nguồn. |

---

## 27. Mapping tiêu chí với backend/database

### 27.1. Bảng dữ liệu đề xuất

| Bảng | Vai trò |
|---|---|
| documents | Lưu bài nộp, file path, trạng thái xử lý. |
| references | Lưu raw reference và metadata đã parse. |
| reference_metadata | Lưu metadata chuẩn hóa và metadata từ API. |
| reference_scores | Lưu điểm C1–C8 và Trust Score. |
| reference_flags | Lưu cảnh báo theo từng nguồn. |
| report_scores | Lưu điểm và chỉ số tổng hợp cấp báo cáo. |
| external_lookup_logs | Lưu lịch sử gọi API metadata. |
| manual_overrides | Lưu thao tác override của giảng viên. |
| evaluation_versions | Lưu version bộ tiêu chí và thông số trọng số. |

### 27.2. Trường điểm đề xuất trong `reference_scores`

```text
reference_id
criteria_version
c1_metadata_completeness
c2_metadata_verification
c3_source_reliability
c4_topic_relevance
c5_recency
c6_citation_quality
c7_source_diversity
c8_integrity_risk
reference_trust_score
confidence_level
created_at
updated_at
```

---

## 28. Mapping tiêu chí với API

| Endpoint | Method | Mục đích | Tiêu chí liên quan |
|---|---|---|---|
| `/api/documents/upload` | POST | Upload file báo cáo | C1, pipeline. |
| `/api/documents/{id}/references` | GET | Lấy danh sách reference | C1–C8. |
| `/api/references/{id}` | GET | Lấy chi tiết một nguồn | C1–C8, evidence, flags. |
| `/api/documents/{id}/score` | GET | Lấy Report Trust Score | Report-level. |
| `/api/documents/{id}/flags` | GET | Lấy cảnh báo tổng hợp | Flags. |
| `/api/references/{id}/override` | POST | Override kết quả | Manual review. |
| `/api/documents/{id}/export` | POST | Xuất báo cáo kết quả | Output. |

---

## 29. Ví dụ diễn giải kết quả

### 29.1. Trường hợp nguồn tốt

```text
REF-003 đạt 91/100. Nguồn có DOI xác minh được, title/author/year khớp metadata bên ngoài, là conference paper phù hợp với chủ đề báo cáo. Citation có một lỗi nhỏ về capitalization nên C6 đạt 8/10. Không cần thay nguồn; chỉ cần chuẩn hóa lại format.
```

### 29.2. Trường hợp nguồn tồn tại nhưng không phù hợp

```text
REF-008 đạt 58/100. Nguồn tồn tại và có metadata khớp, nhưng semantic similarity với chủ đề báo cáo thấp. Nguồn thuộc lĩnh vực quản trị chung, trong khi báo cáo tập trung vào mô hình phân loại văn bản. Cần thay bằng tài liệu liên quan trực tiếp đến NLP hoặc text classification.
```

### 29.3. Trường hợp nguồn rủi ro cao

```text
REF-012 đạt 22/100. DOI không resolve được, title không tìm thấy trong CSDL học thuật và URL không truy cập được. Hệ thống gắn hard flag HF-FAKE-REFERENCE. Cần kiểm tra thủ công và thay bằng nguồn xác minh được.
```

---

## 30. Chính sách dữ liệu và đạo đức

1. TrustLens chỉ đánh giá metadata, cấu trúc citation và mức độ phù hợp; không nên tự động kết luận gian lận nếu thiếu bằng chứng.
2. Các cảnh báo như `fake reference`, `predatory risk`, `AI-generated source` phải được trình bày là rủi ro cần kiểm tra, trừ khi có bằng chứng xác minh rõ.
3. Nếu dùng dữ liệu từ API bên ngoài, cần lưu lại nguồn đối chiếu, thời điểm gọi và trạng thái phản hồi.
4. Hệ thống không nên lưu lâu dài nội dung bài nộp nếu không cần thiết cho mục đích đánh giá.
5. Khi export báo cáo, cần tránh hiển thị dữ liệu nhạy cảm ngoài phạm vi học thuật.
6. Người dùng cần biết điểm số là công cụ hỗ trợ, không phải phán quyết tuyệt đối.

---

## 31. Hạn chế của bộ tiêu chí v1.0

| Hạn chế | Tác động | Biện pháp giảm thiểu |
|---|---|---|
| Parser có thể sai với reference format phức tạp | Điểm C1/C6 có thể thấp không chính xác | Cho phép edit metadata thủ công. |
| API metadata có thể thiếu dữ liệu | Nguồn thật nhưng bị đánh `partially_verified` | Kết hợp nhiều API và manual review. |
| Semantic similarity phụ thuộc model embedding | Có thể đánh giá sai nguồn liên quan gián tiếp | Dùng citation-in-context và override. |
| Không truy cập full text của mọi nguồn | Không kiểm tra được câu trích dẫn cụ thể | Chỉ kết luận ở mức metadata/abstract. |
| Không có danh sách venue uy tín hoàn chỉnh | C3 có thể chưa đủ sâu | Bổ sung whitelist/blacklist thủ công theo khoa. |
| Khác biệt chuẩn citation theo giảng viên | C6 có thể không khớp yêu cầu nội bộ | Cho phép cấu hình style và rule riêng. |

---

## 32. Hướng phát triển v1.1+

| Hướng phát triển | Mô tả |
|---|---|
| Citation-in-context checking | Kiểm tra từng câu có citation có thật sự được nguồn hỗ trợ. |
| Field-specific scoring profile | Tạo profile riêng cho AI, security, web, database, software engineering. |
| Venue reputation database | Tích hợp danh sách journal/conference/publisher đáng tin. |
| Retraction/paper risk check | Kiểm tra nguồn bị rút bài hoặc có cảnh báo học thuật. |
| Local rubric customization | Cho phép giảng viên chỉnh trọng số C1–C8. |
| Reference recommendation | Gợi ý nguồn thay thế khi nguồn yếu hoặc không phù hợp. |
| Multilingual citation support | Hỗ trợ tốt hơn tài liệu tiếng Việt và tiếng Anh. |
| Full-text evidence matching | Kiểm tra nội dung nguồn thay vì chỉ metadata/abstract. |

---

## 33. Checklist triển khai v1.0

| Hạng mục | Trạng thái cần đạt |
|---|---|
| Tách được danh mục tài liệu tham khảo từ PDF/DOCX | Bắt buộc. |
| Parse được author/year/title/DOI/URL | Bắt buộc. |
| Xác minh được DOI hoặc title qua ít nhất một metadata source | Bắt buộc. |
| Tính được C1–C8 cho từng reference | Bắt buộc. |
| Tính được Report Trust Score | Bắt buộc. |
| Sinh bảng cảnh báo và gợi ý sửa | Bắt buộc. |
| Hiển thị breakdown điểm trên FE | Bắt buộc. |
| Export được kết quả | Nên có. |
| Cho phép manual override | Nên có. |
| Lưu evaluation version | Nên có. |

---

## 34. Bảng tóm tắt nhanh cho nhóm phát triển

| Nhóm | Công việc chính | Output |
|---|---|---|
| BA/Academic Evaluation | Hoàn thiện rule C1–C8, test case, expected output | Criteria spec, scoring rubric, sample output. |
| DS/NLP | Thiết kế relevance scoring, threshold, confidence score | Model/algorithm spec, similarity score. |
| Backend | Parser, metadata lookup, scoring engine, database | API, score computation, logs. |
| Frontend | Dashboard, reference table, detail view, export UI | UI đánh giá, cảnh báo, gợi ý sửa. |
| QA | Test case nguồn đúng/sai/cũ/không phù hợp | Test report, bug list. |

---

## 35. Version history

| Version | Ngày | Người phụ trách | Thay đổi |
|---|---|---|---|
| v1.0 | 07/06/2026 | Product/BA + AI/NLP Owner | Khởi tạo bộ tiêu chí C1–C8, scoring model, flags, output schema, report-level metrics. |

---

## 36. Kết luận

Bộ tiêu chí TrustLens v1.0 tập trung vào 8 nhóm đánh giá: độ đầy đủ metadata, xác minh tồn tại học thuật, độ tin cậy nguồn, độ phù hợp, độ cập nhật, chất lượng citation, tính đa dạng nguồn và rủi ro học thuật. Cấu trúc này đủ để triển khai MVP có khả năng giải thích điểm, sinh cảnh báo và hỗ trợ giảng viên ra quyết định khi thẩm định danh mục tài liệu tham khảo trong báo cáo Công nghệ Thông tin.
