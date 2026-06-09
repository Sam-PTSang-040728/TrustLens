
| **Loại kiểm thử**      | **Phạm vi**                                                         | **Công cụ/Phương pháp**        |
|------------------------|---------------------------------------------------------------------|--------------------------------|
| Unit test              | Scoring formula, parsing utility, validation, warning rules.        | pytest cho backend Python.     |
| Integration test       | Upload -\> job -\> extraction -\> report; API auth/permission.      | pytest + test database.        |
| Algorithm validation   | Citation extraction, DOI lookup, title matching, relevance ranking. | Tập file mẫu có nhãn thủ công. |
| UI test thủ công       | Login, upload, dashboard, filter, export.                           | Checklist demo.                |
| Security smoke test    | Không token, sai role, file sai định dạng, file quá lớn.            | HTTP client/Insomnia/Postman.  |
| Performance smoke test | File 10-20 trang, batch 5 file.                                     | Đo thời gian job và response.  |
