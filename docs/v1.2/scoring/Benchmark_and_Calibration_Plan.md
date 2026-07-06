# Kế hoạch Benchmark và Calibration Trust Score v1.2

**Mục tiêu:** đánh giá Trust Score như một công cụ hỗ trợ rà soát, không biến điểm số thành phán quyết tự động về gian lận hoặc tính đúng khoa học.

## 1. Câu hỏi đánh giá

Benchmark phải trả lời riêng:

1. Parser có trích xuất đúng citation fields không?
2. Metadata matching có chọn đúng record không?
3. Credibility evidence có nhất quán theo source type không?
4. Relevance có phân biệt tài liệu liên quan/không liên quan trong tiếng Việt và tiếng Anh không?
5. Recency rule có phù hợp lĩnh vực không?
6. Citation-style detection có bắt lỗi thực tế không?
7. Duplicate/concentration detection có false positive ở mức nào?
8. Report Trust Score có tương quan hữu ích với đánh giá chuyên gia không?

Không dùng một metric tổng hợp duy nhất để trả lời toàn bộ câu hỏi trên.

## 2. Dataset design

### Nhóm dữ liệu

- Báo cáo CNTT tiếng Việt.
- Báo cáo CNTT tiếng Anh.
- PDF và DOCX.
- APA, IEEE và mixed/inconsistent style.
- DOI đúng, DOI sai, DOI conflict, không DOI.
- Journal, conference, book, website, blog, preprint.
- Tài liệu mới, nền tảng cũ nhưng hợp lệ và tài liệu lỗi thời theo ngữ cảnh.
- Duplicate exact và fuzzy.
- Provider unavailable/degraded cases.

### Dataset card

Phải ghi:

- nguồn và quyền sử dụng;
- cách ẩn danh;
- số lượng document/citation;
- phân bố ngôn ngữ, source type, năm, style;
- tiêu chí chọn mẫu;
- limitation và bias;
- train/dev/test split nếu có model learning.

Không dùng bài nộp sinh viên thật ngoài mục đích đã thông báo hoặc chưa có cơ sở phù hợp.

## 3. Annotation protocol

Mỗi mẫu test quan trọng cần ít nhất hai người gán nhãn độc lập.

Nhãn tối thiểu:

- citation boundaries;
- author/title/year/venue/DOI/URL ground truth;
- correct metadata candidate;
- source type;
- relevance class và rationale;
- citation style violation;
- duplicate relation;
- expert review label ở cấp citation/report.

Bất đồng được adjudicate bởi người thứ ba hoặc quy trình thống nhất có ghi nhận.

Đo agreement phù hợp loại nhãn, ví dụ Cohen's kappa hoặc Krippendorff's alpha. Không coi consensus sau thảo luận là independent agreement.

## 4. Metrics theo component

| Component | Metric chính |
|---|---|
| Citation parsing | precision/recall/F1 theo field và exact match |
| Metadata matching | top-1 accuracy, precision/recall, candidate margin analysis |
| C1 | agreement với completeness rubric |
| C2 | precision/recall theo verified/partial/ambiguous/not-found |
| C3 | agreement theo source-type/credibility rubric |
| C4 | macro-F1, calibration, subgroup language analysis |
| C5 | agreement theo domain threshold |
| C6 | precision/recall theo violation type |
| C7 | duplicate precision/recall; concentration consistency |
| Report score | correlation, ranking agreement, calibration/error bands |

Báo cáo false positive/false negative theo subgroup; không chỉ báo cáo average.

## 5. Fixed test vectors

Mỗi scoring version phải có bộ vector cố định chứa:

- input citation/metadata/context;
- expected raw component values;
- expected weighted values;
- expected total/label;
- expected confidence/evidence fields;
- expected warning/penalty.

Vector phải kiểm tra boundary:

```text
49.99 / 50
69.99 / 70
84.99 / 85
```

Và các invariant:

- tổng weight bằng 100;
- weight không âm;
- invalid config fallback/reject an toàn;
- NOT_FOUND không đồng nghĩa fake;
- URL reachable không đồng nghĩa academic;
- confidence tách khỏi Trust Score.

## 6. Calibration

Calibration chỉ thực hiện sau khi rubric và dataset đủ ổn định.

Quy trình:

1. Khóa scoring version.
2. Chạy benchmark mù trên test set.
3. So sánh score/label với expert review.
4. Phân tích reliability theo score bands.
5. Điều chỉnh threshold/weight/profile nếu có bằng chứng.
6. Tăng scoring version khi thay đổi có tác động.
7. Chạy lại fixed vectors và regression suite.

Không điều chỉnh threshold trực tiếp trên final test set rồi công bố cùng kết quả.

## 7. Human-in-the-loop policy

Trust Score là tín hiệu ưu tiên rà soát. Quyết định học thuật phải cho phép:

