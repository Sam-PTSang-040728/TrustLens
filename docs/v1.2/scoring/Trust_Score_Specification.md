# 05. Đặc tả Trust Score v1.2

## 1. Phạm vi và nguồn

Baseline được xác định bởi:

- Backend `app/core/trust_score_definition.py`.
- Backend `app/services/scoring_service.py`.
- Frontend `src/config/trustScoreConfig.ts`.
- Version mặc định `trust-score-v1.2`.

Kế hoạch P0 cũ dùng C1–C8 không còn khớp implementation. Baseline chính thức hiện tại là C1–C7.

## 2. Công thức tổng quát

```text
Reference Trust Score = C1 + C2 + C3 + C4 + C5 + C6 + C7
```

```text
10 + 25 + 20 + 20 + 10 + 10 + 5 = 100
```

Report score được aggregate từ citation score và report-level penalty. Penalty phải tách khỏi component weight và được giải thích trong report. Không công bố công thức aggregate chi tiết hơn khi chưa có spec/test vector chính thức.

## 3. Thành phần

| Mã | Tên | Weight | Mục tiêu |
|---|---|---:|---|
| C1 | Metadata completeness | 10 | Độ đầy đủ trường citation |
| C2 | Identity and metadata verification | 25 | Mức khớp provider |
| C3 | Source credibility evidence | 20 | Loại nguồn, venue, publisher |
| C4 | Relevance to report | 20 | Mức liên quan với report |
| C5 | Recency | 10 | Tuổi tài liệu theo bối cảnh |
| C6 | Citation-style compliance | 10 | Style theo assignment |
| C7 | Duplicate and concentration risk | 5 | Trùng và tập trung loại nguồn |

### C1

Xét author, title, year, venue/publisher, DOI/URL. Required fields phải phụ thuộc source type.

### C2

Xét DOI match, title similarity, author/year consistency, candidate count/margin và provider status. URL trả 200 không đủ cho điểm tối đa.

### C3

Xét source type, publisher, venue, domain, publication/retraction evidence khi có. Whitelist không chứng minh uy tín tuyệt đối.

### C4

Input gồm body report đã loại reference section, assignment context, title/abstract/keyword của reference. Evidence phải ghi provider, model, prompt version, threshold profile, fallback và confidence.

### C5

Ngưỡng mặc định:

| Mức | Tuổi |
|---|---:|
| Fresh | ≤ 3 năm |
| Acceptable | ≤ 5 năm |
| Review | ≤ 10 năm |
| Old | ≤ 15 năm |
| Very old | > 15 năm |

Phải hiệu chỉnh theo lĩnh vực; tài liệu nền tảng không tự động bị coi là lỗi thời.

### C6

So detected style với `required_style`. Style đúng không đồng nghĩa metadata đúng.

### C7

Duplicate detection:

1. DOI chuẩn hóa giống nhau.
2. URL chuẩn hóa giống nhau.
3. Fuzzy title + author + year.

C7 cũng theo dõi rủi ro tập trung nguồn.

## 4. Label

| Điểm | Label |
|---:|---|
| ≥ 85 | `reliable` |
| ≥ 70 | `acceptable` |
| ≥ 50 | `needs_review` |
| < 50 | `high_risk` |

Label là tín hiệu rà soát, không phải phán quyết.

## 5. Confidence

Confidence độc lập với Trust Score. Provider unavailable, fallback lexical hoặc metadata thiếu phải giảm confidence và được ghi trong evidence.

## 6. Evidence contract

```json
{
  "score": 18.0,
  "max_score": 20,
  "raw_score": 18.0,
  "raw_max_score": 20,
  "ratio": 0.9,
  "reason": "Mô tả ngắn",
  "evidence": {},
  "confidence": 0.86
}
```

## 7. Invariant

| ID | Quy tắc |
|---|---|
| TS-INV-001 | Tổng weight active bằng 100 |
| TS-INV-002 | Weight không âm |
| TS-INV-003 | Config sai phải reject/fallback an toàn |
| TS-INV-004 | Report lưu scoring version |
| TS-INV-005 | `NOT_FOUND` không chứng minh fake |
| TS-INV-006 | URL reachable không chứng minh academic |
| TS-INV-007 | Score không thay chuyên gia |
| TS-INV-008 | Confidence khác Trust Score |
| TS-INV-009 | Penalty phải được công khai |
| TS-INV-010 | Thay đổi logic cần test vector và version |

## 8. Preset frontend

Frontend định nghĩa `DEFAULT`, `SOURCE_FOCUS`, `RELEVANCE_FOCUS`. Việc preset thực sự được lưu và áp dụng xuyên backend phải được xác nhận bằng API/integration test; frontend không được là nguồn sự thật độc lập.

## 9. Giới hạn

- Provider không bao phủ mọi nguồn.
- Parser có thể sai format hiếm.
- Abstract có thể thiếu.
- Embedding phụ thuộc ngôn ngữ/model.
- Recency phụ thuộc lĩnh vực.
- Credibility không thể chứng minh hoàn toàn bằng metadata.
- Fuzzy duplicate có false positive.
- Report score cần benchmark với chuyên gia.

## 10. Khi nào tăng version

Tăng version khi đổi weight, label threshold, component logic, aggregate, penalty, provider/model có tác động, calibration profile, source mapping hoặc duplicate threshold. Mỗi version cần changelog, test vector, benchmark summary và compatibility note.
