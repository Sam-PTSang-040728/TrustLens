
| **Bước** | **Mục tiêu**         | **Đầu vào**                            | **Đầu ra**                                |
|----------|----------------------|----------------------------------------|-------------------------------------------|
| NLP-01   | Tiền xử lý văn bản   | Full text, reference section           | Text sạch, câu/đoạn, topic candidates.    |
| NLP-02   | Trích chủ đề báo cáo | Title, abstract/introduction, heading  | Report topic vector và keywords.          |
| NLP-03   | Chuẩn hóa citation   | Raw citation                           | Fields chuẩn hóa và confidence.           |
| NLP-04   | Metadata matching    | DOI/title/author/year                  | Best candidate, match_status, confidence. |
| NLP-05   | Relevance embedding  | Report topic + citation title/abstract | Cosine similarity và relevance_score.     |
| NLP-06   | Warning generation   | Scores + metadata + rules              | Warning code, severity, explanation.      |