- xem evidence;
- xem provider/fallback/confidence;
- ghi nhận reviewer decision;
- override có lý do;
- appeal/correction nếu áp dụng;
- audit scoring version.

## 8. Benchmark report structure

1. Executive summary.
2. Dataset card.
3. Annotation guide và agreement.
4. System/scoring version.
5. Metrics tổng và subgroup.
6. Error analysis.
7. Calibration analysis.
8. Known limitations.
9. Recommended operating threshold.
10. Changelog và approval.

## 9. Release rule

Trước khi benchmark được phê duyệt, UI/report phải ghi rõ:

> Trust Score là tín hiệu hỗ trợ rà soát dựa trên metadata và bằng chứng khả dụng; không phải kết luận tự động về tính đúng khoa học hoặc hành vi gian lận.

## 10. Dataset Card Template

Moi benchmark release phai co dataset card rieng:

| Field | Required content |
|---|---|
| Dataset version | Immutable version, date, owner |
| Source | Synthetic, public, licensed, or anonymized internal source |
| Consent/legal basis | Usage permission and retention rule |
| Privacy handling | Anonymization, PII removal, storage access |
| Size | Document count, citation count, report count |
| Distribution | Language, file type, citation style, source type, year bands |
| Inclusion criteria | Why samples are in scope |
| Exclusion criteria | What is not represented |
| Known bias | Institution/domain/language/provider coverage bias |
| Split | Train/dev/test if any learning/calibration occurs |
| Frozen artifacts | Checksums for files, labels and expected outputs |

No real student submission may enter the benchmark unless the project has a documented
lawful basis, anonymization process and access control.

## 11. Annotation Protocol Checklist

Annotation guide must define:

- citation boundary rules;
- field normalization rules for author/title/year/venue/DOI/URL;
- metadata-match decision rules for exact, partial, ambiguous and not-found;
- credibility source-type rubric;
- relevance labels with positive/negative examples in Vietnamese and English;
- recency/domain exceptions;
- citation-style violation taxonomy;
- exact duplicate, near duplicate and concentration definitions;
- report-level expert review scale.

Acceptance:

- at least two independent annotators for gold samples;
- disagreement log and adjudication owner;
- inter-annotator agreement reported before adjudication;
- label changes are versioned and old benchmark results remain reproducible.

## 12. C1-C7 Metric Requirements

| Component | Primary metric | Required slices |
|---|---|---|
| C1 Completeness | Field-level precision/recall/F1 | PDF/DOCX, APA/IEEE, Vietnamese/English |
| C2 Existence | Verified/partial/ambiguous/not-found precision/recall | DOI, URL, title-only, provider |
| C3 Credibility | Agreement with source-type rubric | journal, conference, book, website, blog, preprint |
| C4 Relevance | Macro-F1 and calibration by confidence band | language, domain, abstract present/missing |
| C5 Recency | Agreement with domain threshold rubric | fast-moving field vs foundational source |
| C6 Style | Precision/recall by violation type | APA, IEEE, mixed style |
| C7 Duplication | Duplicate precision/recall and concentration error rate | exact DOI/URL, fuzzy title, repeated domain |

Report-level score must include correlation/ranking agreement with expert review, but
component metrics must remain visible. A single aggregate accuracy is not sufficient.

## 13. Fixed Test Vector Schema

Each scoring version must ship fixed vectors with this minimum schema:

```json
{
  "vector_id": "trust-score-v1.2-boundary-001",
  "scoring_version": "trust-score-v1.2",
  "input": {
    "citation": {},
    "metadata": {},
    "assignment_context": {}
  },
  "expected": {
    "components": {
      "C1": 0,
      "C2": 0,
      "C3": 0,
      "C4": 0,
      "C5": 0,
      "C6": 0,
      "C7": 0
    },
    "weighted_total": 0,
    "label": "needs_review",
    "confidence": "low",
    "warnings": []
  }
}
```

Required vector groups:

- label boundaries at `49.99/50`, `69.99/70`, `84.99/85`;
- invalid weight config;
- `NOT_FOUND` provider result;
- URL reachable but non-academic;
- provider timeout with fallback;
- duplicate DOI and fuzzy duplicate;
- old but foundational source;
- confidence changes that must not silently change Trust Score.

## 14. Human-in-the-Loop Operating Policy

Trust Score may be used to prioritize review, not to make automatic academic or
disciplinary decisions.

Required product behavior:

- UI/report shows evidence, provider status, fallback and confidence;
- reviewer can record decision and rationale;
- overrides are auditable with scoring version;
- appeal/correction workflow is documented when used in coursework;
- benchmark/calibration status is disclosed if not approved.

Forbidden use:

- automatic fraud finding;
- automatic grade penalty;
- rejecting a citation only because a provider returns `NOT_FOUND`;
- representing Trust Score as probability of misconduct.
